## Automating Question Validation for Reading Comprehension in Multilingual Settings

### Background and Motivation

Multiple-choice questions (MCQs) are a primary method for evaluating reading comprehension in language learning. A key criterion for a high-quality MCQ is that answering the question correctly should require reading the referenced passage rather than relying solely on external knowledge. One way to assess this is by comparing the accuracy of responses when individuals answer questions with and without access to the passage.

While language models can be used to automate this validation process in English, performing the same task in other languages, such as Portuguese, is significantly more challenging due to data scarcity. Our project aims to address this issue by fine-tuning a language model to validate MCQs in both English and Portuguese.

### Problem

Validating multiple-choice questions for reading comprehension is a time-consuming and labor-intensive process. Our goal is to fine-tune a language model in Portuguese for question validation and compare its performance to a fine-tuned question-answering model in English. By evaluating the accuracy of responses with and without the passage, we aim to establish an automated approach for assessing MCQ quality.

### Scope and Methods

This project will focus on fine-tuning a multilingual version of BERT (such as mBERT or XLM-RoBERTa) for multiple-choice question answering in both English and Portuguese. The methodology includes:

- Preprocessing the Pira dataset to extract structured QA pairs.
- Fine-tuning a multilingual BERT-based model to answer MCQs with and without the passage.
- Evaluating model accuracy and response consistency across different conditions.
- Comparing results across languages to identify potential biases and performance gaps.
- Investigating techniques to enhance performance in the lower-resource Portuguese setting.

### Concerns & Limitations

1. Ethical Considerations: Ensuring fairness across different languages is crucial. Since large language models tend to reflect biases present in their training data, we will analyze whether the model exhibits performance discrepancies between English and Portuguese.

2. Data Quality: The Pira dataset is relatively small compared to large scale English-language datasets. Data scarcity in Portuguese may lead to lower model accuracy.

3. Computational Challenges: Fine-tuning large-scale transformer models requires substantial computational resources. We plan to use cloud-based solutions, such as Google Colab or Harvard's cluster, to train and evaluate our models efficiently.
