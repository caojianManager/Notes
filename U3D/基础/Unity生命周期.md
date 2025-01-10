>Awake --> OnEnable --> Start --> FixedUpdate --> Update --> LateUpdate --> OnGUI --> OnDisable --> OnDestory

**主要函数介绍:**

+ **Reset:** 用在检视面板(Inspector)在编辑模式(Editor),给定一个默认值。
+ **Awake:** 常用于游戏开始和数据初始化。在整个生命周期中仅被调用一次，当脚本设置不可用(Enable == False)的使用，Awake仍然会被调用一次。扩：Awake在所有对象被初始化之后调用，所以你可以安全的与其他对象对话或用诸如GameObject.FindWithTag这样的函数搜索他们。每个游戏物体上的Awake以随机的顺序被调用。因此，你应该用Awake来设置脚本间的引用，并用Start来传递信息 ,Awake总是在Start之前被调用。它不能用来执行协同程序。
+ **OnEnable:** 当对象变为可用或激活状态时被调用事件监听
+ **Start:** 在behaviour的生命周期中只被调用一次。它和Awake的不同是Start只在脚本实例被启用时调用。你可以按需调整延迟初始化代码。Awake总是在Start之前执行。这允许你协调初始化顺序
+ **FixedUpdate:** 当MonoBehaviour启用时，其在每一帧被调用。处理Rigidbody时，需要用FixedUpdate代替Update。例如:给刚体加一个作用力时，你必须应用作用力在FixedUpdate里的固定帧，而不是Update中的帧。(两者帧长不同)。
+ **Update:** 是实现各种游戏行为最常用的函数。
+ **LateUpdate:** 每帧调用一次（在 在所有Update函数调用后被调用） 用于更新游戏场景和状态，和摄像机相关的更新。 官网上例子是摄像机的跟随，都是所有的Update操作完才进行摄像机的跟进，不然就有可能出现摄像机已经推进了，但是视角里还未有角色的空帧出现。
+ **OnGUI:** 渲染和处理GUI事件时调用。这意味着你的OnGUI程序将会在每一帧被调用。要得到更多的GUI事件的信息查阅Event手册。如果Monobehaviour的enabled属性设为false，OnGUI()将不会被调用。
+ **OnDisable:** 不能用于协同程序。当对象变为不可用或非激活状态时此函数被调用。
+ **OnDestory:** 当对象被销毁时调用。
+ **OnApplicationQuit:** 当用户停止运行模式时在编辑器中调用。当web被关闭时在网络播放器中被调用。

![[im_15.png]]
![[im_16.png]]