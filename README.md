# Project for the AICS course (MLT LT2318)

## Elin Hagman

### Project Description

Train an image caption model on movie titles and visual features of the poster images and generate a new image titles.

One project used a multi-layer Recurrent Neural Network to generate story titles from plot summaries. The dataset used was the WikiPlots corpus which has a collection of 112,936 story plots extracted from English language Wikipedia.[4]  The dataset was created by searching language article that contains a sub-header with words like "plot" or "plot summary" and thus includes summaries from movies, books, tv episodes, video games, etc.  The character-level language model consistently came up with titles that were both varied and plausible, like "Pirates: A Fight Dance Story", "Cannibal Spy II" and "Conan the Pirate" [5]

### Approach

Build an RNN model that uses pre-trained sub-word embedding and feature fusion layer which concatenates each subword embedding with the visual representation of the poster image. Train the RNN model on the movie titles and the visual vectors.

#### Pre-trained subword embeddings

Subword embeddings represent morphological information by splitting words into smaller instances. The idea of encoding rare and unknown words as sequences of subword units, based on the intuition that various word classes are translatable via smaller units than words (originally used to improve translation models for unknown words). The segmentation made by the Byte-Pair Encoding that is often satisfactory, however, it does not make a linguistic morphological analysis.[2] BPEmb: Tokenization-free Pre-trained Subword Embeddings in 275 Languages is a collection of pre-trained subword embeddings based on Byte-Pair Encoding (BPE) trained on Wikipedia articles. https://nlp.h-its.org/bpemb [1]

#### ResNet

This is the Pytorch version of ResNet residual nets model is pre-trained on the ImageNet classification dataset, as proposed in "Deep Residual Learning for Image Recognition".[3] The versions of the model contains 5, 34, 50, 101, 152 layers respectively.[6]

### Dataset

IMDB dataset. The movie posters are obtained from IMDB website. The dataset contains IMDB Id, IMDB Link, Title, IMDB Score, Genre and link to download movie posters. Each Movie poster can belong to at least one genre and can have at most 3 genre labels assigned to it. https://www.kaggle.com/neha1703/movie-genre-from-its-poster [7]

### Model

### Evaluation

## References

[1]
[BPEmb: Tokenization-free Pre-trained Subword Embeddings in 275 Languages](https://www.aclweb.org/anthology/L18-1473/)

[2]
[Neural Machine Translation of Rare Words with Subword Units](https://www.aclweb.org/anthology/P16-1162/)

[3]
[Deep Residual Learning for Image Recognition](https://arxiv.org/abs/1512.03385)

[4]
[WikiPlots corpus](https://github.com/markriedl/WikiPlots)

[5]
[Story titles, invented by neural network](https://aiweirdness.com/post/160014619217/story-titles-invented-by-neural-network)

[6]
[ResNet By Pytorch Team](https://pytorch.org/hub/pytorch_vision_resnet/)

[7]
[Movie Genre from its Poster: Predicting the Genre of the movie by analysing its poster]((https://www.kaggle.com/neha1703/movie-genre-from-its-poster))

## Notes

- [x] Find the dataset
- [x] Pre-processing: remove years in title from csv
- [x] Remove all records without genre, title or link
- [x] Create new csv with image names, genre and title
- [x] Skip downloading images if they already exist in folder
- [x] Read about pre-trained BERT model
- [x] Read about pre-trained ResNet model
- [x] Scrap initial idea and start over.
- [x] Read about sub-word embeddings
- Subword embeddings attempt to introduce morphological information by splitting words into smaller instances.
- [x] Read paper about BERT sub-word embeddings
- If/how a word gets split depends on the vocabulary size. A smaller vocabulary size yields a segmentation into more subwords, and a large vocabulary will not split frequent words. I chose the smallest vocabulary since the titles are mostly short instances of text.
- [x] Read paper about machine translation of rare words
- The idea of encoding rare and unknown words as sequences of subword units, based on the intuition that various word classes are translatable via smaller units than words (to improve translation models).
- [x] Can't import BPEmb on the server
- Worked after restarting the server...
- [x] How to generate image representation vectors from ResNet
- Using ResNet18, one instance of image vector (created using PIL module) is fed to the model. The image representation is created by copying the output of the final layer (with an output of 512 dimensions) after feeding the image vector to the model.
- [x] Need to fix error with ResNet model for grayscale images
- Fixed error by checking image band and changing the mode to RGB for grayscale images using PIL - seems this is not the best way (forgot source)
- [x] Read about feature fusion layers/multimodal pooling.
- [x] Concatenate token and image representations vectors in network model's forward loop or before?
- Made an implementation that concatenates and adds vectors to a dataset, not sure if it's a good approach
- [ ] GRU or GRUCell? (Pytorch's LSTM too hard)
- [ ] Need to add start and end tags to movie title token list

**Open questions:**

- Which ResNet model is best?
- Which vector dimension and/or vocabulary size of BPEmb is better? (larger vocab means larger segmentations)
