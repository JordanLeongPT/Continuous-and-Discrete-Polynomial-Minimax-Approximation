
## minimax

高阶多项式极小极大逼近工具，涵盖

- 连续型：纯 Remez 交换法，谱微分矩阵 + Brent 精修定位极值，端点强制等振；LP (HiGHS) 仅用于初值/兜底，可选 Lawson ≤10 步轻抛光
- 离散型：ℓ∞ 线性规划（HiGHS）与 Lawson 活跃约束交换，可处理 10⁴ 级采样
- 诊断：迭代历史、条件数、极值点、鲁棒误差（含有界噪声）

依赖：Python ≥3.10，`numpy`、`scipy`、`matplotlib`（实验与可视化）。

### 安装

```bash
pip install -e .
```


### 实验

- 连续极小极大（Remez + Chebyshev）：  
  - 光滑/非光滑快速跑图：`python -m stable_minimax.experiments cont --func {exp,abs,sin20} --degrees 20 50 100 150 --tol 1e-12 --outdir experiments/proj8_output/stable_cont_<func> --no-show`
  - 高阶 |x|：`python -m stable_minimax.experiments cont --func abs --degrees 200 500 --tol 1e-12 --outdir experiments/proj8_output/stable_cont_abs --no-show`
- 离散 ℓ∞（HiGHS + Lawson，含噪声 δ=1e-3）：  
  - n=200 大样本：`python -m experiments.disc_report --Ns 1000 5000 10000 --degree 200 --delta 1e-3 --max-iter 20000 --logs experiments/proj8_output/logs --figs experiments/proj8_output/figs --time-budget 300`
  - n=100 对比：`python -m experiments.disc_report --Ns 1000 5000 10000 --degree 100 --delta 1e-3 --max-iter 20000 --logs experiments/proj8_output/logs_n100 --figs experiments/proj8_output/figs_n100 --time-budget 120`
- 快速演示脚本：  
  - 连续 demo：`python -m experiments.cont_demo [--method {lp,remez}] [--lawson]`  
  - 离散 demo：`python -m experiments.disc_demo`

脚本会输出收敛曲线、残差等振图与噪声情形下的最坏误差表/直方图。

### 测试

```bash
pytest
```

### 主要模块

- `minimax.cheb`: 仿射映射、Chebyshev 递推/Clenshaw、DCT 初值、谱微分矩阵、重心插值
- `minimax.spectral_extrema`: 谱微分零点括区 + Brent 精修，输出候选极值
- `minimax.utils`: 计时、列归一化、极值点交替筛选、等振判定
- `minimax.remez`: Remez 主循环（列缩放 + QR/SVD），极值来自谱法，失败时自动回退 LP，末尾可做 Lawson 轻抛光
- `minimax.linf_lp`: HiGHS 离散 ℓ∞、Lobatto LP 初值与受限 Lawson 迭代（仅在误差下降时接受）

### 调参 / 故障排查

- `eq_tol`：等振幅度容差，光滑函数建议 0.02~0.03，非光滑如 `|x|` 用 0.05
- `lawson_polish_steps`：Lawson 迭代上限（默认 5，硬限制 10）
- `M`：谱极值网格（默认 4097，可按函数振荡程度调大）
- 若 Remez 60 轮仍未收敛，会自动退回到稠密 Lobatto LP + 等振重排，可通过日志或 `history["extremals"]` 检查

### 报告 / 结果

### 报告 / 结果（目录索引）

- 连续（E vs n、残差）：`experiments/proj8_output/stable_cont_*/*.png`  
  - |x|：含 n=20/50/100/150/200/500  
  - exp(x)、sin(20x)：含 n=20/50/100/150
- 离散 ℓ∞（表格与直方图）：  
  - n=200：`experiments/proj8_output/logs/disc_summary.{md,csv}`，直方图在 `experiments/proj8_output/figs/`  
  - n=100：`experiments/proj8_output/logs_n100/disc_summary.{md,csv}`，直方图在 `experiments/proj8_output/figs_n100/`

早期基线（n=50 等）仍在 `experiments/logs/` 与 `experiments/figs/` 供参考。
