# Twitter Location NER

This is a project that I did over the summer to experiment with NER for location extraction from noisy twitter data.

The repository consists of two files:
- [DataGeneration.ipynb](DataGeneration.ipynb)
- [CustomNERModel.ipynb](CustomNERModel.ipynb)

## DataGeneration

For the project we didn't have a lot of data available so I implemented a tool for synthetically generating twitter data from existing examples. It generates tweets in two ways: using a masked Bert model to generate new predicted locations in place of exising ones and using the Nominatim API getting similar locations but expressed in a different way.

The notebook requires an existing csvs/existing_sentences.csv file containing annotated tweets in the following form:

```Evansville #weather on November 13 2015 - 11/13/2015,I-LOC O O O O O O O```

## CustomNERModel

This notebook contains functions for tokenizing training data and a training loop for named entity recognition for location entities. It uses [BertForTokenClassification](https://huggingface.co/docs/transformers/model_doc/bert#transformers.BertForTokenClassification) for classifying the entities. The model uses capitalisation patterns and word embeddings.

I experimented with building an LSTM but that gave poor results and using a pre trained Bert model and training on top with the twitter generated data worked much better.
I also experimented with adding an additional part-of-speech embedding but that worsened the results.

## Findings

When training the model with the data available to me I noticed that since in the tweets only a little portion of the sentence was a location the model was quickly learning to classify everything as a non-location token. At first I was using three tags: `O` - the zero tag, `B-LOC` - the beginnig of a location, `I-LOC` - any following location tokens. This meant that the `B-LOC` and `I-LOC` classes were significantly underrepresented in my training data. Hence I started using only two tags: `O` - the zero tag and `I-LOC` - location token tag. This gave a little better results but the location class was still significantly underrepresented.

In the future it might be beneficial to perform additional data pre-processing such as:
- processing hashtags as they could carry important information
- removing URLs as they are mostly irrelevant and add unnecessary non-location tokens
- processing emojis as they could carry context or location information
