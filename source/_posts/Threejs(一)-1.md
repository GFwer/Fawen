---
title: Threejs（一）
tags: [ThreeJS, Plugin]
category: Plugin
date: 2018-07-09 22:24
---

# Threejs(一)

![threejs组件图](https://static.gongfangwen.com/threejs组件图.png)

##三大组件
三大组件分别为：

- 场景（Scene）：容纳元素；
- 相机（Camera）：定义哪些元素可见；
- 渲染器（Renderer）：如何渲染组件；

案例：

``` js
// 引入 Three.js 库
<script src="https://cdn.bootcss.com/three.js/92/three.min.js"></script>
function init () {
    // 获取浏览器窗口的宽高，后续会用
    var width = window.innerWidth
    var height = window.innerHeight

    // 创建一个场景
    var scene = new THREE.Scene()

    // 创建一个具有透视效果的摄像头
    var camera = new THREE.PerspectiveCamera(45, width / height, 0.1, 800)

    // 创建一个 WebGL 渲染器，Three.js 还提供 <canvas>, <svg>, CSS3D 渲染器。
    var renderer = new THREE.WebGLRenderer()

    // 设置渲染器的清除颜色（即背景色）和尺寸
    renderer.setClearColor(0xffffff)
    renderer.setSize(width, height)

    // 创建一个长宽高均为 4 个单位长度的立方体（几何体）
    var cubeGeometry = new THREE.BoxGeometry(8, 8, 8)

    // 创建材质
    var cubeMaterial = new THREE.MeshBasicMaterial({
        color: 0xff0000,
        wireframe: true
    })

    // 创建一个立方体网格（mesh）：将材质包裹在几何体上
    // Mesh可以把可视化的材质覆盖在数学的几何模型上，形成一个可视的可被添加的对象
    var cube = new THREE.Mesh(cubeGeometry, cubeMaterial)

    // 设置立方体网格位置
    // 设置位置的的方式有三种：
    // ①直接设置坐标 cube.position.x = 10 cube.position.y = 3 cube.position.z = 1
    // ②一次性设置 cube.position.set(10, 3, 1)
    // ③ position 属性是一个 THREE.Vector3 对象 cube.position = new THREE.Vector3(10, 3, 1)
    cube.position.x = 0
    cube.position.y = -2
    cube.position.z = 0

    // 将立方体网格加入到场景中
    scene.add(cube)

    // 设置摄像机位置，并将其朝向场景中心(0, 0, 0)
    camera.position.x = 10
    camera.position.y = 10
    camera.position.z = 30
    camera.lookAt(scene.position)

    // 将渲染器的输出（此处是 canvas 元素）插入到 body
    document.body.appendChild(renderer.domElement)

    // 渲染，即摄像头拍下此刻的场景
    renderer.render(scene, camera)
}
init()
```

## 渲染器 Render

渲染器其实代表`<canvas>`标签；
在现在浏览器上，我们使用`WebGLRender`（使用 WebGL）来渲染图形，速度较快，老的`CanvasRender`（使用 Canvas）已经被弃用；

### 创建渲染器

``` js
var renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth,window.innerHeight); //该方法设置的是canvas标签的大小，等于在style中设置
document.body.appendChild(renderer.domElement);
```

## 相机

所有创建的图形都要放在相机的可视域中才能看的到；

### 类型

官方提供~~蒸饺~~正交相机（OrthographicCamera）、透视相机（PerspectiveCamera）、全景相机（CubeCamera）和 3D 相机（StereoCamera）；

#### 正交相机

正交相机效果类似设计图，没有远近大小的效果；
![正交相机](https://static.gongfangwen.com/正交相机.png)

其构造方法：
[demo，按 o 键显示视域演示](http://threejs-outsidelook.oss-cn-shanghai.aliyuncs.com/r89/source/examples/index.html?q=camera#webgl_camera)

``` js
OrthographicCamera( left, right, top, bottom, near, far )
//六个参数即是指定视域的左右上下近远六个范围
//想想一下整个视域是立方体，相机外围，物体在立方体内部
```

#### 透视相机

透视相机效果类似人眼，有远近大小；
![透视相机](https://static.gongfangwen.com/透视相机.png)

其构造方法：

``` js
PerspectiveCamera( fov, aspect, near, far )
```

> | 参数   | 描述                                                                  |
> | ------ | --------------------------------------------------------------------- |
> | fov    | fov 表示视场，即摄像机能看到的视野。人眼视角接近 180，推荐默认值 50   |
> | aspect | 指定渲染结果的横向尺寸和纵向尺寸的比值。推荐默认值容器 `width/height` |
> | near   | 距离摄像机多近的距离开始渲染。推荐默认值 0.1                          |
> | far    | 指定摄像机从它所处的位置开始能看到多远。默认推荐值 1000               |
>
> (刚才的 demo P 键是透视相机

#### 全景相机

就是全景（类似 VR）相机...
[demo](http://threejs-outsidelook.oss-cn-shanghai.aliyuncs.com/r89/source/examples/index.html?q=dynamic#webgl_materials_cubemap_dynamic2)

#### 3D 相机

类似 3D 照片，左右两个相机模拟人眼拍摄两张照片，通过两眼看到不同图像来让人以为是立体的
[demo(搭配 3D 眼镜观看)](http://threejs-outsidelook.oss-cn-shanghai.aliyuncs.com/r89/source/examples/index.html?q=anaglyph#webgl_effects_anaglyph)

## 相机和渲染器

在 Threejs 中，渲染器可以认为是`<canvas>`标签，相机可以视为画布，这张布在`<canvas>`标签内。
所以以下是等价的：

``` js
renderer.setSize(50,50)
等价于
<canvas style="height:50px;width:50px;">
```

和

``` js
var camera = new Three.OrthographicCamera(-250,250,250,-250,5,99)//左右上下近远（故意和style过不去吧？）
等价于
<canvas height="500" width="500"></canvas>
```

需要注意的是，如果`<canvas>`标签和画布大小不一致显示的内容可能会被拉伸

## 参考文章

- [threejs 教程](https://teakki.com/p/58a2a21af0d40775548c7e68)
- [Three.js 现学现卖](https://aotu.io/notes/2017/08/28/getting-started-with-threejs/index.html)
