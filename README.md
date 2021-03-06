# Music Genre Multiclass Classification

# Overview
## The dataset is derived from:
- T. Bertin-Mahieux, D. P.W. Ellis, B. Whitman, and P. Lamere. The million song dataset. In
Proceedings of the 12th International Conference on Music Information Retrieval (ISMIR), 2011.

- A. Schindler and A. Rauber. Capturing the temporal domain in Echonest Features for improved
classification effectiveness. In Proceedings of the 10th International Workshop on Adaptive Multi-
media Retrieval (AMR), 2012.

## Goals
The goal of this project is to develop a multiclass classification model for the music genre, which consists of:
- classic pop and rock
- dance and electronica
- folk,
- jazz and blues
- metal,
- pop,
- punk,
- soul and reggae

The expected accuracy should be at least better than random guessing the genre, which is 12.5%, 100% divided by 8 genres.

## Dataset
The dataset features:
- ID
    - trackID: unique ID for each track
- Meta
    - duration
    - tempo
    - loudness
    - key
    - mode
    - time signature
- Text
    - title: the title of the song
    - tags: pre-extracted lyrics from the song
- Audio
    - vector pre-extracted from the song

# Implementation
## Pre-processing
1. The audio data has been pre-processed into a waveform vector representation.
2. The tags data contains words extracted from the lyrics, but the sentences are not lyrics sequences.
   Therefore, taking out the stopwords and punctuations.

## Feature Selection Hypothesis and validation/test accuracy
The meta-features are redundant to classify the genre. Therefore, we may combine the meta-features with being trained with audio features or text features. The table below shows the accuracy of the model with different combinations of features. The baseline accuracy is 42%, which is based on the lyrics model and audio model. The goal is trying to ultilize all the features to increase the overall accuracy and build a better model for the genre classification.

| Feature |Validation Accuracy| Test Accuracy |
|:-------:|:--------:|:--------:|
|lyrics (fasttext)   | 50% | 42% |
|lyrics (nbsvm)     | 57% | 45% |
|audio (MLP) | 55% | 44% |
|audio (CNN) | 53% | 45% |          
|audio (MLP), lyrics (fasttext) | 61% | 51% |
|audio (MLP), lyrics (nbsvm) | 63% | 52% |
|audio (MLP), meta | 51% | 44% |
|audio (MLP), lyrics (fasttext), meta | 65% | 50% |
|audio (MLP), lyrics (nbsvm), meta | 62% | 46% |

## Results elaborations
### Text model
According to the text features, there are two models supported for fast verification.
- The `fasttext` model is based on the [fasttext](https://fasttext.cc/) library, implemented by ktrain.
- The `nbsvm` model is proposed by Wang and Manning and elaborated by the [ktrain author](https://medium.com/@asmaiya/a-neural-implementation-of-nbsvm-in-keras-d4ef8c96cb7c)

The ktrain library features simple-to-use and can verify the model performance with a few lines of code.
The models are grid-searchable and can be tuned to achieve better results with [cmet-ml](https://www.comet.ml/docs/python-sdk/Experiment/). The nbsvm model performs better than the fasttext model.

| Model | Validation Accuracy | Test Accuracy |
|:----:|:--------:|:--------:|
|fasttext | 50% | 42% |
|nbsvm | 57% | 45% |

### Audio model
The audio model applies an MLP with two hidden layers. The results are similar to the text model. However, with the meta-data, the accuracy doesn't improve. Although the CNN model attempts to capture the audio features, it makes no difference from the MLP model. Interestingly, the CNN model seems to give stable test accuracy results than the MLP model.

| Model | Validation Accuracy | Test Accuracy |
|:----:|:--------:|:--------:|
|audio (MLP) | 55% | 44% |
|audio (CNN) | 53% | 45% |
|audio (MLP) , meta | 51% | 44% |
|audio (CNN) , meta | 53% | 45% |

### Audio and Text model
The audio and text model combines the audio and text features. The results improve overall by around 9%. Using fasttext and nbsvm models achieves similar accuracy, although nbsvm model gets a slightly higher performance.

| Model | Validation Accuracy | Test Accuracy |
|:----:|:--------:|:--------:|
|audio (MLP), lyrics (fasttext) | 61% | 51% |
|audio (MLP), lyrics (nbsvm) | 63% | 52% |

### Audio and Text and Meta model
The final attempt combines the audio feature, text feature, and the key, mode, time signature meta-features. Again, the results are similar to the previous two models, but the model test performance doesn't improve. Instead, the model achieves a lower accuracy on the validation and the test dataset.

| Model | Validation Accuracy | Test Accuracy |
|:----:|:--------:|:--------:|
|audio (MLP), lyrics (fasttext), meta | 63% | 46% |
|audio (MLP), lyrics (nbsvm), meta | 62% | 46% |


# Conclusion
The given dataset is with a tiny amount of data with the fragile extracted text-features tags. The songs' vectors are also
pre-extracted that the information is limited. Hence, the accuracy of the model is low. Fortunately, the combined model using
audio and text correctly classify the music genre by around 50%, which is better than the baseline accuracy.