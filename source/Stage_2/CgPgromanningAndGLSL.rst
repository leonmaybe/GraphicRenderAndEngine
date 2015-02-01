Shader 
=======

在ogl4.3版本的pipline,可以定制的部分都四部分，

.. csv-table:: 

   vertex shading stage , 顶点数据的处理,
   Tessellation shading stage , 生成图元之前 ,
   Gemetry shading stage , 生成图元,
   Framement shading stage ,
   Compute shading stage ,  这个可以用于纯计算东东，有点类似于cuda的功能 ,

并且采用机制回调的机制，那么需要知道数据是如何进行流动的。并且shader是独立的`语言与函数库 <CgPgromanningAndGLSL>`_ 。并且是实时的编译连接的。 这里两个三个问题：
#. 调用约定是什么。
#. 如何实时动态编译与链接与加载与调用。   `cgc  cg compiler driver  <http://http.developer.nvidia.com/Cg/cgc.html>`_   支持JIT，也支持离线编译。直接加载其bin文件。
   
.. ::
 
   ps_3_0
   // cgc version 2.2.0017, build date Jan 29 2010
   // command line args: -profile ps_3_0 -I../include -DGLSL_COMPILER -DNO_SUPPORT_DDX_DDY -DPASS_DEPTH
   // source file: fancy_cloth_diffuse.cg
   //vendor NVIDIA Corporation
   //version 2.2.0.17
   
   
      
#. shader语言本身复用机制是什么，是不是也象C语言一样，有健全的机制，也还是部分机制。

MojoShader 
===========

#. `MojoShader <https://icculus.org/mojoshader/>`_  cross platform tools physX and apex use it.
  phsyx 使用MojoShader 来做编译器，可以其代码树中看到它，并且它的语法分析是用re2c 来生成的。

调用约定：
==========

#. 函数入口点。
#. 输出与输入参数。 
#. 具有前后依赖的参数如何解决。

对于问题1， GLSL的调用，就像C程序一样，它的入口点是main(),但是它不需要任何参数，也不需要返回值。

对于问题2， 是函数调用最基本问题，GLSL采用全局变量来进行。对变量进行分类定义。

.. csv-table:: 

   in ,  对于每一次的调用，这两个值都会随之更新的，例如顶点，就是会顶点数据在编译之前用glBindAttribLocation指定好，编译后就固定了。 ,
   out ,  一些内建的数据结构gl_Vertex,gl_Normal,gl_Color,gl_multiTexCoordX,gl_ModelViewMatrix,gl_ModelViewProjectionMatrix,gl_NormalMatrix ,
   uniform , 只要ogl application 不更新，这些会保持不变，类似于半常量的作用，例如在native_globe中太阳的位置，在每一frame时，太阳的位置是不变的，但是在frame之间就有可能是变的了,constant across a given primtive.,glGetUniformLocation/glUniform*/glUnfifomrMatrix*()  ,
   const , compile-time constant ,
   buffer , shader storage buffer ,share with app ,
   share , 

#. uniform 在每一次glinkProgram时生成参数的位置，或者使用uniform Blocks 来独立传递，这是使用buffer in GPU, 这时候读取其就要采用glGetMapBuffer, glBindBuffer来设置buffer类型为GL_UNIFORM_BUFFER，glBufferData来批量设置数据，或者使用glGetActiveUniformBlockiv/glBindBufferRange/glBindBufferBase/glUniformBlockBinding. 所有的调整都需要自己根据字节去计算了。
#. in 的参数是在gllinkProgram之前，uniform是在gllinkProgram之后，先get/glunif*()

具有前后依赖的参数如何解决
==========================


这个依赖interfaceBlocks 来解决各个pipline stage的数据传递。那么在哪定义这些传递数据格式呢。  你把自定义的数据顶点数据格式放在GPU的Buffer之中，其数据格式是通过glVertexAttribPointer来指定的,然后，glDisable/enableVertexAttribArray来设置传哪些，哪些不传，再后由glDrawElements来起启动画图。

.. csv-table:: 

   Uniform blocks ,
   Buffer Blocks ,
   in/out Blocks , varying 的属性是传在这里了 ,


 如何实时动态编译与链接与加载与调用
===================================

     <img src="%ATTACHURLPATH%/shadeCompilation.png" alt="shadeCompilation.png" width='661' height='573' />
#. shaderObject 应该类似于obj文件。也就是表示代码是可以复用了。
#. `为GLSL脚本搭建运行环境  <http://blog.csdn.net/youyouxiaomengye/article/details/4974472>`_  是不是非得一个.ver,.frag一块放进一个program.并且各个都有main,如何区别谁是谁，这个是创建glcreateshader时指定的。
       原来在早期的版本，一个fragment shader 只能对应一个vertex shader,如何办呢， glProgramParameteri()有什么

.. csv-table:: 

   glGetShaderiv , 获取编译状态与log ,
   glGetShaderInfoLog ,^ ,
   glGetProgramiv ,^ ,
   gldeleteShader , 只要link之后就可以删除了,
   glIsShader/glIsProgram , 判定当前一个名称是什么，在opengl内部会维护一个状态列表 ,
   glGetSubroutineUniformLocation/glGetSubroutineIndex , 对函数的支持 ,



.. csv-table:: 

   glProgramParameteri ,
   glUseProgram ,把GLSL 加载当前的pipeline中,
   glGenProgramPipeLines/Bind/Delete, glUseProgramStages， glactiveShaderProgram,glProgramUniformMatrix* , 

shader语言本身复用机制是什么，是不是也象C语言一样，有健全的机制，也还是部分机制。以及调试问题
=============================================================================================

opengl并不支持文件系统，所以也就不能支持#include.  它自己内部的虚拟文件系统是非常简单的。解决的方案，等待新版的支持，利用m4等模板技术来实现。提前准备好一个大文件。
#. `how-to-write-reusable-glsl-code <http://stackoverflow.com/questions/13530146/how-to-write-reusable-glsl-code>`_ 
#. `how-to-using-the-include-in-glsl-support-arb-shading-language-include <http://stackoverflow.com/questions/10754437/how-to-using-the-include-in-glsl-support-arb-shading-language-include>`_ 

着色语言有三种CG,GLSL,HLSL（微软的d3d平台），HLSL与CG是有的。CG是都支持的。以后就学CG就行了。不过听老大说CG不行了，还是用GLSL.但是两者差别很小。这些脚本也支持事先编译好，这样可以节省时间，但是就有一个移植性的问题。

现在看来已经没有了CG了，现在只有GLSL了，这个无所谓了，关键是rendering，就是各种光照模型，这个要模型材料本身的属性来决定用什么模型来计算，而不是说一个场景里所有东东都用一个场景计算的。例如人脸的光照模型与头发的光照模型就是不一样，当然真实环境完全按照物理模型来不过就是计算量的问题。

Type
====


.. csv-table:: 

   basic , float, dobule,int,uini,bool ,
   complex , vec,mat,set,opaque,sampler type(texture),image type,atomic counter type , 并且复杂的数据类型有会有一些基本属性，例如vec.length ,

vec 最高不超过四维，如果是坐标那就是(x,y,z,w),如果是颜色v那就是（r,g,b,a),如果纹理坐标那就是(s,t,p,q).
当然对于这些变量类型的操作都会有简单的操作方式,它们之间不能混用，

函数参数
========


.. csv-table:: 

   in ,
   const in ,
   out ,
   inout ,
   return value , any GLSL data type ,


   
.. ::
 
   vec3   luminance = color.rrr;
   color =color.abgr ;// reverse the components of a color 
   

对于mat同样会有各种各样的切片操作。

如何debug
===========

直接使用Nsight 来调试。
#. start Graphic debug
#. Pause and Capture Frame
#. open shader list,debug and edit
#. it use the nvtx to record the draw call and then replay.

.. note:: change TDR, 打开Nsight monitor,然后右键点属性修改TDR为60s,不然，windows 会认为显卡长时间没有反应reload显卡driver.

See also
========

#. `Geometric algorithms on CUDA <http://www.gvu.gatech.edu/people/official/jarek/graphics/reading/cuda.pdf>`_  
#. `Mixing CUDA and OpenGL within the same application <http://gpucomputing.net/?q&#61;node/182>`_  
#. `CUDA, Supercomputing for the Masses: Part 15 <http://www.drdobbs.com/architecture-and-design/cuda-supercomputing-for-the-masses-part/222600097>`_  
#. `Panda3D 手册: 3D 纹理 <http://panda3d.noie.name/manual/3D&#37;20&#37;E7&#37;BA&#37;B9&#37;E7&#37;90&#37;86>`_  

#. `Shader and Program编程基本概念 <http://blog.csdn.net/myarrow/article/details/7737313>`_  

Thinking
========



*Computer INvariance*
GLSL不能保证同样的代码在在不同的shader能得到的相同值，这个是由于浮点数误差与优化影响的，你可以通过两种keyword来控制它。
#. invariant 
#. precise 

-- Main.GangweiLi - 26 Aug 2013


*subroutine*
  1. 先定义函数原型
  1. 定义函数
  1. 定义函数指针


-- Main.GangweiLi - 26 Aug 2013


*宏处理*
支持宏处理，但不支持#include.支持#ifdefine.  现在已经支持了，#include了。


-- Main.GangweiLi - 26 Aug 2013


`OpenGl read and write to the same texture <http://stackoverflow.com/questions/11410292/opengl-read-and-write-to-the-same-texture>`_   opengl 的规范中是不保证这种情况的，因为GPU在读写的时候有缓存机制。可以使用渲染到纹理，然后通过两部实现。或者利用`NVIDIA的扩展 <http://www.opengl.org/registry/specs/NV/texture_barrier.txt>`_  或者直接使用CUDA 就不会有这个问题。
例如计算直方图一样，就要通过两部，需要一个反复复值的问题。

看来直接shader直接实现直方图还是挺烦的一个事情。用opencv计算速度太慢,简单，例如直方图，利用pixel shader 把亮度本身当做指针，然然后写纹理就不行，例一个办法那是直接实现读取与复值功能。 

-- Main.GangweiLi - 05 Mar 2014


*用数学计算来代替分枝*  `why-is-this-glsl-shader-so-slow <http://stackoverflow.com/questions/16337723/why-is-this-glsl-shader-so-slow>`_  branching and looping in shaders is (can be) evi， 因为CPU强项在于并行，要尽可能用并行来取代looping. 
采用预编译指令，来解决分枝也是一种办法。

-- Main.GangweiLi - 05 Mar 2014
