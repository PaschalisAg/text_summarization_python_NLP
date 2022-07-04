# Introduction

This project is part of Codecademy's course [*Apply Natural Language Processing with Python*](https://www.codecademy.com/learn/paths/natural-language-processing).

Main aim of the project is to apply a simple text summarization technique using Python in order to extract the summary of a long text. The text used for this project is my Master's Thesis in Digital Humanities for the University of Groningen.

The length of the text is 47 pages, from which are exluded the *References* section and the technical information of the thesis (for instance, the title, the author, student number, etc.). As it is shown below, the overall length of the text used for this project is **16236** tokens.


```python
import numpy as np
import pandas as pd
import re
from bs4 import BeautifulSoup

import nltk
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize, sent_tokenize
```


```python
with open("text/thesis_final_version.txt", encoding = "utf-8") as inp:
    text = inp.read()
```


```python
stopwords = set(stopwords.words("english"))
tokens = word_tokenize(text)
print(f"The overall length of the text used is: {len(tokens)} tokens")
```

    The overall length of the text used is: 16236 tokens


# Analysis


```python
# calculate how often a word occurs in a text
freqTable = dict()

for word in tokens:
    words = word.lower()
    if word in stopwords:
        continue
# continue in order to skip stopwords in the counting
#if the word is in the freqTable then add one more
    if word in freqTable:
        freqTable[word] += 1
# if it is not in the freqTable, then initiate the counting from 0
    else:
        freqTable[word] = 1
# print(freqTable)
```


```python
# create a dictionary that will sum the frequency of each word
# with respect to its sentence
# this will give to every sentence, based on the words that it contains
# a different weight, which will be used to summarize the text
# with the most frequent/important words-sentences

sentences = sent_tokenize(text)
sentenceValue = dict()

for sentence in sentences:
    for word, freq in freqTable.items():
        if word in sentence.lower():
            if sentence in sentenceValue:
                sentenceValue[sentence] += freq
            else:
                sentenceValue[sentence] = freq
                
# print(sentenceValue)
```


```python
# sort the frequencies of the sentences in an descending order

sort_sentenceValue = sorted(sentenceValue.items(), key=lambda x: x[1], reverse=True)
for i in sort_sentenceValue:
    print(i[0], i[1])



```python
sumValues = 0
for sentence in sentenceValue:
    sumValues += sentenceValue[sentence]
print(sumValues)
```

    773816



```python
average = sumValues / len(sentenceValue)
print(average)
```

    1700.6945054945054



```python
# only sentences with a value higher than the average will be included to the summary

summary = ""
for sentence in sentences:
    if (sentence in sentenceValue) and (sentenceValue[sentence] > average):
        summary += " " + "\n" + sentence
with open("text/thesis_summary.txt", "w") as infile:
    infile.write(summary)
```


```python

```
