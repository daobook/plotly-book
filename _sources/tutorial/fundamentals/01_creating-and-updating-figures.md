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

(plotly:creating-and-updating-figures)=
# {fa}`leaf,text-success mr-1`创建和更新图

参考：[Creating and Updating Figures | Python | Plotly](https://plotly.com/python/creating-and-updating-figures/)

## 字典

在较低的级别上，图可以表示为字典，并使用 `plotly.io` 模块中的函数显示。下图中的图字典描述了一个图。它包含一个轨迹和一个标题。

```{code-cell} ipython3
import plotly.io as pio

fig = {
    'data': [
        {
            'type': 'bar',
            'x': [1, 2, 3],
            'y': [1, 3, 2]
        }
    ],
    'layout': {
        'title': {
            'text': 'Python 字典指定的图形'
        }
    }
}
# 要显示该字典定义的图，请使用低级的 plot .io.show 函数
pio.show(fig)
```

## 图对象

[`plotly.graph_objects` 模块](https://plotly.com/python-api-reference/plotly.graph_objects.html) 提供了一个自动生成的称为 "[图对象（graph objects）](plotly:graph-objects)" 的类层次结构，它有一个顶级类 `plotly.graph_objects.Figure` 可以用来表示图。

```{note}
与使用 Python 字典相比，**推荐的替代方法**是使用 Plotly Express [一次性创建整个图形](plotly:plotly-express)，并操作生成的 `plotly.graph_objects.Figure` 对象，而不是从底层图形对象自底向上组合图。参见 [](plotly:when-use-graph-objects)。
```

与普通的 Python 字典相比，图对象有几个优点。

1. 图对象提供精确的数据验证。如果提供无效的属性名或无效的属性值作为图形对象的键，将引发异常，并给出描述问题的有用错误消息。如果使用普通的 Python 字典和列表来构建图形，情况就不一样了。
2. 图对象以 Python 文档字符串的形式包含每个有效属性的描述，并提供[完整的 API 参考](https://plotly.com/python-api-reference/)。您可以在自己选择的开发环境中使用这些文档字符串来了解可用的属性，作为[在线参考](https://plotly.com/python/reference/index/)的替代方法。
3. 图对象的属性可以通过字典式的键查找（如`fig["layout"]`）或类式的属性访问（如`fig.layout`）来访问。
4. 图对象支持对已构造的图（`.update_layout()`，`.add_trace()`等）进行更新的高级便利函数，如下所述。
5. 图对象构造器和更新方法接受“魔法下划线”（例如 `go.Figure(layout_title_text="The Title"` 而不是`dict(layout=dict(title=dict(text="The Title")))`）以获得更紧凑的代码，如下所述。
6. 图对象支持附加渲染（`.show()`）和导出函数（`.write_image()`），这些函数自动地从 [`plotly.io` 模块](https://plotly.com/python-api-reference/plotly.io.html) 调用适当的函数。

上面例子中的图可以使用图对象而不是字典来指定。

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure(
    data=[go.Bar(x=[1, 2, 3],
                 y=[1, 3, 2])],
    layout=go.Layout(
        title=go.layout.Title(text="Python 字典指定的图形")
    )
)

fig.show()
```

还可以直接通过将字典传递给 `go.Figure` 的构造函数来创建图对象。

```{code-cell} ipython3
import plotly.graph_objects as go

dict_of_fig = {
    'data': [
        {
            'type': 'bar',
            'x': [1, 2, 3],
            'y': [1, 3, 2]
        }
    ],
    'layout': {
        'title': {
            'text': 'Python 字典指定的图形'
        }
    }
}
fig = go.Figure(dict_of_fig)

fig.show()
```

## 将图对象转换为字典和 JSON

图对象可以使用 `fig.to_dict()` 方法转换为它们的 Python 字典表示形式。您也可以使用 `fig.to_json()` 方法检索图形对象的 JSON 字符串表示。

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure(
    data=[go.Bar(x=[1, 2, 3], y=[1, 3, 2])],
    layout=go.Layout(height=600, width=800)
)

fig.layout.template = None  # to slim down the output

print(f"图对象的字典表示：\n\n{fig.to_dict()}")
print("\n\n")
print(f"图对象的 JSON 表示：\n\n{fig.to_json()}")
print("\n\n")
```

## 使用 Dash 表示图

```{code-cell} ipython3
:tags: [remove-input]

from IPython.display import IFrame
snippet_url = 'https://dash-gallery.plotly.host/python-docs-dash-snippets/'
IFrame(snippet_url + 'figure-structure', width='100%', height=630)
```

## 创建图

本节总结了使用`plotly.py`图形库创建新的图形对象图形的几种方法。

>  推荐使用 [Plotly Express](plotly:plotly-express)

### Plotly Express

[Plotly Express](plotly:plotly-express)（来自 `plotly.express` 模块）是一个高级数据可视化 API，它可以在单个函数调用中生成完全填充的图对象。

```{code-cell} ipython3
import plotly.express as px

df = px.data.iris()
fig = px.scatter(df, x="sepal_width", y="sepal_length", color="species", title="A Plotly Express Figure")
# 如果您打印图，您将看到它只是一个带有数据和布局的常规图
# print(fig)
fig.show()
```

### 图对象 `Figure` 构造器

如上所示，您可以通过将轨迹和布局规范传递给 `plotly.graph_objects.Figure` 构造函数来构建完整的图。这些轨迹和布局规范可以是字典或图对象。

在下面的示例中，使用图对象指定轨迹，并将布局指定为字典。

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure(
    data=[go.Bar(x=[1, 2, 3], y=[1, 3, 2])],
    layout={'title': {'text': '由图形对象指定的图形'}}
)

fig.show()
```

### 图工厂

[](plotly:figure-factories)（在 `plotly.py` 中的 `plotly.figure_factory` 模块中）是生成图对象的函数，通常是为了满足专门领域的需求。下面是使用 `create_quiver()` 图工厂构造一个图对象的示例，该图形显示 2D 箭头（quiver）图。

```{code-cell} ipython3
import numpy as np
import plotly.figure_factory as ff

x1,y1 = np.meshgrid(np.arange(0, 2, .2), np.arange(0, 2, .2))
u1 = np.cos(x1)*y1
v1 = np.sin(x1)*y1

fig = ff.create_quiver(x1, y1, u1, v1)
fig.show()
```

### 创建子图

`plotly.subplots.make_subplots()` 函数生成一个图对象，该图预先配置了一组可添加轨迹的子图网格。下面将进一步讨论 `add_trace()` 函数。

```{code-cell} ipython3
from plotly.subplots import make_subplots

fig = make_subplots(rows=1, cols=2)
fig.add_trace(go.Scatter(y=[4, 2, 1], mode="lines"),
              row=1, col=1)
fig.add_trace(go.Bar(y=[2, 1, 3]),
              row=1, col=2)

fig.show()
```

## 更新图

无论图对象是如何构造的，都可以通过向其添加额外的轨迹并修改其属性来更新它。

### 添加轨迹

可以使用 `add_trace()` 方法将新的轨迹添加到图对象中。这个方法接受一个图对象轨迹（`go.Scatter`，`go.Bar` 等的实例），并将其添加到图中。这允许您从一个空的图形开始，并按顺序向其添加轨迹。`append_trace()` 方法做同样的事情，尽管它不返回图。

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure()
fig.add_trace(go.Bar(x=[1, 2, 3], y=[1, 3, 2]))

fig.show()
```

您还可以向图工厂或 Plotly Express 生成的图添加轨迹：

```{code-cell} ipython3
import plotly.express as px

df = px.data.iris()
fig = px.scatter(df, x="sepal_width", y="sepal_length", color="species",
                 title="使用 add_trace() 方法的 Plotly Express 图")

fig.add_trace(
    go.Scatter(
        x=[2, 4],
        y=[4, 8],
        mode="lines",
        line=go.scatter.Line(color="gray"),
        showlegend=False)
)
fig.show()
```

### 给子图添加轨迹

如果使用 `plotly.subplots.make_subplots()` 创建了图，那么可以使用 `add_trace()` 提供的 `row` 和 `col` 参数向特定的子图添加轨迹。

```{code-cell} ipython3
from plotly.subplots import make_subplots

fig = make_subplots(rows=1, cols=2)

fig.add_trace(go.Scatter(y=[4, 2, 1], mode="lines"), row=1, col=1)
fig.add_trace(go.Bar(y=[2, 1, 3]), row=1, col=2)

fig.show()
```

这也适用于 Plotly Express 使用 `facet_row` 和/或 `facet_col` 参数创建的图。

```{code-cell} ipython3
import plotly.express as px

df = px.data.iris()

fig = px.scatter(df, x="sepal_width", y="sepal_length", color="species", facet_col="species",
                 title="Adding Traces To Subplots Witin A Plotly Express Figure")

reference_line = go.Scatter(x=[2, 4],
                            y=[4, 8],
                            mode="lines",
                            line=go.scatter.Line(color="gray"),
                            showlegend=False)

fig.add_trace(reference_line, row=1, col=1)
fig.add_trace(reference_line, row=1, col=2)
fig.add_trace(reference_line, row=1, col=3)

fig.show()
```

### 添加轨迹的便利函数

作为 `add_trace()` 方法的替代方法，图对象图形有一系列 `add_{trace}` 形式的方法（其中 `{trace}` 是一个轨迹类型的名称），用于构造和添加每种轨迹类型的轨迹。

下面是前面的子图示例，使用 `fig.add_scatter()` 添加散点轨迹，使用 `fig.add_bar()` 添加条形轨迹。

```{code-cell} ipython3
from plotly.subplots import make_subplots

fig = make_subplots(rows=1, cols=2)

fig.add_scatter(y=[4, 2, 1], mode="lines", row=1, col=1)
fig.add_bar(y=[2, 1, 3], row=1, col=2)

fig.show()
```

(plotly:magic-underscore-notation)=
### 魔法下划线表示法

为了更容易地使用嵌套属性，图对象构造器和许多图对象方法都支持魔法下划线表示法。

这允许通过使用下划线将多个嵌套属性名连接在一起来引用嵌套属性。

例如，在没有魔法下划线符号的图构造函数中指定`figure`标题需要将 `layout` 参数设置为 `dict(title=dict(text="A Chart"))`。类似地，设置散点轨迹的线条颜色需要将 `marker` 属性设置为 `dict(color="crimson")`。

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure(
    data=[go.Scatter(y=[1, 3, 2], line={'color': 'crimson'})],
    layout={'title':
            {'text': 'A Graph Objects Figure Without Magic Underscore Notation'}}
)

fig.show()
```

使用魔法的下划线符号，您可以通过向图构造函数传递一个名为 `layout_title_text` 的关键字参数和传递 `go.Scatter`构造函数的关键字参数 `line_color`。

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure(
    data=[go.Scatter(y=[1, 3, 2], line_color="crimson")],
    layout_title_text="A Graph Objects Figure With Magic Underscore Notation"
)

fig.show()
```

整个图对象 API 都支持魔法下划线表示法，它通常可以显著地简化涉及深度嵌套属性的操作。

```{important}
当您看到带下划线的关键字参数被传递给图对象构造函数或方法时，几乎总是可以假定它是一个魔法下划线表示法的应用程序。我们必须使用“almost always”而不是“always”，因为 plotly 模式中有一些包含下划线的属性名：`error_x`、`error_y`、`error_z`、`copy_xstyle`、`copy_ystyle`、`copy_zstyle`、`paper_bgcolor` 和 `plot_bgcolor`。这些是在我们规范禁止在属性名称中使用下划线之前(2012-2013年)添加的。
```

### 更新图布局

图对象图形支持 `update_layout()` 方法，该方法可用于更新图布局的多个嵌套属性。

下面是一个使用 `update_layout()` 更新图标题的文本和字体大小的示例。

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure(data=go.Bar(x=[1, 2, 3], y=[1, 3, 2]))
fig.update_layout(title_text="Using update_layout() With Graph Object Figures",
                  title_font_size=30)

fig.show()
```

注意，下面的 `update_layout()` 操作是等价的：

```{code-cell} ipython3
fig.update_layout(title_text="update_layout() Syntax Example",
                  title_font_size=30)

fig.update_layout(title_text="update_layout() Syntax Example",
                  title_font=dict(size=30))


fig.update_layout(title=dict(text="update_layout() Syntax Example"),
                             font=dict(size=30))

fig.update_layout({"title": {"text": "update_layout() Syntax Example",
                             "font": {"size": 30}}})

fig.update_layout(title=go.layout.Title(text="update_layout() Syntax Example",
                                        font=go.layout.title.Font(size=30)))
```

### 更新轨迹

图对象支持 `update_trace()` 方法，该方法可用于更新图的一个或多个轨迹的多个嵌套属性。

为了展示一些示例，我们将从一个包含横跨两个子图的条形和散点轨迹的图开始。

```{code-cell} ipython3
from plotly.subplots import make_subplots

fig = make_subplots(rows=1, cols=2)
fig.add_scatter(y=[4, 2, 3.5], mode="markers",
                marker=dict(size=20, color="LightSeaGreen"),
                name="a", row=1, col=1)

fig.add_bar(y=[2, 1, 3],
            marker=dict(color="MediumPurple"),
            name="b", row=1, col=1)

fig.add_scatter(y=[2, 3.5, 4], mode="markers",
                marker=dict(size=20, color="MediumPurple"),
                name="c", row=1, col=2)

fig.add_bar(y=[1, 3, 2],
            marker=dict(color="LightSeaGreen"),
            name="d", row=1, col=2)

fig.show()
```

注意 `scatter` 轨迹和 `bar` 轨迹都有一个 `marker.color` 属性来控制它们的颜色。下面是一个使用 `update_trace()` 修改所有轨迹颜色的示例。

```{code-cell} ipython3
from plotly.subplots import make_subplots

fig = make_subplots(rows=1, cols=2)

fig.add_scatter(y=[4, 2, 3.5], mode="markers",
                marker=dict(size=20, color="LightSeaGreen"),
                name="a", row=1, col=1)

fig.add_bar(y=[2, 1, 3],
            marker=dict(color="MediumPurple"),
            name="b", row=1, col=1)

fig.add_scatter(y=[2, 3.5, 4], mode="markers",
                marker=dict(size=20, color="MediumPurple"),
                name="c", row=1, col=2)

fig.add_bar(y=[1, 3, 2],
            marker=dict(color="LightSeaGreen"),
            name="d", row=1, col=2)

fig.update_traces(marker=dict(color="RoyalBlue"))

fig.show()
```

`update_trace()` 方法支持一个 `selector` 参数来控制应该更新哪些轨迹。只有具有匹配选择器属性的轨迹才会被更新。下面是一个使用选择器只更新条轨迹颜色的例子。

```{code-cell} ipython3
from plotly.subplots import make_subplots

fig = make_subplots(rows=1, cols=2)

fig.add_scatter(y=[4, 2, 3.5], mode="markers",
                marker=dict(size=20, color="LightSeaGreen"),
                name="a", row=1, col=1)

fig.add_bar(y=[2, 1, 3],
            marker=dict(color="MediumPurple"),
            name="b", row=1, col=1)

fig.add_scatter(y=[2, 3.5, 4], mode="markers",
                marker=dict(size=20, color="MediumPurple"),
                name="c", row=1, col=2)

fig.add_bar(y=[1, 3, 2],
            marker=dict(color="LightSeaGreen"),
            name="d", row=1, col=2)

fig.update_traces(marker=dict(color="RoyalBlue"),
                  selector=dict(type="bar"))

fig.show()
```

魔法下划线表示法可以在选择器中用于匹配嵌套属性。下面是一个更新所有被正式着色为`"MediumPurple"`的轨迹颜色的例子。

```{code-cell} ipython3
from plotly.subplots import make_subplots

fig = make_subplots(rows=1, cols=2)

fig.add_scatter(y=[4, 2, 3.5], mode="markers",
                marker=dict(size=20, color="LightSeaGreen"),
                name="a", row=1, col=1)

fig.add_bar(y=[2, 1, 3],
            marker=dict(color="MediumPurple"),
            name="b", row=1, col=1)

fig.add_scatter(y=[2, 3.5, 4], mode="markers",
                marker=dict(size=20, color="MediumPurple"),
                name="c", row=1, col=2)

fig.add_bar(y=[1, 3, 2],
            marker=dict(color="LightSeaGreen"),
            name="d", row=1, col=2)

fig.update_traces(marker_color="RoyalBlue",
                  selector=dict(marker_color="MediumPurple"))

fig.show()
```

对于带有子图的图，`update_trace()` 方法还支持 `row` 和 `col` 参数来控制应该更新哪些轨迹。只会更新指定子图行和列中的轨迹。下面是一个更新第二个子图列中所有轨迹颜色的示例。

```{code-cell} ipython3
from plotly.subplots import make_subplots

fig = make_subplots(rows=1, cols=2)

fig.add_scatter(y=[4, 2, 3.5], mode="markers",
                marker=dict(size=20, color="LightSeaGreen"),
                name="a", row=1, col=1)

fig.add_bar(y=[2, 1, 3],
            marker=dict(color="MediumPurple"),
            name="b", row=1, col=1)

fig.add_scatter(y=[2, 3.5, 4], mode="markers",
                marker=dict(size=20, color="MediumPurple"),
                name="c", row=1, col=2)

fig.add_bar(y=[1, 3, 2],
            marker=dict(color="LightSeaGreen"),
            name="d", row=1, col=2)

fig.update_traces(marker=dict(color="RoyalBlue"),
                  col=2)

fig.show()
```

`update_trace()` 方法还可以用于由图工厂或 Plotly Express 生成的图。下面是一个将 Plotly Express 生成的回归线更新为虚线的示例。

```{code-cell} ipython3
import pandas as pd
import plotly.express as px

df = px.data.iris()

fig = px.scatter(df, x="sepal_width", y="sepal_length", color="species",
                 facet_col="species", trendline="ols", title="Using update_traces() With Plotly Express Figures")

fig.update_traces(
    line=dict(dash="dot", width=4),
    selector=dict(type="scatter", mode="lines"))

fig.show()
```

### 使用更新方法 `overwrite` 现有属性

`update_layout()` 和 `update_trace()` 有一个 `overwrite` 关键字参数，默认值为 `False`，在这种情况下，更新将递归地应用于现有嵌套的属性结构。当设置为 `True` 时，现有属性的先前值将被提供的值覆盖。

在下面的例子中，当使用 `overwrite=True` 更新 `update_trace()` 中的标记时，标记的红色会被覆盖。请注意，使用魔法下划线来设置 `marker_opacity` 并不会覆盖 `marker_color`，因为属性只会从 `marker.opacity` 级别开始覆写。

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure(go.Bar(x=[1, 2, 3], y=[6, 4, 9],
                       marker_color="red")) # will be overwritten below

fig.update_traces(overwrite=True, marker={"opacity": 0.4})

fig.show()
```

### 有条件地更新轨迹

假设要对轨迹集合进行的更新依赖于某些轨迹属性的当前值。`update_trace()` 方法不能处理这种情况，但是`for_each_trace()`方法可以！

作为它的第一个参数，`for_each_trace()` 方法每次接受并更新一个轨迹的函数。与 `update_trace()` 一样，`for_each_trace()` 也接受 `selector,`、`row` 和 `col`  参数来控制应该考虑哪些轨迹。

下面是一个使用 `for_each_trace()` 将“setosa”的唯一标记转换为 Plotly Express 图中的方形符号的示例。

注意，**这是可能的，因为 Plotly Express 图由输入数据帧中的每个列的单独轨迹组成。**

```{code-cell} ipython3
import pandas as pd
import plotly.express as px

df = px.data.iris()

fig = px.scatter(df, x="sepal_width", y="sepal_length", color="species",
                 title="Conditionally Updating Traces In A Plotly Express Figure With for_each_trace()")

fig.for_each_trace(
    lambda trace: trace.update(marker_symbol="square") if trace.name == "setosa" else (),
)

fig.show()
```

### 更新轴

图对象支持 `update_xaxes()` 和 `update_yaxes()` 方法，它们可用于更新图的一个或多个轴的多个嵌套属性。下面是一个使用 `update_xaxes()` 禁用 Plotly Express 生成的图中所有子图上的垂直网格线的示例。

```{code-cell} ipython3
import pandas as pd
import plotly.express as px

df = px.data.iris()

fig = px.scatter(df, x="sepal_width", y="sepal_length", color="species",
                 facet_col="species", title="Using update_xaxes() With A Plotly Express Figure")

fig.update_xaxes(showgrid=False)

fig.show()
```

还有 `for_each_xaxis()` 和 `for_each_yaxis()` 方法类似于上面描述的 `for_each_trace()` 方法。对于非笛卡儿子图类型(例如 polar)，有额外的 `update_{type}` 和 `for_each_{type}` 方法(例如 `update_polar()`，`for_each_polar()`)。

## 其他更新方法

使用 `plot.py` 图库创建的图形也支持：

- `update_layout_images()` 方法用于 [更新背景布局图像](plotly:images)
- `update_annotations()` 用来 [更新注释](plotly:text-and-annotations)
- `update_shapes()` 用来 [更新形状](plotly:shapes)

### 链接图操作

上面描述的所有图更新操作都是返回对正在修改的图的引用的方法。这使得将多个图形修改操作连接到一个表达式成为可能。

下面是一个创建链接表达式的示例：

- 使用 Plotly Express 绘制带有 OLS 趋势线的 faceted scatter plot
- 使用 `update_layout()` 设置标题字体大小
- 使用 `update_xaxes()` 禁用垂直网格线
- 使用 `update_trace()` 更新趋势线的宽度和横线模式
- 然后使用 `show()` 显示图

```{code-cell} ipython3
import plotly.express as px

df = px.data.iris()

(px.scatter(df, x="sepal_width", y="sepal_length", color="species",
            facet_col="species", trendline="ols",
            title="Chaining Multiple Figure Operations With A Plotly Express Figure")
 .update_layout(title_font_size=24)
 .update_xaxes(showgrid=False)
 .update_traces(
     line=dict(dash="dot", width=4),
     selector=dict(type="scatter", mode="lines"))
).show()
```

### 属性赋值

轨迹和布局属性可以使用属性赋值语法进行更新。下面是一个使用属性赋值设置图标题的示例。

```{code-cell} ipython3
import plotly.graph_objects as go
fig = go.Figure(data=go.Bar(x=[1, 2, 3], y=[1, 3, 2]))
fig.layout.title.text = "Using Property Assignment Syntax With A Graph Object Figure"
fig.show()
```

下面是一个使用属性赋值更新条形轮廓的例子。

```{code-cell} ipython3
import plotly.graph_objects as go

fig = go.Figure(data=go.Bar(x=[1, 2, 3], y=[1, 3, 2]))

fig.data[0].marker.line.width = 4
fig.data[0].marker.line.color = "black"

fig.show()
```
