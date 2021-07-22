---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
kernelspec:
  display_name: ai
  language: python
  name: ai
---

(plotly:graph-objects)=
# 图对象

参考：[Graph Objects | Python | Plotly](https://plotly.com/python/graph-objects/)

## 什么是图对象?

`plotly.graph_objects` 模块(通常按顺序导入)包含一个自动生成的 Python类层次结构，这些类在这个图模式中表示非叶节点。术语“图对象”指的是这些类的实例。`plotly.graph_objects` 模块中有两大主类：[`Figure`](https://plotly.com/python-api-reference/generated/plotly.graph_objects.Figure.html) 和 [`FigureWidget`](plotly:figurewidget)（兼容 `ipywidgets` 的变体），它们都表示整个图。这些类的实例有许多方便的方法来用 Python 操作它们的属性(例如 `.update_layout()` 或 `.add_trace()`，它们都接受“魔法下划线”表示法)，以及渲染它们(例如`.show()`)并将它们导出为各种格式(例如 `.to_json()` 或 `.write_image()` 或 `.write_html()`)。

注意：`Plotly Express` 中的函数(这是 Plotly 库的推荐入口点)都建立在图对象之上，并且全返回 `plotly.graph_objects.Figure` 实例。

图的每个非叶子属性都由 `plotly` 中的一个 `plotly.graph_objects` 层次结构的类实例表示。例如，一个 `fig` 可以有一个属性`layout.margin`包含属性 `t`、`l`、`b` 和 `r`，它们是树的叶子：它们没有子节点。`fig.layout` 的字段是 `plotly.graph_objects.Layout` 类的一个对象，并且 `fig.layout.margin` 表示 `margin` 节点，它有字段 `t`、`l`、`b` 和 `r`，其中包含各自叶节点的值。注意，指定所有这些值不需要使用“魔法下划线”符号创建中间对象： `go.Figure(layout_margin=dict(t=10, b=10, r=10, l=10))`。

列表中包含的对象是属性`data`的值，称为“轨迹”，可以是 40 多种类型中的一种，每种类型在`plotly.graph_objects`中都有相应的类。例如，`scatter`类型的轨迹由`plotly.graph_objects.Scatter`类的实例表示。这意味着一个以`go.Figure(data=[go.Scatter(x=[1,2], y=[3,4)])`将JSON 表示 `{"data": [{"type": "scatter", "x": [1,2], "y": [3,4]}]}`。

(plotly:when-use-graph-objects)=
## 何时直接使用图对象

创建图的推荐方法是使用`plotly.express`模块中的函数，统称为 Plotly express，它都返回 `plot.graph_objects.Figure` 的实例，所以使用 `plotly` 库生成的每个图，实际上都使用了图对象，除非是手工从字典中构建的。也就是说，某些类型的图形还不能用 Plotly Express 创建，比如使用特定的 3D 轨迹类型(如网格或等值面)的图。此外，从使用 Plotly Express 创建的图形开始创建某些图形非常麻烦，例如具有[不同类型的子图](plotly:mixed-subplots)、[双轴图](plotly:multiple-axes) 或具有多个不同类型轨迹的[分面图](https://plotly.com/python/facet-plots/)。

注意，Plotly Express 在一个函数调用中生成的图[很容易在创建时定制](plotly:styling-plotly-express)，并且在创建后使用`update_*`和`add_*`方法进行[操作](plotly:creating-and-updating-figures)。Plotly Express 生成的图总是可以使用图对象从头开始构建，但这种方法通常需要 5-100 行代码，而不是 1 行。下面是一个简单的例子，演示如何从相同的数据生成相同的图形对象，一次使用 Plotly Express，另一次不使用。本例中的数据是 "long form" 的，但 Plotly Express 也接受 ["wide form"](https://plotly.com/python/wide-form/) 的数据，而且 Plotly Express 相对于图对象节省的行数是可以比较的。更复杂的图形，如 [sunbursts](https://plotly.com/python/sunburst-charts/), [parallel coordinates](https://plotly.com/python/parallel-coordinates-plot/), [facet plots](https://plotly.com/python/facet-plots/) 或 [animations](https://plotly.com/python/animations/) 需要更多的图对象代码行，而使用 Plotly Express 从一种表示转换到另一种表示通常只需要更改几个字符。

```{code-cell} ipython3
import pandas as pd

df = pd.DataFrame({
  "Fruit": ["Apples", "Oranges", "Bananas", "Apples", "Oranges", "Bananas"],
  "Contestant": ["Alex", "Alex", "Alex", "Jordan", "Jordan", "Jordan"],
  "Number Eaten": [2, 1, 3, 1, 3, 2],
})

import plotly.express as px

fig = px.bar(df, x="Fruit", y="Number Eaten", color="Contestant", barmode="group")
fig.show()
```

不使用 `px`：

```python
import plotly.graph_objects as go

fig = go.Figure()
for contestant, group in df.groupby("Contestant"):
    fig.add_trace(go.Bar(x=group["Fruit"], y=group["Number Eaten"], name=contestant,
      hovertemplate="Contestant=%s<br>Fruit=%%{x}<br>Number Eaten=%%{y}<extra></extra>"% contestant))
fig.update_layout(legend_title_text = "Contestant")
fig.update_xaxes(title_text="Fruit")
fig.update_yaxes(title_text="Number Eaten")
fig.show()
```

(plotly:mixed-subplots)=
## 混合子图

```{important}
Plotly Express 不支持创建具有任意混合子图的图形，即具有不同类型子图的图形。Plotly Express 仅支持 [facet 图](https://plotly.com/python/facet-plots/)和[边际分布子图](https://plotly.com/python/marginal-plots/)。要制作带有混合子图的图形，请将 [`make_subplots()`](https://plotly.com/python/subplots/) 函数与下图所述的图对象结合使用。
```

```{code-cell} ipython3
import plotly.graph_objects as go
from plotly.subplots import make_subplots

import pandas as pd

# read in volcano database data
df = pd.read_csv(
    "https://raw.githubusercontent.com/plotly/datasets/master/volcano_db.csv",
    encoding="iso-8859-1",
)

# frequency of Country
freq = df
freq = freq.Country.value_counts().reset_index().rename(columns={"index": "x"})

# read in 3d volcano surface data
df_v = pd.read_csv("https://raw.githubusercontent.com/plotly/datasets/master/volcano.csv")

# Initialize figure with subplots
fig = make_subplots(
    rows=2, cols=2,
    column_widths=[0.6, 0.4],
    row_heights=[0.4, 0.6],
    specs=[[{"type": "scattergeo", "rowspan": 2}, {"type": "bar"}],
           [            None                    , {"type": "surface"}]])

# Add scattergeo globe map of volcano locations
fig.add_trace(
    go.Scattergeo(lat=df["Latitude"],
                  lon=df["Longitude"],
                  mode="markers",
                  hoverinfo="text",
                  showlegend=False,
                  marker=dict(color="crimson", size=4, opacity=0.8)),
    row=1, col=1
)

# Add locations bar chart
fig.add_trace(
    go.Bar(x=freq["x"][0:10],y=freq["Country"][0:10], marker=dict(color="crimson"), showlegend=False),
    row=1, col=2
)

# Add 3d surface of volcano
fig.add_trace(
    go.Surface(z=df_v.values.tolist(), showscale=False),
    row=2, col=2
)

# Update geo subplot properties
fig.update_geos(
    projection_type="orthographic",
    landcolor="white",
    oceancolor="MidnightBlue",
    showocean=True,
    lakecolor="LightBlue"
)

# Rotate x-axis labels
fig.update_xaxes(tickangle=45)

# Set theme, margin, and annotation in layout
fig.update_layout(
    template="plotly_dark",
    margin=dict(r=10, t=25, b=40, l=60),
    annotations=[
        dict(
            text="Source: NOAA",
            showarrow=False,
            xref="paper",
            yref="paper",
            x=0,
            y=0)
    ]
)

fig.show()
```

```{code-cell} ipython3

```
