# D3.js Path 相关内容

## 基本概念

饼图和线图都是使用 `path` 元素绘制的。

- `d` 属性：定义路径的绘制方式。
- `fill`：填充颜色。
- `stroke`：边框颜色。
- `stroke-width`：边框宽度。
- `transform`：变换。

`path` 的 `d` 属性是一系列命令和参数的组合，例如：

- `M 10 10`：移动到 (10, 10)
- `H 10`：水平线到 x = 10
- `Z`：关闭路径

### 三次贝塞尔曲线

语法示例：

- 起点 `M x,y`
- 控制点 `C x1 y1, x2 y2, x y`



## 画线图和饼图

- `d3.line`：画折线图
- `d3.arc`：画饼图

### 接口

- `d3.line().x().y()`
- `d3.arc().innerRadius().outerRadius()`



## 示例代码

### 画折现图

```javascript
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <svg id="container" width="1200" height="600" style="margin: 0 auto"></svg>
    <script type="module">
      import * as d3 from "https://cdn.jsdelivr.net/npm/d3@7/+esm";

      const svg = d3.select("#container");
      const margin = {
        top: 100,
        right: 120,
        bottom: 120,
        left: 120,
      };

      const width = svg.attr("width");
      const height = svg.attr("height");
      const innerWidth = width - margin.left - margin.right;
      const innerHeight = height - margin.top - margin.bottom;

      d3.csv("/class3-path/shampoo_sales.csv").then((data) => {
        data = data.map((datum) => {
          datum.sale = +datum.sale;
          datum.day = new Date(datum.day);
          return datum;
        });
        console.log("data", data);

        // field: day and sale
        // 定义x轴和y轴比例尺
        const xScale = d3
          .scaleTime()
          .domain(d3.extent(data, (d) => d.day))
          .range([0, innerWidth]);
        const yScale = d3
          .scaleLinear()
          .domain(d3.extent(data.map((datum) => datum.sale)))
          .range([innerHeight, 0]); // y轴从下到上

        // 绘图区域
        const mainGroup = svg
          .append("g")
          .attr("id", "mainGroup")
          .attr("transform", `translate(${margin.left} ${margin.top})`);

        // x轴和y轴

        // x axis method
        // 这里tickSize是从上往下画的
        const xAxis = d3.axisBottom(xScale);

        // 这里的tickSize默认是从右往左的，所以要取负的反方向
        const yAxis = d3.axisLeft(yScale);

        let xAxisGroup = mainGroup
          .append("g")
          .call(xAxis)
          .attr("id", "xAxis")
          .attr("transform", `translate(0, ${innerHeight})`);
        let yAxisGroup = mainGroup.append("g").call(yAxis).attr("id", "yAxis");

        // 利用d3.line描述path的d属性来绘制折线
        const line = d3
          .line()
          .x((d) => xScale(d.day))
          .y((d) => yScale(d.sale));
        //   .curve(d3.curveStep); // 点之间的差值方式
        // 在mainGroup里添加path
        mainGroup
          .append("path")
          .attr("id", "line")
          .attr("d", line(data))
          .attr("fill", "none")
          .attr("stroke", "#000");
      });
    </script>
  </body>
</html>

```



### 画饼图



```
const pathArc = d3.arc()
  .innerRadius(0)
  .outerRadius(100);

pathArc({ startAngle, endAngle });
```



## 插值方法

两个点之间的连线可以通过各种插值方法来实现：

```javascript
d3.line()
  .x(d => d.x)
  .y(d => d.y)
  .curve(d3.curveCardinal);
```



## 时间数据处理与时间比例尺



```javascript
const xScale = d3.scaleTime();
xScale.domain(d3.extent(data, d => d.date)).range([0, width]);
```



### 单一图元数据绑定

折线图对应单一图元：

```javascript
selection.datum(data);  // 因为折线图的 path 是一个图元
```

而每一个pie则对应一个图元！



## 饼图

饼图适用于将值数组转换为比例数组：

```javascript
const pie = d3.pie()
  .value(d => d.value);
```



一个非常简单的饼图：
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <svg id="container" width="200" height="200" style="margin: 0 auto"></svg>
    <script type="module">
      import * as d3 from "https://cdn.jsdelivr.net/npm/d3@7/+esm";

      const svg = d3.select("#container");

      svg
        .append("path")
        .attr("transform", "translate(100,100)")
        .attr(
          "d",
          d3.arc()({
            innerRadius: 50,
            outerRadius: 100,
            startAngle: -Math.PI / 2,
            endAngle: Math.PI / 2,
          })
        )
        .attr("fill", "#f00")
        .attr("stroke", "#000");
    </script>
  </body>
</html>

```





## 离散比例尺

用于将文本映射到颜色，比如饼图里可以把文本转换成不同的颜色：

```javascript
const s = d3.scaleOrdinal()
  .domain(['a', 'b', 'c'])
  .range(['red', 'yellow', 'green'
```



## 稍微复杂点的饼图情形：arc和pie相结合

d3.pie可以把一个离散的对象数组，计算出各个部分的占比：

```javascript
city,population
沈阳市,829.4
大连市,698.75
鞍山市,344.0 
铁岭市,299.8
锦州市,296.4
朝阳市,295
葫芦岛市,277.0
营口市,243.8
丹东市,239.5
抚顺市,210.7
阜新市,186.2
辽阳市,183.7
本溪市,147.63
盘锦市,143.65
```

对于上面的数据，可以用d3.pie计算出各个部分的占比，arcData里包含了各个弧的占比，除了内外半径：
```javascript
[
    {
        "data": {
            "city": "沈阳市",
            "population": "829.4"
        },
        "index": 0,
        "value": 829.4,
        "startAngle": 0,
        "endAngle": 1.1855848768577961,
        "padAngle": 0
    },
    {
        "data": {
            "city": "大连市",
            "population": "698.75"
        },
        "index": 1,
        "value": 698.75,
        "startAngle": 1.1855848768577961,
        "endAngle": 2.184412261357899,
        "padAngle": 0
    },
    {
        "data": {
            "city": "鞍山市",
            "population": "344.0 "
        },
        "index": 2,
        "value": 344,
        "startAngle": 2.184412261357899,
        "endAngle": 2.6761426660348726,
        "padAngle": 0
    },
    {
        "data": {
            "city": "铁岭市",
            "population": "299.8"
        },
        "index": 3,
        "value": 299.8,
        "startAngle": 2.6761426660348726,
        "endAngle": 3.104691431506258,
        "padAngle": 0
    },
    {
        "data": {
            "city": "锦州市",
            "population": "296.4"
        },
        "index": 4,
        "value": 296.4,
        "startAngle": 3.104691431506258,
        "endAngle": 3.5283800708849062,
        "padAngle": 0
    },
    {
        "data": {
            "city": "朝阳市",
            "population": "295"
        },
        "index": 5,
        "value": 295,
        "startAngle": 3.5283800708849062,
        "endAngle": 3.950067481872427,
        "padAngle": 0
    },
    {
        "data": {
            "city": "葫芦岛市",
            "population": "277.0"
        },
        "index": 6,
        "value": 277,
        "startAngle": 3.950067481872427,
        "endAngle": 4.346024813545455,
        "padAngle": 0
    },
    {
        "data": {
            "city": "营口市",
            "population": "243.8"
        },
        "index": 7,
        "value": 243.8,
        "startAngle": 4.346024813545455,
        "endAngle": 4.694524443371752,
        "padAngle": 0
    },
    {
        "data": {
            "city": "丹东市",
            "population": "239.5"
        },
        "index": 8,
        "value": 239.5,
        "startAngle": 4.694524443371752,
        "endAngle": 5.036877443139587,
        "padAngle": 0
    },
    {
        "data": {
            "city": "抚顺市",
            "population": "210.7"
        },
        "index": 9,
        "value": 210.7,
        "startAngle": 5.036877443139587,
        "endAngle": 5.338062316004233,
        "padAngle": 0
    },
    {
        "data": {
            "city": "阜新市",
            "population": "186.2"
        },
        "index": 10,
        "value": 186.2,
        "startAngle": 5.338062316004233,
        "endAngle": 5.604225692024153,
        "padAngle": 0
    },
    {
        "data": {
            "city": "辽阳市",
            "population": "183.7"
        },
        "index": 11,
        "value": 183.7,
        "startAngle": 5.604225692024153,
        "endAngle": 5.8668154459170605,
        "padAngle": 0
    },
    {
        "data": {
            "city": "本溪市",
            "population": "147.63"
        },
        "index": 12,
        "value": 147.63,
        "startAngle": 5.8668154459170605,
        "endAngle": 6.077844979761426,
        "padAngle": 0
    },
    {
        "data": {
            "city": "盘锦市",
            "population": "143.65"
        },
        "index": 13,
        "value": 143.65,
        "startAngle": 6.077844979761426,
        "endAngle": 6.283185307179586,
        "padAngle": 0
    }
]
```

因此可以画一个更复杂的圆环：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <svg
      id="container"
      width="600"
      height="400"
      style="margin: 0 auto; display: block"
    ></svg>
    <script type="module">
      import * as d3 from "https://cdn.jsdelivr.net/npm/d3@7/+esm";

      const svg = d3.select("#container");
      const width = svg.attr("width");
      const height = svg.attr("height");

      d3.csv("/class3-path/liaoning.csv").then((data) => {
        console.log("data ", data);
        const pie = d3.pie().value((d) => d.population);
        const arcData = pie(data);

        console.log("arcData ", arcData);
        // arcData里包含了各个弧的占比，除了内外半径

        // 预先定义内外半径
        const arcGenerator = d3
          .arc()
          .innerRadius(50) // 内半径
          .outerRadius(150); // 外半径

        // 颜色比例尺，用内置的颜色生成离散序列？
        const colorScale = d3.scaleOrdinal(d3.schemeCategory10);

        // 一个对象对应一个path画成的弧
        svg
          .selectAll(".arcs")
          .data(arcData, (d) => d.data["city"])
          .join(
            // 添加path
            (enter) =>
              enter
                .append("path")
                .attr("class", "arcs")
                .attr("transform", `translate(${width / 2}, ${height / 2})`) // 移动圆心到中间
                .attr("d", (d) => arcGenerator(d)) // pie算出了其他的arc需要的信息，这里join之后只需要传入内外半径
                .attr("fill", (d, i) => colorScale(i)) // 设置颜色
          );

        // 画label
        const arcOuter = d3.arc().innerRadius(180).outerRadius(180);
        svg
          .append("g")
          .attr("transform", `translate(${width / 2}, ${height / 2})`)
          .selectAll("text")
          .data(arcData)
          .join("text")
          .attr("transform", (d) => {
            return `translate(${arcOuter.centroid(d)})`;
          })
          .attr("text-anchor", "middle")
          .text((d) => d.data.city);
      });
    </script>
  </body>
</html>

```

