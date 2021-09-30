# BERT Q&A models pipeline

## Pipeline Outline

- Data pre-processing: Divided the answers to different labels for different models training in 3 steps. Please click the following links to access the code for each step.

  [Step_1 preprocessing](Step_1/Lab4step1.ipynb) || [Step_2 preprocessing](Step_2/Lab4step2.ipynb) || [Step_3 preprocessing](Step_3/Lab4step3_preprocess.ipynb)  
- [Step 1](Step_1/Lab4step1.ipynb): Classification by BERT to predict intent.
- [Step 2](Step_2/Lab4step2.ipynb): Classification by BERT to predict slots.
- Step 3: Extract the specific inquiry by BERT classification ([categorical aspects](Step_3/Lab4step3_cat.ipynb)) and BERT Q&A ([non-categorical aspects](master/Step_3/Lab4step3_noncat.ipynb)).
- [Final](https://github.ubc.ca/cs/COLX_563_lab4_team_cdat/blob/master/Step_1/output_process.ipynb): Combined all the output to one `.csv`/`.tsv` file.

## The Pipeline
Our slot-filling pipeline consisted of 3-steps: (1) identify the intent of the utterance (e.g. `find_restaurant` or `find_hotel`), (2) identify the slots (e.g. `name`, `food`, `pricerange`, etc.), (3) find the answer for each slot from the utterance.

   For the first step, we identified two intents from the training data: `find_restaurant` and `find_hotel`. This allowed us to make a binary classification model with BERT that gave 99.5% accuracy on the validation data. 
   
   Then, using the predictions from step 1, we used a multi-task BERT model to predict whether an utterance contains a certain slot. We identified 8 slots from the training data: `name`, `food`, `internet`, `area`, `parking`, `pricerange`, `stars`, and `type`. Thus, for this step our BERT model had 8 tasks, one for each slot, and gave 92% accuracy on the validation data. 
   
   Given these inital steps, we now had predictions for both the intent and slots of each utterance. These predictions are then fed into step 3. Initially, we set up this step to only have one model (a BERT for Q&A model), but we found that answers to some slots are not found in the utterance. This led us to switch our approach to have two separate models: a BERT for Q&A model that predicted the answers for non-categorical slots (i.e. `name` and `food`) with 95% & 96% accuracies on the validation data, and a multi-task BERT model to predict the answers for the categorical slots with 91% accuracy with the exception of `stars` with which a rule-based classifier was used since the BERT model could not properly identify the numbers.
   
   In addition to the main approach, in parallel, we tried to build an end-to-end pipeline using RASA, a library used mostly for chatbot applications. With proper configuration and sufficient training, such a pipeline took in utterances in `.txt` format, and generated slots predictions in `.json` format, which was then converted into `.csv` for scoring. In terms of performance, this approach scored 40% in accuracy on the validtion data. After fine-tuning, the accuracy increased to around 70%. Though the RASA approach looked fresh and promising, we decided to take the other approach which did better on both validation and test data.
   
## Contributors
- Yundong Yao 
- Linxuan Yang
- Jan Urquico
- Alex Chen
