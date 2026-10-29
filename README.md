# TopoPatchGT

**Topology-Aware Patch Graph Transformer with Adaptive Prior-Guided Graph Learning for Computing Network Traffic Prediction**

[![Python](https://img.shields.io/badge/Python-3.9+-blue.svg)](https://www.python.org/)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.0-ee4c2c.svg)](https://pytorch.org/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

Official implementation of the paper *"TopoPatchGT: Topology-Aware Patch Graph Transformer with Adaptive Prior-Guided Graph Learning for Computing Network Traffic Prediction"* (submitted to *Information Sciences*).

---

## 📖 Overview

Traffic prediction in **computing power networks (CPNs)** is challenging due to multi-scale temporal dynamics and time-varying spatial dependencies that do not necessarily follow physical connectivity. Existing methods often model temporal and spatial patterns in isolation, relying on static topologies that limit their ability to track evolving traffic interactions under dynamic scheduling and resource allocation.

**TopoPatchGT** addresses three critical limitations of prior work:

1. **Inefficient point-wise temporal modeling**
2. **Static topology constraints**
3. **Inability to integrate prior knowledge with adaptive learning**

The model achieves superior or highly competitive forecasting accuracy against **twelve strong baselines**, while remaining **compact (267K parameters)** and computationally efficient — making it suitable for practical CPN deployment.

## ✨ Key Contributions

- **Prior-Aware Adaptive Graph Learning** — dynamically constructs sparse adjacency matrices by fusing static node embeddings, dynamic feature similarity, and optional prior topological knowledge through learnable weighting and multi-objective regularization, capturing both physical and logical network relationships.
- **Patch-Based Temporal Encoding** — segments seasonal components into overlapping patches for efficient multi-scale temporal pattern capture through Transformer self-attention.
- **Topology-Aware Spatial Graph Attention** — incorporates learned adjacency matrices as attention bias and masks, jointly exploiting feature similarity and evolving topological structure.

## 🏗️ Architecture

The model couples three components:

```
Input series ──▶ Series decomposition (trend / seasonal)
                     │
     ┌───────────────┴────────────────┐
     ▼                                 ▼
Patch-based temporal encoding    Prior-aware adaptive graph learning
(Transformer self-attention)      (static + dynamic + prior A_prior)
     │                                 │
     └───────────────┬─────────────────┘
                     ▼
     Topology-aware spatial graph attention
                     ▼
               Prediction head
```

## 📊 Datasets

Four benchmark datasets are used to evaluate the model:

| Dataset | Nodes | Edges | Time Steps | Features | Interval |
|---------|:-----:|:-----:|:----------:|:--------:|:--------:|
| EC      | 1     | --    | 14,772     | 1        | 5 min    |
| ISP     | 1     | --    | 19,887     | 1        | 5 min    |
| Abilene | 12    | 15    | 48,384     | 2        | 5 min    |
| TMV2    | 23    | 37    | 10,772     | 2        | 15 min   |

- **EC** — a genuine computing-power-network trace collected from edge computing nodes (bandwidth utilization, 5-min interval).
- **ISP** — network traffic from an Internet service provider in the UK. [Source](https://github.com/xiaohuiduan/network-traffic-dataset)
- **Abilene** — Internet2 Abilene backbone origin–destination traffic matrices.
- **TMV2** — anonymized intra-domain traffic matrix dataset.

> `EC` is a CPN-specific trace; `ISP`, `Abilene`, and `TMV2` are CPN-relevant network-traffic benchmarks used to validate general spatio-temporal modeling capability.

## ⚙️ Installation

```bash
# Clone the repository
git clone https://github.com/<your-username>/TopoPatchGT.git
cd TopoPatchGT

# Create environment
conda create -n topopatchgt python=3.9
conda activate topopatchgt

# Install dependencies
pip install -r requirements.txt
```

**Main requirements:** PyTorch 2.0, NumPy, Pandas, scikit-learn.

## 🚀 Usage

### Training

```bash
python main.py --dataset Abilene --pred_len 96 --seq_len 96
```

### Evaluation

```bash
python main.py --dataset Abilene --pred_len 96 --is_training 0
```

Prediction horizons evaluated in the paper: `{48, 96, 128, 192, 336, 720}` (input length is set equal to the prediction length).

## 🔧 Key Hyperparameters

| Hyperparameter | Value |
|----------------|:-----:|
| Hidden dimension `d_model` | 64 |
| Optimizer | Adam |
| LR schedule | Cosine annealing |
| Train / Val / Test split | 80% / 10% / 10% |
| Normalization | Z-score |
| Random seeds | {2021, 2022, 2023} |

> Full hyperparameter settings are listed in the paper (Table 2).

## 📈 Results

- Evaluated with **MSE** and **MAE** (lower is better).
- Compared against **12 baselines** across three categories:
  - **MLP-based:** TimeMixer, TiDE, MTS-Mixers, RLinear
  - **Transformer-based:** PatchTST, iTransformer, Crossformer
  - **STGNN-based:** Graph WaveNet, MTGNN, PDFormer, STGTN, TopoFreer
- All results are averaged over **three independent runs**; standard deviations are consistently small, indicating stable and reproducible performance.

See the paper for the full comparison tables and ablation studies.

## 🧪 Experimental Setup

- **Hardware:** NVIDIA RTX 5090 GPU (32 GB)
- **Framework:** PyTorch 2.0
- Baselines re-run from their official open-source codebases under identical splits, normalization, horizons, and hardware.

## 📂 Project Structure

```
TopoPatchGT/
├── data/              # Benchmark datasets
├── models/            # TopoPatchGT model definition
├── layers/            # Graph learning & attention modules
├── utils/             # Data loading, metrics, tools
├── main.py            # Training / evaluation entry point
├── requirements.txt
└── README.md
```



## 📄 License

This project is released under the MIT License. See [LICENSE](LICENSE) for details.
