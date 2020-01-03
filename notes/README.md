# Notes

Here is your wiki where you can document all stages of the project and save intermediate results and analyses.


## Comments/suggestion by Mehdi (2019-12-31)

We can consider simple methods to compare informativeness of movie titles:

- First baseline: predict the genre from movie title. <br/>
  - Method: use a pretraind language model such as BERT to extract distributional features (one of the easy to use implimentations i.e. [hugging-face](https://github.com/huggingface/transformers) or [bert-as-service](https://github.com/hanxiao/bert-as-service))

- Second baseline: predict the genre from movie poster. <br/>
  - Method: use a pretrained object recognition model such as Resnet to extract visual features (I guess in [`torchvision`](https://pytorch.org/docs/stable/torchvision/models.html), or [this implimentation](https://pytorch.org/hub/pytorch_vision_resnet/)). 

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

## Discussion over email (2020-01-03)


Hi! I read [the paper](https://www.researchgate.net/publication/282196711_Automatic_Movie_Posters_Classification_into_Genres) which uses ResNet34 and I was thinking about doing something similar. I didn't about using  two different models. In Method 1, what do you mean by not training? Should I just use create testing data for the models and multiply the probabilities?
 
As for the extension, wouldnt generating a new movie title require a totally different approach using RNNs?

/ Elin

---- 

Hello,

Well, you can train two models:

1. title-to-genre: P(genre | title) 
2. poster-to-genre: P(genre | poster)

Maybe multi-label classification is also a challenge. There are many ways to overcome the problem. One solution is to train a binary classifier for each genre according to the paper. Another way to do it is to train them in one neural network but with sigmoid but with balancing the data for each class (i.e. with random sampling). 

For Method 1 on combined model, I was suggesting to take the previous measures in some way and re-score them by combining the two measures. 
Instead of probability P(genre | title), lets call it a score of genre given the title: S1=S(genre | title), similarly S2=S(genre | poster).
Then you can test with average of two scores, the average might be arithmetic or geometric average or even max function can be used:
sum(S1, S2), prod(S1, S2), max(S1, S2)

You just need to normalise it with alternative choices to sum up to 1. so it becomes a valid probability estimation. 
- P(g | poster, title) + P(not g | poster, title) = 1

Sometimes these methods produces really good results also they reveal how much each precision and recall of each class effected the combined method. 

Instead of doing this, for final multi-label classification, you can also directly take the union of two models. 
- Model 1: Comedy, Action
- Model 2: Family, Drama
- Model 3: Model1⋃Model2 = Comedy, Action, Family, Drama

This method specifically is useful if precision is high but recall is low in two models. But two models have high recall but low precision you can  take their intersection:
- Model 1: Comedy, Action, Family
- Model 2: Comedy, Family, Drama
- Model 3: Model1⋂Model2 = Comedy, Family

Regarding the extension, Yes, it is a different task. I was suggesting that you can think about more linguistic discussions. 
Learning to produce a title for a movie depends on many things, poster and genre might have information about it but it also depends on the story and creativity to express something that is not expressive in other modality.


I will put discussions here in the notes as well.

Best,
Mehdi

