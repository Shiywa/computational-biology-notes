# Spatial Transcriptomics Resources

> 迁移并整理自原 `STpedia` 仓库。原页面内容较早且不完整，因此这里只保留仍有参考价值的技术分类和代表性方法，并将其定位为持续更新的资料索引。

## 技术分类

### Micro-dissected gene expression

- Tomo-seq
- Geo-seq

这类方法通过空间切割或显微解剖获得位置分辨的表达信息。

### In situ hybridization / imaging-based methods

可在此持续补充 MERFISH、seqFISH、STARmap 等成像型空间转录组技术，并记录：

- 空间分辨率
- 可检测基因数量
- 是否需要预设 panel
- 单细胞/亚细胞定位能力

### In situ capturing / sequencing-based methods

原笔记中记录的代表方法：

- Slide-seq
- Slide-seqV2

后续建议补充 Visium、Stereo-seq、HD spatial 等平台，并按分辨率、捕获面积、测序深度和样本类型比较。

## 建议的应用维度

空间组学资料可以按照生物学问题进一步整理：

- Neurology
- Embryonic development
- Pathology
- Inflammatory disease
- Tumor microenvironment
- Spatial immune niches

## 后续维护建议

不要把这个页面做成单纯的软件/论文堆积列表。更有价值的组织方式是对每个平台记录：

| 维度 | 内容 |
|---|---|
| 原理 | imaging / capture / sequencing |
| 分辨率 | spot / cell / subcellular |
| 通量 | 基因数、面积、样本数 |
| 样本要求 | fresh frozen / FFPE 等 |
| 优势 | 主要适用问题 |
| 局限 | 主要技术偏倚 |
| 分析生态 | 主流软件和数据格式 |

> 该页面来自历史资料迁移，涉及具体技术性能时应以最新原始论文和官方文档为准。
