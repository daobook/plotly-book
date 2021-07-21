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

(plotly:horizontal-vertical-shapes)=
# Python 中的水平线、垂线和矩形

参考：[Horizontal and Vertical Lines and Rectangles | Python | Plotly](https://plotly.com/python/horizontal-vertical-shapes/)

*在 plotly 4.12 中引入*

可以通过 `plotly.graph_objects.Figure` 的 `add_hline`, `add_vline`, `add_hrect`, 和 `add_vrect` 方法添加横跨整个 plot 的水平线、垂线和矩形。使用这些方法添加的形状被添加为  [布局形状](plotly:shapes)(如执行 `print(fig)` 时所示)。这些形状固定在一个轴的端点上，而不管绘图的范围如何，固定在另一个轴上的数据坐标上。下面展示了一些可能性，尝试平移和缩放结果图，看看形状如何粘在一些轴上：

```{code-cell} ipython3
import plotly.express as px

df = px.data.iris()
fig = px.scatter(df, x="petal_length", y="petal_width")
fig.add_hline(y=0.9)
fig.add_vrect(x0=0.9, x1=2)
fig.show()
```

这些形状可以通过传递和 `add_shape` 相同参数来设置样式：

```{code-cell} ipython3
import plotly.express as px

df = px.data.iris()
fig = px.scatter(df, x="petal_length", y="petal_width")
fig.add_vline(x=2.5, line_width=3, line_dash="dash", line_color="green")
fig.add_hrect(y0=0.9, y1=2.6, line_width=0, fillcolor="red", opacity=0.2)
fig.show()
```

## Dash 中的水平线和垂线

{term}`Dash` 是使用 Plotly 图形在 Python 中构建分析应用程序的最佳方法。要运行下面的应用程序，请运行 `pip install dash`（或者 `pip install jupyter-dash` 适用于 Jupyter），点击 "Download" 获取代码并运行 `python app.py`。

[官方的 Dash 文档](https://dash.plotly.com/installation) 获得开始，并学习如何像 [Dash 企业](https://plotly.com/dash/) 那样轻松 [开发](https://plotly.com/dash/design-kit/) 和 [部署](https://plotly.com/dash/app-manager/) 应用程序。


```{code-cell} ipython3 hide_code=true
from IPython.display import IFrame
snippet_url = 'https://dash-gallery.plotly.host/python-docs-dash-snippets/'
IFrame(snippet_url + 'horizontal-vertical-shapes', width='100%', height=630)
```

## 添加文本注释

[](plotly:text-and-annotations) 可以选择使用 `annotation_text` 关键字参数添加到 autoshape，并使用 `annotation_position` 参数定位：

```{code-cell} ipython3
import plotly.express as px

df = px.data.stocks(indexed=True)
fig = px.line(df)
fig.add_hline(y=1, line_dash="dot",
              annotation_text="Jan 1, 2018 baseline", 
              annotation_position="bottom right")
fig.add_vrect(x0="2018-09-24", x1="2018-12-18", 
              annotation_text="decline", annotation_position="top left",
              fillcolor="green", opacity=0.25, line_width=0)
fig.show()
```

注释的额外格式可以通过使用 `annotation_` 前缀的 magic-下划线或传递一个 `dict` 或 `go.layout.Annotation` 实例到 `annotation` 参数来完成：

```{code-cell} ipython3
import plotly.express as px

df = px.data.stocks(indexed=True)
fig = px.line(df)
fig.add_hline(y=1, line_dash="dot",
              annotation_text="Jan 1, 2018 baseline", 
              annotation_position="bottom right",
              annotation_font_size=20,
              annotation_font_color="blue"
             )
fig.add_vrect(x0="2018-09-24", x1="2018-12-18", 
              annotation_text="decline", annotation_position="top left",
              annotation=dict(font_size=20, font_family="Times New Roman"),
              fillcolor="green", opacity=0.25, line_width=0)
fig.show()
```

## 添加到多个层/子图

通过将 `row` 和/或 `col` 设置为 `"all"` 来实现相同的行或框可以添加到多个 [](plotly:subplots) / {doc}`../statistical-charts/facet-plots`。默认的 `row` 和/或 `col` 值是 `"all"`。

```{code-cell} ipython3
import plotly.express as px

df = px.data.stocks(indexed=True)
fig = px.line(df, facet_col="company", facet_col_wrap=2)
fig.add_hline(y=1, line_dash="dot", row=3, col="all",
              annotation_text="Jan 1, 2018 baseline", 
              annotation_position="bottom right")
fig.add_vrect(x0="2018-09-24", x1="2018-12-18", row="all", col=1,
              annotation_text="decline", annotation_position="top left",
              fillcolor="green", opacity=0.25, line_width=0)
fig.show()
```

## 参考

更多细节见 [](plotly:shapes) 和 [](plotly:text-and-annotations)。

可以查看 [`add_hline`](https://plotly.com/python-api-reference/generated/plotly.graph_objects.Figure.html?highlight=add_hline#plotly.graph_objects.Figure.add_hline), [`add_vline`](https://plotly.com/python-api-reference/generated/plotly.graph_objects.Figure.html?highlight=add_vline#plotly.graph_objects.Figure.add_vline), [`add_hrect`](https://plotly.com/python-api-reference/generated/plotly.graph_objects.Figure.html?highlight=add_hrect#plotly.graph_objects.Figure.add_hrect), [`add_vrect`](https://plotly.com/python-api-reference/generated/plotly.graph_objects.Figure.html?highlight=add_vrect#plotly.graph_objects.Figure.add_vrect) 的参考文档。

