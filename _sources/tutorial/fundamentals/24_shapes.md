---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.10.3
kernelspec:
  display_name: ai
  language: python
  name: ai
---

(plotly:shapes)=
# 几何形状

参考：[Shapes | Python | Plotly](https://plotly.com/python/shapes/#drawing-shapes-on-cartesian-plots)

如何在 {term}`Python` 中制作 {term}`SVG` 形状。线、圆、矩形和路径的示例。

## 向 Figure 添加线条和多边形

```{margin} 两种方法的区别
- 轨迹可以选择支持悬停标签，并且可以出现在图例中。
- 形状只能在二维笛卡尔子图中绝对或相对于数据坐标定位。
- 轨迹不能绝对定位，但可以相对于任何子图类型中的日期坐标定位。
- 在 [](plotly:text-and-annotations) 中介绍了轨迹也支持 **可选文本**。
```

作为一般规则，有两种向 Figure 添加形状（线或多边形）的方法：

1. **轨迹**：`scatter` 系列（例如 `scatter`、`scatter3d`、`scattergeo` 等）的轨迹类型，可以使用 `mode="lines"` 绘制，并且可以选择支持 `fill="self"` 属性，因此可用于在 Figure 上绘制开放或封闭的形状。
2. **形状**：可以使用 `fig.add_shape()` 将独立的线、椭圆和矩形添加到 Figure 中，它们可以绝对定位在 Figure 内，也可以相对于 2D 笛卡尔子图的轴定位，即在数据坐标中。

```{note}
在 [](plotly:horizontal-vertical-shapes) 中介绍了一些特殊的方法（如 `add_hline`、`add_vline`、`add_hrect` 和 `add_vrect`）用于绘制水平或垂直线或矩形的常见情况，这些线或矩形固定在一个轴上的数据坐标并在另一个轴上绝对定位。
```

## 用散点图轨迹绘制形状

有两种绘制填充形状的方法：散点图轨迹和 [layout.shapes](https://plotly.com/python/reference/layout/shapes/#layout-shapes-items-shape-type) 主要用于 2D 子图，并定义要绘制的形状类型，可以是矩形、圆形、直线或路径（自定义 SVG 路径）。您还可以使用 [scatterpolar](https://plotly.com/python/polar-chart/#categorical-polar-chart)、[scattergeo](https://plotly.com/python/reference/scattergeo)、[scattermapbox](https://plotly.com/python/filled-area-on-mapbox/#filled-scattermapbox-trace) 在任何类型的子图上绘制填充形状。要将区域设置为用纯色填充，您需要定义 [Scatter.fill="toself"](https://plotly.com/python/reference/scatter/#scatter-fill) 将轨迹的端点连接成一个封闭的形状。如果 `mode="lines"`（默认值 `"lines+markers"`），那么您需要在序列的初始点重复一个形状，以获得一个封闭的形状。

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure(go.Scatter(x=[0, 1, 2, 0],
                           y=[0, 2, 0, 0],
                           fill="toself",
                           mode="lines+markers"))
fig.show()
```

您可以通过添加 {doc}`更多的跟踪<../basic-charts/filled-area-plots>` 或使用 `None` 中断该系列来获得更多的形状。

```python
import plotly.graph_objects as go

fig = go.Figure(go.Scatter(x=[0, 1, 2, 0, None, 3, 3, 5, 5, 3],
                           y=[0, 2, 0, 0, None, 0.5, 1.5, 1.5, 0.5, 0.5],
                           fill="toself"))
fig.show()
```

```{code-cell} ipython3
:tags: [remove-input]

import plotly.graph_objects as go

fig = go.Figure(go.Scatter(x=[0, 1, 2, 0, None, 3, 3, 5, 5, 3],
                           y=[0, 2, 0, 0, None, 0.5, 1.5, 1.5, 0.5, 0.5],
                           fill="toself"))
f2 = go.FigureWidget(fig) # 在线输出
f2
```

### Dash 中的形状

{term}`Dash` 是使用 Plotly 图形在 Python 中构建分析应用程序的最佳方法。要运行下面的应用程序，请运行 `pip install dash`（或者 `pip install jupyter-dash` 适用于 Jupyter），点击 "Download" 获取代码并运行 `python app.py`。

[官方的 Dash 文档](https://dash.plotly.com/installation) 获得开始，并学习如何像 [Dash 企业](https://plotly.com/dash/) 那样轻松 [开发](https://plotly.com/dash/design-kit/) 和 [部署](https://plotly.com/dash/app-manager/) 应用程序。

```{code-cell} ipython3
:tags: [remove-input]

from IPython.display import IFrame
snippet_url = 'https://dash-gallery.plotly.host/python-docs-dash-snippets/'
IFrame(snippet_url + 'shapes', width='100%', height=630)
```

### 相对于 Axis 数据定位的垂直和水平线

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure()

# Create scatter trace of text labels
fig.add_trace(go.Scatter(
    x=[2, 3.5, 6],
    y=[1, 1.5, 1],
    text=["垂直线",
          "水平虚线",
          "对角虚线"],
    mode="text",
))

# Set axes ranges
fig.update_xaxes(range=[0, 7])
fig.update_yaxes(range=[0, 2.5])

# Add shapes
fig.add_shape(type="line",
              x0=1, y0=0, x1=1, y1=2,
              line=dict(color="RoyalBlue", width=3))
fig.add_shape(type="line",
              x0=2, y0=2, x1=5, y1=2,
              line=dict(
                  color="LightSeaGreen",
                  width=4,
                  dash="dashdot",))
fig.add_shape(type="line",
              x0=4, y0=0, x1=6, y1=2,
              line=dict(
                  color="MediumPurple",
                  width=4,
                  dash="dot",))
fig.update_shapes(dict(xref='x', yref='y'))
fig.show()
```

### 相对于 Plot 和 Axis 数据定位的线

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure()

# Create scatter trace of text labels
fig.add_trace(go.Scatter(
    x=[2, 6], y=[1, 1],
    text=["Line positioned relative to the plot",
          "Line positioned relative to the axes"],
    mode="text",
))

# Set axes ranges
fig.update_xaxes(range=[0, 8])
fig.update_yaxes(range=[0, 2])

fig.add_shape(type="line",
              xref="x", yref="y",
              x0=4, y0=0, x1=8, y1=1,
              line=dict(
                  color="LightSeaGreen",
                  width=3))
fig.add_shape(type="line",
              xref="paper", yref="paper",
              x0=0, y0=0, x1=0.5, y1=0.5,
              line=dict(
                  color="DarkOrange",
                  width=3))

fig.show()
```

### 相对于 Axis 数据定位的矩形

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure()

fig.add_trace(go.Scatter(
    x=[1.5, 4.5],
    y=[0.75, 0.75],
    text=["Unfilled Rectangle", "Filled Rectangle"],
    mode="text",
))

# Set axes properties
fig.update_xaxes(range=[0, 7], showgrid=False)
fig.update_yaxes(range=[0, 3.5])

# Add shapes
fig.add_shape(type="rect",
    x0=1, y0=1, x1=2, y1=3,
    line=dict(color="RoyalBlue"),
)
fig.add_shape(type="rect",
    x0=3, y0=1, x1=6, y1=2,
    line=dict(
        color="RoyalBlue",
        width=2,
    ),
    fillcolor="LightSkyBlue",
)
fig.update_shapes(dict(xref='x', yref='y'))
fig.show()
```

### 相对于 Plot 和轴数据的矩形定位

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure()

# Create scatter trace of text labels
fig.add_trace(go.Scatter(
    x=[1.5, 3],
    y=[2.5, 2.5],
    text=["Rectangle reference to the plot",
          "Rectangle reference to the axes"],
    mode="text",
))

# Set axes properties
fig.update_xaxes(range=[0, 4])
fig.update_yaxes(range=[0, 4])

# Add shapes
fig.add_shape(type="rect",
    xref="x", yref="y",
    x0=2.5, y0=0,
    x1=3.5, y1=2,
    line=dict(
        color="RoyalBlue",
        width=3,
    ),
    fillcolor="LightSkyBlue",
)
fig.add_shape(type="rect",
    xref="paper", yref="paper",
    x0=0.25, y0=0,
    x1=0.5, y1=0.5,
    line=dict(
        color="LightSeaGreen",
        width=3,
    ),
    fillcolor="PaleTurquoise",
)

fig.show()
```

### 相对于轴的位置和长度放置矩形

通过将字符串 `' domain'` 添加到shape 的 `xref` 或 `yref` 属性中的 axis 引用中，可以将 shape 放置到相对于轴的位置。下面的代码在 x 轴上放置了一个矩形，该矩形从左边开始，在 x 轴上从 60% 开始，在 70% 结束，从底部开始，在 y 轴上从 80% 开始，在 90% 结束。

```{code-cell} ipython3
import plotly.graph_objects as go
import plotly.express as px

df = px.data.wind()
fig = px.scatter(df, y="frequency")

fig.update_layout(xaxis=dict(domain=[0, 0.5]), yaxis=dict(domain=[0.25, 0.75]))

# Add a shape whose x and y coordinates refer to the domains of the x and y axes
fig.add_shape(type="rect",
    xref="x domain", yref="y domain",
    x0=0.6, x1=0.7, y0=0.8, y1=0.9,
)

fig.show()
```

### 高亮显示矩形形状的时间序列区域

```{note}
有一些[特殊的方法 `add_hline`、`add_vline`、`add_hrect` 和 `add_vrect`](plotly:horizontal-vertical-shapes)，用于绘制固定在一个轴上的数据坐标上的水平或垂直的线或矩形，这些线或矩形位于另一个轴绝对位置上。
```

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure()

# Add scatter trace for line
fig.add_trace(go.Scatter(
    x=["2015-02-01", "2015-02-02", "2015-02-03", "2015-02-04", "2015-02-05",
       "2015-02-06", "2015-02-07", "2015-02-08", "2015-02-09", "2015-02-10",
       "2015-02-11", "2015-02-12", "2015-02-13", "2015-02-14", "2015-02-15",
       "2015-02-16", "2015-02-17", "2015-02-18", "2015-02-19", "2015-02-20",
       "2015-02-21", "2015-02-22", "2015-02-23", "2015-02-24", "2015-02-25",
       "2015-02-26", "2015-02-27", "2015-02-28"],
    y=[-14, -17, -8, -4, -7, -10, -12, -14, -12, -7, -11, -7, -18, -14, -14,
       -16, -13, -7, -8, -14, -8, -3, -9, -9, -4, -13, -9, -6],
    mode="lines",
    name="temperature"
))

# Add shape regions
fig.add_vrect(
    x0="2015-02-04", x1="2015-02-06",
    fillcolor="LightSalmon", opacity=0.5,
    layer="below", line_width=0,
),

fig.add_vrect(
    x0="2015-02-20", x1="2015-02-22",
    fillcolor="LightSalmon", opacity=0.5,
    layer="below", line_width=0,
)

fig.show()
```

### 相对于轴数据定位的圆

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure()

# Create scatter trace of text labels
fig.add_trace(go.Scatter(
    x=[1.5, 3.5],
    y=[0.75, 2.5],
    text=["Unfilled Circle",
          "Filled Circle"],
    mode="text",
))

# Set axes properties
fig.update_xaxes(range=[0, 4.5], zeroline=False)
fig.update_yaxes(range=[0, 4.5])

# Add circles
fig.add_shape(type="circle",
    xref="x", yref="y",
    x0=1, y0=1, x1=3, y1=3,
    line_color="LightSeaGreen",
)
fig.add_shape(type="circle",
    xref="x", yref="y",
    fillcolor="PaleTurquoise",
    x0=3, y0=3, x1=4, y1=4,
    line_color="LightSeaGreen",
)

# Set figure size
fig.update_layout(width=800, height=800)

fig.show()
```

### 高亮使用圆形形状的散点簇

```{code-cell} ipython3
import plotly.graph_objects as go

import numpy as np
np.random.seed(1)

# Generate data
x0 = np.random.normal(2, 0.45, 300)
y0 = np.random.normal(2, 0.45, 300)

x1 = np.random.normal(6, 0.4, 200)
y1 = np.random.normal(6, 0.4, 200)

# Create figure
fig = go.Figure()

# Add scatter traces
fig.add_trace(go.Scatter(x=x0, y=y0, mode="markers"))
fig.add_trace(go.Scatter(x=x1, y=y1, mode="markers"))

# Add shapes
fig.add_shape(type="circle",
    xref="x", yref="y",
    x0=min(x0), y0=min(y0),
    x1=max(x0), y1=max(y0),
    opacity=0.2,
    fillcolor="blue",
    line_color="blue",
)

fig.add_shape(type="circle",
    xref="x", yref="y",
    x0=min(x1), y0=min(y1),
    x1=max(x1), y1=max(y1),
    opacity=0.2,
    fillcolor="orange",
    line_color="orange",
)


# Hide legend
fig.update_layout(showlegend=False)

fig.show()
```

### 带有圆形形状的维恩图

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure()

# Create scatter trace of text labels
fig.add_trace(go.Scatter(
    x=[1, 1.75, 2.5],
    y=[1, 1, 1],
    text=["$A$", "$A+B$", "$B$"],
    mode="text",
    textfont=dict(
        color="black",
        size=18,
        family="Arail",
    )
))

# Update axes properties
fig.update_xaxes(
    showticklabels=False,
    showgrid=False,
    zeroline=False,
)

fig.update_yaxes(
    showticklabels=False,
    showgrid=False,
    zeroline=False,
)

# Add circles
fig.add_shape(type="circle",
    line_color="blue", fillcolor="blue",
    x0=0, y0=0, x1=2, y1=2
)
fig.add_shape(type="circle",
    line_color="gray", fillcolor="gray",
    x0=1.5, y0=0, x1=3.5, y1=2
)
fig.update_shapes(opacity=0.3, xref="x", yref="y")

fig.update_layout(
    margin=dict(l=20, r=20, b=100),
    height=600, width=800,
    plot_bgcolor="white"
)

fig.show()
```

### 向子图添加形状

在这里，我们使用 `make_subplots` 创建的不同轴 (`x1`, `x2`) 作为参考系，以便在子图中绘制形状。

```{code-cell} ipython3
import plotly.graph_objects as go
from plotly.subplots import make_subplots

# Create Subplots
fig = make_subplots(rows=2, cols=2)

fig.add_trace(go.Scatter(x=[2, 6], y=[1,1]), row=1, col=1)
fig.add_trace(go.Bar(x=[1,2,3], y=[4,5,6]), row=1, col=2)
fig.add_trace(go.Scatter(x=[10,20], y=[40,50]), row=2, col=1)
fig.add_trace(go.Bar(x=[11,13,15], y=[8,11,20]), row=2, col=2)

# Add shapes
fig.update_layout(
    shapes=[
        dict(type="line", xref="x", yref="y",
            x0=3, y0=0.5, x1=5, y1=0.8, line_width=3),
        dict(type="rect", xref="x2", yref='y2',
             x0=4, y0=2, x1=5, y1=6),
        dict(type="rect", xref="x3", yref="y3",
             x0=10, y0=20, x1=15, y1=30),
        dict(type="circle", xref="x4", yref="y4",
             x0=5, y0=12, x1=10, y1=18)])
fig.show()
```

### 添加相同的形状到多个子绘图

通过使用 `row` 和 `col` 参数中的 `'all'` 关键字，可以将相同的形状添加到多个 facet。例如

```{code-cell} ipython3
import plotly.express as px

df = px.data.tips()
fig = px.scatter(df, x="total_bill", y="tip", facet_row="smoker", facet_col="sex")
# Adds a rectangle to all facets
fig.add_shape(
    dict(type="rect", x0=25, x1=35, y0=4, y1=6, line_color="purple"),
    row="all",
    col="all",
)
# Adds a line to all the rows of the second column
fig.add_shape(
    dict(type="line", x0=20, x1=25, y0=5, y1=6, line_color="yellow"), row="all", col=2
)

# Adds a circle to all the columns of the first row
fig.add_shape(
    dict(type="circle", x0=10, y0=2, x1=20, y1=7), row=1, col="all", line_color="green"
)
fig.show()
```

### SVG 路径

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure()

# Create scatter trace of text labels
fig.add_trace(go.Scatter(
    x=[2, 1, 8, 8],
    y=[0.25, 9, 2, 6],
    text=["Filled Triangle",
          "Filled Polygon",
          "Quadratic Bezier Curves",
          "Cubic Bezier Curves"],
    mode="text",
))

# Update axes properties
fig.update_xaxes(
    range=[0, 9],
    zeroline=False,
)

fig.update_yaxes(
    range=[0, 11],
    zeroline=False,
)

# Add shapes
fig.update_layout(
    shapes=[
        # Quadratic Bezier Curves
        dict(
            type="path",
            path="M 4,4 Q 6,0 8,4",
            line_color="RoyalBlue",
        ),
        # Cubic Bezier Curves
        dict(
            type="path",
            path="M 1,4 C 2,8 6,4 8,8",
            line_color="MediumPurple",
        ),
        # filled Triangle
        dict(
            type="path",
            path=" M 1 1 L 1 3 L 4 1 Z",
            fillcolor="LightPink",
            line_color="Crimson",
        ),
        # filled Polygon
        dict(
            type="path",
            path=" M 3,7 L2,8 L2,9 L3,10, L4,10 L5,9 L5,8 L4,7 Z",
            fillcolor="PaleTurquoise",
            line_color="LightSeaGreen",
        ),
    ]
)

fig.show()
```

---

