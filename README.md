# OpenGL_Learning  
**理解如何使用OpenGL to make graphics happen，and how it works...**  

推荐说明文档https://docs.gl/  

## P2 SettingUp OpenGL and Creating a window  
设置glfw，一个配合OpenGL 使用的轻量级工具程序库，用于创建并管理窗口和OpenGL 上下文，同时还提供了处理手柄、键盘、鼠标输入的功能。

## P3 Using modern OpenGL in C++
在Windows环境下，获取使用modern OpenGL的函数，实际上取决于你所使用的图形驱动gpu（即获取/linking你现有GPU中的函数）：  
<ol>
<li>driver dll文件</li>  
<li>库中的函数指针</li>  
 </ol>
现有的做到的是，例如glew，提供opengl api的规范（函数声明、常量等等）  
创建一个有效的OpenGL渲染上下文（即glfwMakeContextCurrent(window);）并调用	glewInit() 来初始化扩展入口点  
glGetString(GL_VERSION)获取OpenGL版本和正在使用的驱动——4.6.0 NVIDIA 451.48

## P4 Vertex Buffers and Drawing a Triangle in OpenGL  
Buffer用于存储有待绘制的数据，VRAM；Shader（着色器）是运行在GPU上的程序，是一堆我们可以编写的在GPU上运行的代码，用于图形的绘制。从GPU的角度来讲，它选取一些数据，然后用指定的着色器去绘制，这也是OpenGL作为状态机的工作方式。  
<ol>
<li>创建buffer：glGenBuffers，创建n个buffer，给它一个指向unsigned int的指针；函数会返回我们一个整数id，即标识该缓冲区，可用于后续的buffer选择、绑定等等，这也符合OpenGL的工作方式。</li>   
  这样我们就不需要在while (!glfwWindowShouldClose(window))中采用glBegin这样即时绘制的方式，而是在这个循环中对缓冲区发出一个绘制指令（例如 glDrawArrays(GL_TRIANGLES, 0, 3);），将定义好的buffer绘制出来即可。  
  buffer数据以及绑定：  
  
  ~~~
  float positions[6] = {
		-0.5f, -0.5f,
		 0.0f,  0.5f,
		-0.5f,  0.5f
	};

	unsigned int buffer;//类似于缓冲区的起始地址
	glGenBuffers(1, &buffer);//会返回待处理对象的整数id
	glBindBuffer(GL_ARRAY_BUFFER, buffer);
	glBufferData(GL_ARRAY_BUFFER, 6 * sizeof(float), positions, GL_STATIC_DRAW);  
  ~~~
  
<li>Shader则是告诉OpenGL如何解释buffer中的数据/字节，也即我们的数据是如何布局的，放在后面解释。</li>  
</ol>

## P5 Vertex Attributes and Layouts in OpenGL  
顶点属性和布局实际上就是规定buffer中数据的属性与布局，例如顶点坐标、纹理坐标、法线等等。 
~~~
glEnableVertexAttribArray(0);//enable index为0的顶点属性
glVertexAttribPointer(0, 2, GL_FLOAT, GL_FALSE, 2 * sizeof(float), 0);
~~~
参数解释：  
<ol>
  <li>index指的是属性的标识，属性包括位置、纹理、法线等等；</li>
  <li>size指的是每个属性的size，例如二维点的位置坐标size是2；</li>
  <li>pointer指的是每个属性在顶点中的stride。</li>
  </ol>
  
## P6 How Shaders Work in OpemGL
shader是一块运行在GPU上的代码（需要利用GPU的性能去实现绘制），not like 一般c++程序运行在CPU上，或者是过程运行在CPU上，将结果数据发给GPU。  
OpenGL pipeline：在CPU上写了一堆数据，绑定某些状态、发出调用给GPU，GPU的shaders处理调用并开始在屏幕上绘制（即着色阶段，通常分别经过顶点着色器和片段着色器）。
