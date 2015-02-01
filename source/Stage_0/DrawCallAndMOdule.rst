Draw Call
=========

为什么会存在误差呢，与光栅化呢，是因为点是有大小的，线是有宽度的。点是通用glPointSize来设计的，glLIneWidth来设置的。
图元又分为，Triangle,Strips,Fans。但是图元是可以分组的，例如一个一组图元表示一个模型，例如耳朵是一组图元，嘴是一组图元。每一组图元之间是有相互关系，同时它们又构成一个头部。以前一组图元可以通过glBegin/glEnd来分区分，但是通过什么来区分呢。

多边形是通过三种形与线段合成的。多边形前后两类边设置也可以不同的。例如远小近大。

.. csv-table:: 

   glFrontFace ,
   glCullFace ,

最终如何决定是有公式可以计算的。
整个绘图就是一个流水线机制，就相当于多个队列多进程。
`学一学，VBO <http://www.zwqxin.com/archives/opengl/learn-vbo.html>`_  也就是内存分配的问题，因为GPU上有多种速度的存储设备，所以根据用途不同做优化，opengl的基本buffer分为9大类。

.. csv-table:: 

   GL_ARRAY_BUFFER , glVertexAttribPointer 来指定调用规范，这个规范就是VAO，这个是应用程序到shader的入口。如何构成一个glVertex,以及一个glColor呢，如果你起用生成多个GL_ARRAY_BUFFER,glEnable/DisableVertexAttributeArray 来指定启用哪一个。,
   GL_ELEMENT_ARRAY_BUFFER, 固定结构可以直接glDrawElements ,
   GL_COPY_READ_BUFFER/WRITEBUFFER ,
   GL_DRAW_INDIRECT_BUFFER ,
   GL_PIXEL_PACK_BUFFER ,
   GL_PIXEL_UNPACK_BUFFER,
   GL_TEXTURE_BUFFER,
   GL_TRANSFORM_FEEDBACK_BUFFER,
   GL_UNIFOM_BUFFER , 这个为提供独立于编译的uniform量,


对于如何填充BUFFER，有两种方法glBufferData/glBufferSubData,glCearBufferData/glCearBufferSubData,来操作全部或者部分，结构靠自定义与自我维护。同时也可以两个buffer互copy,glCopyBufferSubData.glGetBufferSubData. 以及glMapBuffer/glUnmapBuffer,无非是解决从CPU 内存 到GPU内存的传递，以及GPU内存数据的传递，例如CPU与GPU进行地址共享。采用最后一种，可以直接从硬件读到显卡里。
VBO本身存储数据，VAO存调用约定，而**indirect，多条命令的循环，**instanced采用一条命令的参数的多次循环。就是自己的那个sendMultiCmd实现不同的参数的不同循环方法，我的方法还可以利用回调产生自定义循环。不过我的没有实现代码块的循环。

最主要的是要知道，你是在操作流水线的哪一步部分。 应用程序到shader,shader之间的数据传递。 `Vertex_Array_Object <http://www.opengl.org/wiki/Vertex_Array_Object#Vertex_Array_Object>`_ 这里讲明白了VBO与VAO的关系，VAO是没有数据本身的，你要用BindVAO之后的操作，都会放在VAO中。 `VAO、VBO介绍 <http://blog.csdn.net/xiajun07061225/article/details/7628146>`_ 

从最初的直接指定顶点画图:glBegind/glEnd再加上glVertex这样一点点来画，到后来的，直接顶点数据用glBufferData/glBufferSubData直接放在数组里，然后用drawArray来画,但是这样有一个问题，那就是数据放的顺序就是画的顺序，为了进一步解放，数据按照自身的方式放好，例如nativeglobe直接按照经纬度来进行，逐行保存下来，画的顺序再用index数组来保存，这里保存是顶点的索引并且其所索引还可以是负值与小数，所以同一个顶点可以使用多次，例如七孔桥问题一样，有些一点必须要经过多次，不然不能全覆盖,顶点都是空间的顶点。对于一个模型，你要想好用什么基本图元画，也就要什么图元来进行分割，那么地图是不是也可以直接用线段来连接呢。

%RED%如果用线段，*那么中间的空白如何处理，是否会自动的识别为图元*。应该不会，那就只能自己画三角形来覆盖了，对了应该可以使用曲面细分来实现。这样可以大大减少顶点数目，曲面细分其实就是利用插值，只要规定了，这条线的起点与终点，并且给出拟合方程，求出中间点的个数，并且采用合适的三角形来进填充。曲面细分应该是这样的。
%ENDCOLOR%

例如同心圆是如何实现，内圈如何调到内圈，这里就要分段的功能，在这里opengl就提供了restartingPrimitves的功能，这一堆是由glPrimitiveRestartIndex来通过顶点索引来指定的，那是不是可以同时多种分段，就像字词句一样。对应的属性的变化，还是那个同心圆多层之间变化，也要同一个函数实现,由instancedRending 来实现了。

最终实现字词句的变化，还要再加上glDrawIndirect*,glMultiDrect这些功能了，例如要多行同时画，就可以使用这些。
%RED%
`二次曲面如何画 <http://blog.csdn.net/lcphoenix/article/details/6717525>`_ 虽然有库，但是原理如何实现的。看来都是这球这种实现方法，进行经纬分割进行的， `贝塞尔曲面 <http://yarin.blog.51cto.com/1130898/381866>`_ 

那需要大的正方形，不需要细节，只需要内部的颜色进行渐变以及圆的颜色渐变如何实现。是要靠顶点来计算吗。或者靠点直接来填充或者线来填充。如果使用点的话，就需要大量计算单元得到点坐标，难道这就是需要 `粒子系统 <http://zh.wikipedia.org/wiki/%E7%B2%92%E5%AD%90%E7%B3%BB%E7%BB%9F>`_ 的原因。 `图形学系列 -- 粒子系统概述及其实现 <http://www.linuxgraphics.cn/graphics/particle_system_overview.html>`_  
%ENDCOLOR%
