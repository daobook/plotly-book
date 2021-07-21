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

(plotly:figure-structure)=
# 图数据结构

参考：[The Figure Data Structure | Python | Plotly](https://plotly.com/python/figure-structure/)

对图的数据结构、轨迹和布局进行说明。

## 概览

`plotly` Python 包的存在是为了 [创建、操作](plotly:creating-and-updating-figures) 和 [渲染](plotly:renderers) 由数据结构(也称为 figures)表示的图(即 charts, plots, maps 和 diagrams)。虽然渲染过程在底层使用 [Plotly.js JavaScript 库](https://plotly.com/javascript/)，但使用该模块的 Python 开发人员很少需要直接与 JavaScript 库交互。在 Python 中，图形可以表示为 `dict`，也可以表示为 `plotly.graph_objects.Figure` 类的实例，并在传递给 `Plotly.js` 之前以 [JavaScript 对象表示法](https://json.org/)(JSON)的文本形式序列化。

```{note}
`plotly` 包的推荐入口点是高级 [`plotly.express` 模块，也称为 Plotly Express](plotly:plotly-express) ，它由返回 `plotly.graph_objects.Figure` 对象的函数组成。此页面用于记录这些对象所表示的数据结构体系，以便希望更多地了解如何自定义它们或从其他图形组装它们的用户 [使用 plotly.graph_objects 组件](plotly:graph-objects)。
```

查看任何 `plotly.graph_objects.Figure` 的底层数据结构，包括那些由 Plotly Express 返回的对象，可以通过 `print(fig)` 或在 JupyterLab 中，使用特殊的 `fig.show("json")` 渲染器来实现。图也支持 `fig.to_dict()` 和 `fig.to_json()` 方法。`print()` 图将打印冗长的内容。为简便起见 `layout.template` 被表示为 `'...'`。

```{code-cell} ipython3
import plotly.express as px
import plotly.graph_objects as go

fig = px.line(x=["a","b","c"], y=[1,3,2], title="sample figure")
print(fig)
f = go.FigureWidget(fig) # go.FigureWidget 保证正常显示
f
```

## 在 Dash 中访问图形结构

```{code-cell} ipython3
:tags: [remove-input]

from IPython.display import IFrame
snippet_url = 'https://dash-gallery.plotly.host/python-docs-dash-snippets/'
IFrame(snippet_url + 'figure-structure', width='100%', height=630)
```

## 图作为属性树

`Plotly.js` 支持良定义的模式的输入，该模式的总体架构在本页面中进行了解释，[图参考](https://plotly.com/python/reference/index/)(图参考本身是由模式的[机器可读 JSON 表示](https://raw.githubusercontent.com/plotly/plotly.js/master/dist/plot-schema.json)生成的)中详细记录了它。图以树的形式表示，树的命名节点称为“属性”。树的根节点有三个顶级属性：`data`、 `layout` 和 `frames`。

在文本和[图参考](https://plotly.com/python/reference/index/)中，属性是通过其完整的“路径”来引用的，即以点分隔的父级连接。例如 `"layout.width"` 指的是在一个字典中其键为`width`的属性，该属性是与图根的键`layout`相关联的值。如果父类中的一个是列表而不是字典，当引用抽象中的属性时，会在路径中插入一组方括号 `layout.annotations[].text`。最后，如下所述，顶级 `data` 属性定义了一个称为 “轨迹” 的类型化对象列表，其模式依赖于该类型，这些属性的路径在此引用中列出为 `"data[type=scatter].name"`。

[`plotly.graph_objects` 模块包含一个自动生成的 Python 类层次结构](plotly:graph-objects)，这些类代表图模式中的非叶子属性，并为它们提供了一个 Python API。当 [操作 `plotly.graph_objects.Figure` 对象](plotly:creating-and-updating-figures) 时，属性可以直接使用 Python 对象属性设置，例如`fig.layout.title.font.family="Open Sans"`或使用 `update` 方法和 [](plotly:magic-underscore-notation)，例如 `fig.update_layout(title_font_family="Open Sans")`。

在构建图形时，没有必要填充每个对象的每个属性。在渲染时，JavaScript 层将计算每个需要的未指定属性的默认值，具体取决于指定的属性，如本页所示。例如`layout.xaxis.range`，它可以显式指定，但是如果没有，将基于链接到该轴的每个跟踪的 `x` 值的范围进行计算。尽管 `plotly.graph_objects`模块为属性值提供了 Python 端验证，但 JavaScript 层将忽略未知的属性或畸形的值。还请注意，如果[ `layout.template` 键](plotly:templates)是存在的(因为它是默认的)，那么默认值将首先从模板的内容中提取，只有在那里缺失时，JavaScript 层才会推断出进一步的默认值。内置模板可以通过设置`layout.template="none"`来禁用。

## 顶级 `data` 属性

图的三个顶级属性中的第一个是 `data`，它的值必须是一个称为“轨迹”（"traces"）的字典列表。

* 每个轨迹可能是超过 40 种类型中的一种(见下面按子图类型组织的列表，包括例如 {doc}`../basic-charts/line-and-scatter`, {doc}`../basic-charts/bar-charts`, {doc}`../basic-charts/pie-charts`, {doc}`../3d-charts/3d-surface-plots`, {doc}`../maps/choropleth-maps` 等)，并表示一组相关的图形标记。每个轨迹必须有一个 `type` 属性，该属性定义其他被允许的属性。
* 每个轨迹都绘制在一个 [](plotly:subplots) 上，该子图的类型必须与轨迹的类型兼容，或者是它自己的子图(参见下面)。
* 轨迹可能只有一个 [](plotly:legend) 项，但饼图和漏斗区域轨迹（funnelarea traces）除外(见下文)。
* 某些轨迹类型支持 [带有关联颜色条的连续颜色](plotly:colorscales)，当使用 `coloraxis` 属性时，可以通过轨迹内的属性或布局内的属性来控制颜色条。

## 顶级 `layout` 属性

图的三个顶级属性中的第二个是 `layout`，它的值在文本中称为"布局"，必须是一个字典，包含控制图中非数据相关部分的定位和配置的属性，例如：

- 尺寸和边距，定义了“纸张坐标”的边界
- 图的默认值：[模板](plotly:templates)、[字体、标题、颜色、悬停标签](plotly:figure-labels) 和 mobar
- 可在容器和/或纸坐标中定位 [标题](plotly:figure-labels) 和 [](plotly:legend)
- 可在纸坐标中定位 [标题](plotly:figure-labels) 和 [颜色轴和相关的颜色条](plotly:colorscales)
- 各种类型的子图，可在其上绘制多个轨迹，并在纸坐标中定位：
  - `xaxis`, `yaxis`, `xaxis2`, `yaxis3` 等：X 和 Y 笛卡儿轴，它们的交点是笛卡儿子图
  - `scene`, `scene2`, `scene3`等：3D 场景子图
  - `ternary`, `ternary2`, `ternary3`, `polar`, `polar2`, `polar3`, `geo`, `geo2`, `geo3`, `mapbox`, `mapbox2`, `mabox3`等：三元，极坐标，地理或 mapbox 子图
- 非数据标记，可以在纸坐标中定位，也可以在链接到二维笛卡尔子图的数据坐标中定位
  * `annotations`：[带有或不带有箭头的文本注释](plotly:text-and-annotations)
  * `shapes`：[直线，矩形，椭圆或开放或封闭的路径](plotly:shapes)
  * `images`：[背景或装饰图像](plotly:images)
- 可以在纸坐标中定位的控件，当用户与之交互时，可以触发 Plotly.js 函数
  * `updatemenus`：{doc}`../controls/custom-buttons` 和 {doc}`../controls/dropdowns`
  * `sliders`：{doc}`../controls/sliders`

## 顶级 `frames` 属性

图的三个顶级属性中的第三个是 `frames`，它的值必须是一个字典列表，在 {doc}`animation plot<../animations/index>` 中定义顺序帧。每个帧包含自己的数据属性和其他参数。动画通常是通过 `layout.sliders` 和/或 `layout.updatemenus` 定义的控件来触发和控制的。

## `config` 对象

在 [](plotly:renderers) 时，也可以控制某些不属于图的行为，例如“modebar”的行为，以及图如何与鼠标操作(如滚动等)相关联。包含这些选项的对象称为 `config`，[并且有自己的文档页](plotly:configuration-options)。它在 Python 中作为 `plotly.graph_objects.Figure` 对象上的 `.show()` 方法的 `config` 关键字参数。

## 定位纸，容器坐标，或轴域坐标

在图布局中配置的各种图组件支持名为 `x` 或 `y` 的定位属性，它们的值可以在“纸坐标”(有时称为“绘图分数”（"plot fractions"）或“标准化坐标”（"normalized coordinates"）)中指定。例如，包含 `layout.xaxis.domain` 或 `layout.legend.x` 或 `layout.annotation[].x`。

在纸坐标中定位不是绝对像素，而是相对于原点 `(0,0)` 在 `(layout.margin.l, layout.margin.b)`，且 `(1,1)` 在 `(layout.width-layout.margin.r, layout.height-layout.margin.t)` 处d定义的(注：`layout.margin` 是像素值，`layout.width` 也是)。允许纸张坐标值小于 0 或大于 1，指示 plot 边缘的区域。

要在“纸”坐标中定位一个对象，相应的轴参考系被设置为 `"paper"`。例如，一个形状的 `xref` 属性将被设置为 `"paper"`，因此形状的 `x` 值指的是它在纸坐标中的位置。

请注意 `layout.margin` 属性的内容，默认情况下是根据某些项(如标题或图例)的位置和尺寸计算的，当将 `layout.xaxis.autommargin` 属性设置为 `True` 时，也可以根据 `tick` 标签的位置和尺寸计算。这将自动增加边界值，从而缩小 `(0,0)` 和 `(1,1)` 点之间定义的物理区域。将某些项定位于小于 0 或大于 1 的纸坐标也会触发此行为。然而，`layout.width` 和 `layout.height` 被认为是既定的，所以这个图永远不会因其内容而增加或减少。

The figure title may be positioned using "container coordinates" which have `(0,0)` and `(1,1)` anchored at the bottom-left and top-right of the figure, respectively, and therefore are independent of the values of layout.margin.

Furthermore, shapes, annotations, and images can be placed relative to an axis's
domain so that, for instance, an `x` value of `0.5` would place the object
halfway along the x-axis, regardless of the domain as specified in the
`layout.xaxis.domain` attribute. This behavior can be specified by adding
`' domain'` to the axis reference in the axis referencing attribute of the object.
For example, setting `yref = 'y2 domain'` for a shape will refer to the length
and position of the axis named `y2`.

## 2D 笛卡尔轨迹类型和子图

最常用的一种子图是 [二维笛卡尔（Cartesian）子图](plotly:axes)。与这些子绘图兼容的轨迹支持 `xaxis` 和 `yaxis` 属性，它们的值必须引用图布局部分中的相应对象。例如，如果 `xaxis="x"`，且 `yaxis="y"`(这是默认值)，则该轨迹将绘制在轴配置下的 `layout.xaxis` 和 `layout.xaxis2` 的交集

布局中配置的轴的交点的子绘图上。xaxis和布局。Yaxis，但如果xaxis="x2"和Yaxis ="y3"，则轨迹绘制在布局中配置的轴的交点上。xaxis2 layout.yaxis3。

For example, if `xaxis="x"`, and `yaxis="y"` (which is the default) then this trace is drawn on the subplot at the intersection of the axes configured under `layout.xaxis` and `layout.yaxis`, but if `xaxis="x2"` and `yaxis="y3"` then the trace is drawn at the intersection of the axes configured under `layout.xaxis2` and `layout.yaxis3`. Note that attributes such as `layout.xaxis` and `layout.xaxis2` etc do not have to be explicitly defined, in which case default values will be inferred. Multiple traces of different types can be drawn on the same subplot.

X- and Y-axes support the `type` attribute, which enables them to represent [continuous values (`type="linear"`, `type="log"`)](plotly:axes), [temporal values (`type="date"`)](../financial-charts/time-series) or [categorical values (`type="category"`, `type="multicategory`)](../basic-charts/bar-charts). Axes can also be overlaid on top of one another to create [dual-axis or multiple-axis charts](plotly:multiple-axes). 2-d cartesian subplots lend themselves very well to creating ["small multiples" figures, also known as facet or trellis plots](../statistical-charts/facet-plots).

The following trace types are compatible with 2d-cartesian subplots via the `xaxis` and `yaxis` attributes:

* scatter-like trace types: [`scatter`](../basic-charts/line-and-scatter) and [`scattergl`](../basic-charts/webgl-vs-svg), which can be used to draw [`scatter`](../basic-charts/line-and-scatter), [line plots and curves](../basic-charts/line-charts), [time-series plots](../financial-charts/time-series), [bubble charts](../basic-charts/bubble-charts), [dot plots](../basic-charts/dot-plots) and [filled areas](../basic-charts/filled-area-plots) and also support [error bars](../statistical-charts/error-bars)
* [`bar`](../basic-charts/bar-charts), [`funnel`](../financial-charts/funnel-charts), [`waterfall`](../financial-charts/waterfall-charts): bar-like trace types which can also be used to draw [timelines and Gantt charts](../basic-charts/gantt)
* [`histogram`](../statistical-charts/histograms): an *aggregating* bar-like trace type
* [`box`](../statistical-charts/box-plots) and [`violin`](../statistical-charts/box-plots): 1-dimensional distribution-like trace types
* [`histogram2d`](../statistical-charts/2D-Histogram) and [`histogram2dcontour`](../statistical-charts/2d-histogram-contour): 2-dimensional distribution-like density trace types
* [`image`](../scientific-charts/imshow), [`heatmap`](../scientific-charts/heatmaps) and [`contour`](../scientific-charts/contour-plots): matrix trace types
* [`ohlc`](../financial-charts/ohlc-charts) and [`candlestick`](../financial-charts/candlestick-charts): stock-like trace types
* [`carpet`](../scientific-charts/carpet-plot): a special trace type for building [carpet plots](../scientific-charts/carpet-plot), in that other traces can use as subplots (see below)
* [`splom`](../statistical-charts/splom): multi-dimensional scatter plots which implicitly refer to many 2-d cartesian subplots at once.

## 3D, Polar and Ternary Trace Types and Subplots

Beyond 2D cartesian subplots, figures can include [three-dimensional cartesian subplots](../3d-charts/index), [polar subplots](../scientific-charts/polar-chart) and [ternary subplots](../scientific-charts/ternary-plots). The following trace types support attributes named `scene`, `polar` or `ternary`, whose values must refer to corresponding objects in the layout portion of the figure i.e. `ternary="ternary2"` etc. Note that attributes such as `layout.scene` and `layout.ternary2` etc do not have to be explicitly defined, in which case default values will be inferred. Multiple traces of a compatible type can be placed on the same subplot.

The following trace types are compatible with 3D subplots via the `scene` attribute, which contains special [camera controls](../3d-charts/3d-camera-controls):

* [`scatter3d`](../3d-charts/3d-scatter-plots), which can be used to draw [individual markers](../3d-charts/3d-scatter-plots), [3d bubble charts](../3d-charts/3d-bubble-charts) and [lines and curves](../3d-charts/3d-line-plots)
* [`surface`](../3d-charts/3d-surface-plots) and [`mesh`](../3d-charts/3d-mesh): 3d surface trace types
* [`cone`](../3d-charts/cone-plot) and [`streamtube`](../3d-charts/streamtube-plot): 3d vector field trace types
* [`volume`](../3d-charts/3d-volume-plots) and [`isosurface`](../3d-charts/3d-isosurface-plots): 3d volume trace types

The following trace types are compatible with polar subplots via the `polar` attribute:

* scatter-like trace types: [`scatterpolar` and `scatterpolargl`](../scientific-charts/polar-chart), which can be used to draw individual markers, [curves and filled areas (i.e. radar or spider charts)](../scientific-charts/radar-chart)
* [`barpolar`](../scientific-charts/wind-rose-charts): useful for [wind roses](../scientific-charts/wind-rose-charts) and other polar bar charts

The following trace types are compatible with ternary subplots via the `ternary` attribute:

* [`scatterternary`](../scientific-charts/ternary-plots), which can be used to draw individual markers, [curves and filled areas](../scientific-charts/ternary-contour)

## Map Trace Types and Subplots

Figures can include two different types of map subplots: [geo subplots for outline maps](https://plotly.com/python/map-configuration/) and [mapbox subplots for tile maps](https://plotly.com/python/mapbox-layers/). The following trace types support attributes named `geo` or `mapbox`, whose values must refer to corresponding objects in the layout i.e. `geo="geo2"` etc. Note that attributes such as `layout.geo2` and `layout.mapbox` etc do not have to be explicitly defined, in which case default values will be inferred. Multiple traces of a compatible type can be placed on the same subplot.

The following trace types are compatible with geo subplots via the `geo` attribute:

*   [`scattergeo`](https://plotly.com/python/scatter-plots-on-maps/), which can be used to draw [individual markers](https://plotly.com/python/scatter-plots-on-maps/), [line and curves](https://plotly.com/python/lines-on-maps/) and filled areas on outline maps
*   [`choropleth`](https://plotly.com/python/choropleth-maps/): [colored polygons](https://plotly.com/python/choropleth-maps/) on outline maps

The following trace types are compatible with mapbox subplots via the `mapbox` attribute:

*   [`scattermapbox`](https://plotly.com/python/scattermapbox/), which can be used to draw [individual markers](https://plotly.com/python/scattermapbox/), [lines and curves](https://plotly.com/python/lines-on-mapbox/) and [filled areas](https://plotly.com/python/filled-area-on-mapbox/) on tile maps
*   [`choroplethmapbox`](https://plotly.com/python/mapbox-county-choropleth/): colored polygons on tile maps
*   [`densitymapbox`](https://plotly.com/python/mapbox-density-heatmaps/): density heatmaps on tile maps

