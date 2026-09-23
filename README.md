<!-- PROJECT SHIELDS -->

<!-- PROJECT LOGO -->

<br />

<div align="center">

<h3 align="center">MammoGraph</h3>

<p align="center">

An explainable mammogram analysis framework that uses Graph Neural Transformers for lesion classification and localization, followed by retrieval-augmented generation for evidence-grounded clinical interpretation.

<br />
<br />

<!-- <a href="https://github.com/abrahamjust/MammoGraph"><strong>Explore the Project »</strong></a> -->

<br />
<br />

<!-- <a href="LIVE_DEMO_LINK">View Demo</a> -->

</p>

</div>

---

<details>

  <summary>Table of Contents</summary>

1. [About The Project](#about-the-project)
2. [Features](#features)
3. [Built With](#built-with)
4. [System Architecture](#system-architecture)
5. [Getting Started](#getting-started)
6. [Usage](#usage)
7. [Mammographic Intelligence Pipeline](#mammographic-intelligence-pipeline)
8. [Model Training and Evaluation](#model-training-and-evaluation)
9. [Roadmap](#roadmap)
10. [Acknowledgements](#acknowledgements)
11. [License](#license)
12. [Contact](#contact)

</details>

---

# About The Project

**MammoGraph** is an explainable mammogram analysis framework that combines Graph Neural Transformers (GNTs), computer vision, and retrieval-augmented generation to analyze mammographic findings.

The system transforms mammogram images into spatial graph representations, where feature-map regions are represented as graph nodes and connected according to their spatial relationships. A Graph Neural Transformer then learns contextual relationships between mammographic regions for multi-label lesion classification and localization.

Instead of treating the mammogram as a collection of independent pixels, MammoGraph explicitly models relationships between spatially adjacent regions. The model produces structured findings such as lesion category, localization, objectness, and confidence.

The structured model output is then passed to a medical knowledge retrieval pipeline. Relevant medical evidence is retrieved from a curated knowledge base and supplied to a language model to generate concise, evidence-grounded clinical interpretations.

The project separates automated image analysis from clinical interpretation:

```text
GNT      → What is present?
RAG      → What does medical evidence say?
LLM      → How can the evidence be summarized?
Clinician → Final clinical decision
```

MammoGraph uses the VinDr-Mammo dataset for mammographic image analysis and focuses on explainable lesion detection, classification, and evidence-grounded interpretation rather than autonomous diagnosis or patient-specific outcome prediction.

---

# Features

## Mammogram Processing

- Mammogram image preprocessing
- PNG-based mammogram pipeline
- Image dimension validation
- Ground-truth annotation validation
- Bounding-box preprocessing
- Study-level train / validation / test splitting
- Prevention of study leakage across splits

## Dataset

- VinDr-Mammo dataset support
- 20,000 mammographic images
- 5,000 four-view examinations
- Finding-level annotations
- Breast-level metadata
- BI-RADS information
- Bounding-box annotations
- Multi-label mammographic finding representation

## Mammographic Findings

The classification pipeline supports:

- Mass
- Suspicious Calcification
- Focal Asymmetry
- Architectural Distortion
- Asymmetry
- Suspicious Lymph Node
- Skin Thickening
- Global Asymmetry
- Nipple Retraction
- Skin Retraction

`No Finding` is represented as the absence of abnormal finding labels rather than as an independent abnormality class.

## Graph Construction

- CNN feature extraction
- Spatial feature-map representation
- Conversion of feature-map regions into graph nodes
- Node position encoding
- Spatial k-nearest-neighbor graph construction
- Contextual relationships between neighboring mammographic regions

## Graph Neural Transformer

- Transformer-based graph attention
- Multi-head neighborhood attention
- Spatially aware node representations
- Image-level multi-label classification
- Node-level finding classification
- Objectness prediction
- Bounding-box regression
- Joint classification and localization learning

## Classification

- Multi-label mammographic finding classification
- Focal loss for severe class imbalance
- Per-class evaluation
- AUROC evaluation
- AUPRC evaluation
- Precision and recall
- Macro and micro F1-score
- Balanced accuracy
- Specificity analysis

## Lesion Localization

- Node-level objectness prediction
- Bounding-box regression
- Normalized bounding-box representation
- IoU-based localization evaluation
- Recall@IoU thresholds
- Ground-truth to prediction matching

## Explainability

- Lesion localization
- Finding-specific graph regions
- Objectness scores
- Structured model outputs
- Traceable transition from image finding to medical evidence

## Medical Knowledge Retrieval

- Structured finding extraction
- Medical knowledge retrieval
- Vector-based semantic retrieval
- Evidence-grounded context construction
- Relevant medical literature / knowledge selection

## Retrieval-Augmented Generation

- Retrieval-Augmented Generation pipeline
- Grounded clinical interpretation
- Evidence-aware language generation
- Context-constrained generation
- Separation of model findings from retrieved medical knowledge

## Clinical Interpretation

The system can generate structured interpretations such as:

```text
Finding:
Mass

Location:
Left breast

Model confidence:
High

Clinical interpretation:
A highly suspicious mammographic mass warrants further diagnostic
evaluation. If malignancy is confirmed and remains untreated,
breast cancer can progress locally and potentially spread to
regional lymph nodes or distant organs.
```

The generated interpretation is intended to summarize retrieved medical evidence and is not a substitute for clinical diagnosis or professional medical decision-making.

---

# Built With

## Machine Learning

- Python
- PyTorch
- PyTorch Geometric
- torchvision
- scikit-learn
- ResNet
- Graph Neural Networks
- Graph Neural Transformers
- Transformers
- Sentence Transformers

## Computer Vision

- ResNet backbone
- Feature-map extraction
- Spatial graph construction
- Bounding-box regression
- IoU-based evaluation
- Multi-label image classification

## Retrieval-Augmented Generation

- FAISS
- Sentence Transformers
- Vector similarity search
- Medical knowledge retrieval
- RAG pipeline
- Medical LLM

## Data Processing

- pandas
- NumPy
- OpenCV / image processing utilities
- PyTorch DataLoader
- CSV-based annotation processing

## Development

- Jupyter Notebook
- Google Colab
- Kaggle
- Git
- GitHub

---

# System Architecture

```text
                         ┌──────────────────────┐
                         │   VinDr-Mammo        │
                         │      Dataset         │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │ Image Preprocessing  │
                         │                      │
                         │ Resize / Normalize   │
                         │ Annotation Handling  │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │   CNN Backbone       │
                         │      ResNet          │
                         └──────────┬───────────┘
                                    │
                              Feature Map
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │  Graph Construction  │
                         │                      │
                         │ Feature-map regions  │
                         │ → Graph nodes        │
                         │ → Spatial k-NN edges │
                         └──────────┬───────────┘
                                    │
                                    ▼
                  ┌──────────────────────────────────┐
                  │     Graph Neural Transformer     │
                  │                                  │
                  │ Multi-head graph attention       │
                  │ Contextual node representation   │
                  └───────────────┬──────────────────┘
                                  │
              ┌───────────────────┼────────────────────┐
              │                   │                    │
              ▼                   ▼                    ▼
      ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
      │ Image-level  │    │ Node-level   │    │ Localization │
      │ Classification│   │ Classification│   │ / Objectness │
      └──────┬───────┘    └──────┬───────┘    └──────┬───────┘
             │                   │                    │
             └───────────────────┼────────────────────┘
                                 │
                                 ▼
                       ┌──────────────────────┐
                       │ Structured Finding   │
                       │                      │
                       │ Finding + Location   │
                       │ Confidence + BI-RADS│
                       └──────────┬───────────┘
                                  │
                                  ▼
                       ┌──────────────────────┐
                       │ Medical Knowledge    │
                       │ Retrieval / FAISS    │
                       └──────────┬───────────┘
                                  │
                                  ▼
                       ┌──────────────────────┐
                       │ Medical LLM          │
                       │                      │
                       │ Evidence-grounded    │
                       │ interpretation       │
                       └──────────┬───────────┘
                                  │
                                  ▼
                       ┌──────────────────────┐
                       │ Clinical             │
                       │ Interpretation       │
                       └──────────────────────┘
```

---

# Model Architecture

MammoGraph uses a two-stage representation pipeline.

```text
Mammogram
    │
    ▼
ResNet Feature Extraction
    │
    ▼
Feature Map
    │
    ▼
Spatial Regions
    │
    ▼
Graph Nodes
    │
    ├── CNN feature vector
    └── normalized spatial coordinates
    │
    ▼
Spatial k-NN Graph
    │
    ▼
Graph Neural Transformer
    │
    ├── Image classification
    ├── Node classification
    ├── Objectness
    └── Bounding-box regression
```

The graph representation uses the spatial structure of the CNN feature map to create graph nodes. Neighboring nodes exchange information through transformer-style attention.

The graph model therefore captures both:

- visual information extracted by the CNN
- spatial/contextual relationships between mammographic regions

---

# Getting Started

Follow these steps to run MammoGraph locally or in a GPU notebook environment.

## Prerequisites

- Python 3.10+
- CUDA-capable GPU recommended
- PyTorch
- torchvision
- PyTorch Geometric
- pandas
- NumPy
- scikit-learn
- Jupyter / Google Colab / Kaggle
- VinDr-Mammo dataset access

---

## Installation

### Clone the repository

```bash
git clone https://github.com/abrahamjust/MammoGraph.git
```

### Navigate into the project

```bash
cd MammoGraph
```

### Create a virtual environment

```bash
python -m venv .venv
```

### Activate the environment

Linux / macOS:

```bash
source .venv/bin/activate
```

Windows:

```bash
.venv\Scripts\activate
```

### Install dependencies

```bash
pip install torch torchvision
pip install torch-geometric
pip install pandas numpy scikit-learn matplotlib
pip install sentence-transformers faiss-cpu
```

Install any additional CUDA-specific PyTorch Geometric dependencies according to the installed PyTorch and CUDA versions.

---

# Dataset Setup

MammoGraph uses the VinDr-Mammo dataset.

Official dataset:

```text
https://physionet.org/content/vindr-mammo/1.0.0/
```

The dataset contains:

```text
5,000 examinations
20,000 mammographic images
4 views per examination
```

The project uses study-level splitting to prevent images from the same examination from appearing across training, validation, and test sets.

Expected image organization:

```text
dataset/
└── images_png/
    ├── study_id_1/
    │   ├── image_1.png
    │   ├── image_2.png
    │   ├── image_3.png
    │   └── image_4.png
    │
    ├── study_id_2/
    │   ├── image_1.png
    │   ├── image_2.png
    │   ├── image_3.png
    │   └── image_4.png
    │
    └── ...
```

Annotation metadata is stored in the project CSV used to construct image-level and node-level training targets.

---

# Usage

## 1. Prepare the Dataset

Load the VinDr-Mammo annotations and validate:

- Image identifiers
- Study identifiers
- Image dimensions
- Finding labels
- Bounding boxes
- Train/test split assignments

---

## 2. Generate Train / Validation / Test Splits

The official training examinations are divided into training and validation subsets at the study level.

```text
Official training studies
        │
        ├── Training studies
        │
        └── Validation studies

Official test studies
        │
        └── Test set
```

This prevents leakage between images belonging to the same examination.

---

## 3. Build the Dataset

The PyTorch dataset returns:

```text
Image
Bounding boxes
Finding labels
Metadata
```

Bounding boxes are converted into normalized coordinates for the GNT localization head.

---

## 4. Build the Graph

The CNN feature map is converted into spatial graph nodes.

For example:

```text
Feature Map
48 × 29
   ↓
1392 nodes
```

Each node contains:

```text
CNN feature embedding
+
normalized x coordinate
+
normalized y coordinate
```

Spatial k-nearest-neighbor edges are then constructed between nodes.

---

## 5. Train the GNT

The model jointly optimizes:

```text
Image Classification Loss
        +
Objectness Loss
        +
Node Classification Loss
        +
Bounding Box Regression Loss
```

Focal loss is used for image-level classification to reduce the impact of severe class imbalance.

---

## 6. Evaluate Classification

The classification pipeline reports:

```text
AUROC
AUPRC
Accuracy
Balanced Accuracy
Precision
Recall
F1 Score
Specificity
```

Both macro and micro aggregation are used where appropriate.

---

## 7. Evaluate Localization

Localization is evaluated using:

```text
Intersection over Union (IoU)
Recall@IoU 0.50
Recall@IoU 0.75
```

Predicted normalized bounding boxes are compared against ground-truth lesion annotations.

---

## 8. Generate Structured Findings

The GNT produces structured outputs:

```text
Finding
Location
Objectness
Confidence
Bounding Box
```

These outputs form the interface between computer vision and the downstream medical knowledge system.

---

## 9. Retrieve Medical Evidence

The structured finding is converted into a retrieval query.

```text
GNT Finding
    │
    ▼
Retrieval Query
    │
    ▼
Embedding Model
    │
    ▼
FAISS Vector Search
    │
    ▼
Relevant Medical Evidence
```

---

## 10. Generate Clinical Interpretation

Retrieved evidence is supplied to the medical LLM together with the structured model output.

```text
Structured Finding
        +
Retrieved Evidence
        ↓
Medical LLM
        ↓
Evidence-grounded Interpretation
```

The language model is constrained to interpret the detected finding using retrieved medical knowledge rather than independently inventing clinical evidence.

---

# Mammographic Intelligence Pipeline

The system separates computer vision inference from medical knowledge interpretation.

## Stage 1 — Visual Analysis

The GNT determines:

```text
What is present?
Where is it?
How confident is the model?
```

Example:

```text
Finding:
Mass

Location:
Left breast

Objectness:
0.91

Model confidence:
0.91
```

---

## Stage 2 — Medical Retrieval

The finding is used to retrieve relevant medical knowledge.

```text
Mass
+
Mammographic context
+
Relevant clinical evidence
```

The retrieval system provides supporting information to the generation stage.

---

## Stage 3 — Evidence-Grounded Interpretation

The medical LLM summarizes the retrieved evidence.

Example:

```text
Clinical implication:

A highly suspicious mammographic mass warrants further diagnostic
evaluation. If malignancy is confirmed and remains untreated,
breast cancer can progress locally and potentially spread to
regional lymph nodes or distant organs.
```

The system does not claim to determine an individual patient's future outcome.

---

# Model Training and Evaluation

## Baselines

MammoGraph is evaluated against conventional image-level classification approaches.

The baseline pipeline uses:

```text
Mammogram
    ↓
CNN
    ↓
Global representation
    ↓
Multi-label classifier
```

The proposed pipeline uses:

```text
Mammogram
    ↓
CNN
    ↓
Graph construction
    ↓
Graph Neural Transformer
    ↓
Multi-task prediction
```

This allows the effect of explicit spatial graph reasoning to be evaluated.

---

## Classification Metrics

### AUROC

Measures the ability of the model to rank positive findings above negative findings across classification thresholds.

### AUPRC

Measures precision-recall performance and is particularly useful for highly imbalanced mammographic finding classes.

### F1 Score

Measures the balance between precision and recall.

### Balanced Accuracy

Provides a more informative classification measure when positive and negative examples are highly imbalanced.

---

## Localization Metrics

### Intersection over Union

```text
IoU = Area(Prediction ∩ Ground Truth)
      --------------------------------
      Area(Prediction ∪ Ground Truth)
```

### Recall@0.50

A predicted lesion is considered localized when its IoU with a ground-truth lesion is at least 0.50.

### Recall@0.75

A stricter localization criterion requiring IoU ≥ 0.75.

---

# Ablation Studies

The project evaluates the contribution of individual design decisions.

Potential experiments include:

```text
CNN baseline
      ↓
GNT
      ↓
GNT + Focal Loss
      ↓
GNT + improved pooling
      ↓
GNT + improved graph construction
      ↓
Final GNT
```

Additional ablations can investigate:

- Graph connectivity
- Number of GNT layers
- Number of attention heads
- Backbone architecture
- Loss functions
- Localization supervision
- Node feature composition
- Retrieval configuration

---

# Medical RAG Architecture

The medical interpretation pipeline follows:

```text
GNT Prediction
      │
      ▼
Structured Finding
      │
      ▼
Query Construction
      │
      ▼
Embedding Model
      │
      ▼
FAISS Vector Database
      │
      ▼
Top-K Medical Evidence
      │
      ▼
Context Construction
      │
      ▼
Medical LLM
      │
      ▼
Grounded Clinical Interpretation
```

The retrieval component is designed to reduce unsupported generation by requiring the language model to work from retrieved medical evidence.

---

# Explainability

MammoGraph provides several levels of interpretability.

## Spatial Explainability

The model identifies graph regions associated with abnormal findings.

## Localization Explainability

Bounding boxes indicate where the model identifies a suspicious region.

## Structured Prediction

Instead of producing only free-form text, the computer vision model produces structured findings:

```text
Finding
Location
Objectness
Confidence
Bounding Box
```

## Evidence Explainability

The RAG system provides supporting medical evidence for the generated interpretation.

This creates an explainability chain:

```text
Image
  ↓
Detected Region
  ↓
Finding
  ↓
Retrieved Evidence
  ↓
Clinical Interpretation
```

---

# Roadmap

## Dataset

- [x] VinDr-Mammo integration
- [x] Image validation
- [x] Annotation validation
- [x] Study-level dataset splitting
- [x] Bounding-box preprocessing
- [x] Multi-label target construction

## Computer Vision

- [x] CNN baseline
- [x] Mammogram feature extraction
- [x] Graph construction
- [x] Spatial k-NN connectivity
- [x] Graph Neural Transformer
- [x] Multi-label classification
- [x] Objectness prediction
- [x] Bounding-box regression
- [x] Localization evaluation

## Classification

- [x] Class imbalance analysis
- [x] Focal loss
- [x] Macro AUROC evaluation
- [x] Macro AUPRC evaluation
- [x] Precision / recall evaluation
- [x] F1 evaluation
- [x] Specificity analysis

## Explainability

- [x] Lesion localization
- [x] Structured findings
- [x] Confidence estimation
- [x] Evidence-grounded interpretation

## Medical RAG

- [x] Medical knowledge ingestion
- [x] Text chunking
- [x] Embedding generation
- [x] FAISS vector indexing
- [x] Semantic retrieval
- [x] Evidence context construction
- [x] Medical LLM integration
- [x] Grounded clinical interpretation

## Evaluation

- [x] CNN baseline
- [x] GNT evaluation
- [x] Classification metrics
- [x] Localization metrics
- [x] Ablation experiments
- [x] Qualitative visualization

## Future Improvements

- [ ] Multi-scale graph construction
- [ ] Visual-similarity graph edges
- [ ] Adaptive graph construction
- [ ] Stronger visual backbone
- [ ] Attention-based graph pooling
- [ ] Multi-task BI-RADS prediction
- [ ] Larger medical knowledge base
- [ ] Clinical expert evaluation
- [ ] External dataset validation
- [ ] Prospective clinical evaluation

---

# Acknowledgements

- VinDr-Mammo
- PhysioNet
- VinBigData
- PyTorch
- PyTorch Geometric
- torchvision
- FAISS
- Sentence Transformers
- scikit-learn

---

# License

This project is intended for educational, research, and portfolio purposes.

The VinDr-Mammo dataset is subject to its own licensing and access terms. Users must review and comply with the dataset's official terms before downloading or redistributing the data.

---

# Contact

**Abraham Justin**

GitHub: https://github.com/abrahamjust

Email: abrahamjust@gmail.com

<!-- Project Link: https://github.com/abrahamjust/MammoGraph -->

<p align="right">(<a href="#top">back to top</a>)</p>
