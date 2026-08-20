# Computational Biology Notes

> 个人计算生物学 / 生物信息学方法笔记库。

这个仓库整合了原先分散在多个仓库中的分析笔记，包括 RNA-seq、GEO/microarray、gene-set scoring、R 环境管理、可视化和空间转录组资料。后续建议将仓库名从 `RNA-Seq-associated-research` 重命名为 `computational-biology-notes`。

## 目录

### RNA-seq / expression analysis

- [GEO / Microarray 数据读取与注释](docs/rnaseq/geo-microarray.md)
- [Gene-set scoring：VISION、GSVA、ssGSEA 与候选核心基因](docs/rnaseq/gene-set-scoring.md)

### R

- [R 环境与 RStudio 管理](docs/r/environment-management.md)

### Visualization

- [R 可视化代码片段](docs/visualization/r-plots.md)

### Spatial transcriptomics

- [Spatial Transcriptomics Resources](docs/spatial/spatial-transcriptomics-resources.md)

## 本仓库的定位

这里主要保存：

- 可复用的分析方法和代码框架；
- 软件环境与依赖管理经验；
- 数据库使用和数据预处理经验；
- 可视化模板；
- 对分析方法假设、限制和常见坑的记录。

正式科研项目应继续保留独立仓库，不建议把项目原始数据、大型结果文件或未公开敏感结果堆在这里。

## 迁移来源

本仓库正在吸收以下历史仓库中的有效内容：

- `RNA-Seq-associated-research`
- `Taming-R`
- `Rplots_shiywa`
- `STpedia`

迁移完成并人工确认后，后三个历史仓库可以删除或 archive。

## 维护原则

1. 一个主题一个 Markdown 页面，避免把所有内容塞进 README。
2. 代码示例尽量标明适用的软件版本或说明 API 可能变化。
3. 对经验性阈值明确标注为经验规则，不包装成普适结论。
4. 对统计方法同时记录适用场景和局限。
5. 旧命令如果已经过时，优先更新或明确标注 legacy。

## 未来可扩展主题

```text
docs/
├── rnaseq/
├── single-cell/
├── spatial/
├── statistics/
├── visualization/
├── r/
├── python/
├── linux/
└── databases/
```

这个仓库的目标不是做软件列表，而是积累自己真正使用过、理解过、能够复现的方法和经验。
