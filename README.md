# Speech Techonologies - UPF
## Final Project 2019-2020
#### Javier Rando Ramírez

This repository contains the code used for my final project in Speech Technologies and the explanatory slides.

The goal of the project is adapting CMUSphinx model for non-native Spanish speakers. The dataset used was CORELE, more information can be found here: http://cartago.lllf.uam.es/corele/corpus_es.html.

The obtained accuracy was not the expected probably due to the following limitations:

* A further expansion of the dictionary should be considered.
* The language model wasn't updated
* Data comes from interviews. This data are shorts sentences with interrumptions.

## Executing the code
It must be done in the order indicated by the name of the files:
1. [Crawler](https://github.com/javirandor/speech-technologies/blob/master/1.%20Crawler.ipynb)
2. [Splitting audio and generating unique transcription](https://github.com/javirandor/speech-technologies/blob/master/2.%20Split%20audio%20and%20generate%20transcription.ipynb)
3. [Transcription Normalization](https://github.com/javirandor/speech-technologies/blob/master/3.%20Transcription%20Normalization.ipynb)
4. [Updating dictionary](https://github.com/javirandor/speech-technologies/blob/master/4.%20Update%20Dictionary.ipynb)
5. [Test models](https://github.com/javirandor/speech-technologies/blob/master/5.%20Test%20models.ipynb) (must be done after adapting the model)

## Adapting the model
This process follows the documentation published by CMUSphinx here(https://cmusphinx.github.io/wiki/tutorialadapt/).

For my specific use case, this was the followed process:
1. Creating a working repository where I placed my training audio files and transcription.
2. Copying the default model to the working repository
```
cp -a <your models folder>/es-es/es-es .
cp -a <your models folder>/es-es/es.dict .
cp -a <your models folder>/es-es/es-es.lm.bin .
```
3. Test
