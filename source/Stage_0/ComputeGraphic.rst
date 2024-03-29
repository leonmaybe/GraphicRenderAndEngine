introduction
============

计算机图形研究各种基本图形元素的生成算法，以及图形的T&L 变化，几何变形以及光照算法，各种光线跟踪算法。科学计算的可视化。例如体纹理。 例如一个内体从内外到外的结构都数字化。并且热起来的3D打印。也是体纹理的一种体现吧。

图象处理是景物或图象的分析技术，它所研究的是计算机图形学的逆过程，图象增加、模式识别、景物分析、计算机视觉等，并研究如何从图象中提取二维或三维物体的模型。

`计算机视觉 <http://wenku.baidu.com/view/bb1394f804a1b0717ed5dd03.html>`_  是图处处理的延伸，那另一个问题，如何能让计算机根据你的描述画出一个图形呢。这就要求不但能够识别出来各种物体，而且还要能够画出物体。

.. csv-table:: 

   `CGI <http://en.wikipedia.org/wiki/Computer-generated_imagery>`_  , 
   `GKS  <http://en.wikipedia.org/wiki/Graphical_Kernel_System>`_  , `GKS - The Leader in 3D Scanning & 3D Measurement <http://www.gks.com/‎>`_  , 这个要去看一下,
   `PHIGS  <http://phigs.webs.com/>`_  , 这个与opengl  有什么区别，好像比opengl更高一层的API ,
   `CGM <http://en.wikipedia.org/wiki/Computer_Graphics_Metafile>`_  ,Computer_Graphics_Metafile dds 是3D纹理贴图，最终的效果是不需要贴图，就是直接物理模型的仿真，这个模型不光有外型，还是有内体，并且材质的各种不同。这个就要求，技术本身要进一步发展，如何为物理整体性质建模，例如指定物体内部各个点的模型。另外一点那就是计算能力的进一步提升。同时在建模的时候物体每一点都是有意义的。现在已经有了，现在这个叫做体绘制，volume rendering.


计算机图形是从理论上研究各种计算的原理，你可以根据这些理论去独立设计各种应用，而opengl则是提供了一种最佳实践，并且把应用与下层与数学计算的部分进行了隔离。下层的数学计算，都由驱动层给你做好了。并且也建立模块化的框架。X window只是复柜体通信与管理。与之相关的一绘图也是可以做的。不过X Windows 主要是窗体的管理。并且在其之上还有窗口管理器。应用程序GUI的开发都是基于XWindows的。但是如果是内容的是图形的话，可能就要用到opengl之类的东西。也就是窗体内部的内容是由XWindows,以及各种消息处理也都是由Xwindows来作的。例如在opengl中glut这些库都是基于Xwindows对openGL进行管理的库。并且opengl采用框架与XWindows类似，但是又相反。都是C/S模式。C/S模式是相反。并且在其API之上是基本上感觉不到这些交互的存在的，这一点有点类似于GUI的开发。

计算机图形学
============


`计算机图形学课件 <http://wenku.baidu.com/view/08bd282ecfc789eb172dc8c7.html>`_  主要是研究各种图形的生成算法。现在最基本图像生成算法已经很成熟了。例如直线的生成算法。圆与椭圆生成算法以及各种曲线的生成算法。对于算法来说最重要那就是性能。对于计算机来说最计算量小并且尽可能只用加法与移位这样操作，以及尽可能减少浮点数的运算。这样看来，以及所学的各种曲线方程。不同的表示形式的意义。因为不同的方程对于不同的硬件实现效率也是不一样的。例如对于计算机来说，参数议程与极坐标方程相对来说是最方便的。
同时基本图形的变换。
点线面体的是可以相互转化的。图形的表示一般都是顶点与边来表示。边是连接顶点的。所以不管是线面体都是点的集合。所以可以用有序的点集来表示。点可以坐标来表示。所以点集可成矩阵的的形式。图形的各种变换都是通过坐标变换来得到，而坐标变换都是可以通过矩阵运算来进行的。各种平面图形与立体图形的数据学模型那就是矩阵。 

各种图形用顶点来表示，顶点集合变成矩阵，所有图形变换，通过顶点进行矩阵运算，得到新的顶点，然后再进行顺序连线就得到了新的图形。有了顶点，就可以计算出边线。边线不需要存储，可以通过计算得到。如果反过来呢。如果通过一个有序的点集合，来判断出是一个什么图形，或者计算出其顶点。有什么定律呢。应该可以用梯步或者二阶导来找出拐点来定义顶点。对于图像来说，点的坐标，以及点的各种属性，是不同的。图像处理更多的是基于点上的属性，例如颜色。而图形更多是基于点的坐标。那么二者是否可以关联起来呢。例如通过颜色找到边线的点的集合，然后根据这些点的坐标来判断图的形状。
OPENGL 就是采用的顶点来表示，边线通过计算来得到，什么时候来计算边线，应该是在光栅化的时候吧，并且连线是采用的线段，还是光滑曲线，这就用到直线与曲线的拟合了。应该就是光栅化的的时候来做这些操作。
 有个朋友提出这样的方法:
   首先将待测曲线和已知曲线重心与坐标原点重合，过原点作一条直线相交于两条曲线，计算交点梯度方向，将两个交点梯度方向相减得A1，做足够多的直线，得到A2,...An的值，A1-An做和得S1。对每条已知曲线重复上述操作得到S2...Sn，min{Sn}可以被认为是最接近的曲线。这个不正是最小二乘法嘛。

*图形的变换*  几何变换，投影变换,颜色渐变的等等。有了这些变换，动画就很容易了，特体的各个顶点记录下来，所谓的运动，说白了就是这些变换运算，向前平动，不就相当于平移，转身不就相于旋转吗。但是对于复杂物体来说，例如人，做一个转身动作，身体的各个部分的运动方便是不一切的。因此对于这种复杂的特体是分步分块进行计算，然后合成呢。

opengl也就是这样的干的，每一个BEGIN/END之间都是一个图园，但是的图元指的一组小图形，它们可以具有相同形变性质，例如一个的转身动作是不一样的。每一个部分是一个图元，并且各个图元之间是有相对关系的。同样一个转身每一个图元的转换方程是不一样的，但是它们之间又是有联系的。图元与纹理之间映射，据说是手工一一指定映射的。纹理也产生形变。
要能够各种变换与各种矩阵的性质关联起来了，例如对角阵，对应的那就是比例变换。，直接使用2*2的矩阵变换，就要基本变换矩阵，还有一个通用的3*3的组合变换矩阵。线对称变换，任意点旋转矩阵。三维基本变换利用齐次坐标为4*4的矩阵。OPENGL不正是这样干的嘛。坐标也是采用齐次坐标。
`Model View Matrix，Projection Matrix  <http://blog.sina.com.cn/s/blog_43699c540101o1wu.html>`_ 投影要降维了。
   
.. ::
 
   变换的数学模型矩阵乘法 A*T=B
    a     b
    c     d 
   
   ###
   a b c p
   d e f q
   h i j r
   h m n s
   --------------------------
   a b c d e f h i j 产生比例，对称，错切，旋转等等基本换。
   k m n  产生平移
   p q  r   产生透视变换
   s 产生全比较变换
   



.. csv-table:: 

   几何变换 , 比例   ,   B = C =0 , a=d  ,
     对称    , X轴对称   ,   b=c=0,a=1,d=-1 ,
      ^        ,  Y轴对称  ,   b=c=0,a=-1,d=1 ,
     ^         ,   原点对称 ,  b=c=0, a=d=-1 ,
     ^         , 45度线对称 , a=d=0,b=c=1 ,
     ^         , -45度线对称 , a=d=0,b=c=-1 ,
   错切     , 沿X轴错切  ,   a=d=1,b=0 ,
     ^        , 沿Y轴错切  ,   a=d=1,c=0 ,
    旋转， ,   原点旋转  ,   a=d=con,b=c=sin ,
     ^        ,  平移，投影 ｜就要3*2的矩阵了，与齐次坐标 ,

什么是投影变换，为什么要投影?
投影是也就是降维，例如把三维降到二维。例如相机就是透视的投影的效果。 并且都有对应的变换矩阵的。投影又分为两大类，
#. 透视投影  投影中心到投影平面的之间的距离是有限的，所以要明确地给出投影中心的位置，各种来点，聚结点，最终都是通过求极根来得到极根值。 并且OPENGL的透视投影图，先透视，后正投影变换，正投影变换矩阵是固定的。
   #. 一点透视 
   #. 二点透视
   #. 三点透视
#. 平行投影  投影中心与投影面之间的距离是无限的，我们平时用到三视图，就是这种。
   #. 正投影，三视图，正轴测（等轴测，正二测，正三测）
   #. 斜投影，斜等测，斜二测
例如如何球体转化到平面上，也是利用投影。投影要么是形状不变，要么是面积不变。用到算法有。
#. `Lambert_conformal_conic_projection <http://en.wikipedia.org/wiki/Lambert_conformal_conic_projection>`_ 
#. `Miller_cylindrical_projection <https://en.wikipedia.org/wiki/Miller_cylindrical_projection>`_ 

2. 坐标系
在三维变换系中，我们可以采用`右手坐标系 <http://wenku.baidu.com/view/4c7d4132f111f18583d05acc.html>`_ ，或者左手坐标系。
视窗的变换，一个是坐标系的变换，另一个那就是坐标原点的变换。不同参考点，会不同的视窗。其实就是投影的问题。我眼中你，你眼中他等等。世界坐系，设备坐标系。绝对坐标，例如地球经纬坐标。就是一个绝对坐标。一个系统中要定义一个绝对参考点。为了标准化与设备无关。就义的坐标必值，0，1之间自己按照比例去换算。既然就有一个浮点数的计算了。 对于最初的坐标都我们是定义的世界坐标系。我们在定义坐标的时候，就是定义一个参考值，例如我们在建模的时候，把实际物体的1米当做一个坐标单位，然后在各个计算搜索百分比进行换算，最常见的坐标那就是-1,1然后就是利用百分比在。所以浮点数精度要比较高。
各种图形的交并补关系如何计算。 `凸多边形的交、并求解算法 <http://wenku.baidu.com/view/6d9d520002020740be1e9bc5.html>`_ , `Weiler-Atherton任意多边形裁剪算法 <http://wenku.baidu.com/view/6d9d520002020740be1e9bc5.html>`_ 

+ 多边形以及多边形的填充算法
============================


`扫描线算法 <http://blog.csdn.net/orbit/article/details/7368996>`_ 扫描线算法适合对矢量图形进行区域填充，只需要直到多边形区域的几何位置，不需要指定种子点，适合计算机自动进行图形处理的场合使用，比如电脑游戏和三维CAD软件的渲染等等。本算法的核心是求交点，根据顶点算出边线的点集合。然后，逐行开始，交点必定是偶数个，并且相邻出现，起始点只要不在，从一个方向过去，另一个到另一个点之间都指定在。,主要扫描线的性质。  

 `递归种子算法 <http://blog.csdn.net/orbit/article/details/7323090>`_ 种子填充算法需要给出图像数据的区域，以及区域内的一个点，这种算法比较适合人机交互方式进行的图像填充操作，不适合计算机自动处理和判断填色。 也是顶点先算出边线，然后再向再向四面八方进行搜索，只要不在边界上肯定是多边形内部。所有的判断条件，无非先算出一个点集合，然后看个点是否在这里边。

4连通边界算法有两种

8连通边界算法，泛填充算法
点是否在内部的检验
用单根扫描线
区域的连贯性
利用两根扫描线法，两个梯形底是两个扫描线，腰就是多边形的边。

多边形碰撞算法 也是基于填充算法来的，可以通过google学术进行搜索最新算法。而不必去研究的老的算法。

同时纹理贴图应该也基于这些来的。看看是如何映射的。

裁剪算法
========

   
.. ::
 
   1、从定义的场景中抽取出用于观察的部分；
   2、在三维视图中标识出可见面；
   3、防止线段或对象的边界混淆；
   4、用实体造型来创建对象；
   5、显示多窗口的环境；
   6、允许选择图形的一部分来进行拷贝，移动和删除。 
   

线段的裁剪

.. csv-table:: 

   编码裁减算法  ,编码计算端点坐标和窗口边界之差。然后利用符号进行判定。 ,
   矢量裁减算法 , 也有很多种，可以去搜索一下。｜
   中点分割裁剪算法 ,

多边形裁剪算法，首先要分析有多少种情况，再然后找方法：
逐边裁减算法,新由顶点计算出边线，再由边线的邻接点，重新计算出新的边界线。然后画出。
边界裁剪算法，

曲线
====

规划曲线可以用方程描述的，这一类最容易，另外一种那就是通过样条插值，多样式曲线。曲线方程在画的时候，我们一般都转化为参数方程。如果方程不能转化为参数方程。那就没有办法画了。看到一个图形，第一个要想的是不是有现在曲线方程来表示。另一种是不是可以无限元法来表示。
星条线是有一个方程的，是不可以看GIMP各种中滤境实现原理。

型值，也就是给出一个特殊点，或者最小量的点，求方程。其实是解方程的过程。根据方程的元数来决定需要多个型值点。 所谓控制点，例如圆心等曲线曲身不通过这个点，这个些点能够控制曲线特性。就相当于矩阵方程的特殊值。

曲线就要知道切线，法线以及曲率。在画的时候就会有插值，逼近，和拟合。测量曲线本身连续性与光滑性，可以利用曲线的多阶数来描述这些特性。也就是所有的东西，都要有数学的描述。

曲线的加权合成。知道了n个点内插值的，例如抛物线每三个连续点就可以可确定一个抛物线。但是一个连续的过程，如何可证光滑，就这样有一个光滑的问题。所以在共同部分都要加权。


曲线实现方式与直线是一样的，都是要通过方程来进行计算其值的。对于有规划曲线与直线是一样的。都是通过计算机其点来得到。但是大部分的曲线我们还不能得到方程，只能来拟合了。利用有限元法，并尽可能保证曲线的数学特性，尽可能需要少的点。这样的元曲线，可以基本曲线来做，第一种那就是线段，抛物线，双曲线，贝赛尔曲线。由其贝赛尔曲线是一个无穷极数，可以实现无限的逼近。其实采用无穷极数的方法，也可以采用正弦曲线，以及Sinc函数曲线来进行拟合。关键是各个函数的高阶特性。
`贝塞尔曲线 <http://blog.csdn.net/killwd/article/details/1460478>`_  有各种好玩的特性，就像皮筋效应。 原理见`此 <http://zh.wikipedia.org/wiki/%E8%B2%9D%E8%8C%B2%E6%9B%B2%E7%B7%9A>`_ ,但是它有一个缺点，那就是牵一发而动全身，所以很难控制。所以也就产生B样条函数，这样可以局部调整，同时也又保持前者各种优点。在屏保中，可以就是利用前者实现各种中优美的曲线。




