In many backends, the training data has to be preprocessed before the actual model is built. This preprocessing may be computationally intensive and take up a significant portion of the total training time. If you want to experiment with different parameter settings, it is usually unnecessary to do the preprocessing more than once, as long as your training data and your vocabulary remain unchanged. 

With many Annif backends (fasttext, omikuji, vw_multi, nn_ensemble), you can use the `--cached` option of the `train` command to skip the preprocessing step and instead reuse the already preprocessed training data from the previous training run. Example:

Initial training run:

    annif train fasttext-en /path/to/Annif-corpora/training/yso-finna-en.tsv.gz

(evaluate, adjust the parameters in projects.cfg ...)

Subsequent training runs, reusing already preprocessed training data:

    annif train fasttext-en --cached

Usually the `vocab` and `analyzer` settings affect the preprocessing of training data, so if you adjust these parameters, you cannot use `--cached` but must instead do a full training run. See the documentation of the respective backend for details on which parameters are important for preprocessing.