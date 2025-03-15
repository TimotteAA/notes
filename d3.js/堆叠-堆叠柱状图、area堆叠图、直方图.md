let stack = d3.stack()，创建堆叠柱状图

![image-20250207180120233](https://typora-1316630864.cos.ap-shanghai.myqcloud.com/image-20250207180120233.png)

基于d3.js官方文档的示例，可以这么理解：
![image-20250208162151082](https://typora-1316630864.cos.ap-shanghai.myqcloud.com/image-20250208162151082.png)

从下到上看，其实就是单个柱子的堆叠，而从左向右，x1-x0即为柱子高度



代码示例：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <style>
     #tooltip {
        position: absolute;
        opacity: 0;
        background: white;
        border: 1px solid #ccc;
        padding: 8px;
        pointer-events: none;
        font-family: Arial, sans-serif;
        font-size: 12px;
        box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.1);
    }
  </style>
  <body>
    <svg
      id="container"
      width="900"
      height="700"
      style="display: block; margin: 0 auto;"
    >
    <div id="tooltip" style=" opacity: 0; background: white; border: 1px solid #ccc; padding: 8px; pointer-events: none;">

    </div>

</svg>
    <script type="module">
      import * as d3 from "https://cdn.jsdelivr.net/npm/d3@7/+esm";
      const svg = d3.select("#container");
      const width = svg.attr("width");
      const height = svg.attr("height");
      const data = [
        {
          month: new Date(2015, 0, 1),
          apples: 3840,
          bananas: 1920,
          cherries: 960,
          dates: 400,
        },
        {
          month: new Date(2015, 1, 1),
          apples: 1600,
          bananas: 1440,
          cherries: 960,
          dates: 400,
        },
        {
          month: new Date(2015, 2, 1),
          apples: 640,
          bananas: 960,
          cherries: 640,
          dates: 400,
        },
        {
          month: new Date(2015, 3, 1),
          apples: 320,
          bananas: 480,
          cherries: 640,
          dates: 400,
        },
      ];
      const keys = ["apples", "bananas", "cherries", "dates"]
      // 指定堆叠的上个月不需要
      const stack = d3.stack().keys(keys);

      const stackData = stack(data);
      console.log("stackData ", stackData);

      // 对于某一行，可看到差值正好是某个key的数值
      // 如果从下往上看，则正好是一个柱子堆叠产生的结果   

      [
            [[   0, 3840], [   0, 1600], [   0,  640], [   0,  320]], // apples
            [[3840, 5760], [1600, 3040], [ 640, 1600], [ 320,  800]], // bananas
            [[5760, 6720], [3040, 4000], [1600, 2240], [ 800, 1440]], // cherries
            [[6720, 7120], [4000, 4400], [2240, 2640], [1440, 1840]], // dates
      ]

    // 绘制堆叠柱状图  
      const margin = {
        top: 100,
        right: 120,
        bottom: 120,
        left: 120
      }
      const innerWidth = width - margin.left - margin.right;
      const innerHeight = height - margin.top - margin.bottom;
      const mainGroup = svg.append("g").attr("id", "mainGroup").attr("transform", `translate(${margin.left}, ${margin.top})`)
        .attr("width", innerWidth).attr("height", innerHeight)


        // x轴是离散的
        const xScale = d3.scaleBand(data.map(d => d.month), [0, innerWidth]).padding(0.2);
        // y轴是柱状图范围
        const yExtent = d3.extent(stackData.flat(+Infinity)); // 计算所有层的最小值和最大值
        const yScale = d3.scaleLinear().range([innerHeight, 0]).domain(yExtent);

        const xAxis = mainGroup.append("g").attr("id", "xAxis").call(d3.axisBottom(xScale)).attr("transform", `translate(0 ${innerHeight})`);
        const yAxis = mainGroup.append("g").attr("id", "yAxis").call(d3.axisLeft(yScale).tickSize(-innerWidth).tickFormat(d => `${d}个`));

        // 颜色
        const color = d3.scaleOrdinal()
        .domain(keys)
        .range(d3.schemeSet3)

        // 先绑定柱子
        const bars = mainGroup.selectAll(".bars").data(stackData).join(enter => enter.append("g").attr("class", "bars"))
            .attr("fill", d => {
                // 这里d是stackData的第一层
                // console.log("d", d)
                return color(d.key)
            // 然后继续绑定chunk
            }).selectAll("dataChunk").data(d => {
                // console.log("d", d) // 这里还是第一层
                // 这里就是bar chunk array了
                return d;
            }).join(enter => enter.append("rect").attr("class", "dataChunk").attr("x", d => {
                // console.log("d", d) // 这里进入第二层
                return xScale(d.data.month);
            }).attr("y", d => yScale(d[1])).attr("width", xScale.bandwidth()).attr("height", d => yScale(d[0]) - yScale(d[1]))) // 这里得是d[0] - d[1]

        // 创建 tooltip 元素
        const tooltip = d3.select("#tooltip");
       // 绑定 mousemove 事件
    //    d3.selectAll(".dataChunk")
    //     .on("mousemove", (event, d) => {
    //       const [x, y] = d3.pointer(event);
    //       tooltip
    //         .html(`
    //           <strong>${d.data.month.toLocaleString('default', { month: 'long' })}</strong><br>
    //           ${d.key}: ${d[1] - d[0]}<br>
    //           Total: ${d[1]}
    //         `)
    //         .attr("x", x)
    //         .attr("y", y)
    //         .style("opacity", 1);
    //     })
    //     .on("mouseleave", () => {
    //       tooltip.style("opacity", 0);
    //     });

        d3.selectAll(".dataChunk").on("mousemove", (e, datum) => {
            const [x, y] = d3.pointer(e)
            console.log("datum", datum)
            console.log("x", x, "y", y);
            tooltip
            .html(`
              <strong>${d.data.month.toLocaleString('default', { month: 'long' })}</strong><br>
              ${d.key}: ${d[1] - d[0]}<br>
              Total: ${d[1]}
            `)    
                    .attr("x", x)
            .attr("y", y)
            .style("opacity", 1);
        })
    </script>
  </body>
</html>
```





![image-20250207180312177](https://typora-1316630864.cos.ap-shanghai.myqcloud.com/image-20250207180312177.png)



stack.orders，觉得堆叠顺序



## 堆叠面积图

d3.area，每一个area，都是随x的变化，在上y1和下y1进行插值而出：

![image-20250208131002784](https://typora-1316630864.cos.ap-shanghai.myqcloud.com/image-20250208131002784.png)



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
      width="1200"
      height="1200"
      style="display: block; margin: 0 auto"
    ></svg>
    <script type="module">
      import * as d3 from "https://cdn.jsdelivr.net/npm/d3@7/+esm";
      import dayjs from "https://cdn.jsdelivr.net/npm/dayjs@1.10.7/+esm";

      const svg = d3.select("#container");
      const width = svg.attr("width");
      const height = svg.attr("height");
      const margin = {
        top: 100,
        right: 120,
        bottom: 120,
        left: 120,
      };
      const innerWidth = width - margin.left - margin.right;
      const innerHeight = width - margin.top - margin.bottom;
      // 堆叠的对象
      const KEYS = [
        "Counter-Strike: Global Offensive",
        "Dota 2",
        "PLAYERUNKNOWN'S BATTLEGROUNDS",
        "Apex Legends",
        "Tom Clancy's Rainbow Six Siege",
        "Cyberpunk 2077",
        "Sekiro: Shadows Die Twice",
      ];

      // 初始化画布
      const initChart = () => {
        const mainGroup = svg
          .append("g")
          .attr("id", "mainGroup")
          .attr("transform", `translate(${margin.left}, ${margin.top})`);

        mainGroup
          .append("text")
          .attr("id", "title")
          .text("直方图练习")
          .attr("text-anchor", "middle")
          .attr("font-size", "40px")
          .attr("x", innerWidth / 2)
          .attr("y", "-60");
      };

      let xScale, yScale;
      // 对直方图的数据进行处理用
      const xValue = (d) => Math.log(d.occur);

      const initAxis = () => {
        const mainGroup = svg.select("#mainGroup");

        mainGroup
          .append("g")
          .attr("id", "xAxis")
          .call(d3.axisBottom(xScale).tickSize(-innerHeight))
          .attr("transform", `translate(0, ${innerHeight})`);

        mainGroup
          .append("g")
          .attr("id", "yAxis")
          .call(d3.axisLeft(yScale).tickSize(-innerWidth));

        let yAxisGroup = mainGroup.select("#yAxis");
        yAxisGroup
          .append("text")
          .attr("transform", `rotate(-90)`)
          .attr("x", -innerHeight / 2)
          .attr("y", -50)
          .attr("fill", "#333333")
          .text("Count (Log)")
          .attr("text-anchor", "middle") // Make label at the middle of axis.
          .attr("font-size", "3.5em")
          .attr("font-weight", "bold");
        yAxisGroup.selectAll(".domain").remove(); // we can select multiple tags using comma to seperate them and we can use space to signify nesting;

        let xAxisGroup = mainGroup.select("#xAxis");
        xAxisGroup
          .append("text")
          .attr("y", 60)
          .attr("x", innerWidth / 2)
          .attr("fill", "#333333")
          .attr("font-size", "3.5em")
          .attr("text-anchor", "middle")
          .attr("font-weight", "bold")
          .text("Occurrence");
        xAxisGroup.selectAll(".domain").remove();
      };

      d3.csv("/class5-stack/SteamCharts.csv").then((data) => {
        // 过滤需要的游戏名称
        data = data.filter((datum) => KEYS.includes(datum.gamename));

        // 处理日期并确保去除多余空格
        data.forEach((datum) => {
          datum.timeString = dayjs(
            `${datum.year}-${datum.month.trim()}`
          ).format("YYYY-MM"); // 去除month字段的空格
          datum.value = +datum.avg; // 确保将avg转换为数字，用于堆叠
        });

        // 获取所有的时间，进行排序
        const allDates = Array.from(
          new Set(data.map((datum) => datum.timeString))
        ).sort((d1, d2) => (dayjs(d1).isBefore(dayjs(d2)) ? -1 : 1)); // 按照日期顺序排序

        // 生成堆叠数据
        const stackData = [];
        for (const d of allDates) {
          let newItem = { timeString: d }; // 保留时间字符串而非Date对象
          KEYS.forEach((k) => {
            newItem[k] = data.find(
              (datum) => datum.timeString === d && datum.gamename === k
            );
            if (!newItem[k]) {
              newItem[k] = 0; // 如果该时间点没有该游戏的记录，设置为0
            } else {
              newItem[k] = newItem[k].avg;
            }
          });
          stackData.push(newItem);
        }
        // 上面的预处理老实说我没看懂...

        // 创建堆叠
        const stack = d3.stack().keys(KEYS);
        const stackedData = stack(stackData);

        // xScale 使用日期字符串作为 domain
        xScale = d3
          .scaleBand() // 使用scaleBand来处理离散的时间轴
          .range([0, innerWidth])
          .domain(allDates) // 用日期字符串作为 domain
          .padding(0.1);

        // yScale 设置
        yScale = d3
          .scaleLinear()
          .domain([
            0,
            d3.max(stackedData, (item1) => d3.max(item1, (item2) => item2[1])),
          ])
          .range([innerHeight, 0]);

        // area生成器，这里的d是stackedData[i]中的二维数组
        const area = d3
          .area()
          .x((d) => xScale(d.data.timeString)) // 使用xScale来绘制时间
          .y0((d) => yScale(d[0])) // 堆叠的底部
          .y1((d) => yScale(d[1])); // 堆叠的顶部
        // 这里一定要传入 xScale和yScale

        // 颜色序列
        const color = d3
          .scaleOrdinal()
          .range(d3.schemePaired.concat(d3.schemeCategory10));

        // 帮堆叠数据绑定stackedData
        const mainGroup = d3.select("#mainGroup");
        mainGroup
          .selectAll(".areas")
          .data(stackedData)
          .join((enter) =>
            enter
              .append("path")
              .attr("d", (d) => area(d)) // 使用area生成器来绘制堆叠区域
              .attr("fill", (_, i) => color(i))
          );

        initAxis();
      });

      initChart();
    </script>
  </body>
</html>

```





## 直方图

d3.bin，把数据按照某一区间进行存放，把对象数组转换成对象数组的数组，y轴是这一区间数量（数组长度）

d3.bin().value().thresholds([x1, x2, x3, x4, ..., xn])

可以用scale的ticks作为阈值：

histogram.threshold(xScale.ticks(20))

示例代码：
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
      width="1200"
      height="1200"
      style="display: block; margin: 0 auto"
    ></svg>
    <script type="module">
      import * as d3 from "https://cdn.jsdelivr.net/npm/d3@7/+esm";

      const svg = d3.select("#container");
      const width = svg.attr("width");
      const height = svg.attr("height");
      const margin = {
        top: 100,
        right: 120,
        bottom: 120,
        left: 120,
      };
      const innerWidth = width - margin.left - margin.right;
      const innerHeight = width - margin.top - margin.bottom;

      // 初始化画布
      const initChart = () => {
        const mainGroup = svg
          .append("g")
          .attr("id", "mainGroup")
          .attr("transform", `translate(${margin.left}, ${margin.top})`);

        mainGroup
          .append("text")
          .attr("id", "title")
          .text("直方图练习")
          .attr("text-anchor", "middle")
          .attr("font-size", "40px")
          .attr("x", innerWidth / 2)
          .attr("y", "-60");
      };

      let xScale, yScale;
      // 对直方图的数据进行处理用
      const xValue = (d) => Math.log(d.occur);

      const initAxis = () => {
        const mainGroup = svg.select("#mainGroup");

        mainGroup
          .append("g")
          .attr("id", "xAxis")
          .call(d3.axisBottom(xScale).tickSize(-innerHeight))
          .attr("transform", `translate(0, ${innerHeight})`);

        mainGroup
          .append("g")
          .attr("id", "yAxis")
          .call(d3.axisLeft(yScale).tickSize(-innerWidth));

        let yAxisGroup = mainGroup.select("#yAxis");
        yAxisGroup
          .append("text")
          .attr("transform", `rotate(-90)`)
          .attr("x", -innerHeight / 2)
          .attr("y", -50)
          .attr("fill", "#333333")
          .text("Count (Log)")
          .attr("text-anchor", "middle") // Make label at the middle of axis.
          .attr("font-size", "3.5em")
          .attr("font-weight", "bold");
        yAxisGroup.selectAll(".domain").remove(); // we can select multiple tags using comma to seperate them and we can use space to signify nesting;

        let xAxisGroup = mainGroup.select("#xAxis");
        xAxisGroup
          .append("text")
          .attr("y", 60)
          .attr("x", innerWidth / 2)
          .attr("fill", "#333333")
          .attr("font-size", "3.5em")
          .attr("text-anchor", "middle")
          .attr("font-weight", "bold")
          .text("Occurrence");
        xAxisGroup.selectAll(".domain").remove();
      };

      d3.csv("/class5-stack/suncg_occur.csv").then((data) => {
        data.forEach((datum) => {
          datum.occur = +datum.occur + 1;
        });

        // x比例尺单位是线性的，因为是按照阈值操作的
        xScale = d3
          .scaleLinear()
          .range([0, innerWidth])
          .domain([
            d3.min(data, xValue), // 获取所有 x0 和 x1 的最小值
            d3.max(data, xValue), // 获取所有 x0 和 x1 的最大值
          ])
          .nice();

        // 直方图公式代码？。。。
        const histogram = d3
          .bin()
          .value(xValue)
          .domain(xScale.domain())
          .thresholds(xScale.ticks(20));

        const histData = histogram(data);
        console.log("histData ", histData);

        // y比例尺是出现数量，也就是数组长度
        yScale = d3
          .scaleLinear()
          .range([innerHeight, 0])
          .domain([
            d3.min(histData, (d) => d.length),
            d3.max(histData, (d) => d.length),
          ])
          .nice();

        initAxis();

        // 颜色序列
        const color = d3
          .scaleOrdinal()
          .range(d3.schemePaired.concat(d3.schemeCategory10));

        // data-join
        const mainGroup = d3.select("#mainGroup");
        mainGroup
          .selectAll(".hist")
          .data(histData)
          .join((enter) =>
            enter
              .append("rect")
              .attr("class", "hist")
              .attr("x", (d) => {
                console.log("d", d);
                return xScale(d.x0); //左端点作为起始点
              })
              .attr("y", (d) => yScale(d.length))
              .attr("width", (d) => xScale(d.x1) - xScale(d.x0))
              // 高度计算：底部到高度的距离，注意是反的
              .attr("height", (d) => yScale(0) - yScale(d.length))
              .attr("fill", (d, i) => color(i))
          );
      });

      initChart();
    </script>
  </body>
</html>

```

