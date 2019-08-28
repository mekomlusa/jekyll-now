# Feature Engineering for Machine Learning
## Ch.3: Text Data: Flattening, Filtering, and Chunking

* Bag-of-Words (BOW): turns a text document into a vector of counts. Each word in the document becomes a dimension.
  * Flat - no textual structure is preserved (i.e. the resulting vector has no word order information). Lost semantic meaning!
  * No word hierarchy: words that belong to a larger category are not collapsed together. e.g. "cat" and "dog" will NOT be counted as "animal".
  * Simple yet useful heuristics, capable for coarse tasks such as information retrieval.
* Bag-of-n-grams: extension of BOW. Looking at sequence of `n` tokens now.
  * Larger `n`, richer information; greater cost.
  * In practice, higher order n-grams are usually rare and not providing a lot of values in the final models.
* Filtering methods
  * Stopwords: remove common words like 'the', 'on', etc.
  * Frequency-based filtering: idea for filtering out corpus-specific common words as well as general-purpose stopwords. Use with cautions, though; some frequently occurred words might be useful to keep. Better to be used along with stopwords.
  * Rare words: rare occurrence -> unreliable, computational overhead. Can be collapsed into a new bin called "RARE" when doing BOW.
  * Stemming: different from lemmatization.
    * Stemming may result in words that are not actual words. Stems are created by removing the suffixes or prefixes used with a word. e.g. Goes -> goe, went -> went.
    * Lemmatization: reduces the inflected words properly ensuring that the root word belongs to the language. In Lemmatization root word is called Lemma. A lemma (plural lemmas or lemmata) is the canonical form, dictionary form, or citation form of a set of words. e.g. Goes -> go, went -> go.
    * Stemming could hurt more than it could help. Trimming down words could cause confusion, e.g. "news" vs "new".
  * Parsing and tokenization
  * Collocation: phrase detection
    * Definition: “A collocation is an expression consisting of two or more words that correspond to some conventional way of saying things.” (Manning and Schütze 1999: 151)
    * Not necessarily n-gram. e.g. "He knocks on her door." -> "knock door" is a collocation.
    * BOW has no collocation.
    * Useful as features, but hard to find. Manual curation would be cumbersome.
    * Typical approaches
      * Frequency-based methods: e.g. count the most frequently occuring n-grams
      * Hypothesis testing: check if two words are more likely to occur together, or independently. Likelihood ratio test
      * Pointwise mutual information: sensitive to rare words, not commonly use.
    * Practical approach: find collocation based on n-grams.
    * Chunking & POS tagging: examines neighborhood of a token for POS groupings. More sophisticated than n-grams.
  * A good overview/refresher on introductory NLP techniques. Ideas introduced in this chapter turns a document into a simple flat vector.
