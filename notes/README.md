# Notes

Here is your wiki where you can document all stages of the project and save intermediate results and analyses.


## Comments/suggestion by Mehdi (2019-12-31)

We can consider simple methods to compare informativeness of movie titles:

- First baseline: predict the genre from movie title. <br/>
  - Method: use a pretraind language model such as BERT to extract distributional features (one of the easy to use implimentations i.e. [hugging-face](https://github.com/huggingface/transformers) or [bert-as-service](https://github.com/hanxiao/bert-as-service))

- Second baseline: predict the genre from movie poster. <br/>
  - Method: use a pretrained object recognitionmodel such as Resnet to extract visual features. 

- Model in question: predict the genre from movie title and its poster.
  - Method 1: Combine two probabilities from two models in baselines (without training) and re-arrange the scores to improve their F1 score.
(Increase the recall of the most precise model)<br/>
e.g. Rescore probabilities with multiplication:
P(category | poster, title) ~ (S(category | poster) * S(category | title)) / \sum{c in categories}{S(c | poster) * S(c | title)} 
  - Method 2: Concatenate two features from two baselines and train a new model.

#### Reporting:

A possible report format for the results:

| Models   | P    | R    | F1   |
|----------|------|------|------|
| BERT     | .ddd | .ddd | .ddd |
| Resnet   | .ddd | .ddd | .ddd |
| Method 1 | .ddd | .ddd | .ddd |
| Mehtod 2 | .ddd | .ddd | .ddd |

You can also report this table for each genre category or just report the overall performance.

If you need any help on any part, please contact me.

#### Possible extension:

One general critique to the project is its slim role of language/interaction in the research question. You may find time to consider this suggestion. 
Here is an additional project idea which expands the role of language models:
As *an exploration in creativity in language generation*:
given a poster picture (and optionally the genre), how can we generate a new movie title?

Method: train an image caption model but on movie titles. input visual features from resenet, regenerate the title.

Instead of focusing on improving the model, discuss the problems with current image captioning objective function over tokens (-log P(y_i)).
Discuss, ideas about what would be required in the model to have creative movie title generation system?

