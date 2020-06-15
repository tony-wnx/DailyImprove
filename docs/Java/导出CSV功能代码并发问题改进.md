## 导出CSV功能代码并发问题改进

> 需求：由于服务器硬件设备限制，要求每次只能有一个人进行导出操作，并且要异步导出，导出过程中可以执行其他的操作

* 改进前

* 改进前存在的问题：

  * 使用synchronized锁，单服务的情况下，可能不会出现问题，但是如果是多服务的话，就会存在并发问题。
  * synchronized锁存在性能问题，假如有大量请求同时到达，那么只有一个能获取锁，其他线程都会阻塞在锁处，时间段还好，时间长的话就会出问题。
  * 抛开上边的两个问题，单服务情况下，没有问题，但是使用了Double Check，进行了两次查库操作，造成性能的浪费。

  ```java
  @Controller
  @RequestMapping("/queryController")
  public class QueryController extends BaseController {
      /**
       * Logger for this class
       */
      private static final Logger logger = Logger.getLogger(QueryController.class);
  
      @Autowired
      private QuerySqlServiceI querySqlService;
      @Autowired
      private QueryServiceI queryService;
      
  	/**
       * 根据查询条件导出数据，每次只能有一人进行导出操作
       *
       * @param querySql
       * @param request
       * @param response
       * @throws Exception
       */
      @RequestMapping(params = "exportData")
      public void exportData(QuerySqlEntity querySql, HttpServletRequest request, HttpServletResponse response) {
          if (querySql.getSqlId() == null) {
              feedBackMsg(response, "json", ExportMsgEnum.SQLID_NOT_EXITS.getExportMessage());
              return;
          }
          // 设置数据源
          setDataSource("");
          TaskData taskData = querySqlService.getEntity(TaskData.class, 9999);
          // 获取锁标志位；0：没有线程占据锁；1：有线程占据锁
          if (taskData.getLockFlag() != 0) {
              feedBackMsg(response, "json", ExportMsgEnum.OTHER_USER_EXPORTING.getExportMessage());
              return;
          }
  性能问题: synchronized (QueryController.class) {
              // 锁标志位recheck
  性能问题:    TaskData recheckTaskData = querySqlService.getEntity(TaskData.class, 9999);
              if (recheckTaskData.getLockFlag() != 0) {
                  feedBackMsg(response, "json", ExportMsgEnum.OTHER_USER_EXPORTING.getExportMessage());
                  return;
              }
              try {
                  // 获取锁，修改锁标志位
                  String sql = "UPDATE `task_data` SET `lockFlag` = 1 WHERE `id` = 9999 ;";
                  querySqlService.executeSql(sql);
                  QuerySqlEntity querySqlEntity = this.querySqlService.findUniqueByProperty(QuerySqlEntity.class, "sqlId", querySql.getSqlId());
                  StateObject stateObject = getExportParamObj(querySql, querySqlEntity, request);
                  this.queryService.installSql(stateObject.getParameterMap(), querySqlEntity);
                  // 切换数据源
                  setDataSource(querySqlEntity.getAuthor());
                  // 获取导出的总记录数
                  int totalRows = queryService.getRowCount();
                  if (!isAllowExport(totalRows)) {
                      feedBackMsg(response, "json", ExportMsgEnum.OVER_MAX_MOUNT.getExportMessage());
                      return;
                  }
                  // 异步导出
                  excelExportAsyn(stateObject, totalRows);
              } catch (Exception e) {
                  feedBackMsg(response, "json", ExportMsgEnum.EXPORT_ERROR.getExportMessage());
              }
          }
      }
  
      /**
       * 封装导出需要的参数成对象
       *
       * @return
       */
      private StateObject getExportParamObj(QuerySqlEntity querySql, QuerySqlEntity querySqlEntity, HttpServletRequest request) {
          // 当前用户
          String userName = LoginUtil.getLoginUserInfo().getUserName();
          // 文件存放路径
          String path = ResourceUtil.getConfigByName("download.active.path") + "/";
          SimpleDateFormat sdf = new SimpleDateFormat("yyyyMMdd_HHmmss");
          String date = sdf.format(new Date());
          String fileName = querySqlEntity.getHead() + "_" + date + ".csv";
          File filePath = new File(path);
          if (!filePath.exists()) {
              filePath.mkdirs();
          }
  
          String host = request.getHeader("Host");
          Map parameterMap = request.getParameterMap();
          StateObject stateObject = new StateObject(path, querySql, fileName, userName, parameterMap, host);
          stateObject.setQuerySqlEntity(querySqlEntity);
          return stateObject;
      }
  
      /**
       * 导出数据量校验
       * 导出最大量 50W
       *
       * @return true：数据量<50W,允许导出；false：数据量>50W，不允许导出
       */
      private boolean isAllowExport(int totalRows) {
          int topLimt = 500000;
          String topLimitNumber = ResourceUtil.getConfigByName("toplimitnumber");
          if (StringUtils.isNotBlank(topLimitNumber)) {
              topLimt = Integer.valueOf(topLimitNumber);
          }
          return totalRows < topLimt;
      }
  
      /**
       * 导出操作回馈信息
       *
       * @param response
       * @param type     返回类型
       * @param msg      返回消息内容
       */
      private void feedBackMsg(HttpServletResponse response, String type, String msg) {
          if (type.equals("json")) {
              response.setContentType("application/json;charset=UTF-8");
          } else if (type.equals("html")) {
              response.setContentType("text/html;charset=UTF-8");
          }
          try {
              response.getWriter().write(msg);
              response.getWriter().flush();
          } catch (IOException e) {
              logger.error("queryController：feedBackMsg()：IO异常", e);
          }
      }
  
      /**
       * 异步导出csv文件
       *
       * @param params    参数对象
       * @param totalRows 导出数据总行数
       */
      private void excelExportAsyn(final StateObject params, final int totalRows) {
          ExecutorService pool = Executors.newSingleThreadExecutor();
          pool.execute(new Runnable() {
              @Override
              public void run() {
                  try {
                      String dataSourceType = DataSourceContextHolder.getDataSourceType();
                      String startTime = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(System.currentTimeMillis());
                      String game = params.getQuerySqlEntity().getGame();// 游戏
                      String taskName = params.getQuerySqlEntity().getHead();// 菜单名称
                      String activityVersions = params.getQuerySqlEntity().getActivity();// 活动版本
                      String url = "<a href='http://" + params.getHost() + "/queryController.do?downloadFile&path=" + params.getPath() + params.getFileName() + "' target='_blank'>点击下载</a>";// 下载地址
                      setDataSource("");// 切换数据源
  
                      // 导出中
                      String saveSql = "insert into export_data (taskName,game,activity,url,startTime,userName,exportVolume,status) value(?,?,?,?,?,?,?,?)";
                      querySqlService.executeSql(saveSql, taskName, game, activityVersions, url, startTime, params.getUserName(), totalRows + "条", "数据导出中...");
                      // 导出
                      boolean exportFlag = doExportCsv(params, totalRows, dataSourceType);
                      // 切换数据源
                      setDataSource("");
                      if (exportFlag) {
                          // 导出正常
                          String endTime = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(System.currentTimeMillis());
                          String sql = "update export_data set status = ?,endTime=? where startTime = ? and userName = ? and taskName = ? ;";
                          querySqlService.executeSql(sql, "导出完成，待下载...", endTime, startTime, params.getUserName(), taskName);
                      } else {
                          // 导出异常
                          querySqlService.executeSql("update export_data set status =?  url = '' where userName = ? AND startTime = ? AND taskName = ?", "导出数据失败", params.getUserName(), startTime, taskName);
                          FileUtil.deleteFile(params.getPath());
                      }
                  } finally {
                      // 不管导出成功失败，释放锁
                      String sql = "UPDATE `task_data` SET `lockFlag` = 0 WHERE `id` = 9999 ;";
                      querySqlService.executeSql(sql);
                  }
              }
          });
      }
  
      /**
       * 实际写文件过程
       *
       * @param stateObject
       * @param totalRows
       * @return 是否成功写出文件
       */
      private boolean doExportCsv(StateObject stateObject, int totalRows, String dataSourceType) {
          boolean exportFlag = false;
          File file = new File(stateObject.getPath() + stateObject.getFileName());
          LinkedList<Head> heads = getHeadList(stateObject.getQuerySql());
  
          QueryResultEntity result = null;
          FileOutputStream fw = null;
          OutputStreamWriter out = null;
          BufferedWriter bw = null;
          try {
              fw = new FileOutputStream(file, true);
              out = new OutputStreamWriter(fw, "GBK");
              bw = new BufferedWriter(out);
              bw.write(stateObject.getQuerySqlEntity().getHead());
              bw.newLine();
              boolean isFirst = true;
              for (Head head : heads) {
                  if (isFirst) {
                      bw.write(head.getName());
                      isFirst = false;
                  } else {
                      bw.write("," + head.getName());
                  }
              }
              bw.newLine();
              int row = totalRows / 10000 + (totalRows % 10000 != 0 ? 1 : 0);
              for (int i = 1; i <= row; i++) {
                  // 切换数据源
                  setDataSource(dataSourceType);
                  this.queryService.installSql(stateObject.getParameterMap(), stateObject.getQuerySqlEntity());
                  // 切换数据源
                  setDataSource(stateObject.getQuerySqlEntity().getAuthor());
                  result = queryService.excuteQuery(i, 10000);
                  for (Map<String, Object> line : result.getRows()) {
                      isFirst = true;
                      for (Head head : heads) {
                          if (isFirst) {
                              bw.write(filter(activity.util.StringUtil.objToStr(line.get(head.getKey()))) + "\t");
                              isFirst = false;
                          } else {
                              bw.write("," + filter(activity.util.StringUtil.objToStr(line.get(head.getKey()))) + "\t");
                          }
                      }
                      bw.newLine();
                  }
                  bw.flush();
              }
              exportFlag = true;
          } catch (IOException e) {
              logger.error("queryController:doExportCsv():IO异常", e);
          } catch (Exception e) {
              logger.error("queryController:doExportCsv():未知异常", e);
          } finally {
              if (bw != null) {
                  try {
                      bw.close();
                  } catch (IOException e) {
                      logger.error("queryController:doExportCsv():bw close异常", e);
                  }
              }
              if (out != null) {
                  try {
                      out.close();
                  } catch (IOException e) {
                      logger.error("queryController:doExportCsv():out close异常", e);
                  }
              }
              if (fw != null) {
                  try {
                      fw.close();
                  } catch (IOException e) {
                      logger.error("queryController:doExportCsv():fw close异常", e);
                  }
              }
          }
          return exportFlag;
      }
  
      /**
       * @param str
       * @return
       */
      private static String filter(String str) {
          return "\"" + str + "\"";
      }
  
      /**
       * 获取结果字段表头集合
       *
       * @param querySql 条件
       * @return
       */
      private LinkedList<Head> getHeadList(QuerySqlEntity querySql) {
          // 获取结果字段集合
          List<QueryFieldEntity> fieldList = this.queryService.findByProperty(QueryFieldEntity.class, "sqlId",
                  querySql.getSqlId());
          LinkedList<Head> header = new LinkedList<Head>();
          for (QueryFieldEntity field : fieldList) {
              Head head = new Head();
              head.setKey(field.getFieldName());
              head.setName(field.getDescription());
              header.add(head);
          }
          return header;
      }
  	/**
       * 设置数据源
       *
       * @param gameType 数据源类型
       */
      private void setDataSource(String gameType) {
          if (StringUtils.isEmpty(gameType)) {
              DataSourceContextHolder.setDataSourceType(MyDataSourceType.dataSource_common);
          } else {
              DataSourceContextHolder.setDataSourceType(gameType);
              if (gameType.equals("dataSource_sub_mysql")) {
                  DataBaseContextHolder.setDataBaseType(DataBaseContextHolder.SESSION_FACTORY_MYSQL);
              } else if (gameType.equals("dataSource_comm_actv")) {
                  DataBaseContextHolder.setDataBaseType(DataBaseContextHolder.SESSION_FACTORY_ORACLE);
              }
          }
      }
  
  }
  ```

* 改进后

* 改进后做的优化

  * 去除synchronized锁，使用mysql实现无锁话机制，避免了上述前两个问题。
  * 去除synchronized锁的同时，也去掉了Double Check，减少了一次查库操作，提升了性能。

  ```java
  @Controller
  @RequestMapping("/queryController")
  public class QueryController extends BaseController {
      /**
       * Logger for this class
       */
      private static final Logger logger = Logger.getLogger(QueryController.class);
  
      @Autowired
      private QuerySqlServiceI querySqlService;
      @Autowired
      private QueryServiceI queryService;
  
      /**
       * 根据查询条件导出数据，每次只能有一人进行导出操作
       *
       * @param querySql
       * @param request
       * @param response
       * @throws Exception
       */
      @RequestMapping(params = "exportData")
      public void exportData(QuerySqlEntity querySql, HttpServletRequest request, HttpServletResponse response) {
          if (querySql.getSqlId() == null) {
              feedBackMsg(response, "json", ExportMsgEnum.SQLID_NOT_EXITS.getExportMessage());
              return;
          }
          // 设置数据源
          setDataSource("");
          TaskData taskData = querySqlService.getEntity(TaskData.class, 9999);
          // 获取锁标志位；0：没有线程占据锁；1：有线程占据锁
          if (taskData.getLockFlag() != 0) {
              feedBackMsg(response, "json", ExportMsgEnum.OTHER_USER_EXPORTING.getExportMessage());
              return;
          }
          try {
              // 获取锁，修改锁标志位
              String sql = "UPDATE `task_data` SET `lockFlag` = 1 WHERE `id` = 9999 and `lockFlag` = 0;";
              Integer updateCnt = querySqlService.executeSql(sql);
              // 获取锁失败，直接返回；否则，进行导出操作
              if (updateCnt <= 0) {
                  feedBackMsg(response, "json", ExportMsgEnum.OTHER_USER_EXPORTING.getExportMessage());
                  return;
              }
              QuerySqlEntity querySqlEntity = this.querySqlService.findUniqueByProperty(QuerySqlEntity.class, "sqlId", querySql.getSqlId());
              StateObject stateObject = getExportParamObj(querySql, querySqlEntity, request);
              this.queryService.installSql(stateObject.getParameterMap(), querySqlEntity);
              // 切换数据源
              setDataSource(querySqlEntity.getAuthor());
              // 获取导出的总记录数
              int totalRows = queryService.getRowCount();
              if (!isAllowExport(totalRows)) {
                  feedBackMsg(response, "json", ExportMsgEnum.OVER_MAX_MOUNT.getExportMessage());
                  return;
              }
              // 异步导出
              excelExportAsyn(stateObject, totalRows);
          } catch (Exception e) {
              feedBackMsg(response, "json", ExportMsgEnum.EXPORT_ERROR.getExportMessage());
          }
      }
  
      /**
       * 封装导出需要的参数成对象
       *
       * @return
       */
      private StateObject getExportParamObj(QuerySqlEntity querySql, QuerySqlEntity querySqlEntity, HttpServletRequest request) {
          // 当前用户
          String userName = LoginUtil.getLoginUserInfo().getUserName();
          // 文件存放路径
          SimpleDateFormat sdf = new SimpleDateFormat("yyyyMMdd_HHmmss");
          String date = sdf.format(new Date());
          String path = ResourceUtil.getConfigByName("download.active.path") + date.substring(0, 8);
          String fileName = querySqlEntity.getHead() + "_" + date + ".csv";
          File filePath = new File(path);
          if (!filePath.exists()) {
              filePath.mkdirs();
          }
  
          String host = request.getHeader("Host");
          Map parameterMap = request.getParameterMap();
          StateObject stateObject = new StateObject(path, querySql, fileName, userName, parameterMap, host);
          stateObject.setQuerySqlEntity(querySqlEntity);
          return stateObject;
      }
  
      /**
       * 导出数据量校验
       * 导出最大量 50W
       *
       * @return true：数据量<50W,允许导出；false：数据量>50W，不允许导出
       */
      private boolean isAllowExport(int totalRows) {
          int topLimt = 500000;
          String topLimitNumber = ResourceUtil.getConfigByName("toplimitnumber");
          if (StringUtils.isNotBlank(topLimitNumber)) {
              topLimt = Integer.valueOf(topLimitNumber);
          }
          return totalRows < topLimt;
      }
  
      /**
       * 导出操作回馈信息
       *
       * @param response
       * @param type     返回类型
       * @param msg      返回消息内容
       */
      private void feedBackMsg(HttpServletResponse response, String type, String msg) {
          if (type.equals("json")) {
              response.setContentType("application/json;charset=UTF-8");
          } else if (type.equals("html")) {
              response.setContentType("text/html;charset=UTF-8");
          }
          try {
              response.getWriter().write(msg);
              response.getWriter().flush();
          } catch (IOException e) {
              logger.error("queryController：feedBackMsg()：IO异常", e);
          }
      }
  
      /**
       * 异步导出csv文件
       *
       * @param params    参数对象
       * @param totalRows 导出数据总行数
       */
      private void excelExportAsyn(final StateObject params, final int totalRows) {
          ExecutorService pool = Executors.newSingleThreadExecutor();
          pool.execute(new Runnable() {
              @Override
              public void run() {
                  try {
                      String dataSourceType = DataSourceContextHolder.getDataSourceType();
                      String startTime = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(System.currentTimeMillis());
                      String fileParentDir = params.getPath().substring(params.getPath().lastIndexOf("/"));
                      String game = params.getQuerySqlEntity().getGame();// 游戏
                      String taskName = params.getQuerySqlEntity().getHead();// 菜单名称
                      String activityVersions = params.getQuerySqlEntity().getActivity();// 活动版本
                      String url = "<a href='http://" + params.getHost() + "/banckend/down" + fileParentDir + "/" + params.getFileName() + "' target='_blank'>点击下载</a>";// 下载地址
                      setDataSource("");// 切换数据源
  
                      // 导出中
                      String saveSql = "insert into export_data (taskName,game,activity,url,startTime,userName,exportVolume,status) value(?,?,?,?,?,?,?,?)";
                      querySqlService.executeSql(saveSql, taskName, game, activityVersions, url, startTime, params.getUserName(), totalRows + "条", "数据导出中...");
                      // 导出
                      boolean exportFlag = doExportCsv(params, totalRows, dataSourceType);
                      // 切换数据源
                      setDataSource("");
                      if (exportFlag) {
                          // 导出正常
                          String endTime = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(System.currentTimeMillis());
                          String sql = "update export_data set status = ?,endTime=? where startTime = ? and userName = ? and taskName = ? ;";
                          querySqlService.executeSql(sql, "导出完成，待下载...", endTime, startTime, params.getUserName(), taskName);
                      } else {
                          // 导出异常
                          querySqlService.executeSql("update export_data set status =?  url = '' where userName = ? AND startTime = ? AND taskName = ?", "导出数据失败", params.getUserName(), startTime, taskName);
                          FileUtil.deleteFile(params.getPath());
                      }
                  } finally {
                      // 不管导出成功失败，释放锁
                      String sql = "UPDATE `task_data` SET `lockFlag` = 0 WHERE `id` = 9999 ;";
                      querySqlService.executeSql(sql);
                  }
              }
          });
      }
  
      /**
       * 实际写文件过程
       *
       * @param stateObject
       * @param totalRows
       * @return 是否成功写出文件
       */
      private boolean doExportCsv(StateObject stateObject, int totalRows, String dataSourceType) {
          boolean exportFlag = false;
          File file = new File(stateObject.getPath() + stateObject.getFileName());
          LinkedList<Head> heads = getHeadList(stateObject.getQuerySql());
  
          QueryResultEntity result = null;
          FileOutputStream fw = null;
          OutputStreamWriter out = null;
          BufferedWriter bw = null;
          try {
              fw = new FileOutputStream(file, true);
              out = new OutputStreamWriter(fw, "GBK");
              bw = new BufferedWriter(out);
              bw.write(stateObject.getQuerySqlEntity().getHead());
              bw.newLine();
              boolean isFirst = true;
              for (Head head : heads) {
                  if (isFirst) {
                      bw.write(head.getName());
                      isFirst = false;
                  } else {
                      bw.write("," + head.getName());
                  }
              }
              bw.newLine();
              int row = totalRows / 10000 + (totalRows % 10000 != 0 ? 1 : 0);
              for (int i = 1; i <= row; i++) {
                  // 切换数据源
                  setDataSource(dataSourceType);
                  this.queryService.installSql(stateObject.getParameterMap(), stateObject.getQuerySqlEntity());
                  // 切换数据源
                  setDataSource(stateObject.getQuerySqlEntity().getAuthor());
                  result = queryService.excuteQuery(i, 10000);
                  for (Map<String, Object> line : result.getRows()) {
                      isFirst = true;
                      for (Head head : heads) {
                          if (isFirst) {
                              bw.write(filter(activity.util.StringUtil.objToStr(line.get(head.getKey()))) + "\t");
                              isFirst = false;
                          } else {
                              bw.write("," + filter(activity.util.StringUtil.objToStr(line.get(head.getKey()))) + "\t");
                          }
                      }
                      bw.newLine();
                  }
                  bw.flush();
              }
              exportFlag = true;
          } catch (IOException e) {
              logger.error("queryController:doExportCsv():IO异常", e);
          } catch (Exception e) {
              logger.error("queryController:doExportCsv():未知异常", e);
          } finally {
              if (bw != null) {
                  try {
                      bw.close();
                  } catch (IOException e) {
                      logger.error("queryController:doExportCsv():bw close异常", e);
                  }
              }
              if (out != null) {
                  try {
                      out.close();
                  } catch (IOException e) {
                      logger.error("queryController:doExportCsv():out close异常", e);
                  }
              }
              if (fw != null) {
                  try {
                      fw.close();
                  } catch (IOException e) {
                      logger.error("queryController:doExportCsv():fw close异常", e);
                  }
              }
          }
          return exportFlag;
      }
  
      /**
       * @param str
       * @return
       */
      private static String filter(String str) {
          return "\"" + str + "\"";
      }
  
      /**
       * 获取结果字段表头集合
       *
       * @param querySql 条件
       * @return
       */
      private LinkedList<Head> getHeadList(QuerySqlEntity querySql) {
          // 获取结果字段集合
          List<QueryFieldEntity> fieldList = this.queryService.findByProperty(QueryFieldEntity.class, "sqlId",
                  querySql.getSqlId());
          LinkedList<Head> header = new LinkedList<Head>();
          for (QueryFieldEntity field : fieldList) {
              Head head = new Head();
              head.setKey(field.getFieldName());
              head.setName(field.getDescription());
              header.add(head);
          }
          return header;
      }
  
      /**
       * 设置数据源
       *
       * @param gameType 数据源类型
       */
      private void setDataSource(String gameType) {
          if (StringUtils.isEmpty(gameType)) {
              DataSourceContextHolder.setDataSourceType(MyDataSourceType.dataSource_common);
          } else {
              DataSourceContextHolder.setDataSourceType(gameType);
              if (gameType.equals("dataSource_sub_mysql")) {
                  DataBaseContextHolder.setDataBaseType(DataBaseContextHolder.SESSION_FACTORY_MYSQL);
              } else if (gameType.equals("dataSource_comm_actv")) {
                  DataBaseContextHolder.setDataBaseType(DataBaseContextHolder.SESSION_FACTORY_ORACLE);
              }
          }
      }
  
  }
  
  ```

  