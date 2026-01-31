# Deep Learning for Induction Motor Fault Detection (MCSA + Transfer Learning)

This project develops a **deep-learning-based condition monitoring pipeline** for **induction motor fault detection and classification** using **Motor Current Signature Analysis (MCSA)** from **stator current time-domain signatures**. A three-phase squirrel-cage induction motor is modeled in MATLAB/Simulink, faults are induced under different load conditions, and **transfer learning** is used to classify motor health states.

**Fault classes (multiclass):**
- **Healthy**
- **Single-phasing**
- **Phase-to-phase fault**

**Models evaluated:**
- InceptionV3
- ResNet152
- VGG19

---

## Why this matters (industry relevance)

Induction motors dominate industrial actuation, and unplanned downtime is expensive. **Early fault detection** enables:
- Predictive maintenance scheduling
- Reduced catastrophic failures and secondary damage
- Lower downtime and better production continuity
- Improved energy efficiency by detecting abnormal operation patterns

---

## Technical summary

### 1) Motor modeling + fault injection (simulation)
A 3-phase induction motor model is created in MATLAB/Simulink and simulated under:
- Multiple load levels (0–100%)
- Healthy and faulty conditions

Faults are introduced by disconnecting one or more stator phases (**single-phasing** and **phase-to-phase**). The resulting **stator current signatures** are used as the diagnostic signal.

### 2) Dataset generation
- **276** current time signatures collected from simulation
- Train/validation split reported as ~**82% / 18%**
- Label mapping used:
  - Healthy → `0`
  - Single-phasing → `1`
  - Phase-to-phase → `2`

> Note: Some parts of the report mention additional stator fault types in dataset tables. In this repo, keep the labels consistent with the final class definition used during training.

### 3) Deep learning approach (transfer learning)
Transfer learning is used to reduce data requirements by fine-tuning pre-trained CNN backbones (originally trained on ImageNet) with a smaller custom dataset. The workflow includes:
- Preprocessing and labeling (Google Colab workflow)
- Model adaptation (Flatten + Dense layers + Softmax output)
- Training + validation + confusion matrix evaluation

---

## Results snapshot

### Model performance (reported)
**ResNet152** shows the strongest overall performance:
- Precision ≈ **97%**
- Recall ≈ **96%**
- F1-score ≈ **97%**
- Training accuracy ≈ **96.49%**
- Validation accuracy ≈ **97.92%**

### Per-class accuracy comparison (reported)
| Class | InceptionV3 | ResNet152 | VGG19 |
|---|---:|---:|---:|
| Healthy | 75% | **100%** | 75% |
| Single-phasing | 81% | **94%** | 88% |
| Phase-to-phase | 100% | **100%** | 100% |

---
## How to Run

This project can be run in two stages: **(A) simulation/data generation** and **(B) model training + evaluation**.

### A) Generate stator current data (MATLAB/Simulink)
1. Open the Simulink induction motor model
2. Configure the simulation cases:
   - **Health states:** Healthy, Single-phasing, Phase-to-phase
   - **Load levels:** e.g., 0%, 25%, 50%, 75%, 100%
3. Run simulations and log stator current signals (per phase or a selected phase current).
4. Export each run to CSV and store in:
   - `data/`

**Recommended CSV fields (minimum):**
- `time`
- `current_a` (or `current_b`, `current_c`)
- `label` (0=Healthy, 1=Single-phasing, 2=Phase-to-phase)
- `load_pct`

> Tip: Keep naming consistent so your notebook preprocessing can batch-load runs easily.

---

### B) Train and evaluate the deep learning models (Google Colab / Local Python)
1. Open one of the notebooks:
   - `ResNet152.ipynb` (recommended starting point)
   - `InceptionV3.ipynb`
   - `VGG19.ipynb`
2. Run preprocessing cells:
   - Load dataset from `data/`
   - Apply any reshaping/segmentation steps used in the notebook
   - Confirm label mapping and class balance
3. Train the model and evaluate:
   - Confusion matrix
   - Accuracy/loss curves
   - Precision, Recall, F1-score

---

### C) Reproduce reported results
To reproduce the headline results, run:
- `ResNet152.ipynb` start-to-finish  
using the same dataset split strategy and hyperparameters documented below.

---

## Hyperparameters (Reported)

| Parameter | InceptionV3 | ResNet152 | VGG19 |
|---|---:|---:|---:|
| Learning rate | 0.0001 | 0.001 | 0.0001 |
| Batch size | 16 | 28 | 32 |
| Loss function | Categorical cross-entropy | Categorical cross-entropy | Categorical cross-entropy |
| Epochs | 20 | 15 | 20 |

> Note: If you change the dataset split, preprocessing, or augmentation, your metrics may shift. Keep a fixed random seed for reproducibility when possible.

---

## Limitations

- **Simulation-only data:** Results are obtained from simulated stator current signatures; real-world measurements may include additional noise sources, sensor drift, and non-idealities.
- **Dataset size:** The dataset is relatively small for deep CNN fine-tuning; larger datasets typically improve generalization and robustness.
- **Fault coverage:** The current study focuses on stator-related faults (healthy, single-phasing, phase-to-phase). It does not yet include common industrial fault modes such as bearing faults, rotor bar faults, or eccentricity.
- **Domain shift risk:** Models trained on simulated signatures may not transfer directly to field data without domain adaptation or re-training with real measurements.

---

## Acknowledgements

- Project supervisor(s) and department support (as listed in the project report).
- Contributors and collaborators involved in simulation setup, dataset preparation, and evaluation.
- Open-source libraries and research community enabling transfer learning workflows.


