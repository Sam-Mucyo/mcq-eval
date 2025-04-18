1. Base Features of Data

The size of the train dataset is 1806 rows × 31 columns. The size of the test dataset is 227 rows × 22 columns. The size of the validation dataset is 225 rows × 22 columns.

The total size of our data in megabytes or gigabytes is ??

Source of data: https://github.com/C4AI/Pira/tree/main

2. Dataset Description
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
3. Potential Data Issues and How we Addressed Them
   The bulk of potential data issues were to do with missigness. Because Pira is a government funded, comprehensive Portuguese database in climate Question and Answer data, we found that there were few data issues other than the ones listed here.
   ISSUE: Renaming Columns: The first two columns are just duplicates of the index. We drop the first and rename the second "index", setting it as the index.
   ISSUE: Handling Missingness (NaNs): We begin by visualizing the number of NaNs using the Pandas isna().sum() functionality. Then we took the below steps:
   Paraphrase columns: The paraphrase columns were created by the dataset authors to (1) achieve data augmentation and (2) to provide a more general correct answer for the fine-tuned model that will help us avoid false negatives. We also noticed that all the rows that are missing en paraphrasing are also missing the pt paraphrasing
   Solution: we cannot drop the 222 rows because they represent over 10\% of the data. Instead, we will just ensure that our model uses paraphrasing only when they are available.
   Eid_article_scopus column: This column represents the ID in the Scopus database of the specific article that is being tested.
   Solution: The ID is not particularly important to us because we have the text, so we can drop this column.
   Same Missing Rows Across Columns: the columns in discussion are ‘question_generic', 'answer_in_text', 'answer_difficulty', 'question_meaningful', 'answer_equivalent', 'question_type'. All these columns are quite important to our model because they inform the degree of reasoning that is required to solve the question (i.e. question generic, question difficulty, and question meaningful). They also inform if the answer is placed in the text, which is crucial for our model. As such, we cannot drop these columns. We also cannot impute the row values because we do not have access to the data generating process for each of the variables or the classification criteria.
   Solution: We choose to drop the 51 rows (approx 2.5\% of our train data) because it does not constitute a large portion of our dataset and there is no obvious trend in the missingness. All of the 50 columns that had a validate NaN also had a validation NaN, which was covered by dropping the 51 rows.
   At_labels Column: We again cannot drop this because it is important to our project. Similarly, 178 rows is approximately 10\% of our data, so dropping this would be quite problematic.
   Solution: we will conduct conditional fine-tuning: the model will only use this column if it has a non-NaN value.
