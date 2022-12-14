通过遍历阳光产生的时间,寻找阳光产生的本地Call,使用代码注入器注入,自定义生成阳光

阳光CALL遍历技巧：

 - 进入植物大战僵尸-> 当出现阳光后->马上搜索未知初始数值
 - 返回游戏-> 马上切回CE-> 搜索减少的数值-> 掉一点搜一点
 - 最后排查出它的掉落地址-> 锁定1即可实现无限掉落

1.首先CE打开游戏，然后进入植物大战僵尸，当出现阳光后我们暂停游戏，并马上搜索未知初始数值。

![](/image/1379525-20190718121314392-405103319.png)

2.返回游戏，让阳光下落一点点，马上切回CE，搜索减少的数值，掉一点搜一点。

![](/image/1379525-20190718121434947-1600581787.png)

3.经过我的不断排查，最后排查出以下结果，我们将其加入到地址列表中，依次将我们找到的这几个地址锁定为1，看是否有反映。

![](/image/1379525-20190718121621472-1822797375.png)

4.此处我已经找到了。

![](/image/1379525-20190718121737183-1410348517.png)

5，我们直接在找到的地址上右键，选择找出是谁访问了这个地址，或选中这一行按下`F5`也可以，如下我们需要记下`00413BCB`这个内存地址，然后直接关闭CE，接下来我们要使用OD调试了。

![](/image/1379525-20190718122146635-1635030609.png)

6.打开OD直接附加植物大战僵尸进程，`F9`让程序跑起来，按下`Ctrl + G`输入`00413BCB`，定位代码，然后在该位置按下`F2`下一个断点。

![](/image/1379525-20190718122617016-1121171956.png)

此时往上找,有一个JNZ的跳转指令,我们双击在此处下一个`F2`断点，然后运行游戏，此时会断下，此处的JNZ如果成功跳转则不会生成阳光，那么我们把此处NOP掉看看会出现啥结果。

![](/image/1379525-20190718122814855-1144725334.png)

7.如上图，如果我们将其NOP掉的话，回到游戏会发现无限的出现阳光了，也就是说阳光的CALL就在他跳过的这些指令当中。

![](/image/1379525-20190718123017239-1207231712.png)

8.观察它跳过的代码片段，我们找一下有没有阳光生成的CALL，如下我发现了一些带有参数传递的CALL，这可能就是阳光的生成CALL了。

![](/image/1379525-20190718123511674-1094546304.png)

9.在PUSH的位置上下`F2`断点，然后运行程序并等待阳光生成，生成后会断下`F8`单步跟随，将其中的寄存器参数记录下来。

![](/image/1379525-20190719152046325-964018427.png)

10.使用代码注入器测试效果，默认的PUSH参数是4出现的是正常的阳光，如果我们将其改成6则会出现一个大的阳光。

![](/image/1379525-20190719152137730-584105667.png)

11.如果将注入的参数改为 `push 1` 和 `push 3` 那么屏幕上将出现钻石。

![](/image/1379525-20190719152236113-1778105794.png)

也可以下钻石雨。

![](/image/1379525-20190719152945309-1971739032.png)
