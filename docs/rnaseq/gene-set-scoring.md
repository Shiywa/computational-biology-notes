# Gene-set scoring：VISION、GSVA、ssGSEA 与候选核心基因

> 整理自原 `RNA-Seq-associated-research` README。该页面主要记录分析思路和历史代码框架；实际使用时应根据当前包版本核对 API。

## 为什么需要 gene-set scoring

在 bulk RNA-seq、单细胞转录组和空间组学中，单个基因往往不足以稳定代表某一功能状态，因此常将预定义 gene set 映射为样本/细胞层面的 pathway activity 或 signature score。

常见思路包括：

- rank-based enrichment，例如 ssGSEA；
- kernel/rank transformation，例如 GSVA；
- graph-aware/local autocorrelation，例如 VISION；
- 单细胞 ranking-based 方法，例如 AUCell。

不同方法的统计假设和适用数据层级不同，不建议把不同 score 当成完全等价的量。

## VISION

VISION 的核心思想之一，是结合预先构建的低维/邻接结构，评估 signature 在细胞状态空间中的一致性，并可进一步比较不同群体中的 signature 活性。

历史代码框架：

```r
library(VISION)

count <- Assay.obj@assays$RNA@counts
n.umi <- colSums(count)
scaled_counts <- t(t(count) / n.umi) * median(n.umi)

meta <- Assay.obj@meta.data

vis <- Vision(
  scaled_counts,
  signatures = "KEGG_metabolism_pathway.gmt",
  meta = meta[, c("deve_anno", "State")]
)

vis <- analyze(vis, hotspot = FALSE)
vis <- addProjection(
  vis,
  "UMAP",
  Assay.obj@reductions$harmony_umap@cell.embeddings
)
```

实际使用时需注意：

- Seurat 对象内部 slot API 已多次变化；
- 不建议依赖旧式 `@assays$RNA@counts` 写法而不检查当前 Seurat 版本；
- 并行线程数应遵守服务器资源限制；
- signature score 与 group comparison 的解释应区分。

## GSVA 与 ssGSEA

当前 GSVA 包采用参数对象接口。示例：

```r
library(GSVA)
library(GSEABase)
library(BiocParallel)

gene_sets <- getGmt("KEGG_metabolism_pathway.gmt")
gene_sets_list <- lapply(gene_sets, geneIds)
names(gene_sets_list) <- names(gene_sets)

bpparam <- MulticoreParam(workers = 8)

gsvapar <- gsvaParam(
  mtx,
  gene_sets_list,
  kcdf = "Gaussian"
)

gsva_results <- gsva(gsvapar, BPPARAM = bpparam)
```

ssGSEA：

```r
ssgseapar <- ssgseaParam(mtx, gene_sets_list)
ssgsea_results <- gsva(ssgseapar, BPPARAM = bpparam)
```

### 使用时的原则

- bulk continuous expression 与 sparse single-cell matrix 的分布不同；
- 是否使用 Gaussian/Poisson 等参数应基于输入数据；
- 单细胞数据最好先明确 scoring 的目标是 cell-level、pseudo-bulk 还是 sample-level；
- 多样本研究中应避免把 cell 数量当作独立生物学重复。

## 从 pathway score 寻找候选贡献基因

历史笔记采用过两个直观维度：

1. 基因表达与 pathway activity 的相关性；
2. 基因本身具有足够表达量，便于解释和验证。

示例：

```r
cor_results <- apply(
  expression_matrix,
  1,
  function(x) cor(
    x,
    pathway_score,
    method = "spearman",
    use = "pairwise.complete.obs"
  )
)
```

候选基因可以按照：

- effect/correlation magnitude；
- expression abundance；
- reproducibility across cohorts；
- perturbation evidence；
- prior biological evidence；

综合排序。

> 相关性高不等于该基因是 pathway 的因果调控因子。候选筛选和机制验证必须分开描述。

## 推荐的改进框架

如果目标是寻找某 pathway 的关键调控因子，更建议：

1. 在 sample/pseudobulk 层面先验证 pathway score 稳定性；
2. 使用多个独立 scoring 方法做敏感性分析；
3. 使用跨 cohort 效应一致性而不是单数据集相关性；
4. 加入表达量、细胞比例、批次、条件等协变量；
5. 最终使用 perturbation、遗传学或蛋白层证据支持因果解释。
