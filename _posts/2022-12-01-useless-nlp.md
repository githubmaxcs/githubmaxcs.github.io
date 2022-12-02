---
layout: post
title: Useless NLP
subtitle: Ulysses and Infinite Jest
tags: [mathematics, python, NLP]
---

# WORK IN PROGRESS

# Introduction

I used NLP to gain insight into James Joyce's _Ulysses_ and David Foster Wallace's _Infinite Jest_ ("_IJ_"). It's kind of useless...but kind of fun. (I will not include my sources of _Ulysses_ and _IJ_.)

# __Ulysses__, James Joyce

## Libraries and Packages

"The Natural Language Toolkit, or more commonly NLTK, is a suite of libraries and programs for symbolic and statistical natural language processing for English written in Python."

```python
import nltk
# nltk.download('punkt')
# nltk.download('stopwords')
```

## Preparing the Data

```
# open and read the file
open_jj_ulysses = open('james-joyce-ulysses.txt')
raw_jj_ulysses = open_jj_ulysses.read()

# separate text into a list of words, punctuation marks, and stand-alone characters
find_words_jj_ulysses = nltk.word_tokenize(raw_jj_ulysses)
# use the 'nltk' library to perform analysis
jj_ulysses = nltk.Text(find_words_jj_ulysses)

type(jj_ulysses)
```

```
# Out[]: nltk.text.Text

```
