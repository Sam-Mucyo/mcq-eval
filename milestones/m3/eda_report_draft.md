## Pirá 2.0 Dataset Analysis for Multiple-Choice Question Validation

Group \#

**Team Members**: Cathy Hou, James Alexandr Carr, Joanna Walters, Rosa Wu, and Sam Mucyo

### **0\. Link to Code**

[Project.ipynb](https://colab.research.google.com/drive/1-olBfWPoOowh6HLNoFtQ5Tx2yshtrGrD?usp=sharing#scrollTo=2g2DSOgd3E1a) – Feel free to make a copy to edit

### **1\. Dataset Description**

Source of data: [https://github.com/C4AI/Pira/tree/main](https://github.com/C4AI/Pira/tree/main) 

The Pirá dataset is a bilingual (Portuguese-English) question-answering (QA) dataset focused on topics related to the ocean, the Brazilian coast, and climate change. The data has been pre-split into a train, test, and validation set on the GitHub repository. Pirá is notable for being the first bilingual QA dataset including Portuguese and one of the few QA datasets dedicated to scientific topics and environmental issues. It consists of two types of supporting texts:

1. Scientific paper abstracts (3,891 entries from Scopus)  
2. UN report excerpts (189 passages from World Ocean Assessment I & II)

Each passage is associated with a small set of questions, formulated and answered by human volunteers in both Portuguese and English. The dataset includes:

* Original QA pairs in both languages  
* Human paraphrases of questions and answers for data augmentation  
* Human assessments of QA quality  
* Labels for answerability (some questions are unanswerable by design)  
* A multiple-choice extension, where each question has five options (one correct)

The size of the train dataset is 1806 rows × 31 columns. The size of the test dataset is 227 rows × 22 columns. The size of the validation dataset is 225 rows × 22 columns. The total size of all data is about 33MB, which we think might be small for training a more generalizable model. 

Our project focuses specifically on the multiple-choice QA (MCQ) subset of Pirá 2.0, which supports evaluation on machine comprehension and answer selection tasks. This version includes automatically generated paraphrases, expanding the training data for robust model development.

Each row of the dataset contains the corpus of the text, and depending on the corpus,  either 1\) the eid of the scopus for texts from corpus 1 or 2\) the text excerpt of the UN report for corpus 2\. The question and answer is given in English, Portuguese, and also paraphrased, and there are also fields that attempt to detail the quality of the QA-pair, such as ***question\_meaningful, answer\_in\_text, question\_generic, and answer\_difficulty***. 

### **2\. Meaningful Insights / Summary of Findings**

We are interested in 2 columns: answerability and answer\_in\_text. Per Pira: “Answer Triggering is the problem of finding which questions should be answered and which ones should not; the “answerability” label are created from the manual assessments for question meaningfulness.” Since each row cites a text (either from corpus 1 or 2), the answer\_in\_text column refers to whether that question and answer pair could be found directly in the text. 

The dataset displays a significant class imbalance: among QA pairs with valid answerability labels, 79% are marked as answerable, while only 8% are labeled unanswerable. The remaining 13% have missing `at_labels` and were excluded from this analysis. Similarly, there is also a class imbalance in ‘answer\_in\_text’. 95% of the question-answer pairs were found in text, 3% were NOT found in text, and 3% were missing ‘answer\_in\_text’. 

This imbalance may affect tasks such as answer triggering, which rely on distinguishing answerable from unanswerable inputs. Models trained on this data may become biased toward predicting "answerable" by default unless rebalanced through sampling (SMOTE, etc.) or weighted loss functions. We note that “answerable” is manually labeled, but “answer\_in\_text” may have been done through a model (Pira is ambiguous on this), which explains the additional missigness in “answerable”. ***Due to class imbalance, a purely classification-based task would require aggressive resampling techniques. Rather than have the model evaluate the quality of the Q-A-pair via classification (whether it is answerable/ found in the text or not), we have revised our project to have the model find the areas of text that are most relevant to the question (an attribution task).*** 

We also wanted to understand more about the questions and answers themselves. Most of the questions and answers were from 10-15 words long. We note that this means the training data doesn’t support particularly long, complex questions that may include definitions and complex reasoning. Similarly, there are few answers that require essay-style responses. We note that there were 7 classes of questions: “who, what, when, where, how, why, None”. 99% of the questions were “what” questions, which we believe means the questions tend to be straightforward and simple to reason. We did notice that the “answerability” label varies across these question types (only \~86% of “Who” and “Where” questions were answered), but also recognize that due to significant class imbalance, these results are not as meaningful. ‘answer\_in\_text’ was fairly consistent, with \~95% of questions being found in the text, regardless of the question type. 

We were also curious about the manual process of labeling the data. Each Q-A pair has 2 annotators. We were interested in seeing how differently the 2 annotators answered questions and created questions. We did a character-level similarity score (language agnostic) between the 2 annotators in questions vs answers and defined the similarity threshold to be 70%. We found that the annotators answered questions with at least 70% similarity for \~65% of the questions.

In terms of supporting text sources, about \_\_\_ of passages came from Scopus abstracts and around \_\_\_ are drawn from WOA reports. 

\_\_\_\_\_\_\_\_

(For our use case of evaluating MCQs, the unanswered questions are not applicable and since there’s a relatively small percentage of answerable ones, we think we don’t have to handle this issue in a more specialized way as we would for a classification task where both classes are involved. )? 

The low proportion of unanswerable questions (\~8%) suggests that handling unanswerability is not critical for our task, which focuses exclusively on multiple-choice questions with known correct answers.

**Link to last milestone:** [Milestone 1](https://docs.google.com/document/d/1JIMnzKaSZT439J3ZooWeSCGTbb-c302pKKYLsX7nIhc/edit?tab=t.0)

