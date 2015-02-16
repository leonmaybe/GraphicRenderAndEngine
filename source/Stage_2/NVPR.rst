NV PATH Render
**************

直接用GPU的矢量图，并且还支持3D投影，而现在3D只是简单用线性连接，是不是可以用像矢量图这样去走。

这样可以vertex只放置控制点，然后再tellesation 来进行根据分辨率来细分。


https://developer.nvidia.com/nv-path-rendering


基本操作
========

mvto,lineto,close-path,cubic-curve. 如果是这样，如何画人像呢。 这样就解决了分辨的问题。
