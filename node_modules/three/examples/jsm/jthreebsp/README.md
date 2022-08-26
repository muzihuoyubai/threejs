## 说明

本项目基于 [fox19920726](https://github.com/fox19920726) 的[threebsp](https://github.com/fox19920726/threebsp) 进行修改，感谢原作者开源的代码。四年以前一直使用 Three.js 进行 web 3D 展示的开发工作，弄了两年，后来就没怎么用过了，最近正好又要用一下，发现变化还不小，以前的写的一些库已经不适用于新版的 Three.js 了，百度查资料才知道了 ThreeBSP 这个库，之前都是部门内自己开发各种库，都不知道还有这种开源神器，不过用的时候发现跑不起来，一看 github 有一段时间没更新了，自己看源码发现新版本的 Geometry 基本上废弃了，都是使用 BufferGeometry 了，但是 threebsp 还没进行相关代码的更新，只能自己改造一下，也是很久没研究了，相关知识忘得差不多了，简单修改了一下，基本上能运行了，肯定还有很多有不足的地方，欢迎各位大神进行优化修改。

## 模块化 ThreeBSP 包

### Vue 中的用法（react 暂时不做示例）

安装 Threebsp 包(因为发布到 npm 时包名不能和之前相同，所以发布时改了下名字，叫 jthreebsp)

```sh
npm install jthreebsp
```

必须在前面先引入 THREE 包

```sh
import * as THREE from 'three'
```

再把 THREE 当作参数传入方可使用

```sh
const ThreeBSP = require('jthreebsp')(THREE)
```

### 案例

createMesh 方法

```sh
createMesh(geom) {
  //  创建一个线框纹理
  const wireFrameMat = new THREE.MeshBasicMaterial({
    opacity: 0.5,
    wireframeLinewidth: 0.5
  });
  wireFrameMat.wireframe = true;

  // 创建模型
  const mesh = new THREE.Mesh(geom, wireFrameMat);

  return mesh;
}
```

创建球形几何体

```sh
const sphereGeometry = new THREE.SphereGeometry(50, 20, 20)

const sphere = this.createMesh(sphereGeometry)
```

创建立方体几何体

```sh
const cubeGeometry = new THREE.BoxGeometry(30, 30, 30)

const cube = this.createMesh(cubeGeometry)

cube.position.x = -50
```

生成 ThreeBSP 对象

```sh
const sphereBSP = new ThreeBSP(sphere)

const cubeBSP = new ThreeBSP(cube)
```

以下计算方式取其一即可

进行差集计算（使用该函数可以在第一个几何体中移除两个几何体重叠的部分来创建新的几何体。）

```sh
const resultBSP = sphereBSP.subtract(cubeBSP)
```

进行并集计算（使用该函数可以将两个几何体联合起来创建出一个新的几何体。）

```sh
const resultBSP = sphereBSP.union(cubeBSP)
```

进行交集计算（使用该函数可以基于两个现有几何体的重合的部分定义此几何体的形状。）

```sh
const resultBSP = sphereBSP.intersect(cubeBSP)
```

从 BSP 对象内获取到处理完后的 mesh 模型数据

```sh
const result = resultBSP.toMesh()
```

更新模型的面和顶点的数据

```sh
result.geometry.computeFaceNormals()

result.geometry.computeVertexNormals()
```

重新赋值一个纹理

```sh
const material = new THREE.MeshPhongMaterial({ color: 0x00ffff })

result.material = material
```

将计算出来模型添加到场景当中

```sh
this.scene.add(result)
```
