# Efficient Estimation of Word Representations in Vector Space

* A new method to project words into n-dimensional vector
* Words in this space can be used for mapping semantic relationships (Lisbon -> Portugal as Paris -> France) and syntactic relationships (walk -> walked as run -> ran)

### Intro
* Before word2vec, models commonly treat words as atomic units. Many NLP tasks are limit by size of quality data, where these simple models fail
* This paper aims to learn high-quality word vectors from huge datasets
* Recent techniques have shown that word vectors preserve relationships beyond syntax e.g. vector("King") - vector("Man") + vector("Woman") = vector("Queen")
* This paper introduces new model architectures to improve the quality of word vectors
* Design a new test for syntactic and semantic regularities

### Log-Linear Models
* Neural Network Language Models (NNLM) have some success, but training is limited by the non-linear hidden layer. The authors create simpler models which may not represent the data as precisely, but allows for more efficient training.

**Continuous Bag-of-Words (CBOW)**
* Given some previous words and future words in the training example, attempt to predict the middle word
* E.g. for the sentence "the dog has a toy", try to predict "has" from {"the", "dog", "a", "toy"}
* The word vectors of the input words are averaged -> Word order does not matter -> "bag of words"
* Authors obtain best performance with four historical and four future words
* Training complexity Q = N x D + D * log2(V), where N is number of input words, D is word vector dimensions, and V is size of word corpus. (We take the log2 of V as the output word is (Huffman binary tree encoded)[http://www.trevorsimonton.com/blog/2016/12/15/huffman-tree-in-word2vec.html])

**Skip-gram**
* Given a word in a sentence, try to predict another word in the sentence
* E.g. given "has" try to predict {"the", "dog", "a", "toy"} (effectively oppostive to CBOW)
* Project current word, and try to predict words within a range of this word
* Authors found that increased range increases quality of word vectors, but increases complexity
* Complexity is Q = C X (D + D x log2(V)), where C is range of words to predict

### Results
* Authors define a comprehensive test of semantic and syntactic questions.
* The semantic questions are of the form:
    - **Common capital city** e.g. Athens is to Greece as Oslo is to Norway
    - **All capital cities** e.g. Astana is to Kazakstan as Harare is to Zimbabwe
    - **Currency** e.g. Angola is to kwanza as Iran is to rial
    - **City-in-state** e.g. Chicago is to Illinois as Stockton is to California
    - **Man-Woman** e.g. brother is to sister as grandson is to granddaughter
* Synactic questions are:
    - **Adjective to adverb** e.g. apparent is to apparently as rapid is to rapidly
    - **Opposite** e.g. possibly is to impossibly as ethical is to unethical
    - **Comparitive** e.g. great is to greater as tough is to tougher
    - **Superlative** e.g. easy is to easiest as lucky is to luckiest
    - **Present participle** e.g. think is to thinking as read is to reading
    - **Nationality** e.g. Switzerland is to Swiss as Cambodia is to Cambodian
    - **Past tense** e.g. walking is to walked as swimming is to swam
    - **Plural nouns** e.g. mouse is to mice as dollar is to dollars
    - **Plural verbs** e.g. work is to works as speak is to speaks
* 8869 semantic and 10675 syntactic questions


* Use Google News corpus for training the word vectors
    - Contains 6billion tokens. Restrict vocab to 1M most frequent words

**Comprehensive Test Set**
* With 640d word vectors, CBOW achieves 24%/64%; Skip-gram achieves 55%/59% accuracy in semantic/synethtic tasks

**Training Time**
* 3 training epochs CBOW, 300d vector, 783M words takes 1 day to train, average accuracy 36.1% (averaged across tasks)
* 1 epoch CBOW, 300d vector, 1.6B words takes 0.6 days to train, av. accuracy 36.1%
* 3 epoch Skip-gram, 300d vector, 783M words takes 3 days to train, achieves 53.3%
* 1 epoch Skip-gram, 300d vector, 1.6B words takes 2 days to train, achieves 53.8%

Increase words over training epochs!

* Doubling vector dimensionality yields better results, but also take the most training time


### Uses
Aside from "a is to b as c is to d", one can use word2vec to see which words occupy the same region of the n dimensional space (use d reduction technique like PCA or TSNE to visualise). Furthermore, training a model on a corpus in a different language __should__ yield a similar distribution of word vectors; Finding the closest word in a french model to "King" should be "roi" -> Machine translation.
