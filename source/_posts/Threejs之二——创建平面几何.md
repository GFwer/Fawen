---
title: Threejs（二）
tags: [ThreeJS, Plugin]
category: Plugin
date: 2018-07-10 22:31
---

# Threejs 之二——创建平面几何

或许这张该叫 two.js(捂脸

创建物体的时候需要传入两个参数，一个是几何形状（Geometry），另一个就是材质（Material）；

几何形状储存着形状的各个顶点信息，可以通过制定特征来简便构建形状。

## 基本几何形状

### 立方体

立方体（CubeGeomeyty），通过制定长宽高等来创建。
构造函数：

``` js
THREE.CubeGeometry(width, height, depth, widthSegments, heightSegments, depthSegments)
//x,y,z上的长度及其分段数
```

这样可以简单地创建一个长方体：

> new THREE.CubeGeometry(1, 2, 3);
> //物体的默认位置是原点，对于立方体其原点在几何中心

### 平面

平面（PlaneGeometry），即长方形；
构造函数：

``` js
THREE.PlaneGeometry(width, height, widthSegments, heightSegments)
//参数和立方体类似
```

### 球体

球体（SphereGeometry）就是个球啊
构造函数：

``` js
THREE.SphereGeometry(radius, segmentsWidth, segmentsHeight, phiStart, phiLength, thetaStart, thetaLength)
//半径，经度分段，维度分段，经度开始，经度跨过的弧度，纬度开始，纬度跨过的弧度
```

对于分段相当于该维度上被分为了几段，段数越大越接近真曲线；

### 圆形

圆形（CircleGeometry），通过这样可构造出圆形、扇形：

``` js
THREE.CircleGeometry(radius, segments, thetaStart, thetaLength)
//和球体类似的参数
```

### 圆柱体

圆柱体（CylinderGeometry），通过此构建出圆柱、圆台等：

``` js
THREE.CylinderGeometry(radiusTop, radiusBottom, height, radiusSegments, heightSegments, openEnded)
//上半径、下半径、高度、经度分段、纬度分段，是否无顶面（默认false，即有）
```

### 正四面体

通过下列构造方法可以构建正四面体（TetrahedronGeometry）、正八面体（OctahedronGeometry）、正十二面体（IcosahedronGeometry）：

``` js
THREE.TetrahedronGeometry(radius, detail)
THREE.OctahedronGeometry(radius, detail)
THREE.IcosahedronGeometry(radius, detail)
//半径，精细程度
```

## 文字

文字（TextGeometry）需要外部加载字体文件才可以显示，使用`THREE.FontLoader()`的`loader`方法可以加载文件并加以配置。

``` js
var loader = new THREE.FontLoader();
loader.load('../lib/helvetiker_regular.typeface.json', function(font) {
    var mesh = new THREE.Mesh(new THREE.TextGeometry('Hello', {
        font: font,
        size: 1,
        height: 1
    }), material);
    scene.add(mesh);

    renderer.render(scene, camera);
});
```

参数列表：

> | size           | 字号                       |
> | -------------- | -------------------------- |
> | height         | 文字高度                   |
> | curveSegment   | 分段，可以令文字平滑       |
> | font           | 字体                       |
> | weight         | 是否加粗，normal 或 bold   |
> | style          | 是否斜体,normal 或 italics |
> | bevelEnabled   | 是否使用倒角（边缘斜切）   |
> | bevelThickness | 倒角厚度                   |
> | bevelSize      | 倒角宽度                   |

## 自定义

自定义需要手动设置每个顶点，较为复杂。

``` js
// 初始化几何形状
var geometry = new THREE.Geometry();

// 设置顶点位置
// 顶部4顶点
//Vector3 创建一个顶点矢量信息（顶点）
geometry.vertices.push(new THREE.Vector3(-1, 2, -1));
geometry.vertices.push(new THREE.Vector3(1, 2, -1));
geometry.vertices.push(new THREE.Vector3(1, 2, 1));
geometry.vertices.push(new THREE.Vector3(-1, 2, 1));
// 底部4顶点
geometry.vertices.push(new THREE.Vector3(-2, 0, -2));
geometry.vertices.push(new THREE.Vector3(2, 0, -2));
geometry.vertices.push(new THREE.Vector3(2, 0, 2));
geometry.vertices.push(new THREE.Vector3(-2, 0, 2));

// 设置顶点连接情况
// 顶面
//Face3 创建面（按照index链接顶点）
geometry.faces.push(new THREE.Face3(0, 1, 3));
geometry.faces.push(new THREE.Face3(1, 2, 3));
// 底面
geometry.faces.push(new THREE.Face3(4, 5, 6));
geometry.faces.push(new THREE.Face3(5, 6, 7));
// 四个侧面
geometry.faces.push(new THREE.Face3(1, 5, 6));
geometry.faces.push(new THREE.Face3(6, 2, 1));
geometry.faces.push(new THREE.Face3(2, 6, 7));
geometry.faces.push(new THREE.Face3(7, 3, 2));
geometry.faces.push(new THREE.Face3(3, 7, 0));
geometry.faces.push(new THREE.Face3(7, 4, 0));
geometry.faces.push(new THREE.Face3(0, 4, 5));
geometry.faces.push(new THREE.Face3(0, 5, 1));
```

## 参考资料

由参考资料整理而成

- [Three.js 入门指南](http://www.ituring.com.cn/book/1272)
