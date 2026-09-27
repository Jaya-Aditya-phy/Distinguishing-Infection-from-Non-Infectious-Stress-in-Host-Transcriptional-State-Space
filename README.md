

# Distinguishing Infection from Non-Infectious Stress in Host Transcriptional State Space

> A computational framework for testing whether infection occupies a reproducible, low-dimensional trajectory distinct from generic cellular stress.

---

## Overview

Host cells activate overlapping transcriptional programs in response to infection, sterile inflammation, cytokine exposure, oxidative stress, and other non-infectious perturbations.

This creates a fundamental problem:

**A classifier can achieve high infection-vs-control accuracy while actually learning generic inflammation or cellular stress rather than infection itself.**

This project treats that ambiguity as the central research problem.

Rather than asking only:

> *Can infection be classified from transcriptomic data?*

we ask:

> **Does infection occupy a reproducible transcriptional trajectory that diverges from non-infectious stress, and is that divergence conserved across pathogens and donors?**

The project investigates the **dimensionality, temporal onset, and cross-pathogen conservation** of this divergence.

---

## Research Question

> **Does infection produce a reproducible, low-dimensional trajectory in host transcriptional state space that can be distinguished from generic non-infectious cellular stress?**

The framework is designed to distinguish an infection-associated signal from transcriptional programs such as:

- Interferon-stimulated genes (ISGs)
- NF-κB / inflammatory signaling
- Cytokine responses
- Oxidative stress
- Heat-shock responses
- Apoptosis / cell death

---

# Framework

The project uses a four-layer trajectory-inference framework.

```text
                 RNA-seq data
                     │
                     ▼
        ┌─────────────────────────┐
        │ Layer 0                 │
        │ State-space construction│
        │                         │
        │ PCA / low-dimensional   │
        │ transcriptional space   │
        └────────────┬────────────┘
                     │
                     ▼
        ┌─────────────────────────┐
        │ Layer 1                 │
        │ Divergence variable     │
        │                         │
        │ Infection trajectory    │
        │          vs             │
        │ Stress trajectory       │
        └────────────┬────────────┘
                     │
                     ▼
        ┌─────────────────────────┐
        │ Layer 2                 │
        │ Falsification &         │
        │ generalization          │
        │                         │
        │ Pathogen / donor /      │
        │ dataset holdouts        │
        └────────────┬────────────┘
                     │
                     ▼
        ┌─────────────────────────┐
        │ Layer 3                 │
        │ Scaling & conservation  │
        │                         │
        │ Pathogen diversity →    │
        │ conserved axis          │
        └────────────┬────────────┘
                     │
                     ▼
        ┌─────────────────────────┐
        │ Biological grounding    │
        │                         │
        │ ISG / NF-κB / apoptosis │
        │ oxidative / heat stress │
        └─────────────────────────┘
```

---

## Layer 0 — Transcriptional State Space

Batch- and donor-corrected single-cell or bulk RNA-seq data from infected, stressed, and control cells are projected into a shared low-dimensional representation.

Initial representations will investigate approximately **10–100 PCA components**.

Samples are represented as points in transcriptional state space, allowing temporal responses to be interpreted as trajectories from an unperturbed baseline.

---

## Layer 1 — Divergence Bridge Variable

The central derived quantity is the **directional divergence between infection and non-infectious stress trajectories**.

This variable is intended to connect several otherwise separate diagnostics:

- Classification accuracy
- Divergence timing
- Cross-condition distance
- Trajectory geometry
- Dimensionality of separation

The goal is to determine whether these observations can be explained by a common underlying divergence in host transcriptional state space.

---

## Layer 2 — Falsification-First Validation

Random cell-level train/test splits are explicitly avoided.

Instead, the framework evaluates generalization using:

### Leave-One-Pathogen-Out

One pathogen is completely excluded during training and used only during evaluation.

### Leave-One-Donor-Out

Entire biological donors are held out to test whether the learned signal generalizes beyond donor-specific transcriptional variation.

### Leave-One-Dataset-Out

An independent dataset is held out as an external test.

These validation strategies are designed to reduce leakage arising from:

- Donor identity
- Pathogen identity
- Experimental batch
- Sequencing technology
- Dataset-specific structure

---

## Layer 3 — Scaling and Generalization

The framework investigates whether the detectability of a conserved infection-associated axis depends on the diversity of pathogens and donors represented in the training data.

The proposed hypothesis is that increasing pathogen diversity may reveal a pathogen-conserved component of the host response that is not apparent when considering a single pathogen.

This produces a testable relationship:

```text
Number of pathogens
        │
        ▼
Training diversity
        │
        ▼
Trajectory convergence
        │
        ▼
Divergence detectability
        │
        ▼
Cross-pathogen generalization
```

If a conserved axis does not emerge, that result will be treated as a valid outcome.

---

# Biological Falsification

Any detected divergence must be tested against independently defined biological programs.

The analysis will investigate whether apparent infection-specific separation can instead be explained by:

| Potential confound | Example |
|---|---|
| Interferon response | ISG activation |
| Inflammation | NF-κB / inflammatory signaling |
| Cytokine response | IFN / TNF-associated programs |
| Cell death | Apoptosis / death pathways |
| Oxidative stress | Oxidative-response genes |
| Heat stress | Heat-shock programs |
| Technical variation | Batch / sequencing effects |
| Pathogen signal | Pathogen-derived reads |

The objective is therefore not simply to find a separating classifier.

It is to determine **what biological signal actually produces the separation**.

---

# Primary Case Study

## Human Macrophage Bacterial Infection

The primary analysis uses existing public transcriptomic data.

### GSE145862

The dataset contains scRNA-seq measurements of human monocyte-derived macrophages infected with multiple bacterial species across multiple donors and timepoints.

Pathogens include:

- *Staphylococcus aureus*
- *Listeria monocytogenes*
- *Enterococcus faecalis*
- Group B Streptococcus
- *Yersinia pseudotuberculosis*
- *Shigella flexneri*
- *Salmonella enterica*

The dataset provides the pathogen diversity required for the cross-pathogen component of the framework.

A compatible time-resolved **non-infectious stress / cytokine perturbation dataset** is required for the primary infection-vs-stress comparison.

Candidate datasets will be verified against GEO metadata and the associated literature before being combined.

---

# Cross-Species Extension

An exploratory extension will investigate:

### GSE102160

This dataset contains time-resolved *Salmonella* infection of mouse bone-marrow-derived macrophages.

It will be treated as an **external generalization dataset**, rather than being merged directly into the primary human analysis.

The strongest test of the framework would be transfer across:

```text
Human macrophages
      │
      ├── Held-out pathogen
      │
      ├── Held-out donor
      │
      └── Held-out dataset
               │
               ▼
        Mouse BMDM system
```

Cross-species transfer is therefore treated as an exploratory extension rather than an assumption of conservation.

---

# Objectives

- [ ] Formalize the divergence bridge variable
- [ ] Identify a compatible time-resolved non-infectious stress dataset
- [ ] Verify dataset metadata and experimental compatibility
- [ ] Construct the preprocessing pipeline
- [ ] Build the shared transcriptional state space
- [ ] Reconstruct infection and stress trajectories
- [ ] Define and evaluate trajectory divergence
- [ ] Implement leave-one-pathogen-out validation
- [ ] Implement leave-one-donor-out validation
- [ ] Implement leave-one-dataset-out validation
- [ ] Investigate pathogen-diversity scaling
- [ ] Test biological confounders
- [ ] Evaluate cross-pathogen conservation
- [ ] Explore cross-species transfer
- [ ] Prepare reproducible analysis and manuscript

---

# Why This Is Different

Existing research has extensively investigated:

- Pseudotime and trajectory inference
- Infection-response signatures
- Host-response classifiers
- Transcriptomic disease classification
- Low-dimensional representations of cellular states

The proposed framework instead treats **the separability itself as the object of study**.

Specifically, it asks whether infection-associated divergence has reproducible:

- **Dimensionality**
- **Temporal onset**
- **Cross-pathogen conservation**
- **Cross-donor robustness**
- **Biological specificity**

The aim is therefore not to build another infection-vs-control classifier, but to test whether a **pathogen-independent infection-associated coordinate** exists in host transcriptional state space.

---

# Analysis Philosophy

### Classification is a diagnostic, not the endpoint.

High classification accuracy alone does not establish infection specificity.

### Random cell-level splits are insufficient.

Cells from the same donor, experiment, or pathogen may share substantial biological and technical structure.

### Generalization is part of the hypothesis.

A signal that disappears when the pathogen or donor changes provides fundamentally different evidence from a signal that transfers across them.

### Null results are informative.

If a conserved infection axis cannot be detected under stringent validation, that is a scientifically meaningful result.

### Biological interpretation follows statistical discovery.

Any conserved divergence axis must be tested against known transcriptional stress programs rather than automatically interpreted as infection-specific.

---

# Repository Structure

```text
Divergence-Trajectories/
│
├── data/
│   ├── raw/
│   ├── processed/
│   └── metadata/
│
├── notebooks/
│   ├── 01_data_exploration.ipynb
│   ├── 02_preprocessing.ipynb
│   ├── 03_state_space.ipynb
│   ├── 04_trajectory_analysis.ipynb
│   ├── 05_divergence_analysis.ipynb
│   └── 06_validation.ipynb
│
├── src/
│   ├── preprocessing/
│   ├── trajectory/
│   ├── divergence/
│   ├── validation/
│   └── visualization/
│
├── results/
│   ├── figures/
│   ├── tables/
│   └── models/
│
├── literature/
│
├── README.md
└── LICENSE
```

---

# Reproducibility

All major analyses are intended to be reproducible from publicly available transcriptomic datasets.

The repository will track:

- Dataset metadata
- Preprocessing decisions
- Feature-selection procedures
- Validation splits
- Model configurations
- Analysis scripts
- Generated figures
- Statistical results

Special attention will be given to preventing information leakage across:

```text
Cells
  ↓
Donors
  ↓
Pathogens
  ↓
Datasets
  ↓
Experimental batches
```

---

# Computational Requirements

The project is entirely computational.

No wet-lab reagents or experimental equipment are required.

Initial development is intended to be performed on a personal workstation, with larger analyses potentially requiring additional computational resources.

---

# Project Status

> **Current status: Research proposal / dataset validation**

The research framework has been formulated and the primary human macrophage infection dataset has been identified.

The next major bottleneck is identification and verification of a **compatible time-resolved non-infectious stress dataset**.

---

# Team

**Jaya Aditya** — Project Lead  
IISER Thiruvananthapuram

**Sameer Ahmed**  
IISER Thiruvananthapuram


---

# Citation

If this work develops into a publication or preprint, citation information will be added here.
---

# Disclaimer

This repository contains an experimental research framework.

The proposed divergence variable, scaling relationship, and conserved infection-associated trajectory are **hypotheses to be tested**, not established biological conclusions.

The purpose of the project is to determine whether these properties survive rigorous validation and biological falsification.
