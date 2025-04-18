## Pirá 2.0 Dataset Analysis for Multiple-Choice Question Validation

Group #48

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
## Model Implementation Plan

There are five steps to our mode’s implementation. 

First, to balance computational capacities with cross-lingual comprehension, we begin by selecting a compact multilingual transformer from HuggingFace, like *DistilBERT-multinigula-cased* or *XLM-Robert-small*. Containing 80 to 140 million parameters, these models can grasp sentence-level semantics while still being small enough for us to fine-tune them on a single GPU.  

Second, we will reframe the multiple-choice problem as a six-way classification – the five examiner-provided answers (e.g. A to E) and then a “*no answer*” class. By casting answerability and answer selection as one joint softmax over six labels, the model can determine in a single forward pass if the passage contains the answer and, if so, identify the correct option. We would continue by concatenating the text, question, and candidate answer into paired sequences (e.g. *passage* – *question* – *candidate answer*) and then truncate or pad all the sequences into a uniform length to meet the transformer’s maximum input size. 

Third, we would employ the *MultipleChoice* variant of the selected transformer, which shares on an encoder for all six inputs and then applies a lightweight linear head to the pooled representations, yielding six logit-form outputs. We would then fine-tune the model, considering the following:

1) Optimizer choice – potentially AdamW with a tentative weight decay of 0.01. Its decoupled L2 regularization would maintain the integrity of the pretrained weights but the adaptive momentum should yield stable fine-tuning for our small and specialized corpus.

2) Initial learning rate – the original BERT paper discussed in class was fine-tuned with learning rates of 210-5, 310-5, and 510-5, with 210-5 giving the best validation performance. As the XLM-R and DistilBERT also used  210-5, leading us to believe this could be a good learning rate for the pretrained layers. We would then apply a learning rate of potentially  510-5 for the task-specific head, allowing it to learn quicker. The dual LR structure should ensure that the model can latch onto new answer-selection patterns without destabilizing its cross-language embeddings. However, this will ultimately be subject to a lot of trial and error.

3) Batch size – we are considering an effective size of 32 examples. Given that we have a long-input task, we know that our computer memory will force small per-GPU batch sizes, like 8\. But this can lead to very noisy gradient estimations, slowing convergence and degrading our performance, especially since we expect the answerable versus unanswerable labels to be unbalanced. We find compromising in employing gradient accumulation, like two forward/backward passes of size 16 to simulate a batch of 32\.

4) Gradient clipping – since the passages vary in length and complexity, a single input with many rate tokens or substantial context can cause a gradient spike that destabilizes the optimizer. We want to address this through gradient clipping at an L2 norm of 1, capping the outliers and preventing runaway updates that erase prior learning. But again, we will experiment with this.

5) Epochs – our intuition is that a high-epoch regime might cause over-fitting because we have a relatively small dataset that has the relatively rare “no-answer” class. We will start with a mid-range number of epochs (e.g. 15 \- 20\) with a patience of 3, slowly reducing down towards a more reasonable level. 

The fourth step is to evaluate the model. The most intuitive measure is the fraction of examples where the predicted label matches the true label. If we wish to isolate for if a question is answerable, we can collapse classes 0 to 4 (i.e. the 5 multiple choice answers) against class 5 and report binary F1 and accuracy. Conversely, if we wish to measure how good the model is at answering answerable questions, we will just compute the MCQ accuracy on the subset of answerable questions. We could further measure this retrieval task using Mean Reciprocal Rank, which captures how highly the system ranks the correct answer among all other candidates. This effectively simulates how confident our model is in its answer.

Finally, in our testing phase, each new question-passage pair is tokenized into six candidate sequences, passed into our fine-tuned model, and the highest-scoring index is extracted. The model will either answer the question or label it as unanswerable, which we will compare to the true labels. 

