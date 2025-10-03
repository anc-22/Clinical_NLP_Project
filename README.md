# Clinical NLP Pipeline: From Weak Supervision to Emergency Triage

An educational project implementing weak supervision for clinical NLP. Integrated labeling functions, BioClinicalBERT fine-tuning, and ESI-based triage rules. This project helped me understand modern NLP pipelines and their healthcare applications. Built with guidance from research papers and AI assistance.

## 🎯 Project Overview

This project implements a scalable approach to clinical NLP that:
- Extracts medical entities without manual annotation using weak supervision
- Trains a BioClinicalBERT NER model on automatically generated "silver" training data
- Implements clinical triage rules based on Emergency Severity Index (ESI) standards
- Provides actionable insights for healthcare workflows

## 📊 Dataset

**Source**: Augmented Clinical Notes from Hugging Face
- **Size**: 30,000 clinical note triplets
- **Components**:
  - Real clinical notes from PMC-Patients dataset
  - Synthetic patient-doctor dialogues (generated via GPT-3.5)
  - Structured patient summaries (generated via GPT-4)
- **Repository**: [EPFL-IC-Make-Team/ClinicalNotes](https://huggingface.co/datasets/EPFL-IC-Make-Team/augmented-clinical-notes)
- **Paper**: MediNote: Automated Clinical Notes
- **Language**: English

## 🔧 Pipeline Architecture

### Stage 1: Data Preprocessing
- **JSON Parsing**: Complex nested JSON structures are flattened into manageable CSV formats
- **Text Normalization**: Standardized text processing for downstream tasks
- **Data Explosion**: Nested structures (surgeries, symptoms, diagnoses) expanded into separate tables

Example transformation:
```
Original: 'summary': {"visit motivation": "Mass in right thigh", "admission": [...]}
→ Parsed: Individual fields extracted and normalized
```

### Stage 2: Weak Supervision & Labeling Functions

#### Row-Level Labels
Created domain-specific labeling functions (LFs) that generate weak labels:
- **Surgery LFs**: Detect emergency procedures, specific surgeries (hip/knee replacement)
- **Symptom LFs**: Identify acute onset, pain severity, bilateral symptoms
- **Diagnosis LFs**: Flag critical findings, imaging results
- **Treatment LFs**: Mark emergency treatments, specific medications


#### Span-Level Labels
Generate silver NER training data through:
1. Entity normalization to standard medical schema
2. Dictionary-based spanning with fuzzy matching
3. Confidence scoring based on frequency and context
4. BIO tagging for transformer training

### Stage 3: NER Model Training
- **Model**: BioClinicalBERT fine-tuned for token classification
- **Training Data**: Silver-labeled spans from weak supervision
- **Features**: 
  - Windowed inference for long documents
  - Confidence-based filtering
  - Multiple entity type support

### Stage 4: Clinical Triage System

Implements Emergency Severity Index (ESI) classification:

#### ESI Levels
- **ESI 1** (Immediate): Life-threatening conditions requiring immediate intervention
- **ESI 2** (Emergency): High risk of deterioration, time-critical
- **ESI 3** (Urgent): Stable but needs prompt assessment  
- **ESI 4** (Non-urgent): Can wait extended period
- **ESI 5** (Minor): No acute threat

#### Key Features
- **Multi-source Integration**: Combines NER outputs with labeling function signals
- **Contextual Rules**: Considers entity combinations, negation, proximity
- **Confidence Weighting**: Prioritizes high-confidence predictions
- **Provenance Tracking**: Records which entities triggered each rule

#### Rule Examples
- Cardiac emergency: "chest pain" + acute onset + high severity
- Neurological emergency: focal weakness + laterality + acute presentation
- Critical findings: hemorrhage/embolism + acute temporal pattern


## 📈 Evaluation & Results

### NER Performance
- Precision, Recall, F1 scores per entity type
- Comparison of silver vs. manually annotated test set

### Triage Validation
- ESI distribution analysis
- Rule coverage statistics
- Entity source attribution (NER vs. LF vs. combined)

### Feedback Loop
The system generates actionable feedback for improvement:
- High-priority cases with few extracted entities
- Terms frequently in critical cases but missing from lexicons
- Rules dependent solely on NER (suggesting LF gaps)

## 🔄 Continuous Improvement

The pipeline supports iterative enhancement through:
1. **Labeling Function Refinement**: Based on triage feedback
2. **Active Learning**: Focus annotation on high-uncertainty cases
3. **Rule Adjustment**: Tune ESI thresholds based on clinical feedback
4. **Model Updates**: Retrain with improved silver labels

## 🛠️ Requirements

```
transformers>=4.30.0
torch>=2.0.0
pandas>=1.5.0
numpy>=1.24.0
scikit-learn>=1.2.0
spacy>=3.5.0
seqeval>=1.2.2
```


## 🙏 Acknowledgments

- EPFL-IC-Make-Team for the Augmented Clinical Notes dataset
- Hugging Face for hosting and infrastructure
- BioClinicalBERT authors for the pre-trained model
