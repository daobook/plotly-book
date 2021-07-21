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

# 自定义按钮

参考：[Custom Buttons | Python | Plotly](https://plotly.com/python/custom-buttons/)

## 方法

[updatmenu 方法](https://plot.ly/python/reference/layout/updatemenus/#layout-updatemenus-items-updatemenu-buttons-items-button-method) 确定将使用哪个 [plotly.js 函数](https://plot.ly/javascript/plotlyjs-function-reference/) 来修改图表。有四种可能的方法：

- `"restyle"`：修改**data**或数据属性
- `"relayout"`：修改 **layout** 属性
- `"update"`：修改 **data 和 layout** 属性；联合 `"restyle"` 和 `"relayout"`
- `"animate"`：启动或暂停 [动画](../animations/index)

### `restyle` 按钮

当修改图的数据和数据属性时，应该使用 `"restyle"` 方法。

```{describe} 更新一个数据属性
这个例子演示了如何使用 `"restyle"` 方法更新单个数据属性：chart `type`。
```

```{code-cell} ipython3
:tags: [hide-input]
import plotly.graph_objects as go

import pandas as pd

# load dataset
df = pd.read_csv("https://raw.githubusercontent.com/plotly/datasets/master/volcano.csv")

# create figure
fig = go.Figure()

# Add surface trace
fig.add_trace(go.Surface(z=df.values.tolist(), colorscale="Viridis"))

# Update plot sizing
fig.update_layout(
    width=800,
    height=900,
    autosize=False,
    margin=dict(t=0, b=0, l=0, r=0),
    template="plotly_white",
)

# Update 3D scene options
fig.update_scenes(
    aspectratio=dict(x=1, y=1, z=0.7),
    aspectmode="manual"
)

# Add dropdown
fig.update_layout(
    updatemenus=[
        dict(
            type = "buttons",
            direction = "left",
            buttons=list([
                dict(
                    args=["type", "surface"],
                    label="3D Surface",
                    method="restyle"
                ),
                dict(
                    args=["type", "heatmap"],
                    label="Heatmap",
                    method="restyle"
                )
            ]),
            pad={"r": 10, "t": 10},
            showactive=True,
            x=0.11,
            xanchor="left",
            y=1.1,
            yanchor="top"
        ),
    ]
)

# Add annotation
fig.update_layout(
    annotations=[
        dict(text="Trace type:", showarrow=False,
                             x=0, y=1.08, yref="paper", align="left")
    ]
)

f = go.FigureWidget(fig)
f
```

```{code-cell} ipython3

```
