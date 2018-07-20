# Overview
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

## Leaderboard


## Getting Started

First, make sure you have [docker](https://www.docker.com/get-docker) and [nvidia-docker](https://github.com/NVIDIA/nvidia-docker) installed. Then build the docker image:

```bash
cd dockerfiles && docker build -t decanlp . && cd -
```

You will also need to make a `data` directory and move the examples for the Winograd Schemas into it:
```bash
mkdir .data/schema
mv local_data/schema.txt .data/schema/
```

You can run a command inside the docker image using 
```bash
nvidia-docker run -it --rm -v `pwd`:/decaNLP/ -u $(id -u):$(id -g) decanlp bash -c "COMMAND"
```

## Training

For example, to train a Multitask Question Answering Network (MQAN) on the Stanford Question Answering Dataset (SQuAD):
```bash
nvidia-docker run -it --rm -v `pwd`:/decaNLP/ -u $(id -u):$(id -g) decanlp bash -c "python /decaNLP/train.py --train_tasks squad --gpu DEVICE_ID"
```

To multitask with the fully joint, round-robin training described in the paper, you can add multiple tasks:
```bash
nvidia-docker run -it --rm -v `pwd`:/decaNLP/ -u $(id -u):$(id -g) decanlp bash -c "python /decaNLP/train.py --train_tasks squad iwslt.en.de --train_iterations 1 --gpu DEVICE_ID"
```

To train on the entire Natural Language Decathlon:
```bash
nvidia-docker run -it --rm -v `pwd`:/decaNLP/ -u $(id -u):$(id -g) decanlp bash -c "python /decaNLP/train.py --train_tasks squad iwslt.en.de cnn_dailymail multinli.in.out sst srl zre woz.en wikisql schema --train_iterations 1 --gpu DEVICE_ID"
```

You can find a list of commands in `experiments.sh` that correspond to each trained model that we used to report validation results comparing models and training strategies in the paper.

### Tensorboard

If you would like to make use of tensorboard, run (typically in a `tmux` pane or equivalent):

```bash
docker run -it --rm -p 0.0.0.0:6006:6006 -v `pwd`:/decaNLP/ decanlp bash -c "tensorboard --logdir /decaNLP/results"
```

If you are running the server on a remote machine, you can run the following on your local machine to forward to http://localhost:6006/:

```bash
ssh -4 -N -f -L 6006:127.0.0.1:6006 YOUR_REMOTE_IP
```

If you are having trouble with the specified port on either machine, run `lsof -if:6006` and kill the process if it is unnecessary. Otherwise, try changing the port numbers in the commands above. The first port number is the port the local machine tries to bind to, and and the second port is the one exposed by the remote machine (or docker container).

### Caveats During Training

- On a single NVIDIA Volta GPU, the code should take about 3 days to complete 500k iterations. These should be sufficient to approximately reproduce the experiments in the paper. 
- The model can be resumed using stored checkpoints using `--load <PATH_TO_CHECKPOINT>` and `--resume`. By default, models are stored every `--save_every` iterations in the `results/` folder tree. 
- During training, validation can be slow! Especially when computing ROUGE scores. Use the `--val_every` flag to change the frequency of validation. 
- If you run out of memory, reduce `--train_batch_tokens` and `--val_batch_size`.
- The first time you run, the code will download and cache all considered datasets. Please be advised that this might take a while, especially for some of the larger datasets. 



## Evaluation

You can evaluate a model for a specific task with `EVALUATION_TYPE` as `validation` or `test`:

```bash
nvidia-docker run -it --rm -v `pwd`:/decaNLP/ -u $(id -u):$(id -g) decanlp bash -c "python /decaNLP/predict.py --evaluate EVALUATION_TYPE --path PATH_TO_CHECKPOINT_DIRECTORY --gpu DEVICE_ID --tasks squad"
```

or evaluate on the entire decathlon by removing any task specification:
```bash
nvidia-docker run -it --rm -v `pwd`:/decaNLP/ -u $(id -u):$(id -g) decanlp bash -c "python /decaNLP/predict.py --evaluate EVALUATION_TYPE --path PATH_TO_CHECKPOINT_DIRECTORY --gpu DEVICE_ID"
```

For test performance, please use the original [SQuAD](https://rajpurkar.github.io/SQuAD-explorer/), [MultiNLI](https://www.nyu.edu/projects/bowman/multinli/), and [WikiSQL](https://github.com/salesforce/WikiSQL) evaluation systems.

## Citation

If you use this in your work, please cite [*The Natural Language Decathlon: Multitask Learning as Question Answering*](https://arxiv.org/abs/1806.08730).

```
@article{McCann2018decaNLP,
  title={The Natural Language Decathlon: Multitask Learning as Question Answering},
  author={Bryan McCann and Nitish Shirish Keskar and Caiming Xiong and Richard Socher},
  journal={arXiv preprint arXiv:1806.08730},
  year={2018}
}
```

## Contact

Contact: [bmccann@salesforce.com](mailto:bmccann@salesforce.com) and [nkeskar@salesforce.com](mailto:nkeskar@salesforce.com)
