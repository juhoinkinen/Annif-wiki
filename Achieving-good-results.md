Here are some tips on how to get good quality results, based on experience setting up Annif projects for different scenarios.

# 1. Define the baseline

You will need some documents that have been manually indexed with gold standard subjects. The minimum reasonable number is around 50 documents, but it helps to have at least a few hundred.

Be aware that subject indexing is a subjective task. It is very unlikely that two humans given the same document will come up with exactly the same subjects for it. According to many empirical studies, inter-indexer agreement tends to be somewhere in between 30% and 50%. It's unrealistic to expect that an algorithm can do much better than this - though it must be noted that the differences between humans are often more matters of perspective, whereas algorithms tend to make outright errors that a human would never make. It can be very informative to actually have several people assign subjects for the same set of documents and measure how similar or different they are. It will always surprise everyone involved. For some background on inter-indexer consistency and automated indexing, Alyona Medelyan's PhD thesis [Human-competitive automatic topic indexing](https://researchcommons.waikato.ac.nz/handle/10289/3513) is highly recommended reading.

# 2. Decide what to measure

After collecting gold standard documents you need to define what you want to achieve. There are many metrics that can be used to measure the quality of automated subject indexing and/or classification. Some common ones supported by Annif are:

* [F1 score](https://en.wikipedia.org/wiki/F1_score): this is the harmonic mean of precision and recall, an accuracy metric expressed as a single number between 0.0 (worst) and 1.0 (best). It is commonly used to evaluate automated indexing. However, the F1 score is sensitive to the limit and threshold parameters used to restrict the set of algorithmically suggested subjects. The F1 score will be different if you select the top 5 suggestions from the algorithm than if you take the top 10. If you want to achieve the ideal F1 score, you will therefore have to test different limit and offset settings. The Annif `optimize` command will do this if you need to, but a ranking-based measure will avoid having to choose these parameters.
* [NDCG](https://en.wikipedia.org/wiki/Discounted_cumulative_gain) or **Normalized Discounted Cumulative Gain** is a ranking-based measure. It means that the order of algorithmically suggested subjects is significant: getting the top ranked (highest score) result right will matter more than getting the 2nd or 3rd right. It is also a value between 0.0 (worst) and 1.0 (best).
* [Precision](https://en.wikipedia.org/wiki/Precision_and_recall)@1 is the proportion of the highest ranked results that match the gold standard. It can be useful when comparing classifiers that need to select just one class or category per document. However, if the distribution of classes is uneven, it can be misleading: if 90% of documents belong to one class and the remaining 10% are split between other classes, then just predicting the highest frequency class for every document will achieve a 90% precision but be completely worthless. Precision@5 is sometimes used to evaluate subject indexing algorithms too, although F1 score and NDCG are perhaps more common and useful for that purpose.

When performing subject indexing, NDCG is probably the easiest to optimize, and generally a high NDCG will also imply achieving high F1 scores regardless of the limit and threshold settings.

# 3. Split your corpus into train, validate and test sets

If you happen to have both metadata (short text such as titles, with manually assigned subjects) and fulltext documents, you can use the metadata for training most Annif algorithms and use the fulltext documents for validation and final evaluation (test). However, if you have at least few thousand fulltext documents available, it is useful to split them into several subsets:

* Most documents will go into the training set. The train set will be used to train MLLM and ensemble models such as PAV or the NN ensemble.
* Around 500 documents will go into the validation set. This will be used to test different (hyper)parameters for the algorithms, as well as limit and offset values that will give the best F1 score if you're aiming for that.
* Around 500 documents will go into the test set. These will be put aside for the most part and only evaluate algorithms/models that have been tuned by using the validate set.

You can perform a random split, but if your documents have been published in different times and you're aiming to perform automated indexing for future, yet-unseen documents, the best way to split is by date. For example if you have 5000 documents from the years 2009-2018, with roughly 500 documents per year, you can select documents published in 2018 as the test set, documents published in 2017 as the validate set, and the remaining documents from 2009-2016 as the train set. This way the sets you are measuring with will always be newer than the training data, as will be the case later on. This [blog post](https://www.fast.ai/2017/11/13/validation-sets/) by Rachel Thomas of fast.ai explains very well the reason for such a 3-way split and how to do it properly.

Typical evaluation of a validate set:

    annif eval tfidf-en --limit 5 --threshold 0.2 path/to/corpus/validate/

The scores will be reported at the end. If you're measuring NDCG, set a limit of 20 or more and a threshold of 0.0 (which is the default).

# 4. Configure and train some basic projects/models/backends

The easiest backend to get started with is `tfidf`, as it doesn't require any backend-specific configuration. So train that first, then evaluate it using the validate set. Once you get at least somewhat reasonable results, you can start testing other backends. `tfidf` is the "Hello World" of Annif algorithms: good for getting the basic mechanics working, but most likely not the end result you are aiming for.

Note that sometimes the `tfidf` backend will give very bad suggestions. Don't give up hope yet, as it is possible that it is making systematic errors that can be corrected by a smart ensemble. I've had a `tfidf` algorithm give an F1 score of 0.02, which increased to around 0.60 when wrapped in a PAV ensemble, and even more when combined with other backends.

The second most important backend is probably `mllm`. Its lexical approach complements the statistical approaches used in other backends very well. (Previously Maui was used for this but MLLM is better)

After getting these two to work you can move on to the ensembles, or try to get some of the other backends working. `omikuji` is highly recommended, as it tends to give excellent results with minimal (default) configuration. `fasttext` is another alternative to try, but a problem with fastText is that it has a lot of (hyper)parameters that need to be set just right to get good results.

In case of long documents that have an abstract and/or introduction it can be very advantageous to base the suggestions only on the beginning part of a document. 
This can be achieved by using the `limit` transformation (setting `transform=limit(x)` in the project configuration), which simply truncates an input document to its first `x` characters before passing it to the backend. 
For example for [JYU theses](https://github.com/NatLibFi/Annif-corpora/tree/master/fulltext/jyu-theses#theses-from-university-of-jyv%C3%A4skyl%C3%A4) a good value for `limit` is 5000.

You can also check how changing the `token_min_length` parameter of the [analyzer](https://github.com/NatLibFi/Annif/wiki/Analyzers) of your project affects results. By default the value of this parameter is three, but depending on language and documents it can be worthwhile to change it. For example, decreasing the `token_min_length` to two allows suggestions based on two letter words, such as "UK", which would be discarded by the analyzer when using the default value.

# 5. Configure an ensemble

The basic backends alone do not usually give very good results, as they all have their weaknesses. Combining them into an ensemble can improve results quite a lot. It is easiest to start with a simple `ensemble` backend that just combines results from several backends by taking the mean of scores, i.e. letting the backends vote on what the best result is. However, don't expect a large improvement from this simple strategy - sometimes the results are better than for each individual algorithm, sometimes they are worse. The important step is to get combined results!

Once you have a basic ensemble working, you can try a smart ensemble such as `pav` or `nn_ensemble`. They are otherwise similar to the basic ensemble but require some training documents - a few thousand is a good number. After training the smart ensemble you should see a significant improvement! Note that scores returned from PAV are probability estimates and if you are aiming for a high F1 score, the best strategy is often to pick a large limit value such as 15 and a relatively high threshold value of 0.25 or 0.3.
