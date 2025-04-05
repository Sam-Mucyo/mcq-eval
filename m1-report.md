# Pirá 2.0 Dataset Analysis Report

## 1. Base Features of Data

The dataset is structured as follows:

- **Training dataset**: 1,806 rows × 31 columns
- **Test dataset**: 227 rows × 22 columns
- **Validation dataset**: 225 rows × 22 columns

The dataset is relatively compact in size, with the combined datasets containing 3,612 questions (1,806 in English and 1,806 in Portuguese).

**Source**: [Pirá GitHub Repository](https://github.com/C4AI/Pira/tree/main)

## 2. Dataset Description

The Pirá dataset is a bilingual (Portuguese-English) question-answering (QA) dataset focused on topics related to the ocean, the Brazilian coast, and climate change. It represents the first bilingual QA dataset including Portuguese and one of the few QA datasets dedicated to scientific topics and environmental issues.

### Supporting Texts

- **Scientific paper abstracts**: 3,891 entries from Scopus
- **UN report excerpts**: 189 passages from World Ocean Assessment I & II

### Dataset Features

Each passage is associated with questions formulated and answered by human volunteers in both Portuguese and English. The dataset includes:

- Original QA pairs in both languages
- Human paraphrases of questions and answers for data augmentation
- Human assessments of QA quality
- Labels for answerability (some questions are unanswerable by design)
- A multiple-choice extension, where each question has five options (one correct)

Our project focuses specifically on the multiple-choice QA (MCQ) subset of Pirá 2.0, which supports evaluation on machine comprehension and answer selection tasks. This version includes automatically generated paraphrases, expanding the training data for robust model development.

## 3. Data Analysis and Potential Issues

### Corpus Distribution

The dataset draws from multiple corpus sources:

- **Scopus**: 1,634 questions (90.48%)
- **WOA2**: 111 questions (6.15%)
- **WOA1**: 61 questions (3.38%)

This indicates a significant imbalance in corpus representation, with the vast majority coming from Scopus.

### Answer Triggering Labels

The dataset includes an answer triggering feature:

- **Answerable questions (1.0)**: 1,432 (79.29%)
- **Unanswerable questions (0.0)**: 145 (8.03%)
- **Missing labels**: 229 (12.68%)

This shows a class imbalance with significantly more answerable than unanswerable questions and 13% of the dataset missing labels.

### Language Distribution

The dataset is perfectly balanced in terms of language:

- **English questions**: 1,806 (50%)
- **Portuguese questions**: 1,806 (50%)

### Automatic Translations and Paraphrases

The dataset includes automatic translations and paraphrases for both questions and answers in both languages, which provides:

- Data augmentation opportunities
- Potential for cross-lingual model training
- Variations in phrasing that could improve model robustness

### Examples of Answerable vs. Unanswerable Questions

**Answerable Questions Examples:**

- Question: "What method can be used to estimate air–sea sensible- and latent-heat fluxes?"
  Answer: "The turbulent covariance method."
- Question: "Why is mercury considered one of the ten most toxic substances to human health?"
  Answer: "Because in humans, exposure can be harmful to fetal brain development. Research has demonstrated a link between exposure to this neurotoxin and developmental deficits the fine motor skills, language and memory of the fetus."

**Unanswerable Questions Examples:**

- "What is the content of the study case presented in this paper?"
- "What is the main characteristic of countries where tourism is the main source of employment?"
- "What was the number of accidents involving Petrobras support vessels in 2012?"

## 4. Potential Data Issues and Mitigation Plans

### 1. Missing Values

- **Issue**: Some records are missing answer triggering labels (12.68% of the dataset)
- **Mitigation**:
  - Exclude samples with missing labels during training
  - Use semi-supervised techniques to predict missing labels based on question characteristics
  - Create separate models for answerable/unanswerable classification
  - Implement data imputation techniques based on similar questions

### 2. Class Imbalance

- **Issue**: Significant imbalance between answerable (79.29%) and unanswerable (8.03%) questions
- **Issue**: Imbalance in corpus sources (90.48% from Scopus)
- **Mitigation**:
  - Apply class weighting during model training to give more importance to minority classes
  - Use oversampling techniques like SMOTE for minority classes
  - Implement undersampling for majority classes where appropriate
  - Consider stratified sampling for evaluation metrics to ensure fair assessment
  - Create balanced validation sets to accurately measure performance

### 3. Linguistic Variations

- **Issue**: Automatic translations may contain inaccuracies or semantic drift
- **Issue**: Paraphrases might alter the original meaning slightly
- **Mitigation**:
  - Compare model performance on original vs. translated content
  - Consider ensemble approaches that leverage both original and translated content
  - Implement data cleaning to address translation artifacts
  - Use semantic similarity measures to identify and correct problematic translations
  - Leverage pre-trained multilingual models that can handle cross-lingual variations

### 4. Feature Engineering Needs

- **Issue**: The dataset may benefit from additional preprocessing
- **Mitigation**:
  - Text normalization (stemming/lemmatization) for both English and Portuguese
  - Named entity recognition for scientific terms related to oceanography
  - Domain-specific feature extraction related to oceanography and climate science
  - Develop domain-specific preprocessing pipelines
  - Experiment with different text representation techniques
  - Consider incorporating external knowledge bases for scientific terminology

### 5. Cross-lingual Challenges

- **Issue**: Models may perform differently on English vs. Portuguese content
- **Mitigation**:
  - Evaluate model performance separately by language
  - Consider language-specific fine-tuning
  - Explore multilingual embedding techniques like mBERT or XLM-RoBERTa
  - Implement cross-lingual data augmentation
  - Use translation quality metrics to identify potential issues

## 5. Next Steps

### Data Preprocessing

- Handle missing answer triggering labels through imputation or exclusion
- Implement text normalization techniques for both languages
- Prepare cross-validation splits that account for class imbalance
- Create balanced evaluation sets to ensure fair assessment

### Feature Engineering

- Extract linguistic features from questions and answers
- Create embeddings for questions and possible answers using multilingual models
- Develop domain-specific features for oceanography and climate science
- Implement feature selection to identify most predictive attributes

### Model Development

- Experiment with multilingual transformer models (mBERT, XLM-RoBERTa)
- Develop separate models for answerable/unanswerable classification
- Implement multiple-choice answer selection approaches
- Consider ensemble methods that combine different model architectures

### Evaluation Framework

- Design metrics that account for class imbalance (F1-score, precision, recall)
- Evaluate performance across languages separately and combined
- Compare performance across different corpus sources
- Implement cross-validation strategies that respect the dataset structure
