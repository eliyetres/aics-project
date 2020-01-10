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

---- 

Hi, this has given me a lot to think about.
 
For generating move titles, how would that work? Would I train two models separately?
Would I train a model on just the movie titles first? Using a character- or word based RNN? If I were to use a pre-trained model for this, what kind would it be?
 
/ Elin

---- 

This is completely optional. If you are interested in this idea here is more comments/suggestions:

In short, pre-trained subword embeddings would be the best choice for a short project. 
i.e. subword embeddings from here: https://github.com/bheinzerling/bpemb

subwords paper: https://www.aclweb.org/anthology/P16-1162/
pre-trained models paper: https://www.aclweb.org/anthology/L18-1473/

There are both theoretical reasons for this choice and technical challenges we may avoid with this:
Theoretically:
- You need to have a pre-trained model to be able to generalise from movie names using the pre-trained knowledge.
- Names and titles are usually rare, you probably don’t see it in word models.
Technically:
- For language generation you need a decoder model, but most modern pre-trained language models are encoders (BERT, and ELMo are encoders). Using pre-trained model leads to pre-trained embeddings. Other pre-trained generative models such as transformer-decoder might be hard to start with. (this blog post https://mayhewsw.github.io/2019/01/16/can-bert-generate-text/)

You would need a one-layer LSTM with a pre-trained sub-word embedding, + feature fusion layer (concatenate each subword embedding with poster visual vector or its genre vector).
Then, train the LSTM on movie titles, when you have visual vectors.
When you train LSTM for generative model you want to have a setup similar to this:
"the movie title"
- input: `<s> the \space\ mo vie \space\ ti tle` 
- output: `the \space\ mo vie \space\ ti tle </s>`

You can even pre-train/warm up the LSTM with a larger movie titles corpus that are not in 6000 poster dataset and put zero vectors for the poster/genre.

My prediction is that at the end the model is going to generate quite random and funny movie titles. 

Best,
Mehdi

----

Hi, I don't know, I feel more uncertain now than I was before! The idea with generating new titles seem cool but complicated. I have read the suggestions you sent but I still don't understand how I would implement it.
You said that predicting the genres from movie posters and titles had a too slim role of language in the research question. How is this better? Is the difference that I would try to generate titles instead of predicting genres?
 
/ Elin


## Discussion over email (2020-01-07)

I looked at the subword embeddings. It has it’s own tokenization function, but the module is not installed on the server.  I’m not sure if it’s something I  could solve myself. Is it possible to use a GRU model instead of an LSTM? I find the pytorch implementation of LSTMs a bit hard
 
/ Elin

----

Hello,

You must be able to install python packages with pip3 install --U <package name>
GRU is fine. But please use packages as much as possible and try to avoid implementing these NN layers on your own.

Best,

Mehdi

----

Thanks! I tried this a couple of days ago but I still got errors when I tried to import the module. But it works now... Maybe it needed a restart. ☹
 
/ Elin

----

Hi again! I remember you talked about concatenating text and visual vectors in class. Did you have an example on how to do this?
/ Elin

----

I have Keras example. In tutorials.

A simple solution is to use Reset to produce a vector for each image in your dataset and save them. Then, use those vectors as representation of image in your design. 
In your RNN, concatenate each token embedding at input with the image vector.

Best,
Mehdi

----

I managed to get a 512 dimension tensor with ResNet18 following this example: https://becominghuman.ai/extract-a-feature-vector-for-any-image-with-pytorch-9717561d1d4c
Is this what you meant? And then concatenate the image with each token for every title?’
 
/ Elin

----

It right. 

Mehdi

----

Is a ‘feature fusion layer’ something that exists?
 
I found this example:
https://discuss.pytorch.org/t/concatenate-layer-output-with-additional-input-data/20462/2
where the data is concatenated with the image in the forward loop. Is this what you meant?
 
/ Elin

----

Yes, concatenation is the most straightforward fusion strategy, in my opinion.
Concatenation as fusion of (1) convoluted representation of visual features and (2) token embedding at the input to RNN language model.

In addition to concatenation, one can add dense layers to control the dimensions as well, so the fusion becomes a trainable module.

Instead of fusion, it could be called multimodal pooling as well.


Best,
Mehdi

----

Thanks. I only found one stackoverflow question for pytorch (without answers) when searching for feature fusion layers 😊 But implementing it like in the example, in the forward loop, would mean one that I would need to feed one tokenized title at a time? Is this a good idea or should I do the concatenation before I feed them into the model?
 
/ Elin

----

There are two solutions for RNNs in pytorch, if I remember correctly:

1. Using nn.GRUCell or nn.LSTMCell. Then write a loop, prepare inputs inside loop, including the concatenations.

2. Using nn.GRU or nn.LSTM. Then you need to expand dimnsion of visual vector for sequence length. Then, repeat the tensor along the new dimension by maximum length the sequence. When you got matching dimensions for batch and sequence length for embeddings and visual features, you can concatenate them along the feature dimension.

The only practical difference between LSTM and GRU in pytorch is the initialisation of memory state in LSTM.
There are very brief examples of LSTM and GRU here:
https://pytorch.org/docs/master/nn.html#torch.nn.LSTM

Instead of random inputs in those examples you feed it with pre-trained and predefined vectors from data.

Mehdi

----

Thanks!
 
I need to add start and end tags to the tokenized titles, right? I’m not sure how to do this with pre-trained embeddings. Do I just create random vectors?
 
/ Elin

----

Yes, you can define them manually with zero vectors.

Mehdi


## Meeting on 2020-01-08

Elin's question: I have looked at some examples for RNNs but I’m still not sure how to build it.
For an RNN, should I input one token at a time, or a full title? Should the targets be and index mapping for each token or something else?
 
#### People in the meeting

- Elin
- Mehdi

#### What we did

In the intense 5 hours meeting:

- We read Elin's code including the code for downloading the images, pre-processing the text using BPEmb for titles, and vectorising the image.
- We looked at the challenges in pytorch implementation, to re-write some parts for simplification (i.e. simplifying the use of vocabulary and tokenization with BPEmb in pytorch)
- We processed and saved vectorizations of all images with Resnet (for faster processing time we chose the smallest model Resnet18)
- We tried to impliment the model up to the training steps without defining the loss function.

#### What next?

- Elin needs to figure out how to use a loss function and impliment the training loop.
- Some, ideas for evaluation:
   1. What is the difference in loss of the same test dataset, when using different embeddings trained on the same training datasets? Does the multi-lingual embedding help?
   2. Come up with hypothesis about how system works or what shouldn't work.
   Then test made up movie titles on a few poster images as an examination and development of hypothesis about the MovieTitleModel
- To report about the project, write up two sections first: (1) method (2) evaluations. 

