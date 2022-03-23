---
title: gojs前端插件
date: 2017-05-24
comments: false
toc: true
categories: "前端"
tags: 
---

是一个前端插件，跟go和js没有半毛钱关系

主要可以通过代码动态的生成和修改图表数据(组织架构图，执行流程图等等)

网址:<https://gojs.net/latest/index.html>

如果你想使用，需要下载他的文件

目前需要我们了解的文件其实只有三个，用得到的只有两个

```python
"""
1.go.js
	是使用gojs所必须要导入的js文件
2.go-debug.js
	可以帮你打印一些bug日志  正常线上不会使用
3.Figures.js
	go.js中自带了一些基本的图标，额外扩展的图标需要引入该文件
"""
# 总结你在使用的导入go.js和Figures.js就够了
```

#### 基本使用

gojs使用基本套路是先在页面上写一个div站地方，之后初始化该div，再然后所有的图标渲染都在该div内进行！！！

```js
<div id="myDiagramDiv" style="width:500px; height:350px; background-color: #DAE4E4;"></div>

<script src="go.js"></script>
<script>
  var $ = go.GraphObject.make;
  // 第一步：创建图表
  var myDiagram = $(go.Diagram, "myDiagramDiv"); // 创建图表，用于在页面上画图
  // 第二步：创建一个节点，内容为jason
  var node = $(go.Node, $(go.TextBlock, {text: "jason"}));
  // 第三步：将节点添加到图表中
  myDiagram.add(node)
</script>
```

#### 重要概念

* TextBlock  创建文本
* Shape  创建图形
* Node  节点(结合文本与图形)
* Links  箭头

#### TexBlock

```html
<div id="myDiagramDiv" style="width:500px; height:350px; background-color: #DAE4E4;"></div>
<script src="go.js"></script>
<script>
    var $ = go.GraphObject.make;
    // 第一步：创建图表
    var myDiagram = $(go.Diagram, "myDiagramDiv"); // 创建图表，用于在页面上画图
    var node1 = $(go.Node, $(go.TextBlock, {text: "jason"}));
    myDiagram.add(node1);
    var node2 = $(go.Node, $(go.TextBlock, {text: "jason", stroke: 'red'}));
    myDiagram.add(node2);
    var node3 = $(go.Node, $(go.TextBlock, {text: "jason", background: 'lightblue'}));
    myDiagram.add(node3);
</script>
```

##### Shape

```html
<div id="myDiagramDiv" style="width:500px; height:350px; background-color: #DAE4E4;"></div>
<script src="go.js"></script>
<script src="Figures.js"></script>
<script>
    var $ = go.GraphObject.make;
    // 第一步：创建图表
    var myDiagram = $(go.Diagram, "myDiagramDiv"); // 创建图表，用于在页面上画图

    var node1 = $(go.Node,
        $(go.Shape, {figure: "Ellipse", width: 40, height: 40})
    );
     myDiagram.add(node1);

     var node2 = $(go.Node,
        $(go.Shape, {figure: "RoundedRectangle", width: 40, height: 40, fill: 'green',stroke:'red'})
    );
    myDiagram.add(node2);

    var node3 = $(go.Node,
        $(go.Shape, {figure: "Rectangle", width: 40, height: 40, fill: null})
    );
    myDiagram.add(node3);

    var node4 = $(go.Node,
        $(go.Shape, {figure: "Club", width: 40, height: 40, fill: 'red'})
    );
    myDiagram.add(node4);
</script>
```

##### Node

```html
<div id="myDiagramDiv" style="width:500px; height:350px; background-color: #DAE4E4;"></div>
<script src="go.js"></script>
<script src="Figures.js"></script>
<script>
    var $ = go.GraphObject.make;
    // 第一步：创建图表
    var myDiagram = $(go.Diagram, "myDiagramDiv"); // 创建图表，用于在页面上画图

    var node1 = $(go.Node,
         "Vertical",  // 垂直方向
        {
            background: 'yellow',
            padding: 8
        },
        $(go.Shape, {figure: "Ellipse", width: 40, height: 40,fill:null}),
        $(go.TextBlock, {text: "jason"})
    );
    myDiagram.add(node1);

    var node2 = $(go.Node,
        "Horizontal",  // 水平方向
        {
            background: 'white',
            padding: 5
        },
        $(go.Shape, {figure: "RoundedRectangle", width: 40, height: 40}),
        $(go.TextBlock, {text: "jason"})
    );
    myDiagram.add(node2);

    var node3 = $(go.Node,
        "Auto",  // 居中
        $(go.Shape, {figure: "Ellipse", width: 80, height: 80, background: 'green', fill: 'red'}),
        $(go.TextBlock, {text: "jason"})
    );
    myDiagram.add(node3);
</script>
```

##### Links

```html
<div id="myDiagramDiv" style="width:500px; min-height:450px; background-color: #DAE4E4;"></div>
    <script src="go.js"></script>
    <script>
        var $ = go.GraphObject.make;

        var myDiagram = $(go.Diagram, "myDiagramDiv",
            {layout: $(go.TreeLayout, {angle: 0})}
        ); // 创建图表，用于在页面上画图

        var startNode = $(go.Node, "Auto",
            $(go.Shape, {figure: "Ellipse", width: 40, height: 40, fill: '#79C900', stroke: '#79C900'}),
            $(go.TextBlock, {text: '开始', stroke: 'white'})
        );
        myDiagram.add(startNode);

        var downloadNode = $(go.Node, "Auto",
            $(go.Shape, {figure: "RoundedRectangle", height: 40, fill: 'red', stroke: '#79C900'}),
            $(go.TextBlock, {text: '下载代码', stroke: 'white'})
        );
        myDiagram.add(downloadNode);

        var startToDownloadLink = $(go.Link,
            {fromNode: startNode, toNode: downloadNode},
            $(go.Shape, {strokeWidth: 1}),
            $(go.Shape, {toArrow: "OpenTriangle", fill: null, strokeWidth: 1})
        );
        myDiagram.add(startToDownloadLink);
    </script>
```

#### 数据绑定式(重点)

```html
<div id="diagramDiv" style="width:100%; min-height:450px; background-color: #DAE4E4;"></div>

    <script src="go.js"></script>
    <script>
        var $ = go.GraphObject.make;
        var diagram = $(go.Diagram, "diagramDiv",{
            layout: $(go.TreeLayout, {
                angle: 0,
                nodeSpacing: 20,
                layerSpacing: 70
            })
        });
        // 创建一个节点模版
        diagram.nodeTemplate = $(go.Node, "Auto",
            $(go.Shape, {
                figure: "RoundedRectangle",
                fill: 'yellow',
                stroke: 'yellow'
            }, new go.Binding("figure", "figure"), new go.Binding("fill", "color"), new go.Binding("stroke", "color")),
            $(go.TextBlock, {margin: 8}, new go.Binding("text", "text"))
        );
        // 创建一个箭头模版
        diagram.linkTemplate = $(go.Link,
            {routing: go.Link.Orthogonal},
            $(go.Shape, {stroke: 'yellow'}, new go.Binding('stroke', 'link_color')),
            $(go.Shape, {toArrow: "OpenTriangle", stroke: 'yellow'}, new go.Binding('stroke', 'link_color'))
        );
        // 这里的数据后期就可以通过后端来获取
        var nodeDataArray = [
            {key: "start", text: '开始', figure: 'Ellipse', color: "lightgreen"},
            {key: "download", parent: 'start', text: '下载代码', color: "lightgreen", link_text: '执行中...'},
            {key: "compile", parent: 'download', text: '本地编译', color: "lightgreen"},
            {key: "zip", parent: 'compile', text: '打包', color: "red", link_color: 'red'},
            {key: "c1", text: '服务器1', parent: "zip"},
            {key: "c11", text: '服务重启', parent: "c1"},
            {key: "c2", text: '服务器2', parent: "zip"},
            {key: "c21", text: '服务重启', parent: "c2"},
            {key: "c3", text: '服务器3', parent: "zip"},
            {key: "c31", text: '服务重启', parent: "c3"}
        ];
        diagram.model = new go.TreeModel(nodeDataArray);

        // 动态控制节点颜色变化
        var node = diagram.model.findNodeDataForKey("zip");
        diagram.model.setDataProperty(node, "color", "lightgreen");

    </script>
```

#### 取出gojs自带水印

**需要修改go.js文件源码**

* 需要找到一个特定的字符串注释该字符串所在的一行代码

  ```python
  # 7eba17a4ca3b1a8346
  a.kr=b.V[Ra("7eba17a4ca3b1a8346")][Ra("78a118b7")](b.V,Jk,4,4);
  ```

* 在后面添加一行新的代码

  ```python
  a.kr=function(){return false};
  ```

## 





