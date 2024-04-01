# SCTA Corpus Doc Embeddings 

## Intro

SCTA corpus doc embeddings are an embeddings model with vectors representing "items" within the SCTA corpus (reflective of the state of SCTA data at the time of training).

SCTA corpus-doc-embeddings version 2023-11-28 are the vectors resulting from training on 2023-11-28. 

As the corpus grows and texts within the existing corpus are improved these embeddings will be superceded by updated embeddings. Previous versions and associated training dating will be retrievable via the version history and corresponding versions tags.


## Training Details for version 2023-11-28

The resulting embeddings (and evaluation outcomes) depend on a number of pre-processing steps, document size construction, embedding algorithms, and training parameters.

You can re-run steps in using the `example/similarity-rankings.ipynb` notebook available in codespace here: 

[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://github.com/codespaces/new?hide_repo_select=true&ref=main&repo=780538229)


## Text pre-processing

* All text was converted to lower case
  * `newText = newText.lower()`
* Remove all punctuation
* Normalizing substitutions were made
  * ae->e
  * v->u
  * j->i
  * y->i
  * oe->e
 
## Document Breakdown

The corpus was broken down into documents based on the SCTA "item" structureType, usually representing chapter or questions divisions within a larger "toplevel" text.

One exception was of text within the Summa Theologiae of Thomas Aquinas. 
Since the goal of this exercise was to compare similarity assertions of 19th Leonine editors with similarity assertions of the embeddings model and the Leonine editors made their assertions at the article level, texts from Aquinas's Summa Theologiae were broken down one level deeper than the SCTA item or question level. They were broken down at the article level

The result was a corpus of 23,433 documents constituting 38,205,513 word tokens.

## Training

Training was generated using the doc2Vec algorithm implemented by the Gensim library. 

```python
model = gensim.models.doc2vec.Doc2Vec(vector_size=200, min_count=10, epochs=100)
model.build_vocab(train_corpus)
model.train(train_corpus, total_examples=model.corpus_count, epochs=model.epochs)
```

Three parameters were selected to guide training:

* to generate an vector embedding for document of **size 200**
* to include words that appear less than **10 times**
* to complete **100 training cycles**


## Testing and Evaluation

Evaluation of the model was achieved through a comparison of manual similarity assertions made by the 19th century Leonine editors to those by made by the resulting embeddings. 

More specifically, we focused the assertions of similarity made from a specific article to Thomas Aquinas's Summa Theologiae to comparable questions within his Commentary on the Sentences of Peter Lombard. 

A list of these original 19th century assertions were manually compiled. A function was then written to look for the text with the asserted similarity in the embeddings list of similarity rankings. 
The rank of the target document within the similarity list was recorded. 
If the rank was 0, then this meant that the computer had identified the corresponding text (manually identified as similar by the Leonine editors) as the most similar from among all 23,433 texts in the corpus.
As similar text was run, but but only after focusing the generated similarities list to only those questions that appear within Aquinas Sentences commentary. 
A 0 ranking here meant that among all the questions within the Sentences Commentary by Aquinas, the computer recognized this question as most similar.

In both the cases, the ranking for all asserted similarties were collected and the average ranking was computed. 

An example excerpt of resulting rankings can be seen below:

![image/png](https://cdn-uploads.huggingface.co/production/uploads/6560a782a72f05d2ea5e0336/6P-PC6R9YIfYflyYUplNU.png)

A the chart shows that for 641 asserted parallels the doc embeddings model identifies this assertion is on average the 7th highest recommended document. 
Further approximately 41% (265/641) were the embeddings model top recommendation and 66% (426/641) were within top 5 of recommended documents.


