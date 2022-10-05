# 在QT中实现OpenGL的使用  
## 1. 窗口的建立与初始化绑定  
定义类OpenGLWidget，继承public QOpenGLWidget, QOpenGLFunctions_4_5_Core，并在ui中提升为UI组件。  
重载QOpenGLWidget中的三个虚函数，用于实现经典的OpenGL任务：  
~~~
virtual void initializeGL();
virtual void resizeGL(int w, int h);
virtual void paintGL();
~~~
## 2. 顶点数组对象VAO和顶点缓冲对象VBO  
这里的object指的都是GPU中的一块内存，每个内存对象都有不同的作用，但创建、绑定、数据传送等方式都比较类似，通过不同的类型加以区分。  
a. 标准化设备坐标（取值范围 -1.0~1.0） 
b. VAO存储数据的结构定义，VBO则存储不同的缓冲状态类型（可切换）。VAO记录的是一次绘制中所需要的信息，这包括“数据在哪里glBindBuffer”、“数据的格式是怎么样的glVertexAttribPointer”、shader-attribute的location的启用glEnableVertexAttribArray。  
![image](https://user-images.githubusercontent.com/48549786/193452736-9c6cc0b3-05bc-438e-96b2-c0e8368eef75.png)

## 3. 使用QOpenGLShaderProgram类对shader的创建与使用做封装  
