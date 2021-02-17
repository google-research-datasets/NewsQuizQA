# NewsQuizQA

This repository contains the raw dataset used for quiz-style question-answer
generation, from "Quiz-Style Question Generation for News Stories" to appear in
The Web Conference 2021 (WWW'21).

The dataset consists of news article URLs and their associated human-written
quiz-style question-answer pairs. Each news article has exactly four reference
question-answer pairs. A quiz-style question is one that must be able
to be answered without assuming that the reader has access to any other source
material. For example, certain reading comprehension questions containing
direct references such as "according to the passage" or dangling mentions
such as "what did she say?" would not be considered quiz-style.

For this initial release, 11.6K question-answer pairs are included out of the
20K in the complete dataset. Additional question-answer pairs will be released
in subsequent versions of the dataset.

## Dataset Construction
A proprietary clustering algorithm iteratively loads articles published in a
recent time window and groups them based on content similarity. To construct the
dataset, on a weekly basis the top 50 clusters are taken and for each cluster a
representative article close to the centroid is selected. For each article a
summary is generated using a [PEGASUS](https://arxiv.org/abs/1912.08777) model
fine-tuned on the [CNN/Dailymail](https://github.com/abisee/cnn-dailymail)
summarization dataset. Summaries are used for more efficient data collection
over using entire news articles, which can be long and time consuming to read
for human writers. Each summary is then given to five human writers who are
asked to read the passage and write a question and answer pair that abides the
following rules:

1. The question is answerable based on information from the passage only.
2. The question can stand alone without the passage. I.e., it provides enough background to understand what is being asked, without the passage.
3. The question has a short answer. I.e., not “how” or “why” type questions that can only be answered by a full sentence.
4. The question should be about one of the most interesting or important aspects of the passage.
5. The question ends with a question mark.
6. The question is not a "Yes" / "No" question.
7. The answer is a word or short phrase with no ending punctuation.
8. The answer conveys only the answer, does not contain unnecessary parts, and does not restate parts of the question.

From this process, we collect exactly 26,000 question-answer pairs from 5,200
news article summaries. We then apply a post processing step to mitigate low
quality questions. Namely, the human-written questions are processed via a
state-of-the-art [grammar error correction
model](https://www.aclweb.org/anthology/N19-1333/) in order to fix minor
grammar, spelling, punctuation, and capitalization errors. Then, the shortest
question written for each summary is removed, as well as any question containing
specific blocklisted phrases such as "I" or "According to the passage."
Aftwards, only the summaries with exactly four human written question-answer
pairs are kept for the final dataset. An 80-10-10 split is then randomly sampled
from the 5k summaries to produce training, validation, and test sets, respectively.

# Citation
If you use or discuss this dataset in your work, please cite our paper:

```
@InProceedings{newsquiz2021,
  title = {{Quiz-Style Question Generation for News Stories}},
  author = {Adam D. Lelkes and Vinh Q. Tran and Cong Yu},
  booktitle = {Proc. of the the Web Conf. 2021},
  year = {2021}
}
```
