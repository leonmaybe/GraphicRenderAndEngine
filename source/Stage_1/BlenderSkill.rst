Introduction
============

它有一系列的快捷键，小键盘，1，3，7，以及各种变换，s,r等等快捷键。`快捷键操作视频 <http://v.youku.com/v_show/id_XMjc0OTM3MDgw.html>`_ 
`Greybeard's Blender Tutorials <http://www.ibiblio.org/bvidtute/>`_ 
操作模式
============

blender 的操作模式类似于vim,倾向于键盘操作，首先要分大的模式，object模式，edit模式，然后每一个命令本身也就有模式，例如常用的，R,S,F,等等操作，都可以再分为x,y,z轴，并且可以再加上一个数字，表示操作的次数。例如沿x轴放大几倍，SX7 然后enter 就可以了。

在各种仿射变换的时候，要注意其对应的是global/local坐标系。同时在各种仿射变换的时候，脑袋里要有那个变换矩阵。知道哪一个对应什么参数，这样自己的每一步操作就都知道对应如何编程了。

blender的操作模式是select + modify +save. 所以如何快速的选择效率的关键
1. 点线面之间的转化，是可以通过它们之间的连接来转化的，同时还可以通过操作模式，例如在例如在选择edgeloop时，通过一个点，还是其中一段来得到全部呢，还是要一个个去点，另外可以同时选择所有同心的edgeloop. Ctrl+ RMB,ALT+RMB,CTRL+ALT+RMB对于点操作，都会看不同的效果。同时还可以通过最短路径来进行选择。  另外在编辑的顶点的时候，周围的点也可以随着变的，这个就是`Proportional_Edit <http://wiki.blender.org/index.php/Doc:2.6/Manual/3D_interaction/Transform_Control/Proportional_Edit>`_ ,这个在生成山峰的时候特别的有用。对于每一个顶点指定属性，例如颜色是可以通过`Vertex_Paint <http://wiki.blender.org/index.php/Doc:2.6/Manual/Materials/Special_Effects/Vertex_Paint>`_  来实现的。另外一种那是通过matrial,以及纹理。

.. csv-table:: 

   http://www.blender.org/documentation/blender_python_api_2_70_0/bpy.ops.mesh.html#bpy.ops.mesh.region_to_loop , select boundary region , 
   点线面画 , 先选择一个点然后，ctrl+E 得到edge loop然后再用 ctrl+ select more ,

1. 操作的与或非运算，在分形的雪化的时现，就是用python的set的与或非运算。

.. csv-table:: 

   operation , key ,
   select all/disselect , a ,
   Inverse ,  Ctrl + I ,
   region , Lasso Select,circle select(c),box select, lasso select ,
   by properity , select similar (shift + G), select by group ,
   random ,  这个功能 随机选的功能很好玩 ,
   W,  就相当于我们平时的右键功能 , 因为现在左右键，都已经被占用了 ,
   space , 可以快速搜索各种命令 ,

 
the Object stand for a things, for example, box, body. the whole things. as an object it has its own orignal coordinate. you add/delete/move/modify/select it. 

.. csv-table:: 

   add , shelf +A ,
   delete , X ,
   COPY , Shift +D(Duplicate) ,
   Select , RMB / A  + shift ,
   move , G(Grab) ,
   rotate , R ,
   Scale , S ,
   alt+M , merge , 这些可以是针对物体也可以是针对顶点，或者线的 ,
   ctrl+J , joint , 这个是在object模式，因为进入编辑模式的时候，必须选定一个物体，当用多个基本体组合一个复杂体就需要了。 ,
   P , split ,  ^ ,



`Blender Programming <BlenderProgramming>`_ 
============================================

`MakeHuman <MakeHuman>`_ 
=========================

Edit mode
=========

when you want to devidid the object to face/edge/vertex, then you can use the edit mode. in the edit mode, there is vertex and edge and face. you can select transform between them. for example, you can manipulate a face by extrude. Fill face from vertex or edge.  

Vertex -> edge -> face just the BEGIN/END of the opengl. 并且三者之间可以相互转换的。

.. csv-table:: 

   Extend , 可以拉出一个面 , Alt+E ,
   F ,  有由点生成面 , 例如有一个洞口你要韩封上的时候，这个特别的管用 ,
   ctrl+R , edgeloop 封闭的曲线 , 可以分为取线段/圈/同心圈 ,


all the things in edge should be one object, when add a seperate object should add in the object mode. before entering the object mode you need select one object, entering edit mode. meanings that entering one object edit mode.
 

sculpt 模式
=============

在粗的模型建好之后，可以针对局部的顶点进行微调来用，当然只能操作现有的顶点。所在这个模式，分辨率一定要高，就是修图一样，原图的分辨率要高，要不然就很突兀。 当然这个操作最好要画笔来操作，可以用tegra note来当做画笔来操作。
一般用sculpt都是用做高模的，当然可以拿来做低模，这里有一个Retopology 模式，你可直接用mesh套在一个物体，并且最小化来实现一个低模。并且可以实现不同分辨率的模型。


`modeling <ModelingSkill>`_ 
============================

blender的建模现在还只是皮建模，也就是mesh，而内部是空的，现在更加流行的就体纹理，我是不是可以利用体纹理的肌肉骨骼呢。并且人体也是需要多线程或者多进程来谐调的。每一个进程相当于循环系统，每一个线程相当于一个小功能单元。

对于mesh的建模，当然是因为，最简单的方法那就是模型本身进行采样，建模采用了类似的方法，但是采用多频率采用，在活动关节部分，采样频率高一些，在不大变化的地方，采用低频率采样，至于多高频率，这个就要取决于，变化的频率，但是如何判定模型部分的变化频率呢。如果能给出一个量化的定义，建模就会相对很容易。现在的常识，那就是关节用多一些edgeloop，这样可以减少畸变。对于洞也采用edgeloop来建模。也就是多边型。

当然在建模过程中，其中一个方法那就是根据模型的拓扑结构。不同的拓扑可以不同的方法。可以充分利用物体的对称性，来简化建模过程。对于建模，那就是画线，描点。这时候就可以充分利用各种修改器了。
*修改器* 有点类似于设计模式，并且是利用计算代替存储状态，就像函数式编程，可以链式传递。所以它们之间的先后顺序非常重要。并且它并不修改物体本身的属性。
常用结构

.. csv-table:: 

   结构生成类 , 阵列修改器 ,
   形变类 ,  ,
   模拟类 , 读取网格数据来，并计算 ,



同时还有sculpt模式的雕刻模式。对于光滑的表面，我们也可以采用曲线与曲面来建模，这就用到贝赛尔曲线NURBS,其实也就是多次曲线，线段，我们采用了一次，而曲线，可以根据我们的要求的连续性与光滑性，例如可导，再用最小二乘进行曲线拟合。当然用局部坐标系。而顶点那就是那些数据点。所以知道了模型本身的形状函数，就可以直接关键点来控制，这个有点类似于矢量图的味道。样条曲线，就是曲线来拟合的过程，并且曲线拟合，控制点，还有曲线类型，记录曲线多项式，并且是几个点坐标，坐标是如何计算的。

如果只是这样的点与线我可以利用图像的技巧来建模，例如使用二阶滤波器，就可以把这些主要线条弄出来了，然后再加一些算法判断，就可以快速的建模了。


Texture
=======

还有一种那就是投影建模，就像CAD上的三视图一样，每一个方向建好，然后直接投影合成就行了，当然也提供了一种如何利用照片来进建模的方法。那我是不是可以直接利用照片的全向拼接，然后再反向建模，把UV分解过程给反过来呢。但是如何合成一个三维的数据呢。有办法了，并且自动计算出三视图的分割。在进行UV分解的过程，就是找个线条把其进行切开，对于一个多面体来说可以直接按照边线切，但是曲面是如何来切呢，例如地球，就利用特殊的映射方式了。其实就像图像曲线调整。

当然采样搞定了之后，相当于压缩传输了，之后还要解码恢复，其实建模的过程就相当于A/D转换，到最后显示，就要D/A转换了。当然D/A转换，采用了不同方式，一种采用是texture贴图，但是平面后，还需要三维的数据，再来一个法向贴图与texture对应，就有高度，再加上密度贴图，可以这一点例如颜色，或者重力等等属性，就可直接上了，当然这些值，也是可以通过光照模型实时来计算的，如果这样，原来的texture图最好物体本身的颜色属性，所以对于规则的几何体，就可直接指定其material属性来代替texture图了，当然物体的表面还是可以其实图的，在制作texture贴图的时候，是先切开，再画，然后再图，然后再贴回去，当然这也有一个问题，切开之后，原来连接在一起的面，现在分开的，但上面的texture具有连接续性，分开画需要自己手工记录对应位置太麻烦，一种方法，遇到相领面的时候，在画的接起来，边线做好标号，画好再分开，这个blender已经替我们实现好，有一个专门的模式叫做texture paint.来专门做这个事情。

UV分解与贴图不是同一个东东，UV也是纹理，不过是三维到二维的映射，每一个点都还有对应关系。然后是加载图片，并且拉伸等等进行映射。UV unwrap之后映射关系是要自己逐步指定的。而不是再通过简单计算得到。


建模的方式，一种简单的方法可以一些简单的图元进行拼接而成，然后进行缝合操作。当然这个过程也对于基于图元进行形变，这种属于组合式。

另外一种那就是直接用点与线直接拉成了。当然这个时候可以在像练字一样下面有一个字帖就好了，当然blender提供了这样background image的加上blueprint功能。  各种各样的模型库可以参考 `Volume Texture <Work.VolumeTexture>`_ 
#. http://www.the-blueprints.com/ there is a lot of blueprints for modeling.


要把blender变成像vim一样的工具对自己来说。一切都绕着python来开始，这样可以它们给结合起来。

光照与texture
================

计算光照三个基本条件。
#.  光源，位置与属性
#.  物体，材料属性，例如对光的属性，是反射呢，还是折射。
#. 人眼，也就是摄像机的位置。
对于光源blender已经提供了常见的几种类型了，并且位置指定的。摄像机的参数也是指定的，当然它的摄像机参数不够的，自己还可以利用lensfun来增强其效果。另一个那就是如何保证其跟踪对准的问题，这里blender 也已经实现了，trace to,aim（ctrl+alt+0) 就是对准。当然这个对准的标准是什么呢。是不是当前3D模型，正前方呢。 当然有时候直接跟踪一个物体不方便，让其跟踪一个空物体，然后这个空物体与目标物体关联或者成为目标物体的一部分。

camera default是鱼眼镜头的模式。同时在运动的过程中，镜头的参数是要发生变化，而常规opengl的坐法，与需要镜头变化来做工作让物体来直接做了，并且使用了多级的分辨率来做，其实在不考虑运算量情况下，在视窗之前添加摄像头处理过程，其实就是viewport,这中间还可以有一系列的处理过程。所有关于镜头的知识都可以用到viewport之前这一块。 在blender 中可以直接使用passepartout 功能，来实时查看视窗的大小与位置。一切准备好了之后，F12就开始render了，一般情况不是实时做，当然也可以采用实时做（选择GLSL模式），并且render 的结果不是保存的，这个要自己手动去保存。

那另个问题就来了，人脸与镜头，光源搞定了，更重要的是物体的光照属性，规则图型可以直接用指定参数来搞定了，如果是非规则图形，那就是用法线texture与intense texture来搞定了。 其实texture 就是 `MatPlot中的中grid <Work.MatPlotlib>`_ ,对就用分形来做texture,可以得到意义的精度，因为分形是实时计算的，只要坐标，并且坐标与初始值之间的关系，然后就可以通过分形来得到，这个点精确的颜色，而不是靠模拟。现在问题就是找到一个分形来表示物体。当然是不是三维分形就解决了volume texture的问题。 同时分形是不是可以解决体纹理。  

如何制作法线呢，一个是可以利用高模来bake法线，与intense最后用到低模上。

物体本身的材料属性与texture是两个问题，当然有的时候是可以放在一块的，就像视窗与镜头处理一样。 对于材料属性既可以物体的所有部分都用一个属性，也可不同面采用不同的属性。就像合金一样（material slots来实现)。 但是在同一个面使用多个属性，现在还叠加不了，可以自己实现。当然有可能是可以通过修改器来实现的。 现在NV 已经定义了自己的MDL  快盘\graphic\121115_mdl_introduction.pdf,是不是可以拿来用，并且借用函数式编程与C的语法，还有相关的库来实现。它的哲学类似于sql,不是定义如何实现，只是定义要实现什么。 
  * `MTL material format <http://paulbourke.net/dataformats/mtl/>`_ 

------------
什么使用纹理
-------------
使用纹理经常要matrial材料放在一起了。各个东东在讲解的时候都是分开讲的，对于简单的物体很容易对付，对于复杂物体就没有办法了。一个复杂的物体可能有多种材质组成，另如一件衣服，袖口可能不用材料，光照效果也就不一样，当然上面图案也不一样。
复杂的物体是需要多种材料属性，例如对于组合物体，通过添加matrial slot与对于顶点分组映射不同的属性来实现。而对于复杂的例如合金可能就是直接同一点上多种物理属性的叠加了。这个就是物理化学方程了。这个就要看你建模对象本身了。另外那就是各个建模各个部分依赖关系。

现在关系理顺了。mesh是为了解决形变的问题，matrial是要解决材料其物体属性的问题，如果不做物理仿真，那就只需要其光照，颜色属性就可以了。如果需要物理仿真就需要密度以及等等一些信息。那上面的图案呢，当然一些情况下，图案会包含颜色。就个时候就需要用纹理了。也可以直接用高模直接得到，那只需要指定颜色与物理属性，就不需要texture了。

为了简化计算，采用低模轮廓，再加上物理属性再加上纹理，用了纹理基本上光照属性可能不大需要了。用一个轮廓再加一个贴图就可类似的效果，但是这样会一个问题，贴图的是死的，不随着光照的效果来改变，这个时候采用多种纹理来解决这个问题，其中一个那高度纹理bump texture.另一个那就是法线贴图，各种属性texture,其实半成品的查表，那就是基于UV坐标的各种光照属性也可以是其他物理属性，这样的物理相当于变相曲面细分，这种是直接到像素级别了。当然最直接那就是种基于三维3D的属性，例如空间内每一点的密度，现在采用这种x,y,z到UV的转换，然后与属性bind到一起了。不然的话直接是张量计算了。例如UV图来表达高度，在用一张UV图来表达颜色，在用一张UV图表达高光分布，三者是复用同一个UV坐标的。

那么这么做与直接使用高模直接指定属性的区别，并且各自的优缺点在哪里
直接使用高模好处在灵活，特别是物理仿真的时候，可以根据时间或者其他不断的实时的更新物理属性，顶位置变了，其法线等等也都是会变的，当然纹理也可以采用相对数据来解决这个问题。对于纯刚体运动可能直接用纹理相对计算问题不是很大，但是对于skin动画来说，每一个每个面三个顶点的相对关系与前一个状态的时状态关系发生变化的时候，这种纹理的方式不能直接使用了。或者对于复杂物体部分使用纹理部分使用高模。
当然全用高模意味着计算量大。

Meta Object
===========

这个模型具有stiffness,就可以接伸，就像一个橡皮泥一样。可以随意的拉。然后，曲线，与路径的使用了。并且还有bevel,taper可以使用，使用曲线定义定义一个截面，然后利用路径或者曲线来定义轴向运动。 这个路径可以静态指定的，也可以利用hook来跟踪别的物体的运动轨迹。那这个是不是可以利用分形来计算呢。 另外常用的那就是Spin了，弄一个任意的形状去spin就可得到一些基本模型，例如车轮，用一个圈做轴向圆周运动。流体的运动是不是可以这个呢。

另外就是colin所说的，利用一个基本体，然后不断的接伸调整，就像用圈筒来制作shark一个一样，表面曲线是调整的。


AnimationSystem  
=================


电影制作 
=============

那就是利用green 背影拍摄，然后直接加入3D动画中，但是自己想要的直接融合，实现虚拟与现实的对接与这个差不多，也就是多加了传感器。
现在制作方式是利用node 模式，把每一个动作分开来拍，然后就像GIMP中图层合成一样，这样才直接合成。这个结点系统会相对的更加的灵活。每一frame一个这样的node的合成图，然后再用video sequence editor按照顺序放好，但是这样的工作量太大。应该有更加便捷的工作方式。那个F曲线是不是可以用到这里。

粒子系统 
=============

利用大量的粒子来模拟各种流体，首先要指定一个粒子发生器。然后是指定粒子的数量，大小，形态，物理属性等，例如存活时间。当然速度方程，还有的那就是外力场，例如风吹等效果。

对于毛发，还可以有再粒子父生的功能，就这样大大的简化的毛发的设计。当然做头发，还要设计一个假头皮。方便复用。是不是可以一个通用的毛发，方便人物造型。通过添例子物理以及texture来实现各种效果。

另外可以使用freehand edit 来手工编辑粒子的路线。并指定再生点。这样就大大加块设计速度。  另外粒子输出有一个render属性，可以指输出物体各个属性，并且自己制作输出物体的mesh.

流体动力
在粒子的类型，直接选择fluid,就可以了，同样之前的设置还是有有效的，加以加各种场，同时还要设置domain范围。流体有各种各样的属性，是不是可以直接使用。例如添加障碍等。可以洪水，以杯子倒水，把自己的那个水珠照片再用这样的方式来试一试。

smoke   这个与流体差不多。

soft Body 使其向rubber一样。

Cloth Dynamics  这个只有简单的设置，这些都是基于粒子，但是粒子之间关系就像弹簧模型来制作的。

GameEngine
==========

 其主要部分就是gamelogic,每一个对象的logic,分为三部分。这里采用了MVC 结构,可以直接就像node系统一样进行图形连续编辑，当然可以直接利用python脚本。更加的简单效，无非那是一些if判断。   问题的难点是sensor的triger是如何实现的，对于各个不同的模型，是如何碰撞检测的，不规则物体不会直接用bouncing box来简单实现的吧。

.. csv-table:: 

   Sensor , 相当于事件 , 事先已经有很多的标准事件,
   Controller ,
   Actuator ,


一个物体对象很多属性，你可以为其添加物理仿真属性。然后就要有限元法逐点计算了。 另外就是camera的控制以及场景的控制了，都是可以来改，人物与场景是可以分离的，这也是大部分游戏都是这么做的。例如子弹的实现。

现在问题如何把这些都东东融合到一块。
See also 
=========

#. `【资料】漫画教程（4本 PDF格式） <http://tieba.baidu.com/p/1141288524>`_  
#. `大量漫画教程下载 <http://www.douban.com/group/topic/23282588/>`_  
#. `Sculpt&#95;Mode <http://wiki.blender.org/index.php/Doc:2.6/Manual/Modeling/Meshes/Editing/Sculpt&#95;Mode>`_  
#. `command of blender <http://www.dummies.com/how-to/content/blender-for-dummies-cheat-sheet.html>`_  
#. `blender高级视频教程 <http://www.rr-sc.com/thread-16437042-1-1.html>`_  
#. `21 course tutorial <http://blenderartists.org/forum/showthread.php?243393-Blender-2-6-Video-Tutorial-Series-(21-videos)>`_  
#. `gryllus.net <http://gryllus.net/Blender/3D.html>`_  
#. `blender  基础教程 <http://www.vhxsd.com/playlist/5791&#95;1>`_  
#. `Blender基金会: 开源电影制作教程(1) 面部骨骼设计之拓扑结构 (译) <http://open.sina.com.cn/course/id&#95;262/lesson&#95;3590/>`_  面部的调整
#. `a-better-face-tutorial <http://www.blender-models.com/articles-tutorials/modeling/a-better-face-tutorial/>`_  写一个脚本从照片到模型，只要做一个二阶导就可导出其线条了。
#. `Bump and Normal Maps <http://wiki.blender.org/index.php/Doc:2.4/Manual/Textures/Influence/Material/Bump&#95;and&#95;Normal>`_  
#. `MeshToNurb 指令 <http://wiki.mcneel.com/zh/rhino/meshtonurb>`_  
#. `NURBS  <http://zh.wikipedia.org/wiki/&#37;E9&#37;9D&#37;9E&#37;E5&#37;9D&#37;87&#37;E5&#37;8C&#37;80&#37;E6&#37;9C&#37;89&#37;E7&#37;90&#37;86B&#37;E6&#37;A0&#37;B7&#37;E6&#37;9D&#37;A1>`_  利用高阶典线，那我是不是可以利用分层建模呢。
#. `Blender 3D︰從入門到精通/Blender可以做什么 <http://zh.wikibooks.org/zh/Blender&#95;3D&#37;EF&#37;B8&#37;B0&#37;E5&#37;BE&#37;9E&#37;E5&#37;85&#37;A5&#37;E9&#37;96&#37;80&#37;E5&#37;88&#37;B0&#37;E7&#37;B2&#37;BE&#37;E9&#37;80&#37;9A/Blender&#37;E5&#37;8F&#37;AF&#37;E4&#37;BB&#37;A5&#37;E5&#37;81&#37;9A&#37;E4&#37;BB&#37;80&#37;E4&#37;B9&#37;88>`_  
#. `非常详细的建模教程 <http://gryllus.net/Blender/PDFTutorials/AllPDFTutorials.html>`_  自己可以照一个一个的去做

Thinking
========



*Blender operation mode*
just like vim but with a lot of mouse and gui operation. it use various mode. by tpye special charactor. for example R,G,V,S. there is layer just like the GIMP.

-- Main.GangweiLi - 03 Jun 2013


blender可以直接用来做配成集群。这个是非常方便的。

-- Main.GangweiLi - 13 Dec 2013


它的x,y,z正好用rgb来表示这样会直观的知道，现在方位。

-- Main.GangweiLi - 13 Dec 2013





ctrl+shift 对于同一个键，可以加上这两个来实现三个方向的操作，例如s是垂直方向，而ctrl是水平方向，对于三视图可以利用ctrl来取得反向操作。

-- Main.GangweiLi - 15 Dec 2013


昨天晚上，看了usr/lib/blender/template的脚本，用起来也非常的简单，也就那是scence都应的数据结构操作，可以直接来进行状态操作。

-- Main.GangweiLi - 15 Dec 2013


*UV贴图*
原来先把图形UV分解之后，然后把图再放上去，然后再合回来。

-- Main.GangweiLi - 15 Dec 2013



-- Main.GangweiLi - 15 Dec 2013


*物理特效与仿真*
粒子系统，烟雾，软件，布料，流体仿真，刚体仿真。

-- Main.GangweiLi - 15 Dec 2013


*后期合成*
就是利用了结点系统，利用管线连接。对于图像的处理，采用结点操作，例如模糊，动态等这个有点类似于线性的管道。是不是可以利用python同时把blender与GIMP 融合在一起。

-- Main.GangweiLi - 15 Dec 2013


*建模流程*
可以参考模型来进行画图。

-- Main.GangweiLi - 15 Dec 2013


blender 实现的还只是表面三维，还不是立体三维，也就体纹理建模，而自己软体仿真是需要体纹理的。

-- Main.GangweiLi - 15 Dec 2013




-- Main.GangweiLi - 18 Dec 2013


如何制作全景图
#. 拍摄全景图拼接起来
#. 把全景图用blender映射到立方体或者球体上。
#. 在blender中用摄像机拍摄各个方面的图片，然后保存成纹理。
#. 但是如何使用远近的问题，它们会更自动转化吗。直接进行减。

-- Main.GangweiLi - 25 Feb 2014


*代码与手工* 是没有区别的，要想代码实现，首先要实现手工如何的。然后用代码去记录手工实现的过程。对于难以的规范化的可以去暂时用硬编码去做。

-- Main.GangweiLi - 11 Mar 2014


*Planing* 建模一定规则好，先画哪一块，再画哪一块，并且哪一块要分开化，需要转动的就分开化，这样方便后边的控制，不然要分开的时候就麻烦了。所以再做的，就要规则化，更可能一大块用一个基本拉出来。然后那就是如何对齐的问题，以及原点的问题。

-- Main.GangweiLi - 17 Mar 2014


*分组与批量操作*
blender是提供分组的，对于顶点的分组，material 是针对顶点组的，另外那就是通过脚本直接用for循环来操作。具体可以权威手册的说明 。

-- Main.GangweiLi - 22 Mar 2014


*模糊* 与影像合成，现在知道模糊的用途了，我们对于镜头的各种狂热，就是其变焦能力，也就是模糊的能力，现在有了blender,就可以轻松对各个元素进行不同程度模糊，就可以实现各自己的效果了。对于虚化背景这是么容易的一件事情。难点在在于LOD的参数。相机的模糊参数。利用高模来实现各种各样的场景组合。

-- Main.GangweiLi - 09 Apr 2014


*复用机制*
blender里也是有复用机制，那就是data,object就像class与object之样，例如`Attaching material to object vs to obData <http://blenderartists.org/forum/archive/index.php/t-182687.html>`_ ,并且数据的复用在`Data_system <http://wiki.blender.org/index.php/Doc:2.6/Manual/Data_System>`_ 里有详细的说明，以及如何制作库。就像.a文件一样。这个其实就是opengl中要要用到instance机制。

-- Main.GangweiLi - 17 May 2014


-- Main.GangweiLi - 01 Jun 2013



