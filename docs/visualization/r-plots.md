# R 可视化代码片段

> 迁移并整理自原 `Rplots_shiywa` 仓库。

这个页面用于积累可复用的 R 绘图代码。建议后续每个图形模块都记录：输入数据结构、所需包、核心参数和示例输出。

## Boxplot + Wilcoxon 检验

```r
library(ggpubr)

my_comparisons <- list(
  c("HI-Mock", "Mild"),
  c("HI-Mock", "Moderate"),
  c("HI-Mock", "Severe")
)

p <- ggboxplot(
  xaf1,
  x = "group",
  y = "Value",
  color = "group",
  palette = "lancet",
  add = "jitter",
  short.panel.labs = FALSE,
  nrow = 1
) +
  stat_compare_means(
    comparisons = my_comparisons,
    label = "p.format",
    method = "wilcox.test"
  ) +
  theme(
    axis.text.x = element_text(
      angle = 45,
      hjust = 1,
      vjust = 1
    )
  )

p
```

## 建议的整理方式

后续可以继续增加：

- boxplot / violin plot
- volcano plot
- heatmap / ComplexHeatmap
- enrichment dotplot
- survival plot
- forest plot
- dimensional reduction plot

对于正式文章绘图，建议把颜色、字体、尺寸和导出格式封装成可复用函数，而不是在每个项目中重复手写。
