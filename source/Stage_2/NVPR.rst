NV PATH Render
**************

直接用GPU的矢量图，并且还支持3D投影，而现在3D只是简单用线性连接，是不是可以用像矢量图这样去走。

这样可以vertex只放置控制点，然后再tellesation 来进行根据分辨率来细分。


https://developer.nvidia.com/nv-path-rendering

实现原理
========

都是基于pbuffer的，大部分的矢量图的合成都是利用PBuffer来实现的，而不是vertex butter等等。而在平常的ogl应用中，PBuffer的应用却不是很多。·

并且采用 stencil then Stroke的方式。
根据控制点自动算出path,也就是vertex以及如何连接的方式。中间点可以用插值来实现。
这个就类似于vbo 一样。自己指定一个path name  (path object). 当于ogl 前面又多了生成vertex的步骤. 然后就是ogl的东东了。


glPathCommandsNV 就是把关键点指定，并且把拟合方式也指定一下，后面就由nv的 driver来计算了。

就是把 vertex这一个封装成 path object, 你可以用glPathParameterf/iNV来指定属性。
基本操作
========

mvto,lineto,close-path,cubic-curve. 如果是这样，如何画人像呢。 这样就解决了分辨的问题。
