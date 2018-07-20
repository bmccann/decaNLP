# Leaderboard

# Task Overview
The Natural Language Decathlon is a multitask challenge that spans ten tasks:

### Question Answering
Question answering (QA) models receive a question and a context that contains information necessary to output the desired answer. decaNLP uses the Stanford Question Answering Datasets (SQuAD 1.0) as the dataset for this task.
### Machine Translation 
Machine translation models receive an input document in a source language that must be translated into a target language. decaNLP uses the 2016 English to German training data prepared for the International Workshop on Spoken Language Translation (IWSLT), and we use the 2013 and 2014 test sets for our own validation and test sets, respectively. 

### Summarization
Summarization models take in a document and output a summary of that document. decaNLP uses the non-anonymized version of CNN/DailyMail (CNN/DM) corpus. We include the non-anonymized version of this dataset in decaNLP.

### Natural Language Inference
Natural Language Inference (NLI) models receive two input sentences: a premise and a hypothesis. Models must then output whether the premise entails contradicts or is neutral with respect to the hypothesis. decaNLP uses the Multi-Genre Natural Language Inference Corpus (MNLI).

### Sentiment Analysis
Sentiment analysis models are trained to classify the sentiment expressed by input text. decaNLP includes the unparsed, binary version of the Stanford Sentiment Treebank (SST), which consists of movie reviews with corresponding sentiment (positive, neutral, negative).

### Semantic Role Labeling
Semantic role labeling (SRL) models are given a sentence and predicate (typically a verb) and must determine ‘who did what to whom,’ ‘when,’ and ‘where’. decaNLP includes the Wikipedia domain of QA-SRL 1.0.

### Relation Extraction
Relation extraction systems take in a text document and the kind of relation that is to be extracted from that text. decaNLP includes QA-ZRE, a dataset that maps relations to a set of questions so that relation extraction can be treated as question answering. At validation and test time, the relations are different than those seen in training, so that this is a zero-shot relation extraction task.

### Goal-Oriented Dialogue
Dialogue state tracking is a key component of goal-oriented dialogue systems. Based on user utterances and system actions, dialogue state trackers keep track of which predefined goals the user has for the dialogue system and which kinds of requests the user makes as the system and user interact turn-by-turn. decaNLP includes the English Wizard of Oz (WOZ) restaurant reservation task, which comes with a predefined ontology of foods, dates, times, addresses, and other information that would help an agent make a reservation for a customer.

### Semantic Parsing. 
Semantic parsing requires models to translate unstructured information into structured formats so that users can interact with structured information (e.g. a database) in natural language . decaNLP includes the WikiSQL dataset, which maps natural language questions into structured SQL queries.

### Commonsense Pronoun Resolution. 
As an example of commonsense pronoun resolution, models are required to answer questions like "Joan made sure to thank Susan for the help she had [given/received]. Who had [given/received] help? Susan or Joan?". We started with examples taken from the Winograd Schema Challenge and modified them (resulting in the Modified Winograd Schema Challenge, MWSC) to ensure that answers were a single word from the context and scores are neither inflated nor deflated by oddities in phrasing or inconsistencies between context, question, and answer.


# Citation

If you use this in your work, please cite [*The Natural Language Decathlon: Multitask Learning as Question Answering*](https://arxiv.org/abs/1806.08730).

```
@article{McCann2018decaNLP,
  title={The Natural Language Decathlon: Multitask Learning as Question Answering},
  author={Bryan McCann and Nitish Shirish Keskar and Caiming Xiong and Richard Socher},
  journal={arXiv preprint arXiv:1806.08730},
  year={2018}
}
```

# Contact

Contact: [bmccann@salesforce.com](mailto:bmccann@salesforce.com) and [nkeskar@salesforce.com](mailto:nkeskar@salesforce.com)
