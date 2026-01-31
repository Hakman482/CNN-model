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


