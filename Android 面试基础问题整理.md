## Android 基础问题整理

### 简述Android App 多渠道打包流程?
待补充...

### [Activity 启动模式介绍？](https://www.cnblogs.com/chenxibobo/p/6136626.html)
#### standard
默认模式，可以不用写配置。在这个模式下，都会默认创建一个新的实例。因此，在这种模式下，可以有多个相同的实例，也允许多个相同Activity叠加。
例如：
若我有一个Activity名为A1, 上面有一个按钮可跳转到A1。那么如果我点击按钮，便会新启一个Activity A1叠在刚才的A1之上，再点击，又会再新启一个在它之上……
点back键会依照栈顺序依次退出。
#### singleTop
可以有多个实例，但是不允许多个相同Activity叠加。即，如果Activity在栈顶的时候，启动相同的Activity，不会创建新的实例，而会调用其onNewIntent方法。
例如：
若我有两个Activity名为B1,B2,两个Activity内容功能完全相同，都有两个按钮可以跳到B1或者B2，唯一不同的是B1为standard，B2为singleTop。
若我意图打开的顺序为B1->B2->B2，则实际打开的顺序为B1->B2（后一次意图打开B2，实际只调用了前一个的onNewIntent方法）
若我意图打开的顺序为B1->B2->B1->B2，则实际打开的顺序与意图的一致，为B1->B2->B1->B2。
#### singleTask
只有一个实例。在同一个应用程序中启动他的时候，若Activity不存在，则会在当前task创建一个新的实例，若存在，则会把task中在其之上的其它Activity destory掉并调用它的onNewIntent方法。
如果是在别的应用程序中启动它，则会新建一个task，并在该task中启动这个Activity，singleTask允许别的Activity与其在一个task中共存，也就是说，如果我在这个singleTask的实例中再打开新的Activity，这个新的Activity还是会在singleTask的实例的task中。
例如：
若我的应用程序中有三个Activity,C1,C2,C3，三个Activity可互相启动，其中C2为singleTask模式，那么，无论我在这个程序中如何点击启动，如：C1->C2->C3->C2->C3->C1-C2，C1,C3可能存在多个实例，但是C2只会存在一个，并且这三个Activity都在同一个task里面。
但是C1->C2->C3->C2->C3->C1-C2，这样的操作过程实际应该是如下这样的，因为singleTask会把task中在其之上的其它Activity destory掉。
操作：C1->C2          C1->C2->C3          C1->C2->C3->C2            C1->C2->C3->C2->C3->C1             C1->C2->C3->C2->C3->C1-C2
实际：C1->C2          C1->C2->C3          C1->C2                              C1->C2->C3->C1                               C1->C2
若是别的应用程序打开C2，则会新启一个task。
如别的应用Other中有一个activity，taskId为200，从它打开C2，则C2的taskIdI不会为200，例如C2的taskId为201，那么再从C2打开C1、C3，则C2、C3的taskId仍为201。
注意：如果此时你点击home，然后再打开Other，发现这时显示的肯定会是Other应用中的内容，而不会是我们应用中的C1 C2 C3中的其中一个。
#### singleInstance
只有一个实例，并且这个实例独立运行在一个task中，这个task只有这个实例，不允许有别的Activity存在。
例如：
程序有三个ActivityD1,D2,D3，三个Activity可互相启动，其中D2为singleInstance模式。那么程序从D1开始运行，假设D1的taskId为200，那么从D1启动D2时，D2会新启动一个task，即D2与D1不在一个task中运行。假设D2的taskId为201，再从D2启动D3时，D3的taskId为200，也就是说它被压到了D1启动的任务栈中。
若是在别的应用程序打开D2，假设Other的taskId为200，打开D2，D2会新建一个task运行，假设它的taskId为201，那么如果这时再从D2启动D1或者D3，则又会再创建一个task，因此，若操作步骤为other->D2->D1，这过程就涉及到了3个task了

