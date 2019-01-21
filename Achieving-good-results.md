Here are some tips on how to get good quality results, based on experience setting up Annif projects for different scenarios.

1. Define the baseline

You will need some documents that have been manually indexed with gold standard subjects. The minimum reasonable number is around 50 documents, but it helps to have at least a few hundred.

Be aware that subject indexing is a subjective task. It is very unlikely that two humans given the same document will come up with exactly the same subjects for it. According to many empirical studies, inter-indexer agreement tends to be somewhere in between 30% and 50%. It's unrealistic to expect that an algorithm can do much better than this - though it must be noted that the. It can be very informative to actually have several people assign subjects for the same set of documents and measure how similar or different they are. It will always surprise everyone involved. For some background on inter-indexer consistency and automated indexing, Alyona Medelyan's PhD thesis [Human-competitive automatic topic indexing](https://researchcommons.waikato.ac.nz/handle/10289/3513) is highly recommended reading.

2. Decide what to measure

After collecting gold standard documents you need to define what you want to achieve. There are many metrics that can be used to measure the quality of automated subject indexing and/or classification. Some common ones supported by Annif are:

* [F1 score](https://en.wikipedia.org/wiki/F1_score): this is the harmonic mean of precision and recall, an accuracy metric expressed as a single number between 0.0 (worst) and 1.0 (best). It is commonly used to evaluate automated indexing. However, the F1 score is sensitive to the limit and threshold parameters used to restrict the set of algorithmically suggested subjects. The F1 score will be different if you select the top 5 suggestions from the algorithm than if you take the top 10. If you want to achieve the ideal F1 score, you will therefore have to test different limit and offset settings. The Annif `optimize` command will do this if you need to, but a ranking-based measure will avoid having to choose these parameters.
* [NDCG](https://en.wikipedia.org/wiki/Discounted_cumulative_gain) or **Normalized Discounted Cumulative Gain** is a ranking-based measure. It means that the order of algorithmically suggested subjects is significant: getting the top ranked (highest score) result right will matter more than getting the 2nd or 3rd right. It is also a value between 0.0 (worst) and 1.0 (best).
* [Precision](https://en.wikipedia.org/wiki/Precision_and_recall)@1 is the proportion of the highest ranked results that match the gold standard. It can be useful when comparing classifiers that need to select just one class or category per document. However, if the distribution of classes is uneven, it can be misleading: if 90% of documents belong to one class and the remaining 10% are split between other classes, then just predicting the highest frequency class for every document will achieve a 90% precision but be completely worthless. Precision@5 is sometimes used to evaluate subject indexing algorithms too, although F1 score and NDCG are perhaps more common and useful for that purpose.

When performing subject indexing, NDCG is probably the easiest to optimize, and generally a high NDCG will also imply achieving high F1 scores regardless of the limit and threshold settings.

3. Split your corpus into train, validate and test sets

If you happen to have both metadata (short text such as titles, with manually assigned subjects) and fulltext documents, you can use the metadata for training most Annif algorithms and use the fulltext documents for validation and final evaluation (test). However, if you have at least few thousand fulltext documents available, it is useful to split them into several subsets:

* Most documents will go into the training set. The train set will be used to train Maui and ensemble models such as PAV.
* Around 500 documents will go into the validation set. This will be used to test different (hyper)parameters for the algorithms, as well as limit and offset values that will give the best F1 score if you're aiming for that.
* Around 500 documents will go into the test set. These will be put aside for the most part and only evaluate algorithms/models that have been tuned by using the validate set.

Typical evaluation of a validate set:

    annif eval tfidf-en --limit 5 --threshold 0.2 path/to/corpus/validate/

4. Configure and train some basic projects/models/backends

The easiest backend to get started with is `tfidf`, as it doesn't require any configuration. So train that first, then evaluate it using the validate set. Once you get reasonable results, you can start testing other backends.

Note that sometimes the `tfidf` backend will give very bad suggestions. Don't give up hope yet, as it is possible that it is making systematic errors that can be corrected by a smart ensemble such as PAV. I've had a `tfidf` algorithm give an F1 score of 0.02, which increased to around 0.60 when wrapped in a PAV ensemble, and even more when combined with other backends.

The second most important backend is `maui`. Unfortunately setting it up can take some work, as it is a separate Java application that needs to run in a servlet container and needs to be trained on the command line with data in just the right format. But it is important to get it working because its lexical approach complements the statistical approaches used in other backends very well.

After getting these two to work you can move on to the ensembles, or try to get fastText working. The problem with fastText is that it has a lot of (hyper)parameters that need to be set just right to get good results.

5. Configure an ensemble

The basic backends alone do not usually give very good results, as they all have their weaknesses. Combining them into an ensemble can improve results quite a lot. It is easiest to start with a simple `ensemble` backend that just combines results from several backends by taking the mean of scores, i.e. letting the backends vote on what the best result is. However, don't expect a large improvement from this simple strategy - sometimes the results are better than for each individual algorithm, sometimes they are worse. The important step is to get combined results!

Once you have a basic ensemble working, you can try a smart ensemble such as `pav`. It is otherwise similar to the basic ensemble but requires some training documents - a few thousand is a good number. After training the PAV ensemble you should see a significant improvement! Note that scores returned from PAV are probability estimates and if you are aiming for a high F1 score, the best strategy is often to pick a large limit value such as 15 and a relatively high threshold value of 0.25 or 0.3.

6. Set up feedback / online learning

Here I would tell you that the next step would be to continue training Annif models as new documents are coming in, but it's not yet implemented, so I won't. Thanks for reading this far!