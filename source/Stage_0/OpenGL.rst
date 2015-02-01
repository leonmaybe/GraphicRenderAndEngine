Introduction 
=============

opengl 主要分两部分，一部分那就是画图建模，那些顶点，图元。这一部分解决了先画什么后画什么。如何画问题。但是数据如何而来是需要我们前端提供的。当然中间的一些变化也可以shader来做。后一部分渲染就是着色，如何确定哪一些显示什么样的颜色，一个是通过光照模型来计算，另一种那就是利用texture。
在这一部分，坐标是通过硬件的本身的插值来得到，而其他的信息则是要通过uniform传来过，或者in,out来传递。

对于前端的模型数据，可以用各种建模工具，列如blender,maya等来实现，也可以直接使用算法来实现，例如分形。
另外一个那就是场景的管理问题 与各种物理模型的建立。


要把图形学的知识与opengl对应上。 CG编程主要就指的是对种光照算法，实现一般是基于opengl与d3d的。中间通用部分都已经被这些库实现了。可以大大发挥的地方那就是顶点的光照变换以及fragment  面的光照变化。这两个方面都已经开放了，专业称为渲染脚本。
OPENGL  就是一个状态机，现在它的直接debug, 现在公司在做。一个是把所有的draw call 录制然后回放。同时也可以知道各个draw call 的所要用资源例如贴图。以及CPU时间。要想把这个状态机搞明白。


走到现在，随着每一天的潜移默化，对于graphic了更加深入的认识，那种认识与一开始假设的东东都是不样的，就像我们人一样，从出生走到了现在，已经经历了很多东东，这些即是财富也是羁绊，先前的经验在我们看到新的事务之前，会有一个先验的假投，这应该如何，那应该如何，随着你不断的深入，就发现当初的哪些假设是错误的，哪些正确的，所以一定要清楚是原则是要坚持的，哪些经验是可能是错误的。世界没有完全一模一样的人，同样也没有完全一模一样的事务。所以在凭经验的时候，一定要注意这个了。所以符合理论的知识，这些是不会错的，是你推理的基石。其实就像量子物理一样。所有的一切都是一种假设，在我们还没有发现在更加完美的假设，就把当前这个假设当作真理，什么时候我们才能真正得那些东东。人类已经在漫漫的历史长河中积累了这么多，再离真理还很远，这也是为什么我们科技还需要不断向前发展，还有太多的未解之迷。

同样对于3D也是一样的。ogl是解决了一frame之内是如何产生的，当然在这一frame之内，很多东东是变的静止，而动画则是frame by frame往前走，当然opl也提供了灵活高效的pipepline,从点到面元，再体，再回到2D的显示。

就像画一个人来说吧，一般情况下不会一次能把整人给画出来，因为人体的各个部分的变化是不一致的。如果一致的就可以一次画出来。并且还要先后顺序。
基本的人形，先计算简单位移，为了不需要每一次所有顶点重新传输一次，相当于增量的传输，一次性的把所有基本模形加载好，在画的第一步，先进行顶点的变换，在一frame中计算，可以当做临时的常量来传给ogl,但是在frame与frame之间为变换的，就有了为glunitXXXX一堆的设置。一般我们研究拓扑性质，也就是顶点个数不变，以及它们的之间的相互关系，只改变他们之间的位置。这样就有了所谓的位移变化，以及它相关的光照变化，这些在一frame的计算中都是固定的，光照的位置以及以及光源都是固定的不变的，区别在于如果一次计算不过来，可以分开多次计算。采用单变量分析的方法来进行。

当然了如果CPU的计算能力强的化就没有必曲面细分了，一切就只需要降采样就行了，目前看来硬件的计算能力还远远没有达到人们随心计算的能力，要是达到了就不需要我们的优化了。正是因为资源的限制才能了优化的存在。对于有规律可者是可以通过插值得琶值点，我们只要给出控制点就可以了，然后tesselation的阶段来细分。这样之后就进入了面片阶段，在这个时候，我们同样可以对面片进行再次个性定制，这一个draw主要是画脸，这么我们可以现成的shader来计算其法线以及光照效果。

对于顶点的计算可以两有种，固定数量的顶点只需要计算终点，另一种办法那只循环计算其差值增量。可以把这些值保存起来再用，那就有了transfer feedback的的机制，同样这里还有一个专门独立的computer shader但是这个shader 是在哪一个位置呢。

当然ogl也得到了一次画多笔的用法，那就是drawArray的用法，另外始能的控制，哪一笔画哪一比不画。并且顶点也是复用的。其实就像现在的3D建模一样，先给出一个物体的点云，然后再进行连线。各个点之间的联系。draw要做就是这些连接。

然后就进入光栅化了，把其变成二维图片，但是其三维的信息还是要考保存的，例如其深度信息，因为只有有了这些信息，我们才能解决遮挡的问题。然后我们才能进行合成。如何合成，这个时候同样有shader可以用。


其实整个就是一个流水线的机制，不过要事先把东东都做准备好了，然后在用draw call来trigger它。其实其就是cuda的一个stream,完全可以用CUDA来实现ogl大部分，除了光栅化那部分要靠硬件来做的。kernel那就是shader,想想如何在ogl的流程中来控制kernel的分配执行，是采用的什么自动计算的策略。例如顶点shader的分配是一维的，还是多维的，如何分配才能得到优化呢，同样的还有tesselation shader,geometryshader以及fragmentshader也是一样的。

shader本身的分支，以前内存分配方式是不是都可以用到ogl上。

ogl只是给出一个从点线面的最基本的流程，至于可以定制的地方大部分都已经扩展给做了，当然还有一些新新的东东还没有进来。这个就要看看当前最新论文了。当然一般情况不会对这些基本框架进行改动，而是增加的新东东。每一个基本算法都是基本上围绕一个特定的问题的。例如适合在vertex shader里搞定，还是在geomtry里，以及在fragmentshader里去做，这里的fragment不是一个个面元的，而是指的同一个draw下来的面元。这里包含深度信息。
，
现在明白为什么还有一个vertex attribute object上，这样可以实现顶点数据，可以画多次也就是instance的用法。合成主要是在fragmentshader,例如skin的就在于vertex shader,以及面的计算，以及最后合成一张二维的图上。

http://www.opengl.org/wiki/Rendering_Pipeline_Overview
http://openglinsights.com/pipeline.html
这里有现在pipeline框图了。

.. seealso:: LightAndShadow.rst ,CgPgromanningAndGLSL.rst, DrawCallAndMOdule.rst

openGL
======
*OpenGL pipeLIne*
.. graphic::

        digraph  openglPipeline {

          rankdir=LR;
           {Application;Display;}
           { node [shape=box;style=filled,fillcolor=blue];
            Vertex_assembly;Vertex_operations;Primitive_assembly;Primitive_operations ; Rasterization;FragmentOperations;
             Framebuffer[shape=circle];
           }
            Application -> Vertex_assembly->Vertex_operations->Primitive_assembly->Primitive_operations -> Rasterization->FragmentOperations->Framebuffer->Display;
             Framebuffer->FragmentOperations;
            Vertex [shape=record, label="{ vertex | coords \l colors \l texture coords \l normals \l fog coords}"];
        }

这个相当于一个CS模式的服务，在host端，把顶点，纹理，以及shader脚本都加载上去，并且初始化，然后直接用glDrawXXX就开始化了。
glGenBuffer/glBindBuffer/glBufferData 这个就是申请显卡内存，并且加载资源的赛程，vetex的格式是通过，glVertexAttributePointer 来提定，
glCreateShader/glCreateProgram/glAttachShader/glShaderSource/glComileShader 加载与编译shader, 并且通过，glBindAtrributLocation 来指定shader 传入参数格式。
然后用glLinkProgram加连接shader,  而这个glBindAttribLocation 与glVertexAttribPointer指定的参数要对应一致。

glActiveTexture/glGenTexture/glBindTexture/gltexImage2D/glCompressedTexImage2D 加载资源   glTexParam 来设置各种纹理的参数。 把纹理加载到texture unit 上，而在shader是通过sample2d对应的texture unit 来读取纹理数据的，并且这个参数是通过
glUniformli 来设置传递参数的。

对于计算流程，就如上图了，各种buffer都是为了分配显存，这些就像new/malloc一样，在应用层我们只关心我们能够分配能够。所有数据与状态配置好，然后用drawXXX来触发opengl的执行。每一次调用drawXXX,都会执行一次整个流程，例如顶点的shader,fragment的shader,在shader是可以存储texture的数据的，而最终的输出就是FBO,默认情况下它是直接流到操作系统，当然你可以自建一个FBO,把输出导到texture,然后再texture映射到texture unit中共shader 来调用。只有把texture 中绑定某一个 texture unit 中然后才能在shader使用。 这个就是变相的CUDA，CUDA中只需要一个kernel函数，而在这里则需要特定的流程。如果把显存中数据拿回到内存中怎么办 那就是PBO了。
当一个场景要分多次画，例如每一次只能画一部分，可以把每一部分放在一个FBO中，最后一次都一次都是只画一个虚框，两个对角三角形来，然后把前面只部分的FBO再通过texture 再回来合成最终的图像。 当然要是能把所有东西都一次全部分准备好，每一次的场景只调用一次DrawXXX,其实DrawXXX底层都是调用glClear来实现的。    FBO就像多路输出器，你把多个textureattach到FBO上，渲染就会同时直接输出到多个texture上。并且是一样的，但是有一个问题，那就是多个不一样呢。FBO有各种样的缓存，如何来控制输出到哪一个缓存中呢。  可以研究一下gl_drawBuffer/gl_drawBuffers来解决。   所谓的动态纹理也是这样实现的。
并且对于画图的时候，自己经常的想一笔画，老是整个图形就是一笔画成。其实不是的，先画什么,后画什么，可以说每一个drawcall就相当于画一笔。正是因为一笔画不完，但是又要考虑上一笔的影响怎么办呢，其实就是统里的单变量分析法，每一次将它做其它的东东都是已经画好的，就也形成了大量的常量，这就是为什么ogl每画一笔的时候就会大量，因为相当于其他物体对其的影响就是常量。

为什么需要T&L变换呢，是为了方便复用，不然的话每一次的调要提前计算好顶点，而现在只需要给原始的顶点数据，形变的数据都可以放在T&L的时候去做。
#. `gl fbo<http://www.songho.ca/opengl/gl&#95;fbo.html>`_ 非常不错
#. `终于弄明白了FBO的用法 <http://blog.sina.com.cn/s/blog&#95;4062094e0100alvt.html>`_  要总结一下
#. `PBO的用法 <http://blog.sina.com.cn/s/blog&#95;4062094e0100alvt.html>`_  原来只是为了数据传输的方便
#. `glUseProgram <http://blog.beuc.net/posts/Multiple&#95;glUseProgram/>`_  这个相当于回调函数。

opengl只提供最基本的功能，例如图元，变型，纹理，光照，雾照等基本东西，如何去用以及建立更一层，例如软体等等，以及各种物理模型都是需要你自己建或者利用其上层库来做的。
在光栅化之前都是顶点与图元的操作，即使有计算光照以及FOG以及法线信息，都是只有顶点的。因为在此之前，你不知道其内部具体要有多少个点来组成，也只有当其进行了光栅化之后，这些点才变成可数的了。 并且在光栅化之前这些顶点不会发生质变。当然你可以直接传现成已经光栅化后块过来。opengl也是支持的，那是texture是在光栅化之前还是之后，按道理应该是之后吧。
实现上，主要是GPU与计算机的CPU之间来进行实现。显卡连接显示器。而opengl而是实现那些资源管理并且GPU进行交互接口的一个良好实现。但是要想充分学好opengl首先要弄明白CPU与GPU的通信机制。内存操作与显存操作的有关系。openGL定义一些标准API下面有各个不同的实现方式。它是计算机图形在实践上一种最佳模式的总结。有了它，再不是以前的单打独到了。有了这样一个common的平台。同时它又是在Xwindows之上的东西。`theory to Experiment <这里是自算机图形的发展史][有助于理解各种应用来源]] [[http://www.sumantaguha.com/>`_ ，但是OPENGL的每一条命令要求都是在前面一条执行完才能执行。这样就会有一个问题，不相关的部分，为什么不能

并且framebuffer的分配，是在初始化的时候与XWindows相关的配置是一块的。gl 本身只关心画图。并且由于采用的C/S并且也像X Windows那样透明化，所以对于上层应用程序来说应该感觉不到，所以通常所指的大部分状态都应该是GL server state.

实际3D图形采用的是有限元法来表示。
*Primitive_operations* 包含下面的流程。

.. graphviz::

    digraph Primitive_operations {
         
      rankdir=LR;
      node [width=0.3 height=0.3 label=""]
      node [shape=circle style=invis] start;end;
      node [shape=box,style=""];
      M [label="Model-View \l Matrix"];
      PM [label="Projection \l Matrix"];
      PD [label="Perspective \l Division"];
      V [label="Viewport \l Transformation"];
      start -> M [label="Object Coords"];
      M -> PM [label="Eye Coords"];
      PM -> PD [label="Clip Coords"];
      PD -> V [label="Nomalized Device Coords"];
      V -> end [label="Window Coords"];
    }

opengl 的工作流程
#. 建模  -> 纹理贴图 -> 变形 ->光照渲染

为什么需要贴图呢，原来这样的，因为构成曲面呢，你可以采用无数的小三角形，速度太慢，只要把边线弄出来，然后来内部直接真充图。就可以了。但是控制性就是不很好。同时也还可以加光照。因为有现成光线跟踪算法，你只需要设定光线位置与强度并且类型就可以，计算机可以自动生成。由顶点构成一个一个图元，并且图元不是一个个去建，而是一些些去建，例如BEGIN/END是不能嵌套的，每一次只能一个系列的图元，并且基本图形中，是没有曲线与曲边的。只有直线段。曲线应该都是在光栅化以及之后才会要用到东西。 图元是指基本形状，而分组应该指的是显示列表与BEGIN/END之间会可以产生一个module.这是一个逻辑组，你取决于你的定义。那就有一个问题，这些图元能够保证重复利用，例如一个人的模型，我可以分部分并且参数化每一个部分，在重复另一个人型的，就没有必要再从重头到尾再重写一遍了。

BEGIN/END  有点像GIMP中的路径，不同之处在于路径只能是轮廓。但是如果opengl用轮廓+texture就与GIMP中路径中加贴图是一样的了。并且BEGIN/END之间的操作也是有规定的，哪些可以，哪些不可以。

对于在曲面或曲线的点，各种方向与特征都是与其法线作为参照的。只要知道了法线，其他的东西都可以基于来计算，所以在opengl中也每一个顶点的信息都会有其法线的信息。在光栅化之前的操作，一切都是坐标基础的，而光栅化之后，坐标就变的不那么重要了。而是颜色等等变的重要了。

如果需要变型，这些东西都应该是怎么样顺序呢，它们之间是有演化关系，还是说从下面实现来说，都是每一次重新开始化的。没有什么前后关系。

texture如何映射，都是可以指定的，同时这也是各种渲染脚本所可以自定义的。是线性，还是非线性。或者说有些图就是靠人一个一个点上去去对应的。 2D的贴图光照上去，没有任何深度变化。3D的贴图就会有这种变化。当然也一种模式利用像三视图一样东西，用二维表示组成三维。texture映射不光有光照，还有图层前后深度关系。这些其实就是GIMP中图层操作了。图层之间与图层之内的事情。
`体纹理 <VolumeTexture>`_ 
颜色是不受几何变切的影响，它受光照以及texture合成的影响。光照也在光栅化之前，是利用向量乘法来实现，向量本身方向性。至于光栅化后，每个顶点特性如何影响其内部的点的值，或者采用纹理等等，都是渲染的事情了。光照模型，还有各种材料设置。GIMP处理图形应该都是光栅化之后的图片。


TexCoord,指定，所有的这些本质都是指令矩阵乘法的参数，这个与GIMP中那个卷积模板一样，只是参数不同的，大部分的滤波最终结果在形式上都是卷积操作。雾化操作累似于光照操作，也可以说是另一种光照吧。opengl采用是矩阵右乘，每一个计算操作都要知道其物理意义，以及对应的知阵乘法是什么。这样才能贯通。

BufferObjects 其实就是CUDA中提到的，分配设备中的内存。不同的存储设备速度是不一样的。以前老是以时间换工间，还有一种那就是用空间来换时间。具体采用哪一种，就要谁的overhead高了。并且提供一整套API接口来读写管理，并且可以地址映射的方式。它的用途可以像GIMP的图形一样，在动画的过程中，可以把一些固定的不变图片，放在一个buffer中，然后在后期直接合成就行了。至于是如何合何，就像GIMP的图形合并函数一样，会有种运算模式。而这个在OPENGL中简单的可以直接指定，复杂的那就要用CG脚本来做了。而在OPENGL里也一套为了加速的过程。例如把顶点等等放在一个一数组里，然后直接还使用，另一种方式直接就是VBO模式，最后是显示列表，显示列表是由硬件直接实现的。并且 在光栅化之下，还有各种frameBuffer来来加速的。


渲染分为光栅化之前与之后，之前就是顶点渲染，也就是如何根据顶点基本值，如何来确定其他各种参数算法，基本的规则opengl都自带了，复杂的都留给用户自己了。最终这些渲染脚本都是一些回调函数。并且这些接口规范与模型有详细的文档。基本编程模型叫做programm object.在光栅化之前还是之后，这个是看硬件的计算能力来的，在早些年，硬件能力不行，也就只能顶点渲染，图形内部只能根据顶点来拟合了。现在能够做到基于pixel来渲染，特别是光照，他会把表面上每一个点的法向放在一个张图中，然后逐点去计算。


*Rasterization* is the process by which a primitive is converted to two-dimensional image. 光栅化就是把模型放在屏幕上的过程，并且纹理也在之后，还以雾化都之后，这些都做了之后，才是fragment operations.  而这些操作其实与GIMP中各种操作差不多了，多个buffer,就相当于多个图层了。并且通道，蒙板等等。光栅化的过程，有一个问题，那是插值拟合。但是这样就会一个精度的问题。
光栅化以及后面渲染时候，有一个坐标换算的问题，例如我的模型有10000万个点，而屏幕不会正好是整数倍。多与少都是可能的，同时在纹理贴图与三角形之间也不一定是一一对应的关系。这里有就有一个如何对应的问题，用专业术语来说，那就是反走样，采用多采样的模式。所谓的多采样是遇到模糊的点如何确定当前这一点，可以取4联接点，也可以取8联接点，当然也可以最大，这个就是图像处理中那个模板的大小。另外三角形的大小与贴图的大小不一致的时候，就个时候，就要指定顶点映射坐标，例如这个顶点是在贴图的哪一个点。内部的值逐行扫描得到，另外三角形大，那就有一个问题是重复纹理，还是保持空白，还是贴图边线的颜色一直保持到三角形的边线，还是指定的别的颜色。

*FOG* 的模型，在各种各样的场景中用的是最多的。

*Evaluator* 例如Map1,Map2,EvalCoord提供一些更加通过算法实现，例如多项式，你需要只提供参数。这样大大减少了你的编程压力。 以及DisplayList,NewList/ENDList/CallList.为了提高速度，直接Vertex<VertexArray<VBO<DisplayList.
http://www.ciroduran.com/disenajuegos/wp-content/uploads/downloads/2012/05/ParametricOpenGL_2pp.pdf

分数多多式http://www.math.dartmouth.edu/opencalc2/cole/lecture19.pdf.


Color,Pixels,AndFramebuffers
============================

这些都是光栅化之后的操作，而光栅化是由pipeline固定实现的。也就是插值的过程.对于每一个buffer分为两种，framebuffer用于最终的显示，而renderFrame是用来进行render的，但是它们之间的是由联系的，那renderFrame 是要attach 到framebuffer上的，就相当于renderFrame是从光栅化后到framebuffer的一个通路。并且创建buffer是在窗口初始就是要配置的。是由窗口管理器来提供的。例如glutCreateWindow(()/glutInitDisplayMode. 整个过程都是gen/bind/dele过程。还有查询函数。
renderFrame也是一样的gen/bind/delete过程，另外glrenderbufferStorage存储过程。同时要注意各个buffer的数据格式，同时现在明白了，为什么numpy中，在图像中为什么还要会浮点数了。
同时还有glDraw/Copy/ClearBuffers来操作buffer 的内容。

.. graphviz::

    digraph fragment {
       rankdir=LR;
    Scissor->MSAA -> Stencil -> Depth -> Blend ->Dither -> logicalOP;
    }

VBO,FBO,PBO,texture
===================

其实只要明白了这个pipeline,明白这几个对象操作哪一段存储空间，以及信号的流图。

#. `缓冲区与混合 <http://baike.baidu.com/view/1280561.htm][glclear]], [[http://wenku.baidu.com/view/b8ca5e84ec3a87c24028c4cd.html>`_  opengl也采用缓冲区的办法，就像GIMP中图层与通道一样，例如背景不必每一次重写，只需要用硬件在显示的时候，直接合成就行了。多个缓冲的模式与图层混合的模式是一样的
#. ` OpenGL Frame Buffer Object (FBO)  <http://blog.chinaunix.net/uid-20235103-id-2976141.html>`_ 
#. `OpenGL Pixel Buffer Object (PBO) <http://www.songho.ca/opengl/gl_pbo.html>`_ 
这一章主要是讲了，opengl有几个队列，但是我感觉这一张讲的不好，因为并没有讲清楚，这一张的意义，并且前后关系都没有讲清楚。  不过还是讲了不少东东的。
为了提高移值性，与精确性，在opengl尽可能的采用浮点数，一般都采用归一化，这样的例如颜色，可能没有最精确的，只有最接近的。并讲了各种各样的缓冲区，每一种缓冲区的用途。并且如果opengl支持立体的，还要左右buffer,指的是左右眼是两个buffer,nvidia的3D眼镜就是不断的切换左右眼的图像来达到3D的效果。|

在opengl中，buffer中坐标系是，左下为0，0，右上为1，1.


.. csv-table:: 
   :header: Buffer, Usage

    color buffer ,也就是我们平时操作的那个绘图区域，所代表是逻辑的一点，对于实际的屏幕可能是几点，这几面有一个multisampling,会有另一个或者几个buffer来表示，一个点的在每一个图元的颜色，最后得以一个均值，或计算值，来做作为最终值，是可以设计的，最多可以多少个图元可以参与决定吗。这个可以用glminSample来决定的,
    depth buffer , 来记录每一个点到视点的距离（还是视点平面的距离），有没有方便，每一frame 中视点应该是不变的,glDepthFunc,同时可以预置offset,glPolygonOffset() ,
    stencil buffer , 蒙板效果，效果使用glStencilFuc()/glStencilFuncSeparate来操作，来设置与改变蒙板使用glStencilOp()来操作，是通过，alph通道来操作，或是各个通道独立来操作,
    MSAA buffer ,可以用glGetIntegerv()/gtGetMultisampleFv()来操作。用gl_samplePosition,gl_sampleID来决定 , MSAA 当多个图元过同一个像素点，那么他是由最后一个图元表示呢，还是第一个，还是综合值。会记录在每一个图元的采用位置。,
    对于使用3D眼镜需要屏幕的刷新率到要达120上，这样感觉才不闪。两只眼的图独立各60帧。 
    Blending ,glBlendFunci/glBlendFunc/glBendFuncSeperate指的要进来的fragment与已经在里面的fragment是什么如何来组合 , 
    Dithering ,  这个开始对颜色开始配对，每一个系统都是支持一定数目的颜色，现在就要开始进行匹配，最适合的那个 ,
    logical Op , glLogicOp 与Blend有什么区别，难道是图像处理中的开操作与闭操作吗 ,
    occlusion Query , depth 是基于象数点，那是可以基于图元吗,应该是可以利用occlusion query 来做 glBeginQuery,GlEndQury(),
    glDrawbuffers , 可以确定写在哪一个buffer里，如何计算速度更块，你可以算好很多frame 放在那里等着显示 ,

fragments shader的输入与输出是什么如何来确定。

顶点的颜色对后面如果影响，以及纹理在什么时候加入呢，是放在哪里呢。fragment shader是如何来反馈的。  图元就是几何图形三个顶点。而framgment,就是在光栅化后，屏幕上，包含内部这些区域的点。这个光栅化的过程，每家的实现都可能不一样，其实就是一个插值的过程。例如你为是线性插值，还是二次，三次，还是贝赛尔曲线。


对于种buffer object 的操作，就是对显存的一种操作方式而己。但是ogl换成高大尚的词之后让人变的难以理解，简单的理解这个这么认为，各种genbuffer就是声明一个指针，但是没有明确malloc函数，到直接copy的函数，对于一些静态数据，这可通过这些buffer一次考过去，但是那些动态生成的模似呢，例如水波等需要动态数据，这就需要在各种buffer之间传送数据。

在CUDA里我们知道，不同的传递方式，效率是不同的。要尽可能批量化，对于kernel的那些优化对于shader同样适用。

glTexSubImage2D, 这些也主要是对显存操作方式的不同，不同的API的效率也一样的，这与access pattern是相关的。
`http://kasicass.blog.163.com/blog/static/39561920128983251368`_ 这里对比各种传输效率。

PBO应该对应就是异步传输而己，没有什么神秘可言。 关键是要注意流程，在ogl这个流程里的读到机制。
这个时候，就像做图了，glRasterPos2i.glBindBufferARB,
http://wenku.baidu.com/view/8cdc52c14028915f804dc2ef.html
主要也就是与framebuffer与texture object之间进行数据转换。
`变换 < OpenglTransform.rst>`_ 

==============================

主要 为是了坐标的转换，与如何表达物体的运动。自身的运动以及相对运动。这一部分就是vertex operation.

openGL issue study
==================
.. csv-table::
    
    0 , https://devtalk.nvidia.com/default/topic/541643/tegra-tools/glcompressedteximage2d/ , study it , new , :-) , 29 Apr 2013 06:55 ,
    1 , https://devtalk.nvidia.com/default/topic/540753/tegra-tools/sampler2d-in-vertexshader/ , study it , new , , 29 Apr 2013 06:57 ,
    2 , vertex 定义，数据结构与操作函数 , http://www.opengl.org/wiki/Vertex_Specification , new , , 11 May 2013 06:37 ,
    3 , 要不照着hehe的教程一个一个做 , , new , , 12 May 2013 09:23 ,
    4 , CG编程是不是可以调用cuda , http://blog.csdn.net/leonwei/article/details/4583045 , new , , 12 May 2013 09:48 ,
    5 , cube map Texture , http://game.ceeger.com/Components/class-Cubemap.html , new , , 17 May 2013 07:13 ,
    6 , texture , http://www.humus.name/index.php?page=Textures , new , , 17 May 2013 07:14 ,
    7 , ocean-and-water-rendering-with-triton , http://sundog-soft.com/sds/features/ocean-and-water-rendering-with-triton/?utm_source=opengl&utm_medium=banner&utm_campaign=triton , new , , 17 May 2013 07:16 ,
    8 , frame 如何定义 , , close , , 21 May 2013 06:47 ,

#. `编程基础 <http://wenku.baidu.com/view/d3faee1f964bcf84b9d57bbb.html>`_   clear-> draw -> swap/flush  库可以参考这个，要开始动手写一个了。


resource
========

   `Mesa sourcode <Work.MesaOpenGL>`_ 

standford course 
=================

`opengl <http://graphics.stanford.edu/courses/cs248-07/>`_  这里把这个整个流程讲的很明白
   * `standford  lecuture for opengl <http://www.stanford.edu/class/cs148/lectures.html>`_  %IF{" '' = '' " then="" else="- "}%


bindless texture
================
对比着CUDA来看ogl,现在发现其原来也很简单，就是一个优化pipeline,你要事先把所有的东东都准备好，然后用drawXXX来trigger, 并且trigger一次，都bind一堆的东东，清场一回。并且这种bind在很大程度是没有意义的，只是接口的规范性。而在实际的操作过程实际这些bind操作大大限制效率，因为它破坏了cache的使用，这个是完全与cache背道而弛的。所以为了提高效率，NV提出了自己的bindless机制，这可以就以充分利用cache来提高效率。 利用cache是隐式的优化方式，与简单的只看代码是看不出来的。具体更高的内容要查看`nv bindless`的使用。 



See also
========
   * `OpenGL编程指南 <http://wenku.baidu.com/view/1a08a11fc281e53a5802ff59.html>`_  平行投影与透视投影。透视投影原理就像相机一样把三维转化到二维。同时也要利用齐次坐标系。
   * `最全的AndroidOpenGL开发文档 <http://wenku.baidu.com/view/b2fb07f3f61fb7360b4c6547.html>`_  %IF{" '' = '' " then="" else="- "}%
   * `水效果Ⅰ - 水池 <http://www.zwqxin.com/archives/opengl/water-simulation-1.html>`_  %IF{" '' = '' " then="" else="- "}%
   * `显示列表 <http://bbs.pfan.cn/post-219518.html>`_  %IF{" '就像一个宏录制的功能，不过，是有条件限制的' = '' " then="" else="- "}%就像一个宏录制的功能，不过，是有条件限制的
   * `用opengl-es画图步骤 <http://hi.baidu.com/gmfoqlmthriprur/item/8c5058577e559c464fff2030>`_  %IF{" '' = '' " then="" else="- "}%
   * `when-to-use-glActiveTexture <http://www.opengl.org/discussion&#95;boards/showthread.php/174926-when-to-use-glActiveTexture>`_  %IF{" '' = '' " then="" else="- "}%
   * `GLFW is a free, Open Source, multi-platform library for opening a window, creating an OpenGL context and managing input. It is easy to integrate into existing applications and does not lay claim to the main loop.  GLFW is written in C and has native support for Windows, Mac OS X and many Unix-like systems using the X Window System, such as Linux and FreeBSD. <http://www.glfw.org/>`_  %IF{" '' = '' " then="" else="- "}%
   * `A Framework for Dynamic Deformation of Uniform Elastic Two-Layer 2D and 3D Objects in OpenGL <http://delivery.acm.org/10.1145/1380000/1370282/p145-song.pdf?ip&#61;203.18.50.4&#38;acc&#61;ACTIVE&#37;20SERVICE&#38;CFID&#61;278622627&#38;CFTOKEN&#61;15147707&#38;&#95;&#95;acm&#95;&#95;&#61;1361277579&#95;4b1a96414b5cc49cd8dbec7418a5e96e>`_  看看这篇文章。开始了解图像处理现状。
   * `A Framework for an R to OpenGL Interface for Interactive 3D graphics <http://www.statoek.wiso.uni-goettingen.de/mitarbeiter/ogi/pub/DSC03&#95;RGL.pdf>`_  %IF{" '' = '' " then="" else="- "}%
   * `OpenGL Graphics   in the   Computer Systems Lab <http://www.tjhsst.edu/~dhyatt/superap/opengl.html>`_  %IF{" '' = '' " then="" else="- "}%
   * `OpenGL浅谈 <http://wenku.baidu.com/view/4426e177a417866fb84a8e0b.html>`_  %IF{" 'Opengl入门、浅谈、渲染流水隐喻' = '' " then="" else="- "}%Opengl入门、浅谈、渲染流水隐喻
   * `openGL 的坐标变换 <http://wenku.baidu.com/view/1c3e4a7d27284b73f2425012.html>`_  %IF{" 'openGL 中重要内容之一，要深入研究' = '' " then="" else="- "}%openGL 中重要内容之一，要深入研究
   * `Tiled Map eidtor <http://www.mapeditor.org/>`_  %IF{" '地图制作工具' = '' " then="" else="- "}%地图制作工具
   * `openGL 与GPU的关系 <http://www.builder.com.cn/2008/0703/963021.shtml>`_  %IF{" '' = '' " then="" else="- "}%
   * `openGL 变换数学原理与接口指南 <http://wenku.baidu.com/view/455ed4db76eeaeaad1f330ae.html>`_  %IF{" '' = '' " then="" else="- "}%
   * `显存的前世今生(终极剖析 高手阶进必知) <http://wenku.baidu.com/view/d4ce780f76c66137ee061938.html>`_  %IF{" '' = '' " then="" else="- "}%
   * `CPU显存控制 <http://wenku.baidu.com/view/026a5d7202768e9951e7386d.html>`_  %IF{" '' = '' " then="" else="- "}%
   * `PhysX技术?  游戏物理效果 <http://www.nvidia.cn/object/physx&#95;faq&#95;cn.html>`_  %IF{" '' = '' " then="" else="- "}%
   * `Unity 3D <http://game.ceeger.com/>`_  %IF{" '' = '' " then="" else="- "}%

   * `用PyOpenGL叩开3D的心扉——OpenGL全解析 <http://eyehere.net/2011/learn-opengl-3d-by-pyopengl-1/>`_  %IF{" '' = '' " then="" else="- "}%
   * `OpenGLContext Python tutorials <http://pyopengl.sourceforge.net/context/tutorials/shader&#95;intro.xhtml>`_  %IF{" 'need virutalen study first' = '' " then="" else="- "}%need virutalen study first
   * `glTexGend, glTexGenf, glTexGeni, glTexGendv, glTexGenfv, glTexGeniv <http://msdn.microsoft.com/zh-cn/library/ms537230(v&#61;VS.85).aspx>`_  %IF{" 'MSDN' = '' " then="" else="- "}%MSDN
   * `回顾图形学课程来对照各个函数 <http://blog.csdn.net/wu4long/article/details/6126408>`_  %IF{" '' = '' " then="" else="- "}%
   * `GL学习笔记(2) - 终于搞明白gluPerspective和gluLookAt的关系了 <http://hi.baidu.com/korndorben/item/76c5020453f18212cc34ea90>`_  %IF{" '' = '' " then="" else="- "}%
   * `GLFW is a free, Open Source, multi-platform library for opening a window, creating an OpenGL context and managing input. <http://www.glfw.org/faq.html>`_  %IF{" '' = '' " then="" else="- "}%
   * `OCC是一个免费、开源的二维和三维建模引擎SDK（遵循LGPL开源协议）。适合开发CAD/CAE/CAM程序 <http://www.cppblog.com/mythma/archive/2009/05/26/85770.html>`_  %IF{" '' = '' " then="" else="- "}%
   * `The People Behind Mesa 3D <http://www.phoronix.com/scan.php?page&#61;article&#38;item&#61;mesa&#95;contributors&#38;num&#61;1>`_  %IF{" '' = '' " then="" else="- "}%
   * `OpenGL点阵字体绘制终极解决方案!  <http://blog.csdn.net/wm111/article/details/7665755>`_  %IF{" '' = '' " then="" else="- "}%
   * `appletparadise <http://appletparadise.com/>`_  %IF{" '' = '' " then="" else="- "}%
   * `opengl 的矩阵变换 <http://caobeixingqiu.is-programmer.com/posts/16488.html>`_  %IF{" '' = '' " then="" else="- "}%
   * `glew <http://glew.sourceforge.net/>`_  %IF{" ' is a cross-platform open-source C/C++ extension loading library. GLEW provides efficient run-time mechanisms for determining which OpenGL extensions are supported on the target platform. ' = '' " then="" else="- "}% is a cross-platform open-source C/C++ extension loading library. GLEW provides efficient run-time mechanisms for determining which OpenGL extensions are supported on the target platform. 
   * `很好的视频教程 <http://www.videotutorialsrock.com/opengl&#95;tutorial/color/video.php>`_  %IF{" '' = '' " then="" else="- "}%
   * `Cg vs cuda <https://docs.google.com/viewer?url&#61;http://elrond.informatik.tu-freiberg.de/papers/WorldComp2012/PDP2936.pdf>`_  %IF{" '' = '' " then="" else="- "}%
   * `HDR渲染器的实现(基于OpenGL) <http://dev.gameres.com/Program/Visual/3D/HDRTutorial/HDRTutorial.htm>`_  http://www.openexr.com/ openEXR 这个是HDR的标准格式。
   * `这上面的技术一个一个去试去学 <https://wiki.nvidia.com/engwiki/index.php/Devtech/Android#Releasing.2C&#95;Branches&#95;and&#95;Labels>`_  %IF{" '' = '' " then="" else="- "}%
   * `graphicall   一个比较全的网站 <http://www.graphicall.org/>`_  %IF{" '' = '' " then="" else="- "}%
   * `YafaRay is a free open-source raytracing engine. <http://www.yafaray.org/>`_  %IF{" '' = '' " then="" else="- "}%
   * `pyopengl <http://pyopengl.sourceforge.net/>`_  %IF{" '' = '' " then="" else="- "}%
   * `jet game <http://jet.ro/>`_  %IF{" '' = '' " then="" else="- "}%
   * `红宝书的例子 <http://www.opengl-redbook.com/>`_  %IF{" '' = '' " then="" else="- "}%
   * `Instanced Rendering <http://ogldev.atspace.co.uk/www/tutorial33/tutorial33.html>`_  %IF{" '这个这两天要看一下' = '' " then="" else="- "}%这个这两天要看一下
   * `Xgl <http://zh.wikipedia.org/wiki/Xgl>`_  %IF{" '3d 桌面' = '' " then="" else="- "}%3d 桌面
   * `opengl FAQ <http://www.opengl.org/archives/resources/faq/technical/texture.htm#text0010>`_  %IF{" '' = '' " then="" else="- "}%
   * `where-is-glswapbuffers-defined <http://stackoverflow.com/questions/15753840/where-is-glswapbuffers-defined>`_  %IF{" '这个是不是由ogl来定义的，而是framework自己定义的。' = '' " then="" else="- "}%这个是不是由ogl来定义的，而是framework自己定义的。
   * `opengl中VAO,VBO,IBO用法小结 <http://www.cnblogs.com/eggine/archive/2012/12/07/2807245.html>`_  %IF{" '' = '' " then="" else="- "}%
   * `opengl Momory Model <https://www.opengl.org/wiki/Memory&#95;Model>`_  %IF{" '' = '' " then="" else="- "}%
   * `opengl api manual <http://www.opengl.org/sdk/docs/man/>`_  %IF{" '' = '' " then="" else="- "}%
   * `AB是一家?VAO与VBO <http://www.zwqxin.com/archives/opengl/vao-and-vbo-stuff.html>`_  %IF{" '这个总结一下' = '' " then="" else="- "}%这个总结一下


Thinking
========


openGL 的图元是利用有序的顶点来进行描述的。并且用BEGIN与END来进行标识。 并且核心库只有115个函数，并且只有能处理点，线，多边形。一般情况下，都采用三角形来模拟各种图形。

-- Main.GangweiLi - 19 Feb 2013





.. code::

    for (each photon)
      for (each triangle)
        for (each pixel)
          draw;

    opengl2.0 model
    for (each triangle)
      for (each light)
        for (each pixel)
          draw;




http://bbs.csdn.net/topics/330163685
.. code:: cpp

    #include "GL/glut.h"
    #include "cutil.h"
     
    #define W    640
    #define H    400
    float h_a[W], *d_a;
     
    __global__ void do_cuda(float *a) {
        int inx=blockIdx.x*blockDim.x+threadIdx.x;
        a[inx]=sinf(inx*0.1);
    }
     
    void display() {
        glClear(GL_COLOR_BUFFER_BIT|GL_DEPTH_BUFFER_BIT);
        glLoadIdentity();
        glBegin(GL_POINTS);
        glColor3f(1.0f, 1.0f, 1.0f);
        for(int i=0; i<W; i++) glVertex2f((float)(i*2.0/W-1), 0.2f*h_a[i]);
        glEnd();
        glFinish();
    }
     
    int main(int argc, char **argv) {
        cudaMalloc((void**)&d_a, sizeof(h_a));
        do_cuda<<<20,32>>>(d_a);
        cudaMemcpy(h_a, d_a, sizeof(h_a), cudaMemcpyDeviceToHost);
        glutInit(&argc, argv);
        glutInitDisplayMode(GLUT_RGBA);
        glutInitWindowSize(W, H);
        glutCreateWindow("f=sin(x)");
        glutDisplayFunc(display);
        glutMainLoop();
    }


*如何旋转*
无非加一个角度，每一个重画时，转动一定的角度。无非加个变量，并且指定这个变量的变化规律。每一个变化都会有一个范围，不会是一个无限值。并且各个变量都有依赖关系。就是多顶式的问题。就是实现动画的原理。并且也是仿真的原理。

-- Main.GangweiLi - 25 May 2013


对于linux framebuffer的操作，都是可以直接在/dev/fb0 或者/dev/fb1直接操作的。例如使用dd 直接来读写。http://baike.baidu.com/view/2115202.htm 对于`OpenGL中的Alpha测试,深度测试,模板测试,裁减测试 <http://blog.csdn.net/crazyjumper/article/details/1968567>`_  如何指定模板值，是这样的，OPENGL只能指定状态，有些操作是直接操作，有些设置操作，例如这个顶点或者图元的时候，你可以设置一个模板值，这时候，它会在每一个点操作的时候，去根据你的设置来做设置模板值，其实就像蒙板一样，同样你可以直接采用一个图片来当做模板。

-- Main.GangweiLi - 26 May 2013


*T&L*
是在光栅化之前的。

-- Main.GangweiLi - 28 May 2013


CG脚本是可以实时编译，也可以预编译的。在看到游戏那个load进度条就是在加载资源与做这个事情。最基本上输入是坐标，其他还有颜色，以及纹理坐标等法向。

-- Main.GangweiLi - 29 May 2013

*整个图形的绘制*

#. 应用程序阶段，主要是和CPU，内存打交道，诸如碰撞检测，`场景图建立 <SceneGraph.rst>`_ ，空间八叉树更新，视锥裁剪。  当然这些计算也可以CUDA，openCL来做。事实也是样的，显卡可以分时复用的，你是感觉不到的。例如physX 现在就是CUDA来实现了其中很大一部分。
#. 几何阶段，顶点坐标，法向量，纹理坐标。纹理。光照计算属于几何阶段，因为光照计算涉及视点，光源，和物体的世界坐标。这个应该是老式的坐法了吧。因为新式的模型，光照是可以逐像素计算的。雾化以及涉及物体透明 度的计算属于光栅阶段，国为这两种计算需要深度值信息。而深度值需要先在几何计算算好传递给光栅。
#. 光栅阶段，这时候就是基于图象了。例如color buffer,frame buffer.


法向量从OBject space 到world psace的转换是顶点转换的矩阵的转置。

-- Main.GangweiLi - 01 Jun 2013


Z值的计算并不是线性的欧氏空间，并且是非线性的，如果搞错了就会出现交叠的现象。

-- Main.GangweiLi - 01 Jun 2013


并且深度模板不是单独存在的，而是放在stencil 模板中。

-- Main.GangweiLi - 01 Jun 2013


OPENGL最具有灵活性两部分，顶点渲染，与fragrament的渲染都是以回调的方式实现的，并且可以调用OPENGL的自然的一些计算函数，特别是４阶以下矩阵运算。因为OPENGL的运算一般不超过四维，要不要降维了。对于回调函数的输入，一般是坐标值，颜色值，以及其他一些属性。各种效果都可以此实现。

-- Main.GangweiLi - 01 Jun 2013


*`glFinish,glFlush 同步 <http://blog.csdn.net/xiajun07061225/article/details/7756187>`_  eglWaitClient* 其中一个用途就是调试，一般情况下尽量不使用，会造成性能的下降。

-- Main.GangweiLi - 13 Aug 2013


*坐标定义*
opengl默认是屏幕的中心为坐标原点，x 由左到右，y由上到下。z向人为正。应该是左手左则。但是好多地方说是右手法则。我但是认为是左手法则。
`变换坐标系的函数——glLoadIdentity()与glTranslatef()和glRotatef() <http://hi.baidu.com/zfrog/item/25035520b02ade8e6e2cc3b1>`_  
这些改变坐标原点位置，如果物体本身没有运动的话，其世界坐标系是不会变的，变的只是我们观察点。

-- Main.GangweiLi - 19 Aug 2013


*曲面细分*
就是把原来固定的图元也解放了，至于是几个顶点可以做一个图元，这样可以自己决定了。这个开放之后，就简单了，例如我可以根据3个点做抛物线，或者是什么，完全有自己决定了。

-- Main.GangweiLi - 12 Sep 2013


*glswapBuffters*现在使用了双buffer直接转换了，如果计算速度更块呢，可以把计算好的buffer存储起来，存储成一个queue,这样每一次的刷新的时候，先把copy显存中，在熔接机的实现中，不就是采用的虚拟缓存的机制这样最后合成图像的。现在完全可以实现一个队列这样。可以采用先进先出的方法。只要在swapBuffer上面封装一层，当然这个只有在运算速度大大高于显示速度的时候采用，还是有意义的。

-- Main.GangweiLi - 13 Jan 2014


*glGetString* 来获取显卡的各种信息，如何得到配置，就是通过这个API。并且如果要想使用扩展也要通过glGetString来进行查询，Work.HDRSample就是用的NV扩展从显存读数据到内存中，但是扩展

OPENGL extension
----------------

要使用一个OpenGL扩展，首先必须检查显卡是否支持这个扩展，以下代码可以获取一个显卡支持的的OpenGL扩展::
  
   const char *str = glGetString( GL_EXTENSIONS );

函数返回一个字符串指针，这个字符串就是显卡所支持的所有扩展的扩展名，不同的扩展名之间用空格隔开，形如::

   "GL_ARB_imaging GL_ARB_multitexture GL_ARB_point_parameters ……"

OpenGL扩展往往都会新增一些函数，在Windows平台上，这些函数不是通过.lib库连接到程序里的，而要在运行时动态获得函数的指针。我们以GL_ARB_point_parameters扩展为例看看怎么获得函数指针。

首先要定义函数指针类型，

.. code:: cpp

    typedef void (APIENTRY * PFNGLPOINTPARAMETERFARBPROC)(GLenum pname,
    GLfloat param);
    typedef void (APIENTRY * PFNGLPOINTPARAMETERFVARBPROC)(GLenum pname,
    const GLfloat *params);

这个工作SGI已经为我们做好，它提供了一个头文件 glext.h ，里面有目前绝大多数扩展的常量和函数指针定义，下载下来放到编译器的include/GL文件夹下面，然后在程序里面加上::

    #include <GL/glext.h>

就可以在程序中使用常量和函数指针类型了。

然后要定义函数指针::

    PFNGLPOINTPARAMETERFARBPROC glPointParameterfARB;
    PFNGLPOINTPARAMETERFVARBPROC glPointParameterfvARB;

再检查显卡是否支持GL_ARB_point_parameters扩展，其中isExtensionSupported是自定义的一个函数，就是在glGetString( GL_EXTENSIONS )返回的字符串里查找是否存在指定的扩展名::

    int hasPointParams = isExtensionSupported("GL_ARB_point_parameters");

如果支持，就可以用:cpp:function:`wglGetProcAddress` 函数获取扩展函数的指针::

    if (hasPointParams) 
    {
    glPointParameterfARB = (PFNGLPOINTPARAMETERFARBPROC）\
    wglGetProcAddress( "glPointParameterfEXT" );
    glPointParameterfvARB = (PFNGLPOINTPARAMETERFVARBPROC) \
    wglGetProcAddress( "glPointParameterfvEXT" );
    }

最后就可以在程序里使用扩展函数::

    if (hasPointParams)
    {
    static GLfloat quadratic[3] = { 0.25, 0.0, 1/60.0 };
    glPointParameterfvARB(GL_DISTANCE_ATTENUATION_ARB, quadratic);
    glPointParameterfARB(GL_POINT_FADE_THRESHOLD_SIZE_ARB, 1.0);
    }

另外，下面代码说明如何访问扩展函数：（资料来源于csdn知识库）

调用:cpp:func:`wglGetProcAddress` 函数访问一个不在标准OpenGL库中的扩展函数。如果该扩展函数存在当前的执行(implementation)中，那么:cpp:func:`wglGetProcAddress` 返回一个用来访问该函数的函数指针。否则，`wglGetProcAddress` 返回NULL.

例如，要访问glAddSwapHintRectWIN扩展函数，如下调用wglGetProcAddress::

    // Get a pointer to the extension function.
    typedef void (WINAPI *FNSWAPHINT)(GLint, GLint, GLsizei, GLsizei);
    fnSwapHint = (FNSWAPHINT)wglGetProcAddress("glAddSwapHintRectWIN");

    // Actual call to glAddSwapHintRectWIN.
    if (fnSwapHint != NULL)
    (*fnSwapHint)(0, 0, 100, 100);




*SwapBuffer* 操作系统自身的功能，只有此时还会更新屏幕。

-- Main.GangweiLi - 03 Mar 2014


glDrawXXX的数据也是可以直接放在内存中，利用glVertexAttribPointer 直接来指定其格式的。在用的时候在往显存传送，所以经常会到一些简单的glDrawXXX会直接使用内存端的数据，特别是画一个对角四边形来触发opengl 来执行。

.. code:: cpp
        checkGlError("glUniformMatrix4fv", "drawSkyBox()");

	const float skyQuadCoords[] = {	-1.0f, -1.0f, -1.0f, 1.0f,
				 	 	 	 	 	 	1.0f, -1.0f, -1.0f, 1.0f,
				 	 	 	 	 	-1.0f,  1.0f, -1.0f, 1.0f,
				 	 	 	 	 	 	1.0f,  1.0f, -1.0f, 1.0f};

	glVertexAttribPointer(m_pSkyShader->m_positionAttrHandle, 4, GL_FLOAT, GL_FALSE, 4*sizeof(float), skyQuadCoords);
	checkGlError("glVertexAttribPointer", "drawSkyBox()");

	glEnableVertexAttribArray(m_pSkyShader->m_positionAttrHandle);
	checkGlError("glEnableVertexAttribArray", "drawSkyBox()");

	glDrawArrays(GL_TRIANGLE_STRIP, 0, 4);
	checkGlError("glDrawArrays", "drawSkyBox()");

	glDisableVertexAttribArray(m_pSkyShader->m_positionAttrHandle);


-- Main.GangweiLi - 04 Mar 2014
