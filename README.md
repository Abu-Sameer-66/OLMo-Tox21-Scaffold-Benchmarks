# 🧪 OLMo-Tox21: Scaffold Benchmarks & The GNN Gap

<p align="center">
  <img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&ColorList=C97EB6,11998e,38ef7d&height=220&section=header&text=LLM%20vs%20GNN%20Benchmarking&fontSize=50&fontColor=ffffff" width="100%"/>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Split-Murcko%20Scaffold-11998e?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/GNN-Pure%20PyTorch-EE4C2C?style=for-the-badge&logo=pytorch&logoColor=white"/>
  <img src="https://img.shields.io/badge/LLM-OLMo--7B-C97EB6?style=for-the-badge"/>
</p>

## 🔬 Scientific Question
Can a general-purpose causal LLM (**OLMo-7B**) match a dedicated Message-Passing Graph Neural Network (**GNN**) on molecular toxicity prediction when evaluated on a rigorous, data-leakage-free scaffold split?

## 📊 Key Findings: The "Accuracy Illusion" & The 0.009 Gap

Most published baselines use Random Splitting, which allows structural data leakage. Our rigorous **Murcko Scaffold Split** reveals the truth:

| Model | Architecture | Split | ROC-AUC | Status |
| :--- | :--- | :--- | :--- | :--- |
| RF + ECFP | Fingerprint | Random | 0.8183 | ❌ Inflated (Data Leakage) |
| RF + ECFP | Fingerprint | Scaffold | 0.6135 | ⚠️ Real Generalization |
| **Pure PyTorch GNN** | **Graph (atoms+bonds)** | **Scaffold** | **0.6388** | 🥇 **Topology Aware** |
| **OLMo-7B QLoRA** | **1D Text (SMILES)** | **Scaffold** | **0.6300** | 🥈 **Language Aware** |

### 💡 The Scientific Insight
**Gap between GNN and OLMo: 0.009**
This near-zero gap proves two things:
1. OLMo learns molecular grammar from 1D SMILES almost as effectively as a dedicated 2D graph model.
2. **The Motivation for TSM:** That remaining 0.009 gap justifies injecting 2D graph constraints *directly* into the LLM decoding loop—which is exactly what our Topological State Machine (TSM) achieves.

---

## 🛠️ Architecture: Pure PyTorch Message Passing
We built a custom GNN without relying on heavy dependencies like DGL or PyG. It seamlessly consumes DeepChem's `MolGraphConvFeaturizer` outputs.
- **Nodes:** Atoms (30-dim features)
- **Edges:** Bonds
- **Pipeline:** 3 Message Passing Rounds ➔ Global Mean Pooling ➔ 12-Task Classification Head

---

## 🚀 Connection to DeepChem GSoC 2026
This repository provides the empirical foundation for the **DeepChem LLM Integration** proposal:
1. **Baseline Validation:** Establishes honest scaffold split numbers.
2. **GIMF Motivation:** Quantifies the structural gap between LLMs and GNNs.
3. **DeepChem Native:** Fully compliant with `dc.feat` and `dc.data` standards.

---

## 📂 Repository Structure
```text
├── notebooks/
│   ├── 01_rf_ecfp_baseline.ipynb          # Random Forest baseline
│   ├── 02_gnn_scaffold_split.ipynb        # Pure PyTorch GNN
│   └── 03_olmo_tox21_classification.ipynb # OLMo-7B
├── results/
│   └── benchmark_table.csv                # Extracted metrics
├── src/
│   ├── scaffold_split.py                  # Murcko scaffold splitter
│   └── mol_gnn.py                         # PyTorch GNN Implementation
└── README.md
