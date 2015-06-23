介绍
====

.. graphviz::

   digraph development {
      morph->skeleton->skin-softbody;
      keyframe->layer_skeleton->skin;
   }
   

`骨骼动画详解 <http://blog.csdn.net/ljlees0830/article/details/7453647>`_  这篇文章已经讲的很细了。 

最早的动画就是morph,就是采用keyframe然后插值来实现，然后就是不断的循环了，。

对于一些各向同性简单刚体运动是够了。但是对于各向异性并且关系得复杂的一点直接用skelton动画，在一些基本的物件之间定义约束关系，实现跟随运动例如眼球的跟随运动。中间过程由自算机自己模拟，并且要指定frame rate.定义了约束关系之后，各个物体自身的运动属性可以通过指定那个仿射变换的参数就行了。

对于一些刚体来说没有问题，但是对于人来说表面又有些软，因为这样会出皮肤之间撕裂，skin动画就解决了这个问题。如果是纯软体还有什么方法。
skeleton动画又要分为FK，IK,不管怎样最形成树。由keyframe时root bone的位置，计算各个子bone的位置，最根据所有最新bone的位置，skin 是在skeleton的基础分块 是在来遍历所有顶点，更新所有顶点的位置。然后每一部分根据不同权重受到骨头的影响。并且可以叠加。每一个顶点存储一个bone列表及其权重。 joint 就是子bone的原点在父bone坐标空间的位置，而root bone的原点就是再global world 位置。

`rag doll <http://zh.wikipedia.org/wiki/&#37;E5&#37;B8&#37;83&#37;E5&#37;A8&#37;83&#37;E5&#37;A8&#37;83&#37;E7&#37;B3&#37;BB&#37;E7&#37;BB&#37;9F>`_  就是简单skeleton动画。


实现
====

root的参数可以根据keyframe设置好，也可以物理引擎来实时的计算。在blender 中可以graph editor来定义root的运动曲线，然后由blender自动的捕捉keyframe. F 曲线，就是各个参数随时间的曲线。你可以在物体的任意属性点右键，把其插入keyframe,就可用F曲线去控制了。

熟悉的opengl的流程，再看这些这些建模就容易多了，对于骨骼的更新可以CPU来计算，因为是一个树状的更新，当然也可以用CUDA的来实现并行，而对于skin的点的计算则可以用vertex shader 来计算。 而光照的计算可以放在 geometry shader中进行。texture 上去主要在framebuffer这一层,而图形的合成可以放在pbuffer来操作。当然光照也可以放在这里做。 这样就可以充分利用GPU的并行与CPU的串行。在引擎中，这此简单的操作shader都是自动帮你生成的。

对于skin的mapping中在存储数组也可以在blender 交互的产生利用field实现。当然这些能够与shape keys结合起实现肌肉的运动。
gameworks skinning app 就是在CPU上直接计算 computebones,来直接计算的 bones, 然后直接用vertex shader 来更新mesh的。mesh的基本数据作为静态数据全局数据存放在哪里。实际上就是逐级差分的的过程。

.. csv-table:: 

   octahedron,
   stick  ,
   B-bone ,  这种是针对软骨链来用的 ,
   envelope , 可以通过距离与直接用weightPaint 来指定mesh顶点的关系的，先选定bone,去paint, 红色权重最大 ,


另一个难点那就是layer bone的层次的定义。但是它们之间的关系是如何可影响的， 
关于骨头动画中，骨头的驱动关系，到是底Forward Kinematics, Inverse Kinetics. 这个是不是运动类型来区别呢，例如在武术中原理应该是前向驱动，而一些手本身的动作是后向驱动。IK和FK是组成我们人体运动的两种方式，做绑定的时候这两种运动方式需要配合使用，都需要做，具体的话请去查阅人体动力学的相关知识，从而了解我们身体中的运动哪些行为属于IK，哪些属于FK。  当然这里面有好多免费的动画资源可以利用。
直接按 Alt+A 可以直接预览动画了。

#. `reallusion <http://www.reallusion.com/about/aboutus.aspx>`_  一个比较前沿的公司。
   关于人体骨骼层次定义可以在makehuman中找到现成的`模板 <http://www.makehuman.org/doc/node/defining_the_target_rig_manually.html>`_  . 就是一个树形结构。root是hips. 就是一个树形结构。所谓的力从腰发。

另外就是利用shape Keys在基本的mesh对象进行变形来得到新物体。利用shape keys 一个重要的方面就是在制作动画表情与肌肉运动，例如lip Syncing,Smile and Frown,Wide and Pucker，Puff and Suck.而这些都是直接选定mesh上一组点，做特殊的运动，然后带动周围的点。

动画的复杂尤其是人，各个部分是如何组合并且有依赖关系如何更新这个是难点，理清依赖关系是难点。例如哪些全局的动作哪里局部动作。 而这一块已经有了深入研究，那就是`动作捕捉 <MotionCapture>`_  

最后这些动作再配上声音就可以了。
#. `Character_Animation/Rigging <http://wiki.blender.org/index.php/Doc:2.4/Tutorials/Animation/BSoD/Character_Animation/Rigging>`_ 


key frame + 样条插值
====================

对于刚体的运动可以用平移+ yaw,pitch,and roll 来实现。  实例见Work.HDRSample,物体位置，可以由运动方程来插值求解，而物体自身的运动也可也可以自身运动方程来解决，因为大部分的运动方程不超过三阶，所以三阶样条可以很好的模拟大部分的运动，`龙格现象 <http://zh.wikipedia.org/wiki/%E9%BE%99%E6%A0%BC%E7%8E%B0%E8%B1%A1>`_ 解析了为什么高阶不适合插值的原因。  自身坐标与全局坐标的变换通过矩阵变换就可以实现了。
#. `基于虚拟样机的小型仿人机器人步态规划与步行控制研究 <http://www.docin.com/p-687268763.html>`_ 
#. `样条插值在运动模拟中的应用 <http://wenku.baidu.com/view/7a7a0b385727a5e9856a6138.html>`_ 
#. `Rotation_matrix <http://en.wikipedia.org/wiki/Rotation_matrix>`_ 
#. ` yaw, pitch, and roll <http://planning.cs.uiuc.edu/node102.html>`_ 
#. `样条插值 <http://zh.wikipedia.org/wiki/%E6%A0%B7%E6%9D%A1%E6%8F%92%E5%80%BC>`_ 
#. `四元数 <http://baike.baidu.com/view/319754.htm?func=retitle>`_  就是超复数e,i,j,k，可以非常方便的描述，绕任一轴旋转的问题。 并且可以方便向`欧拉角进行转化 <http://www.cnblogs.com/yiyezhai/p/3176725.html>`_ 

那些benchmark中游戏的路经是如何规划的。在需要控制的地方加一些随机数，再加上一些AI就可以了。那个埃及艳后的，以及moto的那个画面路径。 那么是不是武术也可以这样呢。这个在blender中 follow path的参数可以设置。
  

Body motion 
============

#. `List_of_motion_and_gesture_file_formats <http://en.wikipedia.org/wiki/List_of_motion_and_gesture_file_formats>`_ 
#. ` VICON bodybuilder  <http://www.biomech.uottawa.ca/english/teaching/apa6905/lectures/bodybuilder_v1_2.pdf>`_ 



See also
========

#. `万向节死锁 Gimbal Lock <http://blog.csdn.net/ixsea/article/details/7285203>`_  
#. `学习笔记—四元数与欧拉角之间的转换 <http://www.cppblog.com/heath/archive/2009/12/13/103127.html>`_  
#. `三维旋转：旋转矩阵，欧拉角，四元数 <http://www.cnblogs.com/yiyezhai/p/3176725.html>`_  

#. `FFD （自由形式变形）修改器  <http://www.3dmax8.com/cankao/3dsmax&#95;web/ffd&#95;free&#95;form&#95;deformation&#95;modifiers.html>`_  

Thinking
========





-- Main.GangweiLi - 19 Jan 2014


*最简单的动画* 就是移动摄像头，设定摄像头的路径。

-- Main.GangweiLi - 22 Mar 2014

