---
title: "ThreeBox 构建弧线"
date: 2023-02-02
draft: false
author: 'zhuzhu'
tags: [
    "ThreeBox"
]
categories: [ "ThreeBox" ]
---

<a name="Pcf1L"></a>
## 官网示例的效果图：
1. 代码见参考文献（1）

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1660789/1669359257871-cec4c977-dad7-4b92-831c-1afa75c9841a.png#averageHue=%235c6046&clientId=u00957b73-b343-4&from=paste&height=670&id=lmxUX&name=image.png&originHeight=670&originWidth=1187&originalType=binary&ratio=1&rotation=0&showTitle=false&size=236198&status=done&style=none&taskId=u0522fab6-6290-4cf2-87ce-0c2c7d2a9b9&title=&width=1187)
<a name="MgeE9"></a>
## 代码解读（以构建一条弧线为例）：
<a name="yTCjk"></a>
### 概述（整体思路）：
（1）明确要生成几个点，`下面代码是假设生成25个点。`<br />（2）先生成x坐标和y坐标，以及随机生成一个高度值。`下面代码是中有写这部分实现方式。`<br />（3）根据sin正弦定理，[0,180]对应的sin值得区间为[0, 1]。`Math.PI 除了等于3.1415...之外，还可以看作是Π，一周的弧度是2Π。所以Π是180度。`<br />（4）通过sin值 乘以 高度 构建每个点位的Z轴值。<br />（5）然后收集整理全部坐标即可！
<a name="OHfHT"></a>
### 参考坐标图：

1. **红色代表坐标轴，**
2. **黑色的点代表在xy轴上的每个【x，y】点，**
3. **黄色线是将x, y轴的各个点连接起来的连接线**， 
4. **绿色线即构建之后的弧线**，
5. **浅墨绿色的线是每个黑点 乘以 最大高度得到的高度线。**

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1660789/1669359753289-fee39685-d89d-411b-9f1f-0666f2d41de6.png#averageHue=%23fef9f7&clientId=u00957b73-b343-4&from=paste&height=336&id=u1b8d1f54&name=image.png&originHeight=292&originWidth=355&originalType=binary&ratio=1&rotation=0&showTitle=false&size=10839&status=done&style=none&taskId=uc73158cf-3a3b-4bb4-a413-168aa70cb9b&title=&width=409)
<a name="PTDxO"></a>
### 代码块:
```javascript
buildOneArcLine(){
  
  let line = new Array();

  // 假设我们构建25个点
  let arcSegments = 25;

  // 目标范围 [[-150，150], [-70, 70]]，下面假设 destination = [40, 60];
  let destination = [300 * (Math.random() - 0.5), 140 * (Math.random() - 0.5)];

  // 每条线的最大高度，是个常量,内部算法可不用在意, 下面假设 maxElevation = height
  let maxElevation = Math.pow(Math.abs(destination[0] * destination[1]), 0.5) * 80000;

  // 则下面的语句会得到： [40, 60] ==》 [1.6, 2.4] 即： increment = [1.6, 2.4];
  let increment = destination.map(function (direction) {
    return direction / arcSegments;
  })

  for (let l = 0; l <= arcSegments; l++) {

    /** 下面语句的结果是： 先构建一组[[x,y],[x,y],[x,y],...[x,y]]的点, 即: [[0,0],[1.6,2.4],[3.2,4.8],...[40,60]]
				*  我们考虑以[0,0]点为原点看成一个xyz的三轴坐标。 贴着地面的为x、y轴，垂直于地球的为z轴。 
				*  则下面语句的结果是获取一条在x、y轴的直线。
				*/ 
    let waypoint = increment.map(function (direction) {
      return direction * l
    })
    /** 下面代码中 
				 * 参数分析： （1）maxElevation参数为垂直与球体的线，即Z轴 （2）arcSegments是总点数 （3）l是位于[0, arcSegments]之间的一个数
				 * （4）Math.PI 除了等于3.1415...之外，还可以看作是Π，一周的弧度是2Π。所以Π是180度。
				 * 则：Math.PI*l/arcSegments 这个公式是在计算一个 `半弧`， 这个的值🐟范围为： 0 - 180。
				 * Math.sin(Math.PI*l/arcSegments)的值为： sin(0) - sin(180) 即 0 - 1;
				 * 所以我们需要想象一下Z轴的值应该和最大高度（maxElevation）有关，
				 */
    // 
    let waypointElevation = Math.sin(Math.PI * l / arcSegments) * maxElevation; // 获取的是当前点的高度
    // 这句话刚好把height值放入了每一个[x, y]数组中，组成：[x, y, height] 比如 waypoint = [1.6, 2.4, height]
    waypoint.push(waypointElevation);
    // 循环25次后，这个数组中存入了25个[x, y, height]的点。构成一个弧线！
    line.push(waypoint);
  }
}
```

<a name="hzjkD"></a>
## 参考文献：
[threebox/examples at master · jscastro76/threebox](https://github.com/jscastro76/threebox/tree/master/examples#02-linehtml)  




