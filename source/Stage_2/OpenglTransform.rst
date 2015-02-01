仿射变换affinte transform.
`透视变换 <http://zh.wikipedia.org/wiki/%E9%80%8F%E8%A7%86>`_ 最重要的是，p,q,r点的值的意义。
#. `乱弹纪录IV:Transform Feedback <http://www.zwqxin.com/archives/opengl/talk-about-transform-feedback.html>`_  这个功能，就是在流水线加了下反馈机制，这样处理粒子系统会非常方便，因为能够实现自循环了。 glTransformFeedbackVarying来配置feedback数据。回流的数据是在变顶图元之前顶点数据。所以分两部，在变成图元之前进行截流。
glBeginTransformFeedback/glPauseTransformFeedback/glResumeTransformFeedback/glEndTransformFeedback.
另外那就是把视锥给搞明白了。 还有一点很重要那就是法向与顶点的变换不是直接可以用的`逆矩阵转置 <http://dev.gameres.com/Program/Visual/3D/3Dfaxiangliang.pdf>`_ 
`mvp <http://stackoverflow.com/questions/5550620/the-purpose-of-model-view-projection-matrix>`_   
#. M   maps from an object's local coordinate space to world space
#. v     view from world space to camrera space 类似于glulookat. 也就是定义视锥的大小。
#. P    project from camera to screen.

`why-do-we-move-the-world-instead-of-the-camera <http://gamedev.stackexchange.com/questions/40741/why-do-we-move-the-world-instead-of-the-camera>`_ 
`Using Viewing and Camera Transforms <http://www.opengl.org/archives/resources/faq/technical/viewing.htm>`_ 
`sec09_camera <http://njoubert.com/teaching/cs184_fa08/section/sec09_camera.pdf>`_ 

MVP,M指object coordinates->Eye coordinates-> clips->ndc->window coordinates. object coordinates 每一个物体有一个坐标原点，所有的坐标都是基于此点的坐标，然后原点在世界坐标系里值，来得到物体世界坐标系。然后时入 Eye coordinates. 这个就是视锥变换，眼的位置就是在原点，向-z看（屏幕内），+x为右。就是物体围着眼转，而不是眼围着物体转。那这就有一个自转与公转的问题。

投影与你看的方向是没有关系的为什么，只一点投影的来灭点位置。·


整个过程M V P 最终到了屏幕。 进入Camera视锥就可以进行裁减了，然后来进行图元的组装，这样是为了尽可能减少计算量。并且这个时候坐标已经转换好了，z值就是深度值了。 那个视锥是指的人眼吗。

图元之前的变化流程`详细版 <http://blog.csdn.net/lyx2007825/article/details/8792475>`_  ，` 简化版 <http://blog.csdn.net/blucexi/article/details/1252708>`_ 
`Using Viewing and Camera Transforms, and gluLookAt() <http://edge.globalenglish.com/templates/bin/apps/edge.asp#/study/courses/C6A1A8>`_ 

-- Main.GangweiLi - 02 Sep 2013
