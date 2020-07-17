## Redis @Cacheable并发场景下返回null

```java
 
public RedisCacheElement get(final RedisCacheKey cacheKey) {
    Assert.notNull(cacheKey, "CacheKey must not be null!");
    // 判断Key是否存在
    Boolean exists = (Boolean) redisOperations.execute(new RedisCallback<Boolean>() {
        @Override
        public Boolean doInRedis(RedisConnection connection) throws DataAccessException {
            return connection.exists(cacheKey.getKeyBytes());
        }
    });
    if (!exists.booleanValue()) {
        return null;
    }
    // 获取key对应的值
    return new RedisCacheElement(cacheKey, fromStoreValue(lookup(cacheKey)));
}
 
// 获取值
protected Object lookup(Object key) {
    RedisCacheKey cacheKey = key instanceof RedisCacheKey ? (RedisCacheKey) key : getRedisCacheKey(key);
    byte[] bytes = (byte[]) redisOperations.execute(new AbstractRedisCacheCallback<byte[]>(
            new BinaryRedisCacheElement(new RedisCacheElement(cacheKey, null), cacheValueAccessor), cacheMetadata) {
        @Override
        public byte[] doInRedis(BinaryRedisCacheElement element, RedisConnection connection) throws DataAccessException {
            return connection.get(element.getKeyBytes());
        }
    });
    return bytes == null ? null : cacheValueAccessor.deserializeIfNecessary(bytes);
}
```

### 原因：

​	先判断key是否存在，如果存在，调用lookup()方法获取值。并发场景下存在一个问题，当一个线程进入get()方法并且判断key存在，然后执行后续的lookup()方法获取值，但在执行该方法前，key过期了，这时就会返回null

### 解决：

​	spring-data-redis 1.8.11版本之后做了修复，官网说明地址：https://jira.spring.io/browse/DATAREDIS-777