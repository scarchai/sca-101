# sca-101

`sca-101` 用于整理侧信道分析（Side-Channel Analysis，SCA）的基础实验，帮助读者通过可运行的示例了解常见分析方法。目前包含针对 AES 的相关功耗分析（Correlation Power Analysis，CPA）和差分功耗分析（Differential Power Analysis，DPA），以及针对 RSA 的简单功耗分析（Simple Power Analysis，SPA）实验。

> 请仅将实验用于学习，以及对自己拥有或已获授权的数据和设备进行分析。

## 实验目录

| 路径 | 内容 |
| --- | --- |
| `cpa_aes/CPA_demo.ipynb` | AES CPA 实验 Notebook |
| `cpa_aes/aes_200_plaintext.npy` | CPA 所需明文数据（需自行准备） |
| `cpa_aes/aes_200_trace.npy` | CPA 所需功耗轨迹数据（需自行准备） |
| `dpa_aes/DPA_demo.ipynb` | AES DPA 实验 Notebook |
| `dpa_aes/ds_atmega2560_aes_power.ets` | DPA 所需功耗轨迹数据集（需自行准备） |
| `spa_rsa/spa_demo.ipynb` | RSA SPA 单条轨迹分析 Notebook |
| `spa_rsa/rsa1024-xmega-single-trace.ets` | RSA SPA Notebook 使用的单条功耗轨迹（需自行准备） |
| `spa_rsa/rsa-single-trace.ets` | RSA 单条功耗轨迹数据（需自行准备） |
| `spa_rsa/simpleserial-rsa-xmega.c` | ChipWhisperer XMEGA RSA 目标固件示例 |
| `spa_rsa/readme.md` | RSA 轨迹采集说明及参考资料 |
| `requirements.txt` | Python 依赖及版本 |

CPA 实验针对 AES 首轮 SubBytes 中间值建立汉明重量（Hamming Weight）假设，并计算假设与功耗轨迹采样点之间的皮尔逊相关系数。DPA 实验则根据 AES 首轮 S-box 输出的一个比特将轨迹分组，比较两组的平均功耗差。两个 Notebook 都会分析密钥字节猜测、绘制结果并与实验密钥对比。

RSA SPA 实验使用 ChipWhisperer XMEGA 的 RSA-1024 单条功耗轨迹，观察运算期间的功耗变化，并展示如何从轨迹中区分 RSA 运算模式。采集设备、固件替换和采集步骤请参考 [`spa_rsa/readme.md`](spa_rsa/readme.md)。

## 实验数据

实验数据文件不包含在仓库中。`.gitignore` 会忽略 `*.ets`、`*.npy`、`*.h5`、`*.npz` 和 `*.csv` 等数据文件；因此各 Notebook 运行所需的数据需要自行获取，并分别放到上表对应路径。DPA Notebook 默认从 `dpa_aes` 目录读取 `ds_atmega2560_aes_power.ets`；RSA SPA Notebook 默认从 `spa_rsa` 目录读取 `rsa1024-xmega-single-trace.ets`。请勿将敏感或未经授权的数据加入版本控制。

## 环境准备

建议使用 Python 3 和虚拟环境。在仓库根目录执行：

```bash
python -m venv .venv
```

激活虚拟环境：

```bash
# Windows PowerShell
.\.venv\Scripts\Activate.ps1

# macOS / Linux
source .venv/bin/activate
```

安装依赖：

```bash
python -m pip install -r requirements.txt
```

## 运行实验

在仓库根目录启动 Notebook：

```bash
jupyter notebook
```

然后打开 `cpa_aes/CPA_demo.ipynb`。也可以直接在终端切换到实验目录后启动：

```bash
cd cpa_aes
jupyter notebook CPA_demo.ipynb
```

在 Notebook 中按顺序运行单元格。程序会提示输入要分析的轨迹数量；输入值应不超过数据文件中的轨迹数。运行完成后，可以查看各密钥字节的相关性图、恢复出的密钥以及与实验密钥的逐字节比较。

运行 DPA 实验时，先切换到 DPA 目录，再启动 Notebook：

```bash
cd dpa_aes
jupyter notebook DPA_demo.ipynb
```

该 Notebook 从 `.ets` 数据集中读取明文、密钥和功耗轨迹，使用前 8000 条轨迹及前 50000 个采样点，并将每 10 个采样点取平均后进行分析。运行结束后会显示各密钥字节的差分功耗结果及密钥对比。数据集较大，运行需要较多内存和计算时间；请仅使用自己拥有或已获授权的数据和设备。

运行 RSA SPA 实验时，先将所需的 `rsa1024-xmega-single-trace.ets` 数据文件放入 `spa_rsa` 目录，再从该目录启动 Notebook：

```bash
cd spa_rsa
jupyter notebook spa_demo.ipynb
```

在 Notebook 中按顺序运行单元格以查看单条轨迹和模式匹配分析结果。SPA Notebook 还使用 `scared` 包进行峰值检测；如运行时报缺少该包，可在已激活的虚拟环境中执行 `python -m pip install scared`。

## 依赖

依赖及固定版本见 [`requirements.txt`](requirements.txt)，其中包括 NumPy、Matplotlib、Jupyter、`tqdm` 和 `estraces`。
