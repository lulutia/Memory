title: WEBGL系列之二维操作
categories: technology
comments: true
tags: [前端,计算机]
---
背景介绍
* WebGL的技术规范继承自免费和开源的OpenGL标准[是用于渲染2D、3D矢量图形的跨语言、跨平台的API]

<!-- more -->

* 具体的说，WebGL是从OpenGL的一个特殊版本OpenGL ES中派生出来的，后者专用于嵌入式计算机，智能手机，家用游戏机等设备。它从OpenGL中移除了许多陈旧无用的旧特性，这使得它在保持轻量级的同时，仍具有足够的能力来渲染出精美的三维图形。
* OpenGL2.0开始支持的可编程着色器方法被OpenGL ES2.0继承，成为了WebGL1.0标准的核心部分。而着色器方法或称为着色器，是使用一种类似C的编程语言实现精美的视觉效果。而编写着色器的语言称为着色器语言，WebGL使用OpenGL ES着色器语言，亦即GLSLES。
* WebGL规范的建立由Khronos(也负责OpenGL规范的更新和标准化)建立的WebGL工作小组负责。2011年发布了WebGL规范的第一个版本。[WebGL2.0](https://www.khronos.org/registry/webgl/specs/latest/2.0/)处于[Editor's Draft](http://stackoverflow.com/questions/6692762/what-is-the-difference-between-a-w3c-working-draft-and-an-editors-draft)状态，它主要提供与OpenGL3.0新特性的对应，现阶段它并不与WebGL1.0完全向下兼容。

基本概念
  WebGL主要是用于渲染2D、3D矢量图形。在进入它的使用细节之前，我们先分析下它到底做了件什么事以及其执行的顺序。
  如果你有过一点三模建模方面的经验的话，你就会知道，所有的三维模型都是由点，线，三角形构成的。譬如下面这只超级出名的bunny兔子。模型的精细程度与其面片的数量直接相关，当面片数量足够多时，人类的视觉是无法分辨三角形的。
  同理，如果我们打开WebGL的绘图API，我们就会发现，它处理的也是点，线，三角形。那么它是如何由这些最基础的东西构建出整个世界的呢。其实，它主要执行了三方面的操作：
  
  * 获取顶点坐标
  	* 简单图形，可以显示定义
  	* 复杂图形，一般会在三维软件导出模型时一同导出
  	* 顶点坐标会写入缓冲中等待使用
  * 进行图元装配：即将顶点根据primitive(原始的连接关系）还原成网格结构
  	* 在WebGL中，为了使用者对顶点的位置有足够的控制权，提供了一个顶点着色器的东西。它是用上面提到的opengl es编写的，然后以字符串的形式嵌入在js文件中的，在程序开始运行前就已经设置好了
  	* 顶点着色器中主要对点的特性进行处理，比如进行坐标的转换等，然后由GPU执行图元装配。此处是以单个点为单位执行的操作，有多少个点，这段程序就会执行多少次

		```js
		var VSHADER_SOURCE = 
		'void main() {\n' +
		'gl_Position = vec4(0.0, 0.0, 0.0, 1.0);\n' + //设置坐标，必须
		'gl_PointSize = 10.0;\n' + // 设置尺寸，非必需，默认1.0
		'}\n';
		```
  * 进行光栅化：将图转化为一个个栅格组成的图象
  	* 上面一部生成了模型的网格形态，但是要呈现实际的模型还需要“着色”，这部分WebGL也为我们提供了片元着色器来进行控制。
  	* 片元着色器主要进行材质，颜色的处理等，它会进行逐片元处理过程，片元这边可大概理解为像素(图像的单元)

  	```js
  	var FSHADER_SOURCE = 
	'void main() {\n' +
	'gl_FragColor = vec4(1.0, 0.0, 0.0, 1.0);\n' + // 设置颜色
	'}\n';

  	```
  	
 下面图示为整体流程:
 
 ![整体流程](http://okzzg7ifm.bkt.clouddn.com/01.png?imageView2/2/w/900/h/400/q/75|watermark/2/text/bHVsdXRpYQ==/font/5a6L5L2T/fontsize/240/fill/IzAwMDAwMA==/dissolve/20/gravity/SouthEast/dx/10/dy/10|imageslim)
GLSE中的数据类型
* 基本类型
	* float: 表示浮点数
	* int： 整型 
	* bool： 布尔类型
	* 齐次坐标: (x, y, z, w) 等价于三维坐标 (x/w, y/w, z/w)，w必需大于等于0，齐次坐标的存在，使得用矩阵乘法来描述顶点变换称为可能，三维图形系统在计算过程中，通常使用齐次坐标来表示顶点的三维坐标，这样能够提高处理三维数据的效率
* 矢量和矩阵
	* vec4: 表示由四个浮点数组成的矢量，类似有 vec2,vec3,ivec2,ivec3,ivec4,bvec2,bvec3,bvec4
	* mat2: 表示2*2的浮点数元素的矩阵，类似有 mat3,mat4, 注意**列主序**
* 结构体
	使用struct，将已存在的类型聚合到一起，就可以定义为结构体
* 数组
	只支持一维数组，而且数组对象不支持pop()和push()等操作
* 取样器(纹理)：必须通过该类型变量访问纹理，两种基本的取样器类型如下
	* sampler2D
	* samplerCube
	* 注意：取样器变量只能是uniform变量，或者需要访问纹理的函数
* 函数

```c
	返回类型函数名(type0, arg0, type1, arg1, ...typen, argn) {
		函数计算
		return 返回值
	}
```
* 存储限定字
	* const: 不可变
	* attribute: 只能出现在顶点着色器中，只能被声明为全局变量，被用来表示逐点的信息
	* uniform：可以用在顶点和片元着色器中，必须是全局变量，uniform变量是只读的，它可以是数组和结构体之外的任意类型，它包含了“一致”[即非逐顶点／片元的]的数据
	* varying: 必须全局，从顶点着色器向片元着色器传输数据，在两个着色器中必须同名同类型
* 精度限定字
	* highp: 高精度
	* mediump: 中精度
	* lowp: 低精度
	* 可以使用关键字precision来声明着色器的默认精度，这行代码必须在顶点着色器或片元着色器的顶部，格式为**precision 精度限定字 类型名称**
	* 注意：片元着色器中的float类型没有默认精度，需要手动指定，否则会出现编译错误
* 预处理指令
	预处理指令用来在真正编译之前对代码进行预处理，都以#开始

核心思想
* [获取WebGL上下文](http://lulutia.com/webgldemo/01/)：
![获取上下文](http://okzzg7ifm.bkt.clouddn.com/02.png?imageView2/2/w/900/h/400/q/75|watermark/2/text/bHVsdXRpYQ==/font/5a6L5L2T/fontsize/240/fill/IzAwMDAwMA==/dissolve/20/gravity/SouthEast/dx/10/dy/10|imageslim)

	```js
	var canvas = document.getElementById('example');
	var ctx = canvas.getContext('2d');
	ctx.fillStyle = 'rgba(0, 0, 255, 1.0)';
	```
* [初始化着色器](http://lulutia.com/webgldemo/03/)：
	详细见具体API 1-13部分，具体流程如下:
	
	![初始化着色器](http://okzzg7ifm.bkt.clouddn.com/03.png?imageView2/2/w/900/h/400/q/75|watermark/2/text/bHVsdXRpYQ==/font/5a6L5L2T/fontsize/240/fill/IzAwMDAwMA==/dissolve/20/gravity/SouthEast/dx/10/dy/10|imageslim)

* [设置canvas背景色并清除canvas](http://lulutia.com/webgldemo/02/)：

	```js
	// 设定背景色，一旦指定了背景色后，背景色就会常驻WebGL系统，在下一次再调用这个函数前不会改变
	gl.clearColor(0.0, 0.0, 0.0, 1.0); //见API 15

	// 用背景色清空canvas绘图区域，此方法继承自OpenGL，基于多基本缓冲区模型清空绘图区域，实际上是在清空颜色缓冲区，类似的还有 gl.DEPTH_BUFFER_BIT,gl.STENCIL_BUFFER_BIT
	gl.clear(gl.COLOR_BUFFER_BIT); // 见API16
	```
* 右手坐标系是WebGL默认的坐标系统
* [JS与着色器之间传输数据(以attribute举例)](http://lulutia.com/webgldemo/04/)
	详细见具体API 17-20部分，具体流程如下: [动态传输例子点我](http://lulutia.com/webgldemo/05/), [点我](http://lulutia.com/webgldemo/06/)
	
	![js vs 着色器](http://okzzg7ifm.bkt.clouddn.com/04.png?imageView2/2/w/900/h/400/q/75|watermark/2/text/bHVsdXRpYQ==/font/5a6L5L2T/fontsize/240/fill/IzAwMDAwMA==/dissolve/20/gravity/SouthEast/dx/10/dy/10|imageslim)

* [缓冲区对象](http://lulutia.com/webgldemo/07/)
	WebGL提供了**缓冲区对象**，它可以一次性的向着色器传入多个顶点的数据。使用缓冲区对象向顶点着色器传入多个顶点数据的步骤如下:
	详细见具体API 21-25部分，注意**可以创建多个缓冲区对象**

	![缓冲区对象](http://okzzg7ifm.bkt.clouddn.com/05.png?imageView2/2/w/900/h/400/q/75|watermark/2/text/bHVsdXRpYQ==/font/5a6L5L2T/fontsize/240/fill/IzAwMDAwMA==/dissolve/20/gravity/SouthEast/dx/10/dy/10|imageslim)

* [WebGL可绘制的基本图形](http://lulutia.com/webgldemo/08/)，主要运用于API 14，[对比例子点我](http://lulutia.com/webgldemo/09/)
	WebGL可以绘制gl.POINTS, gl.LINES, gl.LINE_STRIP, gl.LINE_LOOP, gl.TRIANGLES, gl.TRIANGLE_STRIP, gl.TRIANGLE_FAN 七种，它们的绘制顺序如下：
	
	![基本图形](http://okzzg7ifm.bkt.clouddn.com/06.png?imageView2/2/w/900/h/500/q/75%7Cwatermark/2/text/bHVsdXRpYQ==/font/5a6L5L2T/fontsize/240/fill/IzAwMDAwMA==/dissolve/20/gravity/SouthEast/dx/10/dy/10%7Cimageslim)
	
* 移动、旋转、缩放
	* 对于[平移](http://lulutia.com/webgldemo/10/)而言，就是对顶点的每个分量加上其在对应轴上平移的距离, 因此就是在顶点着色器中对原有的position加上移动的position即可

	```js
	newX = oldX + moveX;
	newY = oldY + moveY;
	newZ = oldZ + moveZ;
	```
	* 对于[旋转](http://lulutia.com/webgldemo/11/)而言，有公式如下[绕Z轴旋转]，因此将顶点着色器中的坐标换为下面的计算即可：**注意JS内置的Math.cos()和Math.sin()接受的是弧度制，而并非角度**

	```js
	newX = oldX * cosβ - oldY * sinβ
	newY = oldX * sinβ + oldY * cosβ
	newZ = oldZ
	```
	```js
	'void main() {\n' +
	'gl_Position.x = a_Position.x * u_CosB - a_Position.y * u_SinB;\n' + //设置坐标，必须
	'gl_Position.y = a_Position.x * u_SinB + a_Position.y * u_CosB;\n' +
	'gl_Position.z = a_Position.z;\n' + 
	'gl_Position.w = 1.0;\n' +
	'}\n';
	```
	* 当情况复杂起来后，需要进行[矩阵变换](http://lulutia.com/webgldemo/12/)，比如根据上面可以得到平移矩阵，旋转矩阵和缩放矩阵如下：之后将它与坐标相乘即可得到变换后的坐标,因为[矩阵计算比较复杂](http://lulutia.com/webgldemo/13/)，一般情况下都使用封装好的矩阵操作库进行计算
		
		![矩阵](http://okzzg7ifm.bkt.clouddn.com/07.png?imageView2/2/w/900/h/400/q/75|watermark/2/text/bHVsdXRpYQ==/font/5a6L5L2T/fontsize/240/fill/IzAwMDAwMA==/dissolve/20/gravity/SouthEast/dx/10/dy/10|imageslim)

		```js
		gl_Position = u_xformMatrix * a_Position ;
		```

* [动画](http://lulutia.com/webgldemo/14/)
	* 根据不同时间确定不同的状态
	* 在每次绘制前，清除上次绘制的内容，并绘制相应状态下的图形
	* 调用requestAnimationFrame(fn)进行持续绘制
* 颜色
  之前的所有例子我们都是直接设定了颜色值并传入片元着色器，其颜色值是统一的不变的，但是实际中，我们可能会对不同的点着色不同，因此顶点着色器与片元着色器之间应该有某种联系
	* 实际在顶点着色器与片元着色器中主要执行了两个步骤：
		* 图形装配过程：将孤立的顶点坐标装配成几何图形
		* [光栅化](http://lulutia.com/webgldemo/15/)过程：将装配好的几何图形转化为片元
	* 光栅化过程中生成的片元都是带有坐标信息的，调用片元着色器时这些坐标信息也随着片元传了进来，可以通过片元着色器中的内置变量来访问片元的坐标
	* 在WebGL中，**如果顶点着色器与片元着色器中有类型和命名都相同的varying变量，那么顶点着色器赋给该变量的值就会自动被传入片元着色器**，但是，片元着色器中的v_Color变量和顶点着色器中的v_Color实际并不是一回事，顶点着色器中的v_Color变量在传入片元着色器之前经过了内插过程，所以称为varying
	* [整个颜色的处理过程如下](http://lulutia.com/webgldemo/16/)：

		![颜色处理](http://okzzg7ifm.bkt.clouddn.com/08.png?imageView2/2/w/900/h/400/q/75|watermark/2/text/bHVsdXRpYQ==/font/5a6L5L2T/fontsize/240/fill/IzAwMDAwMA==/dissolve/20/gravity/SouthEast/dx/10/dy/10|imageslim)
	
* [纹理](http://lulutia.com/webgldemo/17/)
	真实世界中的颜色变化是相当细粒度的，我们如果全部通过自己定义来做则会陷入无休止的工作中，因此在三维图形学中，纹理映射就成为了一项关键技术。所谓纹理映射，就是将一张图像映射到一个几何图形的表面上去，它根据纹理图像，为之前光栅化后的每个片元涂上合适的颜色，其中组成纹理图像的像素称为纹素，其具体流程如下：
	
	![纹理处理](http://okzzg7ifm.bkt.clouddn.com/09.png?imageView2/2/w/900/h/400/q/75|watermark/2/text/bHVsdXRpYQ==/font/5a6L5L2T/fontsize/240/fill/IzAwMDAwMA==/dissolve/20/gravity/SouthEast/dx/10/dy/10|imageslim)
	
	* 在执行纹理映射的过程中，会涉及到纹理坐标，WebGL系统中的纹理坐标是二维的，使用st坐标系统。其坐标值与图像自身的尺寸无关，左下角的坐标为(0.0, 0.0), 右下角为(1.0, 0.0), 右上角为(1.0, 1.0), 左上角为(0.0, 1.0)。纹理映射需要顶点着色器和片元着色器的配合，首先在顶点着色器中为每个顶点指定纹理坐标，然后在片元着色器中根据每个片元的纹理坐标从纹理图像中抽取纹理像素颜色。
	* 详细流程见API 27-32，具体代码处理流程如下：在GLSE中运用texture2D(sample2D, sampler, vec2, coord): 来从sample指定的纹理上获取coord指定的纹理坐标处的像素颜色, [修改纹理坐标和映射方式点我](http://lulutia.com/webgldemo/18/), [多重纹理点我](http://lulutia.com/webgldemo/19/)
	
	![纹理代码处理](http://okzzg7ifm.bkt.clouddn.com/10.png?imageView2/2/w/900/h/400/q/75|watermark/2/text/bHVsdXRpYQ==/font/5a6L5L2T/fontsize/240/fill/IzAwMDAwMA==/dissolve/20/gravity/SouthEast/dx/10/dy/10|imageslim)
	
	
具体API
**注：文中demo根据WebGL编程指南demo改编而来**

1. [gl.createShader(type)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/createShader): 创建由type指定的着色器对象，type的值分别为gl.VERTEX_SHADER表示顶点着色器 gl.FRAGMENT_SHADER表示片元着色器
2. [gl.shaderSource(shader, source)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/shaderSource): 将source指定的字符串形式的代码传入shader指定着色器，如果之前已经向shader传入过代码了，旧的代码将会被替换掉
3. [gl.compileShader(shader)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/compileShader): 编译shader指定的着色器中的源代码
4. [gl.getShaderParameter(shader, pname)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/getShaderParameter): 获取shader指定的着色器中，pname指定的参数信息pname: 指定待获取参数的类型，可以是gl.SHADER_TYPE gl.DELETE_STATUS或者gl.COMPILE_STATUS，根据pname的不同，返回不同的值
5. [gl.getShaderInfoLog(shader)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/getShaderInfoLog): 获取shader指定的着色器的信息日志
6. [gl.deleteShader(shader)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/deleteShader): 如果不需要这个着色器，可以用gl.deleteShader()函数来删除着色器，注意，如果着色器对象还在使用，那么deleteShader()并不会立刻删除着色器而是要等到程序对象不再使用该着色器后，才将其删除
7. [gl.createProgram()](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/createProgram): 创建程序对象
8. [gl.attachShader(program, shader)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/attachShader): 将shader指定的着色器对象分配给program指定的程序对象，着色器在附给程序对象前，并不一定要为其指定代码或进行编译(也就是说，把空的着色器附给程序对象也是可以的)
9. [gl.linkProgram(program)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/linkProgram): 连接program指定的程序对象中的着色器进行这一步的目的是确保：
 * 顶点着色器和片元着色器的varying变量同名同类型，且一一对应
 * 顶点着色器对每个varying变量赋了值
 * 顶点着色器和片元着色器中的同名uniform变量也是同类型的
 * 着色器中的attribute变量，uniform变量和varying变量的个数没有超过着色器的上限
10. [gl.getProgramParameter(program, pname)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/getProgramParameter): 获取pname指定的程序对象中pname指定的参数信息。返回值随着pname的不同而不同pname：gl.DELETE_STATUS gl.LINK_STATUS gl.VALIDATE_STATUS gl.ATTACHED_SHADERS gl.ACTIVE_ATTRIBUTES gl.ACTIVE_UNIFORMS, 根据pname的不同，返回值不同, 程序对象即使连接成功了，也有可能运行失败，比如没有为取样器分配纹理单元。这些错误是在运行阶段而不是连接阶段产生的
11. [gl.getProgramInfoLog(program)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/getProgramInfoLog): 获取program指定的程序对象的信息日志
12. [gl.deleteProgram(program)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/deleteProgram): 删除程序对象
13. [gl.useProgram(program)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/useProgram): 告知WebGL系统绘制时使用program指定的程序对象
14. [gl.drawArrays(mode, first, count)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/drawArrays): 按照mode参数指定的方式绘制图形(gl.POINTS, gl.LINES, gl.LINE_STRIP, gl.LINE_LOOP, gl.TRIANGLES, gl.TRIANGLE_STRIP, gl.TRIANGLE_FAN), first: 指定从哪个顶点开始绘制 整型, count: 指定绘制多少个点 整型
15. [gl.clearColor(red, green, blue, alpha)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/clearColor): 确定当清除颜色buffer时使用的颜色
16. [gl.clear(mask)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/clear): 指定被清除的buffer，可能的值gl.COLOR_BUFFER_BIT，gl.DEPTH_BUFFER_BIT，gl.STENCIL_BUFFER_BIT 
17. [gl.getAttribLocation(program, name)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/getAttribLocation): 获取由name参数指定的attribute变量的存储地址, program: 指定包含顶点着色器和片元着色器的着色器程序对象, name: 指定想要获取其存储地址的attribute变量的名称
18. [gl.vertexAttrib3f(location, v0, v1, v2)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/vertexAttrib): 将数据(v0, v1, v2)传给由location参数指定的attribute变量, gl.vertexAttribXf是一系列同族函数，其中X可以为1，2，3，4，无论哪一个，第四个值都为1.0，其他未填位填充0.0, gl.vertexAttribXv是其矢量版本，接受类型化数组, gl.vertexAttrib 基础函数名 x 参数个数 v参数类型 还能够有gl.vertexAttrib[123]f这种用法
19. [gl.getUniformLocation(program, name)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/getUniformLocation): 与API17类似
20. [gl.uniform[1234][fi][v]()](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/uniform): 与API18类似 
21. [gl.createBuffer()](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/createBuffer): 创建缓冲区对象, 这一步的结果是WebGL系统中多了一个新创建出来的缓冲区对象
22. [gl.bindBuffer(target, buffer)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/bindBuffer): 允许使用buffer表示的缓冲区对象并将其绑定到target表示的目标上，target: gl.ARRAY_BUFFER 表示缓冲区对象中包含了顶点的数据，gl.ELEMENT_ARRAY_BUFFER表示缓冲区对象中包含了顶点的索引值，buffer: 指定之前由gl.createBuffer()返回的待绑定的缓冲区对象将缓冲区对象绑定到目标。这个目标表示缓冲区对象的用途, 我们不能直接向缓冲区写入数据，而只能向目标写入数据，所以要向缓冲区写入数据，必须先绑定。
23. [gl.bufferData(target, ArrayBufferView srcData, usage)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/bufferData): 这个API在WebGL1.0和2.0上有所不同，具体见规范。总的来说是开辟存储空间，向绑定在target上的缓冲区对象写入数据data
24. [gl.vertexAttribPointer(index, size, type, normalized, stride, offset)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/vertexAttribPointer): 将绑定到target的缓冲区对象分配给由location指定的attribute变量, size: 指定缓冲区中每个顶点的分量个数(1-4), type: 指定数据格式 [gl.UNSIGNED_BYTE, gl.SHORT, gl.UNSIGNED_SHORT, gl.INT, gl.UNSIGNED_INT, gl.FLOAT], normalized: 传入true, false表明是否将非浮点数的数据归一化到[0, 1]或[-1, 1]区间, stride: 指定相邻两个顶点间的字节数，默认0, offset: 指定缓冲区对象中的偏移量(以字节为单位)
25. [gl.enableVertexAttribArray(index)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/enableVertexAttribArray): 开启index指定的attribute变量，链接变量与分配给它的缓冲区对象，使顶点着色器能够访问缓冲区内的数据
26. [gl.uniformMatrix[234]x[234]fv(location, transpose, array...)](https://developer.mozilla.org/en-US/docs/Web/API/WebGL2RenderingContext/uniformMatrix): 将array表示的n*n矩阵分配给由location指定的uniform变量，transpose在WebGL中必须指定为false
27. [gl.createTexture()](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/createTexture): 创建纹理对象以存储纹理图像
28. [gl.pixelStorei(pname, param)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/pixelStorei): 使用pname和param指定的方式处理加载得到的图片, pname:gl.UNPACK_FLIP_Y_WEBGL: 对图像进行Y轴反转，默认为false; gl.UNPACK_PREMULTIPLY_ALPHA_WEBGL: 将图像RGB颜色值的每一个分量乘以A，默认为false, param: 指定非0(true)或0(false)，必须为整数。使用原因：WebGL纹理坐标系统中t轴的方向和PNG,BMP,JPG等格式图片的坐标系统的Y轴方向是相反的，所以要对纹理图像进行Y轴反转
29. [gl.activeTexture(texture)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/activeTexture): 激活texture指定的纹理单元
30. [gl.bindTexture(target, texture)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/bindTexture): 开启texture指定的纹理对象，并将其绑定到target上, 如果已经激活了某个纹理单元，则纹理对象也会绑定到这个纹理单元上; target: gl.TEXTURE_2D  gl.TEXTURE_CUBE_MAP; 
在WebGL中，没法直接操作纹理对象，必须通过将纹理对象绑定到纹理单元上，然后通过操作纹理单元来操作纹理对象
31. [gl.texParameter[fi](target, pname, param)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/texParameter): 将param的值赋给绑定到目标的纹理对象的pname参数上, 具体参数见规定，通常可以不用调用，使用默认值就可以了
32. [gl.texImage2D(target, level, internalformat, format, type, image)](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/texImage2D): 将image指定的图像分配给绑定到目标上的纹理对象