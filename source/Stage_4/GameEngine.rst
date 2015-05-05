:author: GangweiLi
:comment: No
:CreateDate: 14-07-14
:status: C
:name: GameEngine
:LastModified: |today|

==========
GameEngine
==========

Introduction
============

*GameEngine* 最重要的资源的分配与各个子系统的交互。一个最简单的模型，就是一个全局变量加上一个死循环。当然这种循环不一定非是由应用程序来实现。关键是一个闭环的状态机。对于一个专用机或虚拟机的完全可以使用中断来实现这个闭环的状态机。OS内核就是利用这样的机制来实现。  例如FPS游戏,人与各种武器之间如何交互，虚拟世界中各种物体是如何交互的，并且要尽可能符合现实，当然不必完全一样，但是一定要合理。

*GEA_Architecture.pdf* 笔记就是现在这篇。

常用的工具
==========
主要是 `ConfigurationManagement.rst` 版本控制工具与build以及 `ContinuousIntegration.rst` CI的工具。这些可以参考自己的。
对于代码好办，直接存储就行了，但是对于那些asset的二进制。其中一个办法那就是转化文本格式，unreal并且已经做了这样的转换。

对于不同平台，以及各种不同asset的格式之间要进行各种各样的转换。同时不同平台也支持不同的文件格式。在build的时候就生成对应的格式。

碰撞检测
========

在Blender中，一些简单的判定那就是直接根据坐标属性来判断了。现在想起来了，当然在学校的时候做那个飞机的混伤仿真的时候用过个。可以采用有限元法分析，当然也可以简单的使用bind box来采用检测。


Game loop
=========

需要重复的事情放在loop里，而native_globe中把一大堆不需要重复东东放在loop里，并且再用一个变量来记录状态，这种做法有点脱裤子放屁的味道，直接放在loop之前进行初始化不就行了。还省得检测了。几件事在要做，这看如何设计了，一个loop就要完成一帧，还是不一定，一般情况是一个loop,例如输入，碰撞检测，声音，图形，HID检测，以及ruler的检测，等等。最简单的那就是都在一个loop来完成，这个逻辑容易，但是由于单核自算性能达到速度要求。采用多线程，这时候就需要各个事情之间的同步，例如 update,render要分开以及声音的输出要及时，由于各个线程之间的执行速度是不同的，但是各个事件之间又有逻辑关系，例如图形与声音要同时发生，但是render太慢就会产生超音速的问题。这也是游戏大量的工作所在，各个部分能够正常的sync工作是很难的，因为每一部分执行时间又是事件知道的。这样就需要采用各种各样的机制，例如proxy模式，对同一个函数调用都给一个线程。例如pentaK中对log的读写一样，都交给了Nlog来完成，这样中间proxy层解决两个进程同时打开一个文件时的冲突，每一个进程都往NLOG来送log，而NLOG自己来决定写在哪里。当然之间自然需要一个队列了。

什么时候把GPU与模式与GPU的模式通用化，就能够真正模仿神经元了。

scecne space management
=======================

现在对于空间管理有了更深的认识，这与ogl的culling的区别在哪里，例如使用八叉树，它首先解决了要画一些东东，根据当前agent actor 或者camera来决定，但是具本物体的遮挡是由ogl可以由ogl来计算，也可以在空间管理的时候就算。



Low-Level Engine
================

游戏需要的资源，meshes,textures,animations,audio, game object.
内存分配的问题，动态分配就会Fragmentation(虚拟内存可以重定向解决这个问题）,固定分配又会造成浪费。这是一个纠结的事情。其中一个半办法那就是周期性的碎片整理。

对于引擎的配置管理，以及In-Game Menu的动态实现这些可以在引擎嵌入一个脚本引擎就可以了。就可以很容易的实现这些。

以及各种资源的导入以及预处理，它们的格式可能并不是gameEngine能够直接用的格式这就需要转换了。

.. csv-table::

   stack 
   pool 
   double queue 

另外一种机制那就是尽可能利用缓存机制，这个要求框架设计的时候尽可能考虑到这些，在gameloop的时时候，要尽可能分类批处理，而不是随机的处理。

对于资源的管理，来保证没有重复，以及跨平台等等，以及版本控制等等。有可能需要wrap现有的filesystem api,也有可能需要的是定制的filesystem.

如何在文件系统这一层实现就像数据库中view的功能呢，那就是link的用法，就可以实现两者分离，让存储与逻辑分别高效的存储，然后通过link来进行关联。
当然也可以采用GFS或者数据库来存储。

HID  这一块也很好的处理，例如游戏的手柄,摄像头，例如现在Kinect等等，如何交互利用中断，以及使用driver 采用寄存器等等。经常要用的就是那些位操作。


对于各种调试工具，以及游戏的runtime调试工具等，这些现在不正是我们所在玩的devtool.
调试的手段不外乎logging and Tracing 或者采用独立的channels. 就像m4那样。 cruash report,这些好的机制，对于解决问题很关键。

当然对于graphic的调试工具还有Debug Drawing Facilites, 这些引擎都要提供，并且各种辅助线来加快开发。这些NSight等等开发工具应该是提供这些，还有battle. perfhudes 等等都要慢慢熟悉这些工具。当然对于复杂的场景可以部分显示，例如隐去背景等等。
还有的功能那就是抓屏以及movie record,这些功能不也正是我现在所需要吗。对了In-Game Profiling这些功能，就类似于process 的功能，这些可以每天当问题问他们。


并且引擎的runtime 框架与tooledit时的框架与结构也不一样，当然如果保持一样会有很多的优势，同样维护的难度也特别的大。

Graphics and Motion
===================

对于游戏的动画，采用最原始的cel animation.各种分层组合动化，以及刚体运动。什么时候把数据传给gpu,这个是先在CPU端用八叉树做完剔除之后，把需要显示东东交给OGL,然后OGL再进行显示的处理，例如每个word的管理，在这一时刻玩家只在这个小房间里，只需要把这个小房间的所有mesh都发给ogl来进行显示，然后ogl再进一步研究物体的之间的遮挡问题，进一步优化，到最终的显示。所有动化都体现在进入ogl之前各个参数的变化，例如太阳的位置，必须在进入ogl之前给传递进来，例如一个mesh的形变计算，都是在这个时候传进来。最后让ogl由点到面最后到体这样的画出来。

当然的场景可能还要分成多个pass来做。





游戏团队的构成
==============

   * Engineer
   * Artists  (content is king)
      1. concept artists 策划
      1. 3D modelers 
      1. Texture artists
      1. Lighting artists
      1. Animators
      1. Motion capture actors
      1. Sound designers
      1. Voice actors
      1. Composers
   * Game Designers
   * Producers

游戏开发流程
============

首先是有一个想法，构建一个故事背景，然后构建每一个人物角色，再然后就要从各个人物的脚度来观察这个整个故事世界。其实就像拍电影一样。与电影的不同的是，故事的主解可能不一定是一个。特别是这种网游与角色游戏。一个个的小故事，一个个小的任务最后组成整个故事的成长。然后再整个的故事背景中。同时在这个过程中就需要找各种各样的人来一起工作了。这个是自己所缺的。自己一直是单打独斗。现在需要加一些小工作室来试试。

今天与一个工作室聊了一下，它们一共5个人。

+-------+--------+
| job   | number |
+-------+--------+
|  动画 |  2     |
+-------+--------+
|  美工 |  1     |
+-------+--------+
|  建模 |  1     |
+-------+--------+
|  开发 |  1     |
+-------+--------+



game genres
===========

不同的风格的游戏对于引擎会不同的要求，当然越通用效率当然也就会越低，那也就是为什么会有各种各样的引擎。一个是由于游戏风格的，另外是由于硬件的不同。例如户外游戏对于 LOD(level of detail) 的要求比较高，而室内去不同。

.. csv-table:: game genres
   :header: Category,Definition,Remark

   FPS   , first persion shooter ,
   MMOG , massive multiplayer online game ,
   RTS , real-time stragegy , 红警 ,
   Third-person character-based ,  http://en.wikipedia.org/wiki/Platformer , 超级致玛丽就是这种 ,
   Fighting Games ,   http://en.wikipedia.org/wiki/Fighting_game ,  这个是自己要找的  ,
   Racing Games , htt p://en.wikipedia.org/wiki/Racing_game ,
   sports , football,baseball ,
   role-playing games ,
   environmental/social simulation games   ,  SimCity ,
   conversion of non-electronic games ,  chess,card games ,


Game Engine Survey
==================

   * The Quake Family of Engines
   * The Unreal Family of Engines
   * The Half Life source Engine
   * MS XNA Game Studio
   * Proprietary in-House Engines
   * Open Source Engines 
       1. OGRE
       1. Panda3D
       1. Yake
       1. Crystal Space
       1. Torque and Irrlicht.

底层库的支持
============

对于路径的操作，windows 下有一个 `Shlwapi-Instruction <http://www.cnblogs.com/ubunoon/archive/2009/11/13/Shlwapi-Instruction.html>`_ 来方便的取得相对与绝对路径等。


Gameplay 
========

随时能够停下来，随时保存还是分段保存，在网络游戏的时候，哪些是需要远程重现的。这些都是要规划的。
#. `随机数和概率在游戏中的使用 <http://blog.csdn.net/ybhou/article/details/10034153>`_ 装备强化的成功率，副本里装备的掉落率，通关奖励翻牌的掉落率，攻击暴击的概率，攻击MISS的几率，梦幻西游里碰到变异宝宝的概率等等都可以这样实现。 各种系统行为的初始值都可以用随机数来产生。   


Thinking
========

游戏开发，基本都是c++/c，这主要是由于性能的原因。

-- Main.GangweiLi - 30 Dec 2013


*agent-based* 现在的游戏主要是这种agent-based computer simulations. 如何直接做到交互呢。

-- Main.GangweiLi - 30 Dec 2013


动画至少24fps,一般都要达到30-60fps,一些要求高120fps,这就对游戏性能有了一个最简单的要求，那就是在1frame之内把游戏的所有状态都要更新一片。这个运算量与速度的要求是很高的。
每一更新周期内loop,各个子系统都要有机会参与计算与更新状态，例如AI，game logic,物理引擎，这些东东的速度多块才能算是实时，并且如何分时计算。 例如[[Work.SceneGraph][场景的管理]]。你可以只用一个简单的数组加一个switch.当然也可以使用复杂的算法例如BSP等。
AI就是一个行为树，如何自主的遍历一个数，这个行为数决定了什么产生什么行为。
-- Main.GangweiLi - 30 Dec 2013


It's safe to say that the more general purpose a game engine or middleware component is, the less optimal it is for running a particular on a particular platform.

-- Main.GangweiLi - 30 Dec 2013


*Runtime Engine Architecture* 也是采用分层模块化的设计。

-- Main.GangweiLi - 30 Dec 2013


*现在看来* 都平淡多了，例如那些库，一些算法，例如字符串的使用，你直接写效率，要比调用库的效率高，当然算法本身除外，因为算法就那些基本算法，没有什么大的变化，通用意味着封装更多，overhead更高。

-- Main.GangweiLi - 31 Dec 2013


之前对于文件的管理，内存的管理等是很泛的概念，以及对于操作系统的理解，现在都明白这些都是基于被处理对象的，不同的算法适用于不同的处理对象。这对效率的影响很大，对于内存，我们可以直接malloc申请一块内存之后，直接自己管理，而非交给操作系统去管理，当然扔给操作系统管理也是一种方式，哪些常驻内存等等是要分析处理对象的。内存管理最简单的那就是自己申请自己释放了，当然也可以利用Ｃ＋＋自身gc方式，当然这不是最好的方式，最好的方式那就要定制了。目前看来主要就是如何实现referencecount的管理，并且在有信赖的情况下，另外就是这个pattern来进行gc的处理，例如多长时间进行一次更新活动。

-- Main.GangweiLi - 31 Dec 2013


*engine arctecture* 现在知道了，对于engine的理解了有了更深的认识，也就是各个部分之间的通信关系，可以对于NEAT来进行，但是还是有很多的一样。一个最简单的engine, 就是几个全局变量再加上一个for循环就搞定了，这个与自己的那个beer.tcl是一样的。例如渲染由一个全局数据结构表示，physX由一个全局的数据结构表示，等。每一个数据结构只保存GO的部分属性，它们之间用GUID来进行联系，现在看来其实他更像是ouch的那个多队列的多流水线的交易系统。

当然engine还有一个作用那就是做结构的转换，对于上一层应用来说，GO可能是包含所的信息的，例如就是c++的对象，每一个属性块，又是一个类，而对于运行时系统就是一个个table与之对应。

.. code-block:: cpp

    class actor {
       string GUIID 
       string Name 
       -------------------------------------------
       class  body {
            weight,
            hight,
            width,
       }
       
       class MeshData {
           type,
           data
       }
    
       class Capacity {
           weapon,
           skills,
       }
    }

in the engine of runtime it may be like this 

+-------+---------+
| table |  actor  |
+=======+=========+
|GUID   |    Name |
+-------+---------+


+-----+--------+------+-------+------------------------+
|table| body   |                                       |
+=====+========+======+=======+========================+
|GUID | weight | hight| width | or foreginKey of actor |
+-----+--------+------+-------+------------------------+

+-----+------+------------------------------+
|table MeshData                             |
+=====+======+==============================+
|type | data | GUID or foreign Key of actor |
+-----+------+------------------------------+

+------------------------------------------------+
|table Capacity                                  | 
+========+=======================================+
| weapon | skills | GUID or foreign key of actor |
+--------+--------+------------------------------+
    

由于GameEngine来实现各个阶段数据格式的转换。

.. code-block:: cpp

    Game engine {
       init System,
       ImportData;
       for (;;) {
             update body;        
             update MeshData,
             update Capacity;
             draw the actor from intermation from body,meshData,Capacity instore in frame queue;
             display pop up frame queue;
       }
    }


如何设计类 `CAndCPluss.rst`,由于GameEngine来实现各个阶段数据格式的转换。 例如 `HDRSample.rst` 那样，每一个物理物体都封装成一个类，它的接口有init/udpate/render,这个拼口，然后由引擎调用各个接口。每一个都自己保存的数据与实例。 

.. code-block:: cpp 

   map objects  {
      init;update; 
       render to  texture_n;
   }
   把所有texture_n 放在一起，然后再最合成最终的效果图，各个texture的深度信息如何合并。
   这个时候，texture unit 多少就决定了，你的场景复杂度了，一个大场景可以分割各个小场景，但是最多能够一次分割为多少个，就看texture unit的个数了。


-- Main.GangweiLi - 13 Jan 2014


*NVSample* 每一个sample中都会有一个engine,从每一个小engine,去理解整个结构。 每一个样例都是一个小的game,把GEA的知识用到每一个example中。

-- Main.GangweiLi - 13 Jan 2014


*Object vs compoent* 这样就有一个GO自身之间的通信问题，object自身有关联

-- Main.GangweiLi - 14 Jan 2014




-- Main.GangweiLi - 16 Jan 2014


*physX* 最常用的方法是逼近与简化。利用80/20原则来解决大部分问题。

-- Main.GangweiLi - 16 Jan 2014


游戏的math最重要那就是保持各个坐标系的一致性。特别是左手定则还是右手定则。 另外就法向的计算。

-- Main.GangweiLi - 18 Jan 2014


*GEA* 应该做为一本reference,每一次看游戏都拿他来对比，并拿他来做参考做分析。而不是现在一下子来弄完。

-- Main.GangweiLi - 19 Jan 2014



*静态与动态*
静态图像的处理就简单，GameEngine的核心是动态元素。并且不要受限于现在做法。更要倾向于并行的发展。

-- Main.GangweiLi - 19 Jan 2014


*Runtime Object Model*
#. object-centric.
#. Property-centric.

各有特点，并且在实现上相互仿像的，树形结构也会有一系列的扩展问题，也就是当分类分到不知道如何分的问题，因为一个object树建立起来后，就是一个分类规则，并且树形结构的缺点那不是只能一个分类规则，这样出现了分到不知道如何分的问题。一个解决方案那就是is-a 变成has-a的关系。 在实现上has-a就是property-centric的设计。每一个property一个table,每一个actor是记录外键，也就是哪一张表就行了。

-- Main.GangweiLi - 19 Jan 2014


整个社会本身是分层并行的，而我们的现实也是这样的，而我们用计算机的实现的方式却不是这样的，采用的大管家的模式，分时复用各种资源。这个大管家就是engine,来做评判，谁应该做什么，并且应该什么时候做。
什么时候一个东东知能达到一定程度，就像手机发展智能手机，远远超过的计算机，这一个整体能力，虽然单项能力不是不如计算机。当功能达到完备集的时候，就会发生质变了。现在终于明白量变与质变的关系，最简单的按照易经六十四卦来说，已经能够完备的表示万物了。同样的到理，计算机的位数到64位应该会停很长的时间。因为64也是一个完备集。再一次冲破会来带来另一次的质变。

例如tango就类似于这种，计算能力以及传感器目前来看是最全的。

对于游戏来说也一样，还是采用的精英文化，一个大管家或者几个小管家多核并行。然后决定万事务的显示。例如大管家引擎觉的让你显示红色，就应该显示红色。不是说自己来显示红色。
是可是元数个视角的问题，每一个人只管更新自己以及能力半径内的事情，就像现在网游一样。
但是现在还做不到。例如opengl要统一分时的复用，声音也是分时复用的。只有一个声音设备。一个显示设备。

如何做到，系统每一个元素都自主计算的颜色。显示只是看自己想看的。
现在不是的，现在排队统一管理的。来统一更新状态。来统一读取事件。来统一显示。每一个更新速度是不一样的，例如神经网络要1-2秒才更新一次。例如这一个字符传给CPU时，它已经做了很多事情。因为CPU更新速度特别快，给人感觉就像并行一样。例如一个角色自身的动画，每一个动作都肯定初始值的记录，然后是时间面，以及终值。一般情况下还会有当前值的记载。中间过程可以通过插值来实现。


在简单的sample里，我可以设备一个光源，一个物体，并且如何显示都是设定好。引擎更加进一层，例如查看系统里多光源，然后这一次渲染要加入哪些光源。都是由引擎来指令的。
这些都是由引擎来过虑系统里所有部件，物理以及碰撞检测，以及空间的八叉树管理，等等。如果自己以及事件处理，都是采用回调的方式。什么时候把回调变成中断的方式，那就更上一层楼了，这样变成自主了。即使是GPU现在 还要多个thread共享PC寄存器。

问题的关键是事务的之间联系，以及如何传动。而这个因果联系就是由引擎这个管家来管着的。

多线程的GameLoop的模式
一种是采用fork/join对每一个任务进行这样操作，也就是提高lantency的过程。这种计算模式比较适合多线程以及CUDA。
one thead per Subsystem这一种比较适合跨平台。
gameEngine经常的子系统有，rendering Engine,physics simulation,animation pipeline,audio engine. Game Main Egine.
另一种就是jobs模式
这个有点像event queue，因为在于内部包含数据与执行代码本身，只是执行在不同的地方。问题在于这些东东数据传输大嘛，包含数据与代码都要传。但是灵活性最好。
异步的机制，这发出一frame下一frame loop时查询来等。
而unreal采用后两者的之合。one thread per subsystem,并且在哪一个component的需要大量的计算的时候，就采用job的方式，这样既提高latency也提高了through.可以尽可能提高硬件的利用率。而在unreal中就是采用TaskGraphsystem来实现的。
具体可以参考，https://wiki.unrealengine.com/Multi-Threading:_How_to_Create_Threads_in_UE4一个用例。
