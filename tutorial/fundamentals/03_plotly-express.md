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

(plotly:plotly-express)=
# Plotly Express

参考：[Plotly Express | Python | Plotly](https://plotly.com/plotly-express/)

Plotly Express 是一个用于创建图的简洁、一致的高级 API。

## 概览

`plotly.express` 模（通常被导入记作 `px`）包含可以一次创建整个图的函数，称为Plotly Express或 PX。Plotly Express 是 `plotly` 库的内置部分，是创建大多数常见图的推荐起点。每个 Plotly Express 函数在内部使用 [graph objects](plotly:graph-objects) 并返回 `plotly.graph_objects.Figure` 实例。在整个 `plotly` 文档中，您将在任何适用页面的顶部找到构建图形的 plotly Express 方法，然后是关于如何使用图对象构建类似图的部分。在 Plotly Express 的一个函数调用中创建的任何图形都可以单独使用图对象创建，但需要5到100倍以上的代码。

Plotly Express [提供了 30 多个函数，用于创建不同类型的图](https://plotly.com-api-reference/plotly.express.html)。这些函数的 API被精心设计成尽可能的一致和易于学习，使得在整个数据探索过程中从散点图到柱状图到直方图再到太阳爆发图都很容易转换。向下滚动查看 Plotly Express 图的图库，每个图都是在一个函数调用中完成的。

Plotly Express 目前包含以下函数：

* **Basics**: [`scatter`](../basic-charts/line-and-scatter), [`line`](../basic-charts/line-charts), [`area`](../basic-charts/filled-area-plots/), [`bar`](../basic-charts/bar-charts), [`funnel`](../financial-charts/funnel-charts), [`timeline`](https://plotly.com/gantt/)
* **Part-of-Whole**: [`pie`](../basic-charts/pie-charts), [`sunburst`](../basic-charts/sunburst-charts), [`treemap`](../basic-charts/treemaps), [`funnel_area`](../financial-charts/funnel-charts)
* **1D Distributions**: [`histogram`](../statistical-charts/histograms), [`box`](../statistical-charts/box-plots), [`violin`](../statistical-charts/violin), [`strip`](../statistical-charts/strip-charts)
* **2D Distributions**: [`density_heatmap`](../statistical-charts/2D-Histogram), [`density_contour`](../statistical-charts/2d-histogram-contour)
* **Matrix Input**: [`imshow`](../scientific-charts/imshow)
* **3-Dimensional**: [`scatter_3d`](../3d-charts/3d-scatter-plots), [`line_3d`](../3d-charts/3d-line-plots)
* **Multidimensional**: [`scatter_matrix`](../statistical-charts/splom), [`parallel_coordinates`](../scientific-charts/parallel-coordinates-plot), [`parallel_categories`](../statistical-charts/parallel-categories-diagram/)
* **Tile Maps**: [`scatter_mapbox`](../maps/scattermapbox), [`line_mapbox`](../maps/lines-on-mapbox/), [`choropleth_mapbox`](../maps/mapbox-county-choropleth/), [`density_mapbox`](../maps/mapbox-density-heatmaps)
* **Outline Maps**: [`scatter_geo`](../maps/scatter-plots-on-maps/), [`line_geo`](../maps/lines-on-maps), [`choropleth`](../maps/choropleth-maps)
* **Polar Charts**: [`scatter_polar`](../scientific-charts/polar-chart), [`line_polar`](../scientific-charts/polar-chart), [`bar_polar`](../scientific-charts/wind-rose-charts)
* **Ternary Charts**: [`scatter_ternary`](../scientific-charts/ternary-plots), [`line_ternary`](../scientific-charts/ternary-plots)

