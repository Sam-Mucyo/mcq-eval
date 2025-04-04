1) Base Features of Data

The size of the train dataset is 1806 rows × 31 columns. The size of the test dataset is 227 rows × 22 columns. The size of the validation dataset is 225 rows × 22 columns.

The total size of our data in megabytes or gigabytes is ??

Source of data: https://github.com/C4AI/Pira/tree/main 

2) Dataset Description
The Pirá dataset is a bilingual (Portuguese-English) question-answering (QA) dataset focused on topics related to the ocean, the Brazilian coast, and climate change. The data has been pre-split into a train, test, and validation set on the GitHub repository. Pirá is notable for being the first bilingual QA dataset including Portuguese and one of the few QA datasets dedicated to scientific topics and environmental issues. It consists of two types of supporting texts:
Scientific paper abstracts (3,891 entries from Scopus)
UN report excerpts (189 passages from World Ocean Assessment I & II)
Each passage is associated with a small set of questions, formulated and answered by human volunteers in both Portuguese and English. The dataset includes:
Original QA pairs in both languages
Human paraphrases of questions and answers for data augmentation
Human assessments of QA quality
Labels for answerability (some questions are unanswerable by design)
A multiple-choice extension, where each question has five options (one correct)
Our project focuses specifically on the multiple-choice QA (MCQ) subset of Pirá 2.0, which supports evaluation on machine comprehension and answer selection tasks. This version includes automatically generated paraphrases, expanding the training data for robust model development.

3) Summarize the cleaning steps. I wrote these out in the ipynb, you can just copy them and do a little light formatting.


