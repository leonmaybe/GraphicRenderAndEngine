introduction
============

今天才算是对分形的更进一步认识，分形下面也细分很多种，以前所知道只是一个数学分形，后面更加复杂的随机分形，以及自仿射分形，多重分形。如何全用分形。其中一个那就是如何度量它，自然也就了下面那些方法。如何使用分形呢，还是找到其规律。例如植物生长的分形主要是L-SYSTEM来实现，不同植物所需要pattern是不样的。那就需要根据观察，来设计其pattern. 可以使用L-Studio或者vlab来做实验。在这个过程中有随机过程。但是每一种植物随机过程也可以也是不一样的。

对于地形的生成，常用中位连接法，再加上高度图，当然各种地形的生成也是不同的规律，这里主要用到是不动点理论，不动点可以通过傅里变换的谱分析来得其不动点信息，然后再根据不动点周围的地形信息不同的生成模型来进行分形生成。例如M集与J集就有点像河流的分布。

例如树就可以用分形树来生成。

对于flame,更适合用迭代模型，现在研究一下aphosyis 去做一下。

对于建筑可以 strcture synth 会更合适。

当然还里还有其他类型生成方法，例如context free 的cfdg 生成方法。现在明白了在这些做图的过程，所有设置都是基于当前坐标的。所谓的使用堆栈，都是一个形象化的一个说法，其实就像M 语言的函数定义一样，对于树形那就是可以深度广度同时控制。例如画了一个左子叶，然后回来画右子叶，那就是需要堆栈来进行回退了。


分形压缩，已经有好多研究了，其中一个就是利用J集当做字典来模拟各种图形，得到其参数，以后只要传输这些参数就可以了。关键是一个速度与函数库的问题。因为各种图像终究都是可以用无穷极数去拟合的，例如三次样条等。如何全用这些。

defination and fundmental concept
=================================

维度，测度的维度，用点来测度线是无限长的。同样用线来测面也是无限大的。测量必须同维。
没有特定的长度与但是自相似

.. csv-table:: 

   自相似维数 ,设A是一个有界子集，可以分成N个相等的且与A相似的部分，则称A为自相似集。如果每部分与A的相似比为r=(1/N)^(1/D)，则称D为A的自相似维数，记为dimsA=D ,
   Hausdoff 维数 ,  利用度量空间距离来定义 ,
   盒维数 , 对于给定的对象， 用很小的单元块ε充填它，最后数一数所使用的小单元数目N。改变ε的大小，自然会得到不同的N值，ε越小，得到的N显然越大 ，ε越大，得到的N就越小。将测到的结果在“双对数”坐标纸上标出来， 往往会得到一条直线，此直线的斜率的绝对值就是对象的维数 ,

d≥dimbA≥dimhd

.. csv-table:: 

   `泛函数分析 <FunctionAnalysis>`_ 

:Koch-Kurve
===========

三分，去除中间三分之一，并在其上构造三角形。
`python实现 <http://stackoverflow.com/questions/932222/implementing-the-koch-curve>`_  利用状态机实现。只要初始常度，并且迭代次数知道之后每个小边长知道了，然后就可以要根据步长进行计算下一个值，也可以直接使用矩阵变换直接求。利用opengl的矩阵直接来求。

*实现* 不断的细分，是不是用树形来存储其结构。但是只有不动点需要存储，其他的点不需要的。因为它是被替换掉的。 

对于`雪花的blender 实现 <https://github.com/Tlousky/blender_scripts/wiki/Snowflake-Generator>`_ ，采用的是不动点，然后从点到线的扩展。而在这里采用不断的细分的方法来实现的。对于每一条边进行三分，然后再对其中间两点再细分，找到不动点，然后计算移动点。而连线是在细分的过程继承而来的。
这里的关键是blender中点线面结构是如何可存储的，应该是一个链表，这样就会方便的进行插入计算与细分。如果插入的位置与索引值是一一对应的，我们就可以通过二分直接到中间值，而不是实现中采用集合运算去求不同动点。因为线就是点的集合，然后通过集合运算来得连接点。

对于blender的由点，线面的都有一个是否已经被选中的状态。我们是可以代码直接控制每一个点的状态。并且基本模式就是选中对象然后进行操作。编辑模式，选中模式都是可以通过代码控制的。现在终于明白了，如何使用代码来进行选择了，例如正方体如何找到上方的那个面，一个办法，那就是知道其四顶点的坐标，然后bmesh.select_flush()就可以了，可以通过filter其法向值就可以得到其上方面的。然后那个面的状态置成选中状态，然后就可以直接操作了。

对于分形的迭代是可以用CUDA并行来实现的，每一次的迭代的各个边之间是可以独立计算的。例如一个核就是变换。然后循环的计算就行，弄好一个链表结构来进行存储数据就行了。

Mandelbrot set
==============

#. `Mandelbrot set  <http://en.wikipedia.org/wiki/Mandelbrot_set>`_  z=z^2+c  z 代表图像像素点，颜色可以根据收敛速度来计算。初始值如何设置。`Fractals with Python <http://0pointer.de/blog/projects/mandelbrot.html>`_  这里的设置方法是按行与列进行扫描。那么叠代在哪里呢。 逐行逐渐扫描，每一点的坐标为启始值，然后根据利用公式迭代n次，然后根据其绝对值进行来map颜色。 这里利用了mandelbrot的一些性质，`几个定理与推论 <>`_    来是计算等势面，根据原点位置，来计算每一点在哪一个等势面上。并且是迭代多少次的等势面。

康托尔集
========

#. `康托尔集 <http://zh.wikipedia.org/wiki/&#37;E5&#37;BA&#37;B7&#37;E6&#37;89&#37;98&#37;E5&#37;B0&#37;94&#37;E9&#37;9B&#37;86>`_  直接把三分去中间，这样直接操作下去。得到一个线段集合。是含有数字1的小数所对应的点，最终都被删除了!
反过来，可以重新定义康托尔集合D：康托尔集合是由0和1，以及(0,1)内那样的一些点组成的集合，这些点在3进制小数表示中不出现数字1。

cantor 尘
=========

把一个单位正形，将其16个小正形，并去掉12个每一行每列有组只有一个小正形。

涡旋曲线
========

按照自45度旋转，并且长度递减。如何利用图形学的变换来实现呢。长度利用尺度变换来做。把启始点转化为原点。这个最简单利用方程计算出点，然后直接连接就行了，也可以三次曲线来连接，利用blender的线关系直接联接起来就行了，或者采用计算几何网格三角形计动计算出来。

皮亚诺曲线
==========

与正方形的关系。
`Peano 曲线 <http://math.fudan.edu.cn/gdsx/XXYD/Peano%E6%9B%B2%E7%BA%BF.pdf>`_ 

复数娄列
========


对于一个复数数列，无非是四种，收敛，发散，周期，无秩。
zn=Zn-1^2+zc,是不是还可以是别的多项式，复数方程组。
对于julia set 不存在混合的性质，而对于mandelbrot set则存在混合，同时存在，多周期的性质，收敛等。

 `julia&#95;set <http://en.wikipedia.org/wiki/Julia&#95;set>`_   对于某个常量zc使得数列{zn}不发散的初值z0的集合称为Julia集合。进行深入的研究其性质。

当zc为实数，Julia set 关于x,y对称，否则只有关于原点对称了。

`Mandelbrot set <http://en.wikipedia.org/wiki/Mandelbrot_set>`_  赋予初值Z0且zc=z0,并使得zn不发散的z0集合称为mandelbrot集合。

生成办法
========

  * 叠代* 充分利用pthon 的生成器语法来写代码。
#. 实数相空间上的非线性映射，非线性微分方程求解，保守系统准规则斑图。
#. 复域上各式广义的Julia集和Mandelbrot集" 等势面着色“ 方法，球面，双曲面对称图形的动力学生成。
#. IFS，分形插值和小波变换方法。一定要试一下小波变换。
#. 林德梅叶形式语言方法。
   1.DLA模型，CA（ 细胞自动机），SOC(自组织临界性方法）。

如果生成元每段线段的长度相同，我可以利用生成元来做，也就是一个小小的状态机。每一个就代表生成元旋转角度。做几个动作。使用什么样生成元参数，以及迭代次数来保证首尾相接呢。
生成元法，边长相等与不等的做法。
不动点法，如何使用yield来产生。不动点法可以根据方程来求。
利用图形来填充，在不动点上利用基本图形来填充，并且有一个尺度的变换，如何利用图形学的变换来实现呢。

.. csv-table:: 

   分形=原形+生成元+迭代,
   分形=公理+产生式+解释 ,
   分形=初条件+输入+反馈 ,


Sample Study
============

[[FermatSpiral]]  *对于structure synth* 完全可以用blender中array modifier 再加上循环步长都通过一个空对象来各个属性来实现。次数大小或都可以代码控制。  就类似于FermatSpiral一样。
` 场景生成 <SceneryContentGernate>`_  

See also
========



#. `科学家是怎样看待世界的？ <http://songshuhui.net/archives/39756>`_  大自然的各种shape可是利用数学公式来得到
#. `缠论与分形学 <http://www.360doc.com/content/11/0126/09/464381&#95;89066420.shtml>`_  好像还可以用做投资理论
#. `缠论 <http://baike.baidu.com/view/3361133.htm>`_  学一些基本原理利用最新理论
#. `但是很多人利用缠论来忽悠很多不明真相的散户朋友。目前缠论已经沦为骗子忽悠散户的工具，本人作为第一批接触缠论的人 <http://bbs.tianya.cn/post-stocks-929632-1.shtml>`_  做出自己的分析
#. `磁区理论 <http://bbs.10jqka.com.cn/ths,5502,89,1>`_  
#. `fractint <http://www.fractint.org/>`_  最著名的分形软件
#. `用opengl画分形，这样最效率，比用opencv 要看会好很多 <http://www.cnblogs.com/easymind223/archive/2012/07/07/2580835.html>`_  
#. `数学之美之分形——C++及OpenCV实现Julia集和Mandelbrot集绘制 <http://blog.csdn.net/xizhibei/article/details/6664184>`_  不过用CPU速度就远不及GPU了
#. `大自然的分形几何学 PDF <http://blog.sina.com.cn/s/blog&#95;67532f7c01013huy.html>`_  还有代码 
#. `徒手画分形图形，此MM乃神人也！ <http://www.matrix67.com/blog/archives/4000>`_  
#. `超玄幻！冲击人眼球的分形艺术照片 <http://blog.sina.com.cn/s/blog&#95;59a923a60100mwyn.html>`_  
#. `Fractal Science Kit <http://www.fractalsciencekit.com/index.htm>`_  
#. `An Introduction to Fractals <http://paulbourke.net/fractals/fracintro/>`_  
#. `Plasma fractal using Python Image Library (Python recipe) <http://code.activestate.com/recipes/577113-plasma-fractal-using-python-image-library/>`_  
#. `炫丽的3D分形图像艺术 <http://c.chinavisual.com/2009/07/03/c58754/p2.shtml>`_  现在讲的大部分都是2D，3D大部分都是基于opengl来做了。
#. `Fractals with Python <http://0pointer.de/blog/projects/mandelbrot.html>`_  
#. `度量空间 <http://blog.sina.com.cn/s/blog&#95;486c2cbf0102dzqm.html>`_  相对于拓扑空间，`度量空间笔记 <http://cos.name/cn/topic/17106>`_  空间与集合的是等价的，集合的集合叫做集类。
#. `python 实现 去试一试 <http://www.chinavib.com/thread-96990-1-1.html>`_  
#. `经典的分形算法 <http://www.douban.com/note/230496472/>`_  
#. `利用Collatz 来实现分形。 <http://iphyer.github.io/blog/2013/01/13/pythoncollatz/>`_  只要能把最终的颜色与初始值映射起来，就可以做分型。
#. `分形编码 <http://baike.baidu.com/view/5682461.htm>`_  现在这是一个正在发展的方向，可以研究一下，自己的压缩图像想法是一样的
#. `Interactive 3D Graphics <https://www.udacity.com/course/cs291>`_  老李翻墙看下
#. `图形学创世纪：当科学照进影视与生活 <http://tech.sina.com.cn/zl/post/detail/it/2013-10-31/pid&#95;8436547.htm>`_  老李看看
#. `Images should be based upon this Template parameter set (save this file as template.upr), which uses the Barnsley 2 Julia fractal formula.  <http://www.parkenet.org/jp/ufchallenge3.html>`_  

Thinking
========



*ubuntu的软件中心有现成生成软件*包括基本图形，还有自然界的东东。首先通过这些软件获得大量感性的认识，掌握其原理，然后在自己能够编码实现，利用python或者opengl.或者cuda.

-- Main.GangweiLi - 15 Sep 2013


*空间*

在数学中，通常把赋予某些数学结构的集合称为空间。

-- Main.GangweiLi - 16 Sep 2013


*实现*不断的细分，是不是用树形来存储其结构。但是只有不动点需要存储，其他的点不需要的。因为它是被替换掉的。

-- Main.GangweiLi - 17 Sep 2013


自己的思维比较的慢的原因，自己忘了数据结构，遇到这个的问题，利用什么样的数据结构来进行表达最好呢，列表，树，结构体与队列。

-- Main.GangweiLi - 17 Sep 2013


P进制小数在分形里可以直接与图形对应起来。

-- Main.GangweiLi - 17 Sep 2013


*Apophysis* 采用的是IPS，迭代来产生。

-- Main.GangweiLi - 26 Mar 2014


