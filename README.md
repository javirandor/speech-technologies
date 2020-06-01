# Speech Techonologies - UPF
## Final Project 2019-2020
#### Javier Rando Ramírez

This repository contains the code used for my final project in Speech Technologies and the explanatory slides.

The goal of the project is adapting CMUSphinx model for non-native Spanish speakers. The dataset used was CORELE, more information can be found here(http://cartago.lllf.uam.es/corele/corpus_es.html)

The obtained accuracy was not the expected probably due to the following limitations:

* A further expansion of the dictionary should be considered.
* The language model wasn't updated
* Data comes from interviews. This data are shorts sentences with interrumptions.

## Requirements
* Pocketsphinx
* [Spanish pre-trained model](https://sourceforge.net/projects/cmusphinx/files/Acoustic%20and%20Language%20Models/Spanish/)

## Executing the code
It must be done in the order indicated by the name of the files:
1. [Crawler](https://github.com/javirandor/speech-technologies/blob/master/1.%20Crawler.ipynb)
2. [Splitting audio and generating unique transcription](https://github.com/javirandor/speech-technologies/blob/master/2.%20Split%20audio%20and%20generate%20transcription.ipynb)
3. [Transcription Normalization](https://github.com/javirandor/speech-technologies/blob/master/3.%20Transcription%20Normalization.ipynb)
4. [Updating dictionary](https://github.com/javirandor/speech-technologies/blob/master/4.%20Update%20Dictionary.ipynb)
5. [Test models](https://github.com/javirandor/speech-technologies/blob/master/5.%20Test%20models.ipynb) (must be done after adapting the model)

## Adapting the model
This process follows the documentation published by CMUSphinx here(https://cmusphinx.github.io/wiki/tutorialadapt/).

To execute the following instructions, you will have to copy these scripts to your working folder from `cmu-sphinxtrain` previously. These are the commands for MAC.

```
cp /usr/local/Cellar/cmu-sphinxtrain/HEAD-0c21a6e/libexec/sphinxtrain/bw <working directory>
cp /usr/local/Cellar/cmu-sphinxtrain/HEAD-0c21a6e/libexec/sphinxtrain/mllr_solve <working directory>
cp /usr/local/Cellar/cmu-sphinxtrain/HEAD-0c21a6e/libexec/sphinxtrain/map_adapt <working directory>
cp /usr/local/Cellar/cmu-sphinxtrain/HEAD-0c21a6e/libexec/sphinxtrain/mk_s2sendump <working directory>
```

For my specific use case, this was the followed process:
1. Creating a working repository where I placed my training audio files and transcription.
2. Copying the default model to the working repository
```
cp -a <your models folder>/es-es/es-es .
cp -a <your models folder>/es-es/es.dict .
cp -a <your models folder>/es-es/es-es.lm.bin .
```
3. Generating acoustic features files
```
sphinx_fe -argfile es-es/feat.params -samprate 16000 -c corele.fileids -di . -do . -ei wav -eo mfc -mswav yes
```
4. Accumulating observation counts
```
./bw \
 -hmmdir es-es \
 -moddeffn es-es/mdef \
 -ts2cbfn .ptm. \
 -feat 1s_c_d_dd \
 -fdictfn es-es/noisedict \
 -svspec 0-12/13-25/26-38 \
 -cmn current \
 -agc none \
 -dictfn es.dict \
 -ctlfn corele.fileids \
 -lsnfn corele.transcription \
 -accumdir .
```
5. Creating a transformation with MLLR
```
./mllr_solve \
    -meanfn es-es/means \
    -varfn es-es/variances \
    -outmllrfn mllr_matrix -accumdir .
```
6. Create a folder for the adapted model
```
cp -a es-es es-es-adapt
```
7. Updating the acoustic model files with MAP
```
./map_adapt \
    -moddeffn es-es/mdef \
    -ts2cbfn .ptm. \
    -meanfn es-es/means \
    -varfn es-es/variances \
    -mixwfn es-es/mixture_weights \
    -tmatfn es-es/transition_matrices \
    -accumdir . \
    -mapmeanfn es-es-adapt/means \
    -mapvarfn es-es-adapt/variances \
    -mapmixwfn es-es-adapt/mixture_weights \
    -maptmatfn es-es-adapt/transition_matrices
```
8. Recreating the adapted sendump file
```
./mk_s2sendump \
    -pocketsphinx yes \
    -moddeffn es-es-adapt/mdef \
    -mixwfn es-es-adapt/mixture_weights \
    -sendumpfn es-es-adapt/sendump
