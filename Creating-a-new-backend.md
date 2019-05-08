Annif backend code is in the [annif/backend]() module. Each backend is implemented as a subclass of `AnnifBackend`, or its more specific subclass `AnnifLearningBackend` for backends that support online learning.

A backend must define these fields:

* `name`: a name for the backend (a single word, all lowercase)
* `needs_subject_index`: boolean value; defaults to False if not set; set to True if the backend makes use of the `SubjectIndex` passed as `project.subjects` to most methods
* `needs_subject_vectorizer`: boolean value; defaults to False if not set; set to True if the backend makes use of the `TfIdfVectorizer` passed as `project.vectorizer` to most methods

A backend needs to implement these key methods:

* `initialize` (optional): set up the necessary internal data structures
* `train` (optional): train the model on a given document corpus
* `_suggest`: this is the key method that is given a single document (text) and returns suggested subjects

Learning backends additionally implement:

* `learn`: continue training the model on the given corpus