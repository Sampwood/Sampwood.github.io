---
title: threejs study
categories:
  - coding
  - js
tags:
  - WebGL
  - threejs
date: 2018-06-03 17:20:56
update: 2018-06-03 17:20:56
---


### 简述
threejs是一个用来创建三维图形应用的js库。
说到三维图形应用，就不得不提一句webGL。在百度百科中是这么描述webGL的：
`WebGL（全写Web Graphics Library）是一种3D绘图协议，这种绘图技术标准允许把JavaScript和OpenGL ES 2.0结合在一起，通过增加OpenGL ES 2.0的一个JavaScript绑定，WebGL可以为HTML5 Canvas提供硬件3D加速渲染，这样Web开发人员就可以借助系统显卡来在浏览器里更流畅地展示3D场景和模型了，还能创建复杂的导航和数据视觉化。`
也就是说：`WebGL可以看做是将OpenGL ES（OpenGL for Embedded Systems，OpenGL嵌入式版本，针对手机、游戏机等设备相对较轻量级的版本）移植到了网页平台`。

Three.js封装了底层的图形接口，使得程序员能够在无需掌握繁冗的图形学知识的情况下，也能用简单的代码实现三维场景的渲染。
除了WebGL之外，Three.js还提供了基于Canvas、SVG标签的渲染器。

<!-- more -->

下面就开始具体说下threejs的代码实现。

#### hello, world!
一个典型的Three.js程序至少要包括渲染器（Renderer）、场景（Scene）、照相机（Camera），以及你在场景中创建的物体。
```javascript
// 渲染器（Renderer）
var renderer = new THREE.WebGLRenderer({
    canvas: document.getElementById('mainCanvas') // 先要在html中定义id为mainCanvas的<canvas></canvas>元素
});
renderer.setClearColor(0x000000); // 设置背景色

// 场景（scene）：相当于大的容器
var scene = new THREE.Scene(); // 一般说，场景里没有很复杂的操作，在程序最开始的时候进行实例化，然后将物体添加到场景中即可。

// 照相机（Camera）
var camera = new THREE.PerspectiveCamera(45, 4 / 3, 1, 1000); // 这个是透视相机。当然还有其他相机
camera.position.set(0, 0, 5);
scene.add(camera);

// 物体
var cube = new THREE.Mesh(new THREE.CubeGeometry(1, 2, 3), // CubeGeometry是长方体
    new THREE.MeshBasicMaterial({ // material是材质，用于物体的颜色，贴图之类的
        color: 0xff0000
    })
);
scene.add(cube);

// 最后一步：渲染
renderer.render(scene, camera);
```

### 相机
WebGL和Three.js使用的坐标系是右手坐标系。

![坐标系](/images/threejs-coordinate.png)

针对投影方式的不同，照相机又分为正交投影照相机（Orthographic Camera）与透视投影照相机（Perspective Camera）。

#### 正交投影照相机
正交投影照相机（Orthographic Camera）设置起来较为直观，它的构造函数是：

```javascript
new THREE.OrthographicCamera(left, right, top, bottom, near, far)
```

这六个参数分别代表正交投影照相机拍摄到的空间的六个面的位置，这六个面围成一个长方体，我们称其为视景体（Frustum）。只有在视景体内部（下图中的灰色部分）的物体才可能显示在屏幕上，而视景体外的物体会在显示之前被裁减掉。

![正交相机](/images/orthographic-camera.png)

为了保持照相机的横竖比例，需要保证(right - left)与(top - bottom)的比例与Canvas宽度与高度的比例一致。(ps: 其实意思是如果比例不一致的时候，会用相机的内容去填充canvas的大小，所以相机的内容的长宽会被相应的拉伸)

near与far都是指到照相机位置在深度平面的位置，而照相机不应该拍摄到其后方的物体，因此这两个值应该均为正值。为了保证场景中的物体不会因为太近或太远而被照相机忽略，一般near的值设置得较小，far的值设置得较大，具体值视场景中物体的位置等决定。

使用实例：
```javascript
var camera = new THREE.OrthographicCamera(-2, 2, 1.5, -1.5, 1, 10); // 创建实例
camera.position.set(0, 0, 5); // 设置位置
camera.lookAt(new THREE.Vector3(0, 0, 0)); // 默认照相机是沿z轴负方向观察的，可以通过lookAt函数指定它看着原点方向。
```

#### 透视投影照相机
透视投影照相机（Perspective Camera）的构造函数是：

```javascript
new THREE.PerspectiveCamera(fov, aspect, near, far)
```

![透视相机](/images/perspective-camera.png)

透视图中，灰色的部分是视景体，是可能被渲染的物体所在的区域。fov是视景体竖直方向上的张角（是角度制而非弧度制），如侧视图所示。
aspect等于width / height，是照相机水平方向和竖直方向长度的比值，通常设为Canvas的横纵比例。
near和far分别是照相机到视景体最近、最远的距离，均为正值，且far应大于near。

使用实例：
```javascript
var camera = new THREE.PerspectiveCamera(45, 400 / 300, 1, 10);
camera.position.set(0, 0, 5);
```

### 物体（网格）
在创建物体时，需要传入两个参数，一个是几何形状（Geometry），另一个是材质（Material）。

#### 几何形状
几何形状（Geometry）最主要的功能是储存了一个物体的顶点信息。WebGL需要程序员指定每个顶点的位置，而在Three.js中，可以通过指定一些特征来创建几何形状，例如使用半径创建一个球体，从而省去程序员一个个指定顶点的工作量。

##### 基本几何形状
在长方体中，width是x方向上的长度；height是y方向上的长度；depth是z方向上的长度；后三个参数分别是在三个方向上的分段数，如widthSegments为3的话，代表x方向上水平分为三份。一般情况下不需要分段的话，可以不设置后三个参数，后三个参数的缺省值为1。其他几何形状中的分段也是类似的，下面不做说明。

```javascript
// 虽然这一形状的名字叫立方体（CubeGeometry），但它其实是长方体，也就是长宽高可以设置为不同的值。其构造函数是：
new THREE.CubeGeometry(width, height, depth, widthSegments, heightSegments, depthSegments)

// 平面，这里的平面（PlaneGeometry）其实是一个长方形，而不是数学意义上无限大小的平面
new THREE.PlaneGeometry(width, height, widthSegments, heightSegments)

// 球体
new THREE.SphereGeometry(radius, segmentsWidth, segmentsHeight, phiStart, phiLength, thetaStart, thetaLength)

// 圆形
new THREE.CircleGeometry(radius, segments, thetaStart, thetaLength)

// 圆柱体
new THREE.CylinderGeometry(radiusTop, radiusBottom, height, radiusSegments, heightSegments, openEnded)

// 正四面体、正八面体、正二十面体
new THREE.TetrahedronGeometry(radius, detail)
new THREE.OctahedronGeometry(radius, detail)
new THREE.IcosahedronGeometry(radius, detail)

// 圆环面
new THREE.TorusGeometry(radius, tube, radialSegments, tubularSegments, arc)

// 圆环结
new THREE.TorusKnotGeometry(radius, tube, radialSegments, tubularSegments, p, q, heightScale)
```

##### 文字形状
文字形状（TextGeometry）可以用来创建三维的文字形状。

创建文字形状的流程和之前介绍的基本几何形状是类似的，其构造函数是：
```javascript
new THREE.TextGeometry(text, parameters)
```
其中，text是文字字符串，parameters是以下参数组成的对象：

- size：字号大小，一般为大写字母的高度
- height：文字的厚度
- curveSegments：弧线分段数，使得文字的曲线更加光滑
- font：字体，默认是'helvetiker'，需对应引用的字体文件
- weight：值为'normal'或'bold'，表示是否加粗
- style：值为'normal'或'italics'，表示是否斜体
- bevelEnabled：布尔值，是否使用倒角，意为在边缘处斜切
- bevelThickness：倒角厚度
- bevelSize：倒角宽度

使用文字形状需要下载和引用额外的字体库。

这里，我们以`helvetiker`字体为例。我们在`Three.js GitHub master/examples/fonts`目录下，下载`helvetiker_regular.typeface.json`文件放在你的目录下，然后用以下方法加载：
```javascript
var loader = new THREE.FontLoader();
loader.load('../lib/helvetiker_regular.typeface.json', function(font) {
    var mesh = new THREE.Mesh(new THREE.TextGeometry('Hello', {
        font: font,
        size: 1,
        height: 1
    }), material);
    scene.add(mesh);

    // render
    renderer.render(scene, camera);
});
```

##### 自定义形状
对于Three.js没有提供的形状，可以提供自定义形状来创建。
自定义形状使用的是Geometry类，它是其他如CubeGeometry、SphereGeometry等几何形状的父类，其构造函数是：`new THREE.Geometry()`


#### 材质
材质（Material）是独立于物体顶点信息之外的与渲染效果相关的属性。通过设置材质可以改变物体的颜色、纹理贴图、光照模式等。

##### 基本材质
使用基本材质（BasicMaterial）的物体，渲染后物体的颜色始终为该材质的颜色，而不会由于光照产生明暗、阴影效果。如果没有指定材质的颜色，则颜色是随机的。其构造函数是：
```javascript
new THREE.MeshBasicMaterial(opt)
```
其中，opt可以缺省，或者为包含各属性的值。opt可以是以下的几个较为常用的属性：
- visible：是否可见，默认为true
- side：渲染面片正面或是反面，默认为正面THREE.FrontSide，可设置为反面THREE.BackSide，或双面THREE.DoubleSide
- wireframe：是否渲染线而非面，默认为false
- color：十六进制RGB颜色，如红色表示为0xff0000
- map：使用纹理贴图

##### Lambert材质
Lambert材质（MeshLambertMaterial）是符合Lambert光照模型的材质。Lambert光照模型的主要特点是只考虑漫反射而不考虑镜面反射的效果，因而对于金属、镜子等需要镜面反射效果的物体就不适应，对于其他大部分物体的漫反射效果都是适用的。
其构造函数是：
```javascript
new THREE.MeshLambertMaterial(opt)
```
opt可以是以下的属性：
- color是用来表现材质对散射光的反射能力，也是最常用来设置材质颜色的属性。除此之外，还可以用ambient和emissive控制材质的颜色。
- ambient表示对环境光的反射能力，只有当设置了AmbientLight后，该值才是有效的，材质对环境光的反射能力与环境光强相乘后得到材质实际表现的颜色。
- emissive是材质的自发光颜色，可以用来表现光源的颜色

##### Phong材质
Phong材质（MeshPhongMaterial）是符合Phong光照模型的材质。和Lambert不同的是，Phong模型考虑了镜面反射的效果，因此对于金属、镜面的表现尤为适合。
其构造函数是：`new THREE.MeshPhongMaterial(opt)`

同样地，可以指定emissive和ambient值，还可以使用specular值指定镜面反射系数作说明。

##### 法向材质
法向材质可以将材质的颜色设置为其法向量的方向，有时候对于调试很有帮助。
法向材质的设定很简单，甚至不用设置任何参数：`new THREE.MeshNormalMaterial()`

##### 材质的纹理贴图
材质的纹理贴图的意思就是说，threejs可以支持我们自己倒入图片作为纹理贴图，添加到相应的材质中。

实例代码：
```javascript
// 实例化一个加载器
var loader = new THREE.TextureLoader();
// 加载一个资源
loader.load(
    // 资源链接
    'src/img/chess.png',
    // 资源加载完成后的回调函数
    function ( texture ) {
        // do something with the texture
        var material = new THREE.MeshBasicMaterial( {
            map: texture
         } );
        var plane = new THREE.PlaneGeometry(4, 4);
        var cube = new THREE.Mesh(plane, material);
        scene.add(cube);
        renderer.render(scene, camera); // 导入纹理之前，已经完成了一次渲染，所以导入之后还需要重新渲染
    },
    // 资源加载过程中的回调函数
    function ( xhr ) {
        console.log( (xhr.loaded / xhr.total * 100) + '% loaded' );
    },
    // 资源下载出错时的回调函数
    function ( xhr ) {
        console.log( 'An error happened' );
    }
);
```

#### 网格
网格是最常用的一种物体。网格是由顶点、边、面等组成的物体；其他物体包括线段（Line）、骨骼（Bone）、粒子系统（ParticleSystem）等。
网格的构造函数是：`Mesh(geometry, material)`

##### 修改属性
除了在构造函数中指定材质，在网格被创建后，也能对材质进行修改：
```javascript
// 实例化一个网格
var material = new THREE.MeshLambertMaterial({
    color: 0xffff00
});
var geometry = new THREE.CubeGeometry(1, 2, 3);
var mesh = new THREE.Mesh(geometry, material);
scene.add(mesh);

// 修改网格颜色
mesh.material = new THREE.MeshLambertMaterial({
    color: 0xff0000
});
// 位置
mesh.position.z = 1;
mesh.position.set(1.5, -0.5, 0);
mesh.position = new THREE.Vector3(1.5, -0.5, 0);
// 缩放,具体方法与上例相同，分别表示沿x、y、z三轴缩放或旋转。
mesh.scale =
// 旋转,具体方法与上例相同，分别表示沿x、y、z三轴缩放或旋转。
mesh.rotation =
```

### 动画
正如动画片的原理一样，动画的本质是利用了人眼的视觉暂留特性，快速地变换画面，从而产生物体在运动的假象。而对于Three.js程序而言，动画的实现也是通过在每秒中多次重绘画面实现的。
所以可以使用`setInterval`来实现，也可以用：`requestAnimationFrame`。

#### 使用stat.js观察FPS
stat.js是Three.js的作者Mr. Doob的另一个有用的JavaScript库。stat.js就能提供实时监测动画效果。实例图如下：

![statjs](/images/statjs.png)

实例代码：
```javascript
// 对其初始化并将其添加至屏幕右上角
var stat = null;

function init() {
    stat = new Stats();
    stat.domElement.style.position = 'absolute';
    stat.domElement.style.right = '0px';
    stat.domElement.style.top = '0px';
    document.body.appendChild(stat.domElement);

    // Three.js init ...
}

// 在动画重绘函数draw中调用stat.begin();与stat.end();分别表示一帧的开始与结束
function draw() {
    stat.begin();

    mesh.rotation.y = (mesh.rotation.y + 0.01) % (Math.PI * 2);
    renderer.render(scene, camera);

    stat.end();
}
```

### 外部模型
Three.js允许用户导入由3ds Max等工具制作的三维模型，并添加到场景中。

Three.js有一系列导入外部文件的辅助函数，是在three.js之外的，使用前需要额外下载，在https://github.com/mrdoob/three.js/tree/master/examples/js/loaders可以找到。

`*.obj`是最常用的模型格式，导入`*.obj`文件需要OBJLoader.js；导入带`*.mtl`材质的`*.obj`文件需要MTLLoader.js以及OBJMTLLoader.js。另有PLYLoader.js、STLLoader.js等分别对应不同格式的加载器，可以根据模型格式自行选择。

目前，支持的模型格式有：

- *.obj
- *.obj, *.mtl
- *.dae
- *.ctm
- *.ply
- *.stl
- *.wrl
- *.vtk

无材质模型导入实例代码：
```javascript
// 本例子需要OBJLoader.js
// 创建loader实例
var loader = new THREE.OBJLoader();
loader.load('../lib/port.obj', function(obj) {
    mesh = obj; //储存到全局变量中
    scene.add(obj);
});
```

有材质模型导入实例代码：
```javascript
// 本例子需要MTLLoader.js与OBJMTLLoader.js
var loader = new THREE.OBJMTLLoader();
loader.addEventListener('load', function(event) {
    var obj = event.content;
    mesh = obj;
    scene.add(obj);
});
loader.load('../lib/port.obj', '../lib/port.mtl');
```

### 光与影
图像渲染的丰富效果很大程度上也要归功于光与影的利用。

#### 环境光
环境光是指场景整体的光照效果，是由于场景内若干光源的多次反射形成的亮度一致的效果，通常用来为整个场景指定一个基础亮度。因此，环境光没有明确的光源位置，在各处形成的亮度也是一致的。其构造函数是：`new THREE.AmbientLight(hex)`。

关于环境光有个好玩的现象：
```
当我们将环境光设置为红色，场景内同样放置绿色0x00ff00和白色0xffffff的长方体。
渲染的结果是这两个长方体都被渲染成了环境光的红色！！！

这一结果可能有些出乎你的意料。其实，环境光并不在乎物体材质的color属性，而是ambient属性。ambient属性的默认值是0xffffff。
ambient属性表示的是物体反射环境光的能力。对于0x00ff00的物体，红色通道是0，而环境光是完全的红光，因此该长方体不能反射任何光线，最终的渲染颜色就是黑色；而对于0xffffff的白色长方体，红色通道是0xff，因而能反射所有红光，渲染的颜色就是红色。
```

当环境光不是白色或灰色的时候，渲染的效果往往会很奇怪。因此，环境光通常使用白色或者灰色，作为整体光照的基础。

#### 点光源
点光源是不计光源大小，可以看作一个点发出的光源。点光源照到不同物体表面的亮度是线性递减的，因此，离点光源距离越远的物体会显得越暗。
点光源的构造函数是：`new THREE.PointLight(hex, intensity, distance)`

#### 平行光
对于任意平行的平面，平行光照射的亮度都是相同的，而与平面所在位置无关。
平行光的构造函数是：`new THREE.DirectionalLight(hex, intensity)`

#### 聚光灯
聚光灯是一种特殊的点光源，它能够朝着一个方向投射光线。聚光灯投射出的是类似圆锥形的光线，这与我们现实中看到的聚光灯是一致的。

其构造函数为：
```javascript
new THREE.SpotLight(hex, intensity, distance, angle, exponent)
```
相比点光源，多了angle和exponent两个参数。angle是聚光灯的张角，缺省值是Math.PI / 3，最大值是Math.PI / 2；exponent是光强在偏离target的衰减指数（target需要在之后定义，缺省值为(0, 0, 0)），缺省值是10。

在调用构造函数之后，除了设置光源本身的位置，一般还需要设置target：
```javascript
light.position.set(x1, y1, z1);
light.target.position.set(x2, y2, z2);
```
除了设置light.target.position的方法外，如果想让聚光灯跟着某一物体移动（就像真的聚光灯！），可以target指定为该物体：
```javascript
var cube = new THREE.Mesh(new THREE.CubeGeometry(1, 1, 1), new THREE.MeshLambertMaterial({color: 0x00ff00}));
var light = new THREE.SpotLight(0xffff00, 1, 100, Math.PI / 6, 25);
light.target = cube;
```

#### 阴影
在Three.js中，能形成阴影的光源只有`THREE.DirectionalLight`与`THREE.SpotLight`；而相对地，能表现阴影效果的材质只有`THREE.LambertMaterial`与`THREE.PhongMaterial`。因而在设置光源和材质的时候，一定要注意这一点。

实例代码：
```javascript
// 首先，我们需要在初始化时，告诉渲染器渲染阴影：
renderer.shadowMapEnabled = true;
// 然后，对于光源以及所有要产生阴影的物体调用：
xxx.castShadow = true;
// 对于接收阴影的物体调用：
xxx.receiveShadow = true;
```

对于聚光灯，需要设置shadowCameraNear、shadowCameraFar、shadowCameraFov三个值，类比我们在第二章学到的透视投影照相机，只有介于shadowCameraNear与shadowCameraFar之间的物体将产生阴影，shadowCameraFov表示张角。

对于平行光，需要设置shadowCameraNear、shadowCameraFar、shadowCameraLeft、shadowCameraRight、shadowCameraTop以及shadowCameraBottom六个值，相当于正交投影照相机的六个面。同样，只有在这六个面围成的长方体内的物体才会产生阴影效果。

为了看到阴影照相机的位置，通常可以在调试时开启light.shadowCameraVisible = true。

![shadow](/images/shadow.jpeg)

如果想要修改阴影的深浅，可以通过设置shadowDarkness，该值的范围是0到1，越小越浅。

另外，这里实现阴影效果的方法是Shadow Mapping，即阴影是作为渲染前计算好的贴图贴上去的，因而会受到贴图像素大小的限制。所以可以通过设置shadowMapWidth与shadowMapHeight值控制贴图的大小，来改变阴影的精确度。

而如果想实现软阴影的效果，可以通过renderer.shadowMapSoft = true;方便地实现。




## 参考
1. [Three.js入门指南](http://www.ituring.com.cn/book/1272)