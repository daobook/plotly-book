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

(plotly:tutorial)=
# `plotly.py` 概览

```{important}
本篇内容大都翻译自 [plotly/plotly.py: The interactive graphing library for Python (includes Plotly Express) ](https://github.com/plotly/plotly.py)，还有很多空白需要填充。欢迎贡献！
```

[`plotly` Python](https://plotly.com/python/) 库是一个交互式的、[开源](https://plotly.com/python/is-plotly-free)的绘图库，它支持超过 40 种独特的图表类型，涵盖了广泛的统计、金融、地理、科学和三维用例。

基于 Plotly JavaScript 库([plotly.js](https://plotly.com/javascript/))，Plotly 允许 Python 用户创建漂亮的基于 web 的交互式可视化，可以在 Jupyter 笔记本中显示，保存为独立的 HTML 文件，或使用 Dash 作为纯 Python 构建的 web 应用程序的一部分。plotly Python 库有时被称为 `plotly.py`，以区别于 JavaScript 库。

与 [orca](https://github.com/plotly/orca) 图像导出工具的深度集成，plotly 还提供了对非 web 上下文的强大支持，包括桌面编辑器（如 QtConsole, Spyder, PyCharm）和静态文档发布（如将笔记本导出为具有高质量矢量图像的 PDF）。

```{describe} 导航
1. 您可以直接进入如何制作 {doc}`基本图表<./basic-charts/index>`、{doc}`统计图表<./statistical-charts/index>`、{doc}`科学图表<./scientific-charts/index>`、{doc}`金融图表<./financial-charts/index>`、{doc}`地图<./maps/index>` 和 {doc}`三维图表<./3d-charts/index>`的示例。
2. 如果你喜欢先了解图的基础原理,你可以阅读 [图的数据结构](plotly:figure-structure)，[如何创建和更新数据](plotly:creating-and-updating-figures)，[如何渲染数据](plotly:renderers)，[如何使用模板](plotly:templates)，[如何将图导出为不同格式的数据](plotly:static-image-export) 和 [高级 API  Plotly Express](plotly:plotly-express) 来做以上的事情。
3. 也可以查看详尽的参考指南：[Python API 参考](https://plotly.com/python-api-reference)或 [图参考](https://plotly.com/python/reference)
```

有关使用 Python 构建包含 plotly 图的 web 应用程序的信息，请参阅 [Dash 用户指南](https://dash.plotly.com/)。

如果您需要任何与 Plotly 相关的帮助，可以加入 [Plotly 社区论坛](http://community.plotly.com/)。

```{important}
注意:使用 `plotly.py` 不需要：互联网连接、帐户或支付。在版本 4 中，plotly 包中删除了所有“在线”功能，现在作为单独的、可选的 `chart-studio` 包可用（见下文）。`plotly.py` 版本4 是“离线”的，不包括任何上传图或数据到云服务的功能。
```

```python
import plotly.graph_objects as go
fig = go.Figure(data=go.Bar(y=[2, 3, 1]))
fig.write_html('first_figure.html', auto_open=True)
```

## 在 Dash 中 Plotly Charts

{term}`Dash` 是使用 Plotly 图形在 Python 中构建分析应用程序的最佳方法。要运行下面的应用程序，请运行 `pip install dash`，点击“下载”获取代码并运行 `python app.py`。

开始于[官方的 Dash文档](https://dash.plotly.com/installation)，并学习如何轻松[风格化](https://plotly.com/dash/design-kit/)和[部署](https://plotly.com/dash/app-manager/)应用程序于这样的 [Dash 企业版](https://plotly.com/dash/)。

```{code-cell} ipython3
:tags: [remove-input]

from IPython.display import IFrame
snippet_url = 'https://dash-gallery.plotly.host/python-docs-dash-snippets/'
IFrame(snippet_url + 'getting-started', width='100%', height=630)
```

# 支持 Jupyter Notebook

在经典的 [Jupyter Notebook](https://jupyter.org/) 中使用，需要 `pip` 安装 Notebook 和 ipywidgets 包：

```shell
pip install "notebook>=5.3" "ipywidgets>=7.5"
```

可以直接使用笔记本渲染器内联显示图形:

```{code-cell} ipython3
import plotly.graph_objects as go
fig = go.Figure(data=go.Bar(y=[2, 3, 1]))
fig.show()
```

或使用 `FigureWidget` 对象：

```{code-cell} ipython3
import plotly.graph_objects as go
fig = go.FigureWidget(data=go.Bar(y=[2, 3, 1]))
fig
```

有关渲染器框架的更多信息，请参见用 [](plotly:renderers)，有关使用 `FigureWidget` 的更多信息，请参见 [](plotly:figurewidget)。

## 支持 JupyterLab

要在 [JupyterLab](https://jupyterlab.readthedocs.io/en/stable/) 中使用，请使用 `pip` 安装 JupyterLab 和 ipywidgets 包：

```shell
pip install jupyterlab "ipywidgets>=7.5"
```

然后运行以下命令安装所需的JupyterLab扩展(注意，这将需要安装 [Node](https://nodejs.org/))：

```shell
# JupyterLab renderer support
jupyter labextension install jupyterlab-plotly@4.14.3

# OPTIONAL: Jupyter widgets extension
jupyter labextension install @jupyter-widgets/jupyterlab-manager plotlywidget@4.14.3
```

并使用 `plotly_mimetype` 渲染器内联显示图：

```{code-cell} ipython3
import plotly.graph_objects as go
fig = go.Figure(data=go.Bar(y=[2, 3, 1]))
fig.show()
```

或者使用 `FigureWidget` 对象(如果执行了上面的“OPTIONAL”步骤)。

```{code-cell} ipython3
import plotly.graph_objects as go
fig = go.FigureWidget(data=go.Bar(y=[2, 3, 1]))
fig
```

如果你在使用 JupyterLab 时遇到任何问题，尤其是在使用多个 Python 环境时，请查看[故障排除指南](https://plotly.com/python/troubleshooting/)。

## 静态图像的导出

`plotly.py` 支持 [静态图像导出](plotly:static-image-export)，使用 [kaleido](https://github.com/plotly/Kaleido) 包(从 plotly 版本4.9开始推荐支持)或 [orca](https://github.com/plotly/orca) 命令行实用程序(从plotly版本4.9开始支持)

## 扩展地理支持

一些 `plot.py` 特性依赖于相当大的地理形状文件。需要安装 `plotly-geo`：

```shell
 pip install plotly-geo==1.0.0
```

细节见：{doc}`./maps/county-choropleth`

## Chart Studio 支持

`chart-studio` 包可以用于上传 plotly 的 Chart Studio Cloud 或 On-Prem 服务。这个包可以使用 `pip` 安装。

```shell
pip install chart-studio==1.1.0
```

此包是可选的，如果没有安装，则不可能将图上传到 Chart Studio 云服务。
