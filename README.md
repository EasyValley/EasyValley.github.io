

``` javascript


import * as THREE from 'three';
import '../css/shared.scss';
import * as OrbitControlsFunction from 'three-orbit-controls';
var OrbitControls = OrbitControlsFunction(THREE);



/**
 * 创建场景
 */
var scene = new THREE.Scene();
scene.background = new THREE.Color(0xcce0ff);

/**
 * 创建相机
 */
var camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.set(10, 10, 10);
var orgin = new THREE.Vector3()
new OrbitControls(camera);


/**
 * 创建渲染器
 */
var renderer = new THREE.WebGLRenderer({
    antialias: true
});
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setPixelRatio(2);
document.body.appendChild(renderer.domElement);

/**
 * 监听尺寸改变
 */
window.addEventListener('resize', () => {
    renderer.setSize(window.innerWidth, window.innerHeight);
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
})

/**
 * 添加几何
 */
var loader = new THREE.TextureLoader();
var cubeTextureLoader = new THREE.CubeTextureLoader();
var geometry = new THREE.BoxGeometry(1, 1, 1);
var picArr = [
    require('./pos-x.png'), require('./neg-x.png'),
    require('./pos-y.png'), require('./neg-y.png'),
    require('./pos-z.png'), require('./neg-z.png')
]
//map 贴 图
//envMap 环境反关
var material = new THREE.MeshLambertMaterial({
    map: loader.load(require('./pic.png')),
    envMap: cubeTextureLoader.load(picArr),
    side: THREE.DoubleSide
});
var bricks = [
    new THREE.Vector2(0, .666),
    new THREE.Vector2(.5, .666),
    new THREE.Vector2(.5, 1),
    new THREE.Vector2(0, 1)
];

var clouds = [
    new THREE.Vector2(.5, .666),
    new THREE.Vector2(1, .666),
    new THREE.Vector2(1, 1),
    new THREE.Vector2(.5, 1)
];

var crate = [
    new THREE.Vector2(0, .333),
    new THREE.Vector2(.5, .333),
    new THREE.Vector2(.5, .666),
    new THREE.Vector2(0, .666)
];

var stone = [
    new THREE.Vector2(.5, .333),
    new THREE.Vector2(1, .333),
    new THREE.Vector2(1, .666),
    new THREE.Vector2(.5, .666)
];

var water = [
    new THREE.Vector2(0, 0),
    new THREE.Vector2(.5, 0),
    new THREE.Vector2(.5, .333),
    new THREE.Vector2(0, .333)
];

var wood = [
    new THREE.Vector2(.5, 0),
    new THREE.Vector2(1, 0),
    new THREE.Vector2(1, .333),
    new THREE.Vector2(.5, .333)
];


// 清除现有的UV映射，geometry对象的faceVertexUvs属性包含该geometry各个面的坐标映射。
geometry.faceVertexUvs[0] = [];

// 立方体的每个面实际上是由2个三角形组成的。所以我们必须单独映射每个三角形
// 个面的顶点坐标的定义顺序必须遵循逆时针方向。为了映射底部三角形，我们需要使用的顶点指数0，1和3，
// 而要映射顶部三角形，我们需要使用索引1，2，和顶点的3。
geometry.faceVertexUvs[0][0] = [bricks[0], bricks[1], bricks[3]];
geometry.faceVertexUvs[0][1] = [bricks[1], bricks[2], bricks[3]];

geometry.faceVertexUvs[0][2] = [clouds[0], clouds[1], clouds[3]];
geometry.faceVertexUvs[0][3] = [clouds[1], clouds[2], clouds[3]];

geometry.faceVertexUvs[0][4] = [crate[0], crate[1], crate[3]];
geometry.faceVertexUvs[0][5] = [crate[1], crate[2], crate[3]];

geometry.faceVertexUvs[0][6] = [stone[0], stone[1], stone[3]];
geometry.faceVertexUvs[0][7] = [stone[1], stone[2], stone[3]];

geometry.faceVertexUvs[0][8] = [water[0], water[1], water[3]];
geometry.faceVertexUvs[0][9] = [water[1], water[2], water[3]];

geometry.faceVertexUvs[0][10] = [wood[0], wood[1], wood[3]];
geometry.faceVertexUvs[0][11] = [wood[1], wood[2], wood[3]];

var cube = new THREE.Mesh(geometry, material);
scene.add(cube);


/**
 * skybox全景
 */
var skyGeometry = new THREE.BoxBufferGeometry(1000, 1000, 1000);

var materials = [];


for (var i = 0; i < 6; i++) {
    materials.push(new THREE.MeshBasicMaterial({
        map: loader.load(picArr[i]),
        side: THREE.DoubleSide
    }));
}

var skyBox = new THREE.Mesh(skyGeometry, materials);
scene.add(skyBox);



/**
 * 添加环境光
 */
var light = new THREE.AmbientLight(0xffffff, 1); // soft white light
scene.add(light);

/**
 * 添加聚光灯
 */
var spotLight = new THREE.SpotLight('#ffffff', 0.8);
spotLight.position.set(10, 10, 10);
scene.add(spotLight);

/**
 * 聚光灯照射的对象
 */
var targetObject = new THREE.Object3D();
scene.add(targetObject);
spotLight.target = targetObject;

var hightLight = {
    i: 0.5,
    a: 0.01,
    update: function () {
        this.i += this.a;
        if (this.i > 2 || this.i < 0.3) {
            this.a = -this.a;
        }
    }
}

/**
 * 添加辅助性
 */
scene.add(new THREE.AxesHelper(50));
var degree = 0;
function animate() {
    degree += 0.01;
    camera.position.x = Math.cos(degree) * 16;
    camera.position.z = Math.sin(degree) * 16;

    requestAnimationFrame(animate);
    renderer.render(scene, camera);
    hightLight.update();
    spotLight.position.set(hightLight.i, hightLight.i, hightLight.i);
    camera.lookAt(orgin);

}
animate();
```