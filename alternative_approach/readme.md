
# overview

In addition to the main approach, in parallel, we tried to build an end-to-end pipeline using [RASA](https://rasahq.github.io), a library used mostly for chatbot applications. With proper configuration and sufficient training, such a pipeline took in utterances in .txt format, and generated slots predictions in .json format, which was then converted into .csv for scoring. In terms of performance, this approach scored 40% in accuracy on the validation data. After fine-tuning, the accuracy increased to around 70%. 
## Steps
- Training 

    1.  take an utterance from the training set

    2. manually pre-process the raw string into a dict-like object per requirements of the RASA model

    3. repeat step 1-2 for all utterances in training set

    4. feed processed data into the pipeline

- Predicting 

    1. feed dev or test set in their raw .txt form to the pipeline

    2. get slots predictions in .json format

    3. pick slots of interest and convert them into .csv format

_Note: Please refer to the demo.ipynb in the same folder on how to reproduce the results._
# Pipeline configuration

A RASA pipeline is configured with a .yaml file. Our configuration is as below:
    
```yaml
language: "en"

pipeline:
  - name: SpacyNLP
    model: "en_core_web_sm"
  - name: SpacyTokenizer
  - name: RegexFeaturizer
  - name: LexicalSyntacticFeaturizer
  - name: CountVectorsFeaturizer
  - name: CountVectorsFeaturizer
    analyzer: "char_wb"
    min_ngram: 1
    max_ngram: 4
  - name: DIETClassifier
    epochs: 700
  - name: EntitySynonymMapper
  - name: ResponseSelector
```




After training a dataset of around 1.3K utterances, this pipline would obtain 69.976% accuracy on dev set, and 71.000% on public test set. 

(Results are in TestSetPredictions.csv in the same folder)

# Components

Our pipeline consists of several coponents. Here's the run-down:

| Name                   | Description                                                          |
|----------------------------|--------------------------------------------------------------------------------|
| SpacyNLP                   | classic spacy model                                                            |
| en_core_web_sm             | a pretrained model  widely-used in RASA                                        |
| SpacyTokenizer             | tokenze sentences, which also met lab requirement of using a token-level model |
| RegexFeaturizer            | regular expression library for filtering                                       |
| LexicalSyntacticFeaturizer | create lexical and syntactic features from an utterance                          |
| CountVectorsFeaturizer     | Fearurizer for tokens                                                          |
| CountVectorsFeaturizer     | Fearurizer for entities                                                        |
| char_wb                    | where params for this CountVectorsFeaturizer are stored                        |
| EntitySynonymMapper        | handle synonyms                                                                               |
| ResponseSelector           |RASA is built for chatbots. But in our task, we only care about the "response" part of the "chat" Thus this component.                                                                                |

For more information, please refer to RASA's official documention [here](https://rasa.com/docs/rasa/components/).
