---
title: jsts-polygon-study
categories:
  - coding
  - js
tags:
  - polygon
date: 2018-05-14 09:57:56
update: 2018-05-14 09:57:56
---

### 一些多边形的判断和操作

```javascript
/**
 * 点到线的映射坐标
 * @param  {{x: Number, y: Number}} p 点
 * @param  {{x: Number, y: Number}} a 线段起始点
 * @param  {{x: Number, y: Number}} b 线段终止点
 * @param  {{x: Number, y: Number}} 线上的点
 */
function getFocusPoint(p, a, b) {
  let point = {};
  // 如果a.x === b.x 说明是条竖着的线
  if (a.x - b.x === 0) {
    p.x = a.x;
    p.y = p.y;
  } else {
    let k = (a.y - b.y) / (a.x - b.x);
    let l = a.y - k * a.x;
    let m = p.x + k * p.y;

    point.x = (m - k * l) / (k * k + 1);
    point.y = k * point.x + l;
  }

  return point;
}

/**
 * 计算点到线段的距离
 * @param  {{x: Number, y: Number}} p 点
 * @param  {{x: Number, y: Number}} a 线段起始点
 * @param  {{x: Number, y: Number}} b 线段终止点
 * @return {Number}   距离
 */
function pointToSegmentDist(p, a, b) {
  let AB = [b.x - a.x, b.y - a.y]
  let AP = [p.x - a.x, p.y - a.y]
  let AB_AP = AB.x * AP.x + AB.y * AP.y
  let distAB2 = AB.x * AB.x + AB.y * AB.y
  let D = [a.x, a.y]
  if (distAB2 != 0) {
    let t = AB_AP / distAB2
    if (t > 1) {
      D = [b.x, b.y]
    } else if (t > 0) {
      D = [a.x + AB.x * t, a.y + AB.y * t]
    } else {
      D = [a.x, a.y]
    }
  }
  let AD = [p.x - a.x, p.y - a.y]
  return Math.sqrt(AD.x * AD.x + AD.y * AD.y)
}

/**
 * 判断点是否在多边形内部，一般用射线法
 * @param  {{x: Number, y: Number}} p 点
 * @param  {Array} polygon 多边形坐标
 * @return {Number} 1: 在内部，0: 在外部
 */
function pointInPolygon(p, polygon) {
  // 统计p点右边交点的个数
  var count = 0
  for (var i = 0; i < ring.length - 1; i++) {
    var a = polygon[i]
    var b = polygon[i + 1]
    if ((a.y > p.y !== b.y > p.y) &&
      (p.x < (b.x - a.x) * (p.y - a.y) / (b.y - a.y) + a.x)) count++;
  }
  return count % 2
}

/**
 * 判断点到多边形的最近距离
 * @param  {{x: Number, y: Number}} p 点
 * @param  {Array} polygon 多边形坐标
 * @return {Number} 最短距离；点在多边形内部，距离为正；在多边形外部，距离为负；在边上，距离为零
 */
function pointToPolygonDist(p, polygon) {
  // 统计p点右边交点的个数
  var count = 0
  var minDist = Infinity
  for (var i = 0; i < ring.length - 1; i++) {
    var a = polygon[i]
    var b = polygon[i + 1]
    if ((a.y > p.y !== b.y > p.y) &&
      (p.x < (b.x - a.x) * (p.y - a.y) / (b.y - a.y) + a.x)) count++;
    minDist = Math.min(minDist, pointToSegmentDist(p, a, b))
  }
  if (count % 2 === 0) minDist = -minDist
  return minDist
}
```

### jsts
JSTS是一个Javascript符合OGC规范的简单要素空间位置关系判定和函数的类库，JSTS也是总所周知的java类库JST的一个接口。

下面是一些jsts的操作：
```javascript
let a_coor = new jsts.geom.Coordinate(0, 0);
let b_coor = new jsts.geom.Coordinate(1, 0);
let c_coor = new jsts.geom.Coordinate(1, 1);
let d_coor = new jsts.geom.Coordinate(0, 1);

let factory = new jsts.geom.GeometryFactory();

let ring = factory.createLinearRing([a_coor, b_coor, c_coor, d_coor, a_coor]);
let line = factory.createLineString([new jsts.geom.Coordinate(0.5, 0), new jsts.geom.Coordinate(1, 0)]);
let polygon = factory.createPolygon([a_coor, b_coor, c_coor, d_coor, a_coor]);

let x = 0.5
let y = 0
let mouse_coor = new jsts.geom.Coordinate(x, y);
let mouse_point = factory.createPoint(mouse_coor);

let distance = new jsts.operation.distance.DistanceOp(mouse_point, ring);

console.log('nearest points', distance.nearestPoints());
console.log('distance', distance.distance())
console.log(ring.contains(mouse_point))
```

## 参考
1. [JSTS demonstration](http://bjornharrtell.github.io/jsts/)
2. [JTS javadoc](http://locationtech.github.io/jts/javadoc/)
3. [JSTS gh-pages](https://github.com/bjornharrtell/jsts/tree/gh-pages)