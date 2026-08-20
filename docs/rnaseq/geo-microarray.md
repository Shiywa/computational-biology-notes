# GEO / Microarray 数据读取与注释

> 整理自原 `RNA-Seq-associated-research` README。

## 1. 使用 GEOquery 获取表达矩阵

```r
library(GEOquery)

gset <- getGEO(
  "GSE80697",
  GSEMatrix = TRUE,
  getGPL = FALSE,
  destdir = ".",
  AnnotGPL = FALSE
)
```

样本信息：

```r
meta <- pData(gset[[1]])
```

表达矩阵：

```r
library(Biobase)
ex <- exprs(gset[[1]])
```

对于 microarray 数据，可以根据平台和研究设计决定是否需要额外标准化。历史示例中使用：

```r
library(limma)
ex <- normalizeBetweenArrays(ex)
```

> 是否需要再次标准化取决于 GEO series matrix 中提供的数据是否已经经过作者预处理，不能机械重复执行。

## 2. 使用 Bioconductor annotation package 映射 probe → symbol

先查看平台：

```r
index <- gset[[1]]@annotation
index
```

如果存在对应 annotation package，可以提取 probe-symbol 对照表。例如：

```r
library(HsAgilentDesign026652.db)
library(BiocGenerics)

ids <- toTable(HsAgilentDesign026652SYMBOL)
```

合并表达矩阵：

```r
library(dplyr)

ex_df <- as.data.frame(ex) |>
  mutate(probe_id = rownames(ex))

exp <- ex_df |>
  inner_join(ids, by = "probe_id")
```

一个基因可能对应多个 probes。历史分析策略是保留平均表达最高的 probe：

```r
exp1_max <- exp |>
  group_by(symbol) |>
  slice_max(
    order_by = rowMeans(across(where(is.numeric))),
    with_ties = FALSE
  ) |>
  ungroup() |>
  as.data.frame()
```

需要注意：

- “取平均表达最高 probe”是一种经验规则，不是所有平台唯一正确的方法；
- 某些研究可能更适合 probe-level differential analysis 后再汇总；
- 应保留 probe ID 到 gene symbol 的映射记录，以便复现。

## 3. annotation package 不可用时直接读取 GPL

对于较老平台，Bioconductor annotation package 可能不存在或难以安装。这时可以直接从 GEO 获取 GPL：

```r
gpl <- getGEO(index)
platform <- Table(gpl)
platform$ID <- as.character(platform$ID)
```

然后与表达矩阵合并：

```r
exp <- as.data.frame(ex) |>
  mutate(ID = rownames(ex)) |>
  inner_join(platform, by = "ID")
```

不同 GPL 的 gene symbol 列名并不统一，应先检查：

```r
colnames(platform)
```

不要假定所有平台都使用 `GENE_SYMBOL`。

## 4. 推荐的实际工作流

1. 阅读 GEO 页面和原论文，确认作者上传的数据层级；
2. 确认 platform / GPL；
3. 检查 series matrix 是否已经 log2/normalized；
4. 保存原始 probe-level matrix；
5. 建立显式 probe → gene 映射；
6. 记录去重规则；
7. 再进行 PCA、差异分析和下游 pathway 分析。

## 常见问题

### 表达值是否需要 log2？

先查看数值范围和 GEO 数据处理说明。不要仅根据“microarray”这一数据类型自动再次 log2。

### 为什么很多 probe 没有 gene symbol？

常见原因包括旧探针、未注释转录本、平台注释更新以及数据库版本差异。建议报告映射成功比例。
