# SCTA Corpus Doc Embeddings 

## Intro

SCTA corpus doc embeddings are an embeddings model with vectors representing "items" within the SCTA corpus (reflective of the state of SCTA data at the time of training).

SCTA corpus-doc-embeddings version 2023-11-28 are the vectors resulting from training on 2023-11-28. 

As the corpus grows and texts within the existing corpus are improved these embeddings will be superceded by updated embeddings. Previous versions and associated training dating will be retrievable via the version history and corresponding versions tags.


## Training Details for version 2023-11-28

The resulting embeddings (and evaluation outcomes) depend on a number of pre-processing steps, document size construction, embedding algorithms, and training parameters.

You can re-run steps in using the `example/similarity-rankings.ipynb` notebook available in Codespace here: 

[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://github.com/codespaces/new?hide_repo_select=true&ref=main&repo=780538229)

(Note: it can take a little time for github to create the space and for all the associated data to be retrived, so be patient)


## Text pre-processing

* Convert to lower case
* Remove all punctuation
* Normalizing substitutions were made
  * ae->e
  * v->u
  * j->i
  * y->i
  * oe->e
 
## Document Breakdown

The corpus was broken down into documents based on the SCTA "item" structureType, usually representing chapter or questions divisions within a larger "toplevel" text.

One exception in this use was the *Sentences* Commentary and *Summa Theologiae* of Thomas Aquinas. 

The goal of this exercise was to compare similarity assertions of the 19th Leonine editors with similarity assertions of the embeddings model. Since the Leonine editors made their assertions at the article level, texts from Aquinas's *Summa Theologiae* and *Sentences* Commentary were broken down one level deeper than the SCTA item or question level. They were broken down at the article level.

The result at the time of the last training (2023-11-28) was a corpus of 23,433 documents constituting 38,205,513 word tokens.

## Training

Training was generated using the doc2Vec algorithm implemented by the Gensim library (gensim, v4.3.0: https://radimrehurek.com/gensim/models/doc2vec.html)

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

Evaluation of the model was achieved through a comparison of the claims of similarity made by the 19th century Leonine Editors between articles within Aquinas's *Summa Theologiae* and articles in his *Sentences Commentary* to similar assertions made by the resulting embeddings. 

A list of these original 19th century assertions were manually compiled. A function was then written to look for the text (for which similarity was asserted) in the embeddings ordered list of similarity rankings. 

The embeddings ranking of the target document within the similarity list was recorded. 

If the rank was 0, this meant that the embeddings model had identified the corresponding text (manually identified as similar by the Leonine editors) as the most similar from among all candidate documents.

An example excerpt of resulting rankings can be seen below. 

"matchFiltered" means results when an article from the Summa Theolgoiae is compared only to texts within Thomas Aquinas's *Sentences Commentary*. 

matchUnfiltered means results when compared to all (23,000+) possible items/documents within the SCTA corpus.

<img width="631" alt="image" src="https://github.com/scta/corpus-doc-embeddings/assets/1146685/3a768cf8-272a-4464-8117-b349d58fe4ed">

The chart shows that for 641 asserted parallels, the doc embeddings model (when filtered to look only for matches in the *Sentences Commentary*) identifies the manual assertion, on average, as the 7th highest recommended document. 

The median of 1 shows that the results are even better than this. Approximately 41% (265/641) of manually asserted matches were the embeddings model's top recommendation and 66% (426/641) were within top 5 of recommended documents.


