# R 环境与 RStudio 管理

> 迁移并整理自原 `Taming-R` 仓库。

## 使用 micromamba 管理 R 环境

micromamba 可以同时管理 R 和 Python 环境，适合在 Linux 上隔离不同项目的 R 版本和依赖。

示例：

```bash
micromamba create -n R440 -c conda-forge r-base=4.4.1
micromamba install -n R440 -c conda-forge r-ggplot2 r-dplyr
```

安装包时建议显式固定 `r-base` 版本，尤其是在较大的环境中更新 CRAN 包时，避免求解器为了满足依赖而意外回退 R 版本。

例如：

```bash
micromamba install -n R440 -c conda-forge r-your_package r-base=4.4.1
```

需要注意：

- conda-forge 中已经打包的 CRAN 包非常适合用 micromamba 安装；
- Bioconductor 或 GitHub 来源的包不一定都存在对应 conda 包；
- 安装前应检查 transaction summary 中是否出现非预期的 downgrade；
- 项目环境应尽量固定版本并记录环境文件。

## 指定 RStudio 使用的 R

在 Linux 桌面环境下，可以通过环境变量指定 RStudio 使用某个 R：

```bash
export RSTUDIO_WHICH_R="/path/to/env/bin/R"
```

也可以在 RStudio 配置中指定 `r-bin`。历史上曾使用：

```json
{"r-bin": "/path/to/env/bin/R"}
```

不推荐直接覆盖系统级 `/usr/local/bin/R` 软链接，因为这会影响其他用户或系统程序。

## 桌面启动与终端启动不一致

如果终端执行 `rstudio` 时使用了正确 R，而桌面图标启动仍使用旧版本，原因通常是桌面启动器没有继承交互式 shell 中的环境变量。

可以检查 `.desktop` 文件中的 `Exec=` 配置。例如：

```text
Exec=env RSTUDIO_WHICH_R=/path/to/env/bin/R rstudio %F
```

修改系统桌面文件后可更新 desktop database：

```bash
sudo update-desktop-database
```

> 在多人服务器环境中，不建议普通用户修改系统级 desktop 文件。更推荐用户使用自己的桌面启动配置或直接从终端启动。

## 推荐原则

- 每个主要项目使用独立环境；
- 不直接修改系统 R；
- 记录 R 版本和关键包版本；
- 能用 conda-forge/micromamba 稳定解决的系统依赖，优先在环境层解决；
- GitHub/Bioconductor 包安装后尤其要记录 commit/version。
