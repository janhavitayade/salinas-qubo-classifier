# Quantum-Classical Hybrid Band Selection for Hyperspectral Image Classification

**QUBO + QAOA band selection on real IBM Quantum hardware, benchmarked against simulated annealing and statevector simulation, evaluated on the Salinas hyperspectral dataset (224 spectral bands, 54,129 pixels, 16 classes).**

[![Qiskit](https://img.shields.io/badge/Qiskit-2.0-6929C4?logo=ibm)](https://www.ibm.com/quantum/qiskit)
[![IBM Quantum](https://img.shields.io/badge/IBM%20Quantum-ibm__fez-blue)](https://quantum.ibm.com)
[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?logo=python&logoColor=white)](https://www.python.org)
[![scikit-learn](https://img.shields.io/badge/scikit--learn-ML%20pipeline-F7931E?logo=scikit-learn&logoColor=white)](https://scikit-learn.org)

---

## Project Resources

- 📄 [One-Page Project Summary](docs/salinas_qubo_onepager.pdf)
- 📘 [Full Research Paper](docs/paper.pdf)
- 📊  [Live Interactive Dashboard](https://janhavitayade.github.io/salinas-qubo-classifier/)

---

## Project Summary

Hyperspectral image classification involves hundreds of spectral bands, many of which contain redundant information. Selecting a compact subset of informative and non-redundant bands can significantly reduce computational complexity while maintaining classification performance.

This project formulates hyperspectral band selection as a **Quadratic Unconstrained Binary Optimization (QUBO)** problem and solves it using both classical and quantum approaches, including **QAOA executed on real IBM Quantum hardware (ibm_fez)**.

### Key Results

| Metric | Result |
|----------|----------|
| Best Overall Accuracy (OA) | **94.85%** |
| Best Average Accuracy (AA) | **97.60%** |
| Best Kappa Score | **0.9426** |
| Dimensionality Reduction | **224 → 20 bands (~91% reduction)** |
| Quantum Hardware | IBM Quantum `ibm_fez` |
| Best Classifier | XGBoost |

---

## Problem Formulation

Band selection is formulated as a constrained optimization problem that:

- Maximizes Fisher discriminant scores of selected bands.
- Minimizes redundancy between selected bands.
- Enforces selection of exactly *k* bands.

The objective balances discriminative power and diversity within the selected spectral subset.

---

## Methodology

```text
224-band Salinas dataset
        │
        ▼
Fisher Discriminant Analysis
        │
        ▼
QUBO Construction
        │
        ▼
Optimization
 ├─ Simulated Annealing
 ├─ QAOA Statevector (16 qubits)
 ├─ QAOA Statevector (20 qubits)
 ├─ QAOA IBM Quantum (30 qubits, 15 bands)
 └─ QAOA IBM Quantum (30 qubits, 20 bands)
        │
        ▼
Feature Extraction
        │
        ▼
8 Machine Learning Classifiers
        │
        ▼
Performance Evaluation
```

### Machine Learning Classifiers

The selected band subsets were evaluated using the following 8 classifiers:

| Classifier | Abbreviation | Category | Tuning Method |
|---|---|---|---|
| Support Vector Machine (RBF kernel) | SVM | Kernel-based | RandomizedSearchCV, 5-fold CV |
| Random Forest | RF | Tree ensemble (bagging) | RandomizedSearchCV, 5-fold CV |
| Extra Trees | ET | Tree ensemble (bagging) | RandomizedSearchCV, 5-fold CV |
| Histogram Gradient Boosting | HGB | Tree ensemble (boosting) | RandomizedSearchCV, 5-fold CV |
| XGBoost | XGB | Tree ensemble (boosting) | RandomizedSearchCV, 5-fold CV |
| K-Nearest Neighbors | KNN | Instance-based | RandomizedSearchCV, 5-fold CV |
| Logistic Regression | LR | Linear model | RandomizedSearchCV, 5-fold CV |
| Multi-Layer Perceptron | MLP | Neural network | RandomizedSearchCV, 5-fold CV |

---

## Experimental Configurations

| Config | Solver | Qubits | Bands | Spatial Features | Split | Best OA | Best AA |
|----------|----------|----------|----------|----------|----------|----------|----------|
| 1 | Simulated Annealing | – | 15 | No | 80/20 | 91.06% | 95.06% |
| 2 | QAOA Statevector | 16 | 15 | No | 80/20 | 89.19% | 91.68% |
| 3 | QAOA Statevector | 20 | 15 | No | 80/20 | 89.19% | 91.68% |
| 4 | IBM Quantum (`ibm_fez`) | 30 | 15 | No | 80/20 | 91.46% | 95.37% |
| 5 | IBM Quantum (`ibm_fez`) | 30 | 20 | Yes | 90/10 | **94.85%** | **97.60%** |

---

## Results

### Classification Performance

<p align="center">
<img src="assets/accuracy_comparison_ibm20b.png" width="850">
</p>

### Confusion Matrix (Best Configuration)

<p align="center">
<img src="assets/confusion_matrix_ibm20b.png" width="650">
</p>

The best-performing configuration combines:

- IBM Quantum QAOA-selected bands
- 20 spectral bands
- 3×3 spatial-spectral feature extraction
- XGBoost classifier

---

## Band Selection Analysis

### Fisher Score Distribution

<p align="center">
<img src="assets/fisher_scores_annealing.png" width="850">
</p>

### Statevector QAOA (16 Qubits)

<p align="center">
<img src="assets/band_selection_sv16q.png" width="750">
</p>

### IBM Quantum QAOA (15 Bands)

<p align="center">
<img src="assets/band_selection_ibm15b.png" width="750">
</p>

### IBM Quantum QAOA (20 Bands)

<p align="center">
<img src="assets/band_selection_ibm20b.png" width="750">
</p>

### Key Observation

Increasing qubit count alone does not necessarily improve classification performance. The diversity and quality of the candidate spectral bands have a greater impact on downstream accuracy than the number of qubits available.

---

## Quantum vs Classical Performance

### IBM Quantum Hardware

- Backend: `ibm_fez`
- Architecture: Heron r2
- QAOA depth: p = 1
- Real hardware execution
- 30 logical qubits

### Observations

- Real quantum hardware achieved a QUBO cost very close to the simulated annealing optimum.
- Hardware execution avoided the exponential memory growth associated with statevector simulation.
- Simulated annealing remained a strong classical baseline.
- Results demonstrate the feasibility of quantum-assisted optimization for hyperspectral band selection on current NISQ-era hardware.

---

## Dataset

Experiments were performed using the Salinas hyperspectral scene dataset.

Dataset source:

https://zenodo.org/records/15771735

Required files:

- `Salinas.mat`
- `Salinas_gt.mat`

These files are not included in this repository.

---

## Interactive Dashboard

An interactive dashboard is included as:

```text
index.html
```

The dashboard contains:

- Experimental overview
- Performance summaries
- Visualizations
- Quantum vs classical comparison
- Key findings

---

## Repository Structure

```text
salinas-qubo-classifier/
├── README.md
├── index.html
├── notebooks/
│   ├── Salinas_QUBO_Final_Annealer_15_bands.ipynb
│   ├── Salinas_QUBO_Final_IBM_30qubits_15_bands_80-20.ipynb
│   ├── Salinas_QUBO_Final_stateVector_16_qubits_15_bands.ipynb
│   ├── Salinas_QUBO_Final_statevector_20qubits_15_bands.ipynb
│   ├── Salinas_QUBO_IBM_30qubits_20_bands_part_1_90-10.ipynb
│   └── Salinas_QUBO_IBM_30qubits_20_bands_part_2_90-10.ipynb
├── assets/
│   ├── accuracy_comparison_ibm20b.png
│   ├── band_selection_ibm15b.png
│   ├── band_selection_ibm20b.png
│   ├── band_selection_sv16q.png
│   ├── confusion_matrix_ibm20b.png
│   └── fisher_scores_annealing.png
├── docs/
│   ├── paper.pdf
│   └── salinas_qubo_onepager.pdf
├── requirements.txt
└── .gitignore
```

---

## Reproducing the Experiments

1. Download the Salinas dataset.
2. Open the desired notebook in Google Colab or Jupyter Notebook.
3. Upload:
   - `Salinas.mat`
   - `Salinas_gt.mat`
4. Install dependencies:

```bash
pip install -r requirements.txt
```

For IBM Quantum notebooks:

```python
import os

os.environ["IBM_QUANTUM_TOKEN"] = "your-token"
os.environ["IBM_QUANTUM_INSTANCE"] = "your-instance"
```

---

## Requirements

```text
numpy
scipy
scikit-learn
matplotlib
xgboost
qiskit>=2.0.0
qiskit-ibm-runtime>=0.30.0
```

---

## Citation

If you use this work for academic or research purposes, please cite:

Tayade, J.K. & More, C.

*Quantum-Classical Hybrid Pipeline for Hyperspectral Band Selection and Classification: A Comparative Study Using QUBO Optimization on IBM Quantum Hardware.*

2026.