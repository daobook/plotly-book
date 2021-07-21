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

# Python 中的填充区域 plot

参考：[Filled Area Plots | Python | Plotly](https://plotly.com/python/filled-area-plots/)

如何在 Python 中使用 Plotly 绘制填充区域图。

## 使用 `plotly.express` 填充 plot

[](plotly:plotly-express) 是 Plotly 的易于使用的高级接口，它 [操作各种类型的数据](plotly:px-arguments) 并产生 [易于样式的图形](plotly:styling-plotly-express)。

`px.area` 创建一个堆叠的区域 plot。每个填充区域对应于 `line_group` 参数给出的列的一个值。

```{code-cell} ipython3
import plotly.express as px
df = px.data.gapminder()
fig = px.area(df, x="year", y="pop",
              color="continent",
              line_group="country")
fig.show()
```

## 在 Dash 填充区域 plot

{term}`Dash` 是使用 Plotly 图形在 Python 中构建分析应用程序的最佳方法。要运行下面的应用程序，请运行 `pip install dash`（或者 `pip install jupyter-dash` 适用于 Jupyter），点击 "Download" 获取代码并运行 `python app.py`。

[官方的 Dash 文档](https://dash.plotly.com/installation) 获得开始，并学习如何像 [Dash 企业](https://plotly.com/dash/) 那样轻松 [开发](https://plotly.com/dash/design-kit/) 和 [部署](https://plotly.com/dash/app-manager/) 应用程序。

```{code-cell} ipython3
:tags: [remove-input]

from IPython.display import IFrame
snippet_url = 'https://dash-gallery.plotly.host/python-docs-dash-snippets/'
IFrame(snippet_url + 'filled-area-plots', width='100%', height=630)
```

## 使用 `plot.graph_objects` 填充区域图表

### 基本覆盖区域图

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure()
# fill down to xaxis
fig.add_trace(go.Scatter(x=[1, 2, 3, 4],
                         y=[0, 2, 3, 5],
                         fill='tozeroy'))
# fill to trace0 y
fig.add_trace(go.Scatter(x=[1, 2, 3, 4],
                         y=[3, 5, 1, 7],
                         fill='tonexty'))

fig.show()
```

### 没有边界线的覆盖区域图

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure()
fig.add_trace(go.Scatter(x=[1, 2, 3, 4],
                         y=[0, 2, 3, 5],
                         fill='tozeroy',
                         mode='none'))  # override default markers+lines

fig.add_trace(go.Scatter(x=[1, 2, 3, 4],
                         y=[3, 5, 1, 7],
                         fill='tonexty',
                         mode='none'))

fig.show()
```

### 区域图内部填充

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure()
fig.add_trace(go.Scatter(x=[1, 2, 3, 4],
                         y=[3, 4, 8, 3],
                         fill=None,
                         mode='lines',
                         line_color='indigo'))
fig.add_trace(go.Scatter(x=[1, 2, 3, 4],
                         y=[1, 6, 2, 6],
                         fill='tonexty',  # fill area between trace0 and trace1
                         mode='lines',
                         line_color='indigo'))

fig.show()
```

### 堆叠区域图

`stackgroup` 参数用于在同一组中添加不同跟踪的 `y` 值。同一组中的跟踪将填充到该组的下一个跟踪。

```{code-cell} ipython3
import plotly.graph_objects as go

x = ['Winter', 'Spring', 'Summer', 'Fall']

fig = go.Figure()
fig.add_trace(go.Scatter(
    x=x, y=[40, 60, 40, 10],
    hoverinfo='x+y',
    mode='lines',
    line=dict(width=0.5, color='rgb(131, 90, 241)'),
    stackgroup='one'))  # define stack group

fig.add_trace(go.Scatter(
    x=x, y=[20, 10, 10, 60],
    hoverinfo='x+y',
    mode='lines',
    line=dict(width=0.5, color='rgb(111, 231, 219)'),
    stackgroup='one'))

fig.add_trace(go.Scatter(
    x=x, y=[40, 30, 50, 30],
    hoverinfo='x+y',
    mode='lines',
    line=dict(width=0.5, color='rgb(184, 247, 212)'),
    stackgroup='one'))

fig.update_layout(yaxis_range=(0, 100))
fig.show()
```

## 具有归一化值的堆叠区域图

```{code-cell} ipython3
import plotly.graph_objects as go

x = ['Winter', 'Spring', 'Summer', 'Fall']
fig = go.Figure()

fig.add_trace(go.Scatter(
    x=x, y=[40, 20, 30, 40],
    mode='lines',
    line=dict(width=0.5, color='rgb(184, 247, 212)'),
    stackgroup='one',
    groupnorm='percent'))  # sets the normalization for the sum of the stackgroup

fig.add_trace(go.Scatter(
    x=x, y=[50, 70, 40, 60],
    mode='lines',
    line=dict(width=0.5, color='rgb(111, 231, 219)'),
    stackgroup='one'))

fig.add_trace(go.Scatter(
    x=x, y=[70, 80, 60, 70],
    mode='lines',
    line=dict(width=0.5, color='rgb(127, 166, 238)'),
    stackgroup='one'))
    
fig.add_trace(go.Scatter(
    x=x, y=[100, 100, 100, 100],
    mode='lines',
    line=dict(width=0.5, color='rgb(131, 90, 241)'),
    stackgroup='one'))

fig.update_layout(
    showlegend=True,
    xaxis_type='category',
    yaxis=dict(
        type='linear',
        range=[1, 100],
        ticksuffix='%'))

fig.show()
```

### 选择悬停点

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure()
fig.add_trace(go.Scatter(x=[0, 0.5, 1, 1.5, 2],
                         y=[0, 1, 2, 1, 0],
                         fill='toself',
                         fillcolor='darkviolet',
                         hoveron='points+fills',  # select where hover is active
                         line_color='darkviolet',
                         text="Points + Fills",
                         hoverinfo='text+x+y'))

fig.add_trace(go.Scatter(x=[3, 3.5, 4, 4.5, 5],
                         y=[0, 1, 2, 1, 0],
                         fill='toself',
                         fillcolor='violet',
                         hoveron='points',
                         line_color='violet',
                         text="Points only",
                         hoverinfo='text+x+y'))

fig.update_layout(
    title="hover on <i>points</i> or <i>fill</i>",
    xaxis_range=[0, 5.2],
    yaxis_range=[0, 3])

fig.show()
```

## 参考

有关更多信息和属性选项，请参见 [scatter-line](https://plotly.com/python/reference/scatter/#scatter-line) 和 [scatter-fill](https://plotly.com/python/reference/scatter/#scatter-fill)！
