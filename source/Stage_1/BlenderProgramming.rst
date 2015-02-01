`Blender 开发环境  <http://wiki.blender.org/index.php/Dev:Doc/New_Developer_Env>`_ 
=======================================================================================

#. `开发文档 <http://wiki.blender.org/index.php/Dev:Contents>`_ 

Blender 的RNA模型
====================

Blender 的数据结构采用了 `RNA 模型 <http://wiki.blender.org/index.php/Dev:2.5/Source/Architecture/RNA>`_  其实各种数据结构模型都是解决mapping的关心，这是一种仿生结构。最终是通过指针来进行链接来形成一张大网，对于GameEngine 的研究，对于各个对象的存储生成与管理上，为了提高效率，存储要尽可能连续存储，而引用相对的灵活，这个过程就要各种各样的的数据表，例如相当于每一个对象都是table,而其ID就是索引，所以如何提高cache的效率，就非常的重要了，gameEngine提到几种内存管理的办法，例如`Work.GameEngine <Object vs compoent>`_  因为如果直接全用OS默认管理方式，那就是不断指针不断的指来指去，所有的过程都要靠查找。因为不知道你的邻居是什么样的，而数据库表不一样了，就解决了这个问题你的邻居是谁。所以根据应用合理设置内存管理是一个有效的办法，那就是内存数据库，但是也要看你的overhead是不是有这么高。最简单的那就是自己定义几个全局的数组就行了。

指针的相互引用就增加了引用计数，并且还会造成循环引用计数的问题，也就无法释放内存了，解决办法之一就是对引用进行分类，强引用，弱引用权值不一样，一些情况下弱引用不改变引用计数。这个与网络风暴一样，去环，在链路上加权值。例如父对子有引用，子对弱引用 。

RNA数据库，其实就是把对象表象，在后台存储是表来实现。这样就解决了连续性问题。一个对象就是一个表结构。并且这个表是动态创建的。每一个RNA定义都有三部分，access,define,type.其实就是么数据库建立表的结构的过程。对于已经定义的结构，就是在数据表中加一条项，生成一个索引，也就是所谓的objectID.
Mesh 模型
===========

`BMesh/Design <http://wiki.blender.org/index.php/Dev:2.6/Source/Modeling/BMesh/Design>`_  是由vertex/edge/face组成。

Pose/bone 模型
================


数据结构
============

好的数据结构不是采用编程语言默认的数据结构，而是采用数据构的扁平化结构，在存储上数据表，而在使用上是面向对象。类之间的引用，就用外键。

.. csv-table:: 

   `bpy.types.Object <http://www.blender.org/documentation/blender_python_api_2_57_release/bpy.types.Object.html>`_   ,基它的结构定义都可以从它得到。,
   http://www.blender.org/documentation/blender_python_api_2_57_release/bpy.types.Material.html#bpy.types.Material  ,Material  ,
   http://www.blender.org/documentation/blender_python_api_2_57_release/bpy.types.AnimData.html#bpy.types.AnimData , AnimData ,
   http://www.blender.org/documentation/blender_python_api_2_57_release/bpy.types.AnimViz.html#bpy.types.AnimViz  , AnimViz ,
   http://www.blender.org/documentation/blender_python_api_2_57_release/bpy.types.ID.html#bpy.types.ID ,bpy.types.ID ,
   http://www.blender.org/documentation/blender_python_api_2_57_release/bpy.types.ObjectModifiers.html#bpy.types.ObjectModifiers ,ObjectModifiers,
   http://www.blender.org/documentation/blender_python_api_2_57_release/bpy.types.bpy_prop_collection.html#bpy.types.bpy_prop_collection , bpy_prop_collection,
   http://www.blender.org/documentation/blender_python_api_2_57_release/bpy.types.MotionPath.html#bpy.types.MotionPath,MotionPath,
   http://www.blender.org/documentation/blender_python_api_2_57_release/bpy.types.Pose.html#bpy.types.Pose,Pose ,
   http://www.blender.org/documentation/blender_python_api_2_57_release/bpy.types.Action.html#bpy.types.Action , Action ,
   http://www.blender.org/documentation/blender_python_api_2_59_0/bpy.types.MeshVertex.html , bpy.types.MeshVertex ,
   http://www.blender.org/documentation/blender_python_api_2_57_release/bpy.ops.uv.html ,bpy.ops.uv ,

与外部程序集成
=====================

blender自己内置了3.2的python,当想到与其他的方式集成的时候，就是如何加载包的问题，那个都是可以通过全用sys.path的添加以及imp的使用来实现。 `info_tips_and_tricks.html#use-an-external-editor <http://www.blender.org/documentation/blender_python_api_2_63_release/info_tips_and_tricks.html#use-an-external-editor>`_  还可以把`blender 当做module来用 <http://wiki.blender.org/index.php/User:Ideasman42/BlenderAsPyModule>`_ 。


如何开发流程
==================

对于日常用的脚本，在操作的时候，只要把把上边的toolbar往下拉一些就可以看到console了，就知道调用使用了哪些API，并且它有丰富的库与模板来供你使用。
很大一部分操作源码，是scripts/startup中。

interface
=========


.. csv-table:: 

   bpy  ,代表整个blender,
   bpy.context , 代表当前的整个context,例如对于编辑的切换就是在改的 ,
   bpy.data , 对于整个RNA数据库的查询都是通过它来查询的  ,
   bpy.opts/bpy.types.opts,  包含了所有已经注册的Operator,所有界面操作都在这里,
   bpy.types.Panel , 自定义Panel , 需要实现draw方法,
   toolsettings = context.tool_settings , toolsettings 是所有设置都可以可以在这里 ,


添加自己的动作，就只要继承bpy.types,Operator,并在其中实现自己execute,函数就行了，就像vsauto中那个runcontext一样。 `bpy.types.Operator <http://www.blender.org/documentation/blender_python_api_2_57_release/bpy.types.Operator.html>`_  实现一个bpy.types.operators 一个例子。主要的接口是execute,invoke.invoke用来初使化。那些变量属性会在blender面板里初始化。

动画接口的操作

.. csv-table:: 

   http://www.blender.org/documentation/blender_python_api_2_70_0/bpy.ops.anim.html#bpy.ops.anim.driver_button_add , 如何添加drive ,
   http://wiki.blender.org/index.php/Dev:2.5/Py/Scripts/Cookbook/Code_snippets/Armatures , Three ways to create objects,
   http://wiki.blender.org/index.php/Dev:2.5/Py/Scripts/Cookbook/Code_snippets/Armatures ,Cookbook/Code snippets/Armatures ,
   http://blender.stackexchange.com/questions/3549/how-can-a-bones-tail-be-changed-by-a-script, how-can-a-bones-tail-be-changed-by-a-script,

resource 
=========


.. csv-table:: 

   blendernation  python-scripts , http://www.blendernation.com/category/blender/python-scripts/ ,
   Mesh Generation with Python , http://prideout.net/blog/?p=44 ,
   Py/Scripts/Cookbook ,http://wiki.blender.org/index.php/Dev:2.5/Py/Scripts/Cookbook ,

See also
========


Thinking
========

