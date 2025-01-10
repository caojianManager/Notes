## 一.线程，进程和协程的区别

[[Unity中的进程，线程和协程]]

## 二.本地坐标和世界坐标

世界坐标：指物体在场景中的坐标，当物体没有父节点的时候，其Position就是世界坐标，rotation同理。
本地坐标：指物体相对父物体的坐标而言，这个相对坐标是以父物体本身为坐标轴进行计算的，与世界坐标没有必然联系。 对于没有父物体的物体，可以认为不存在本地坐标.

>可使用TransformPoint方法将本地坐标系转为世界坐标系

## 三.Unity生命周期

>Awake --> OnEnable --> Start --> FixedUpdate --> Update --> LateUpdate --> OnGUI --> OnDisable --> OnDestory

**主要函数介绍:**

+ **Reset:** 用在检视面板(Inspector)在编辑模式(Editor),给定一个默认值。
+ **Awake:** 常用于游戏开始和数据初始化。在整个生命周期中仅被调用一次，当脚本设置不可用(Enable == False)的使用，Awake仍然会被调用一次。扩：Awake在所有对象被初始化之后调用，所以你可以安全的与其他对象对话或用诸如GameObject.FindWithTag这样的函数搜索他们。每个游戏物体上的Awake以随机的顺序被调用。因此，你应该用Awake来设置脚本间的引用，并用Start来传递信息 ,Awake总是在Start之前被调用。它不能用来执行协同程序。
+ **OnEnable:** 当对象变为可用或激活状态时被调用事件监听
+ **Start:** 在behaviour的生命周期中只被调用一次。它和Awake的不同是Start只在脚本实例被启用时调用。你可以按需调整延迟初始化代码。Awake总是在Start之前执行。这允许你协调初始化顺序
+ **FixedUpdate:**
+ **Update:**
+ **LateUpdate:**
+ **OnGUI:**
+ **OnDisable:**
+ **OnDestory:**
+ **OnApplicationQuit:**

![[im_15.png]]
![[im_16.png]]