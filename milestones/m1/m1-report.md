# Pirá 2.0 Dataset Analysis for Multiple-Choice Question Validation

## 1. Dataset Description

The Pirá dataset is a bilingual (Portuguese-English) question-answering (QA) dataset focused on topics related to the ocean, the Brazilian coast, and climate change. It is particularly valuable for our project as it represents the first bilingual QA dataset including Portuguese and one of the few QA datasets dedicated to scientific topics and environmental issues.

### Dataset Structure and Size

- **Training dataset**: 1,806 rows × 31 columns
- **Test dataset**: 227 rows × 22 columns
- **Validation dataset**: 225 rows × 22 columns

The combined datasets contain 3,612 questions (1,806 in English and 1,806 in Portuguese), providing a balanced representation of both languages, which is crucial for our cross-lingual validation task.

**Source of data**: https://github.com/C4AI/Pira/tree/main

### Supporting Texts

The questions are based on two types of supporting texts:

- **Scientific paper abstracts**: 3,891 entries from Scopus
- **UN report excerpts**: 189 passages from World Ocean Assessment I & II

### Key Features Relevant to Our Project

The dataset includes several features that make it particularly suitable for our multiple-choice question validation task:

- **Bilingual content**: All questions and answers are available in both English and Portuguese
- **Multiple-choice format**: Each question has five options (one correct)
- **Answer triggering labels**: Indicates whether questions are answerable based on the passage
- **Automatic translations and paraphrases**: Provides variations in phrasing that can help test robustness
- **Human quality assessments**: Offers insights into question quality

These features align well with our project goal of fine-tuning a language model to validate multiple-choice questions in both English and Portuguese by comparing performance with and without access to the passage.

## 2. Potential Data Issues and Mitigation Strategies

### Data Cleaning Needs

**Issue**: The first two columns are just duplicates of the index, requiring initial cleanup.

**Mitigation Strategy**:

- Drop the first column and rename the second as "index", setting it as the dataset index
- This basic cleaning step ensures the data structure is optimal before further processing

### Missing Data

**Issue 1**: 178 rows (approximately 10% of the dataset) are missing answer triggering (AT) labels, which are crucial for determining whether a question can be answered based solely on the passage.

**Mitigation Strategy**:

- We will conduct conditional fine-tuning: the model will only use the AT labels column if it has a non-NaN value
- This approach avoids losing valuable training data while ensuring we only train on reliable label information

**Issue 2**: 51 rows (approximately 2.5% of the train data) are missing values across multiple important columns: 'question_generic', 'answer_in_text', 'answer_difficulty', 'question_meaningful', 'answer_equivalent', and 'question_type'.

**Mitigation Strategy**:

- Drop these 51 rows since they represent a small portion of the dataset and there is no obvious trend in the missingness
- All rows that had validation NaN values were covered by dropping these 51 rows

**Issue 3**: 222 rows (over 10% of the data) are missing paraphrase data in both English and Portuguese.

**Mitigation Strategy**:

- We cannot drop these rows as they represent a significant portion of the data
- Instead, we will ensure our model uses paraphrasing only when they are available
- This maintains dataset size while working around the missing paraphrase information

**Issue 4**: The 'eid_article_scopus' column contains IDs for Scopus database articles.

**Mitigation Strategy**:

- Since we have the actual text content, we can safely drop this column as the ID is not particularly important for our modeling tasks

### Data Imbalance

**Issue 1**: Significant imbalance between answerable (79.29%) and unanswerable (8.03%) questions, which could bias our model toward predicting questions as answerable.

**Mitigation Strategy**:

- Implement class weighting during model training to give more importance to unanswerable questions
- Create balanced evaluation sets to accurately assess model performance
- For the next milestone, we will prepare stratified sampling for cross-validation to ensure representative distribution

**Issue 2**: Imbalance in corpus sources (90.48% from Scopus,, 6.15% from WOA2, 3.38% from WOA1), which may limit the model's ability to generalize across different text types.

**Mitigation Strategy**:

- Monitor performance separately for each corpus source
- Apply domain adaptation techniques to improve performance on underrepresented sources
- For the next milestone, we will create corpus-specific evaluation metrics to identify any systematic biases

### Linguistic Variations and Translation Quality

**Issue**: Automatic translations and paraphrases may contain inaccuracies or semantic drift, potentially affecting the quality of our training data.

**Mitigation Strategy**:

- Conduct quality assessment of translations using semantic similarity measures
- Compare model performance on original vs. translated content
- Ensure our model uses paraphrasing only when they are available
- For the next milestone, we will develop a filtering mechanism to identify and address problematic translations

### Cross-lingual Performance Gaps

**Issue**: Models may perform differently on English vs. Portuguese content due to inherent differences in language structure and the typically better resource availability for English.

**Mitigation Strategy**:

- Utilize multilingual pre-trained models (mBERT or XLM-RoBERTa) that have been exposed to both languages
- Evaluate performance separately by language to identify gaps
- For the next milestone, we will implement language-specific fine-tuning if necessary

## 3. Preprocessing Steps for Next Milestone

### Data Cleaning and Preparation

1. **Handle Missing Values**:

   - Drop the 51 rows with missing values across multiple important columns
   - Implement conditional training for rows with missing AT labels
   - Ensure the model uses paraphrasing only when available

2. **Balance Dataset**:

   - Implement stratified sampling for training and evaluation
   - Create balanced mini-batches during training to address class imbalance

3. **Text Normalization**:
   - Apply language-specific preprocessing for both English and Portuguese
   - Implement stemming/lemmatization appropriate for scientific terminology

### Feature Engineering

1. **Question-Passage Relevance Features**:

   - Create features measuring lexical overlap between questions and passages
   - Implement TF-IDF and BM25 similarity scores as baseline features

2. **Multilingual Embeddings**:

   - Generate contextual embeddings using multilingual models
   - Create separate embeddings for questions, passages, and answer options

3. **Domain-Specific Features**:
   - Extract named entities related to oceanography and climate science
   - Create features capturing scientific terminology and domain-specific concepts

### Evaluation Framework

1. **Cross-lingual Evaluation**:

   - Develop separate evaluation metrics for English and Portuguese
   - Implement cross-lingual transfer evaluation to measure knowledge transfer between languages

2. **MCQ Validation Metrics**:

   - Create metrics specifically for evaluating the model's ability to identify questions that require the passage
   - Implement evaluation with and without passage access to measure information gain

3. **Balanced Performance Reporting**:
   - Report performance across different corpus sources
   - Ensure evaluation metrics account for class imbalance (F1-score, precision, recall)



By the next milestone, we will have implemented the preprocessing steps outlined above to create a clean, balanced dataset ready for model training. Our approach will specifically address the challenges of cross-lingual question validation, with a focus on developing robust evaluation metrics that can accurately assess whether a question requires reading the passage to be answered correctly.

This analysis sets the stage for fine-tuning multilingual transformer models that can effectively validate multiple-choice questions across both languages, ultimately contributing to more efficient and accurate assessment tools for reading comprehension in bilingual educational contexts.
