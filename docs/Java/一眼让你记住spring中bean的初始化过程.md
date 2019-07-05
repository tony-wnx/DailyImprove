>今天被一个小朋友问起了Spring中Bean的生命周期过程，结果场面一度十分尴尬，仅管我给他讲出了Bean的生命周期过程，但是讲的不是特别详细，也不够通俗易懂，看着他一脸懵逼的样子，于心不忍，于是决定一定要想出一个通俗易懂的例子让他一目了然，并且让他这辈子也忘不掉！

**流程图任你出，记得住算我输**
![在这里插入图片描述](https://github.com/tony-wnx/DailyImprove/tree/master/docs/Java/img/flowChart.png)
讲完了，就上边一张图，你记住了吗？不管你记没记住，反正我是记不住。那怎么办，来个生活中的实际案例吧。

**通俗形象理解**
>我们结合上面那张图和一台新款保时捷911跑车从生产到发布的过程来讲一讲Spring中的Bean的实例化过程。

![在这里插入图片描述](https://github.com/tony-wnx/DailyImprove/tree/master/docs/Java/img/Porsche.png)

* 首先，你说你想要一台最新款的保时捷911跑车（就问你这车骚气不），怎么办呢？买一辆！但是如果连造这辆车的工厂都没有，你从哪里去买？所以，首先要有一个保时捷汽车制造工厂来生产汽车，这个工厂负责处理保时捷名下各种型号各种款式的汽车的订单等来进行汽车的设计和制造。**对应图中的BeanFactoryPostProcessor（Bean工厂后处理器）**

* 汽车制造工厂有了，但是保时捷旗下有各种型号款式的汽车，你总不能所有的汽车都用一条流水生产线吧，所以，对于不同型号的汽车应该分开使用不同的流水线，每条流水线都生产不同型号的保时捷汽车。**对应图中的BeanPostProcessor（Bean后处理器）**

* 流水线有了，接下来就可以制造汽车了。但是这么多的流水线，哪一条才是生产我想要的保时捷911的呢？这时你就需要根据需求，找到与之对应的流水线去制造。**对应图中的InstanstiationAwareBeanPostProcessorAdapter（实例化感知的Bean处理器适配器）**

* 对应的流水线也确定了，那么接下来就要开始准备制造所需要的汽车零件和工具了，这些全都是你要开始装配保时捷911之前需要准备好的。**对应图中的调InstantiationAwareBeanPostProcessor的postProcessorBeforeInstantiation方法**

* 各种汽车零件和装配工具都准备好了，接下就该是按照保时捷911的设计图纸进行组装了。**对应图中的调用bean的构造函数**

* 在组装过程中，流水线平台还需要进行对车身喷涂不同的颜色等操作，也就是设置它们的属性。**对应图中的调InstanstiationAwareBeanPostProcessor的postProcessPropertyValue方法**

* 给汽车设置完一系列属性之后，设计师们根据某些属性值的不同给汽车起了不同的名字，比如：保时捷911  Speedster、保时捷911 Carrera S、保时捷911 Carrera S Cabriolet等。**对应图中的调用BeanNameAware的setBeanName方法**