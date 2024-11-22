The core of Annif can be installed from PyPI with a single command. It consists of pure Python code and the only requirement with native code dependencies is NumPy.

There are some optional features that depend on external native code libraries:

# voikko analyzer

Assuming you are using Ubuntu, you fill first need to install the `libvoikko1` and `voikko-fi` packages:

    sudo apt install libvoikko1 voikko-fi

Then install the optional feature:

    pip install annif[voikko]

If you have installed Annif from GitHub, use this instead:

    poetry install -E voikko

# spaCy analyzer

Install the optional feature:

    pip install annif[spacy]

If you have installed Annif from GitHub, use this instead:

    poetry install -E spacy

You will need to download [language-specific models](https://spacy.io/usage/models) separately. Typically there are several different models available for each language - the smallest ones work just fine as we only need support for lemmatization but not any advanced features supported in the larger models.

To download the small model for English:

    python -m spacy download en_core_web_sm

To download the small model for German:

    python -m spacy download de_core_news_sm

# estnltk analyzer

Install the optional feature:

    pip install annif[estnltk]

If you have installed Annif from GitHub, use this instead:

    poetry install -E estnltk

# Language filtering with pycld3
<details>
<summary><b><u>
DEPRECATION NOTE: THIS OPTIONAL DEPENDENCY IS NO LONGER NEEDED IN ANNIF SINCE 0.60.

Language detection is now performed with Simplemma, which is installed by default instead of being an optional extra.
</b></u></summary>

[lang_filter](https://github.com/NatLibFi/Annif/wiki/Transforms#filter_lang-transform) transform relies on [Compact Language Detector v3](https://github.com/google/cld3), which is implemented in C++, but binary packages of it are available on PyPI via [`pycld3`](https://pypi.org/project/pycld3/).

Install the optional feature:

    pip install annif[pycld3]

If you have installed Annif from GitHub, use this instead:

    pip install .[pycld3]
    pip install -e .  # make sure the Annif installation remains in editable mode
</details>

# fastText backend

Using the fastText backend requires installing the fastText Python wrapper, which is provided by [fastText-wheel](https://pypi.org/project/fasttext-wheel/) and is not included by default when installing Annif.

Install the optional feature:

    pip install annif[fasttext]

If you have installed Annif from GitHub, use this instead:

    poetry install -E fasttext

# Neural network backend (nn_ensemble)

Using the nn_ensemble backend requires TensorFlow 2. PyPI provides pre-built packages of TensorFlow so no compilation is necessary. You can install the optional dependencies like this:

    pip install annif[nn]

If you have installed Annif from GitHub, use this instead:

    poetry install -E nn

If this fails with an error like `Could not find a version that satisfies the requirement tensorflow==2.0.*`, you may need to upgrade your `pip` first, like this:

    pip install -U pip

# vw_multi backend
<details>
<summary><b><u>
DEPRECATION NOTE: THIS BACKEND IS NO LONGER AVAILABLE IN ANNIF SINCE 0.56
</b></u></summary>

Note that the `vw_ensemble` backend was removed already in Annif 0.45.

Using the `vw_multi` backend requires installing the Vowpal Wabbit bindings for Python, which is not included by default when installing Annif. The bindings require building VW from source, so you need to install some libraries first (see [Dependencies](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Dependencies) in the VW wiki for more details if necessary). On a typical Ubuntu 16.04 or 18.04 system this should be enough:

    sudo apt install libboost-program-options-dev libboost-python-dev zlib1g-dev cmake libboost-system-dev libboost-thread-dev libboost-test-dev

You can install the optional dependencies like this:

    pip install annif[vw]

If you have installed Annif from GitHub, use this instead:

    pip install .[vw]
    pip install -e .  # make sure the Annif installation remains in editable mode

If the build still fails and you get an error like this:

    ImportError: /usr/lib/x86_64-linux-gnu/libboost_python-py27.so.1.58.0: undefined symbol: PyClass_Type

the likely reason is that the VW bindings are being built with the wrong (Python 2) version of libboost-python. You can fix it by fiddling with symlinks like this:

    sudo ln -sf /usr/lib/x86_64-linux-gnu/libboost_python-py35.a /usr/lib/x86_64-linux-gnu/libboost_python.a
    sudo ln -sf /usr/lib/x86_64-linux-gnu/libboost_python-py35.so /usr/lib/x86_64-linux-gnu/libboost_python.so

If there are still import errors they could be resolved by using `libboost_python3` instead of `libboost_python` in the above symlinks.
</details>

# Omikuji backend

Omikuji is implemented in Rust, but generally it doesn't have to be built from Rust sources as binary packages are available on PyPI. See the [omikuji README](https://github.com/tomtung/omikuji#python-binding) for details if you have issues.

Install the optional feature:

    pip install annif[omikuji]

If you have installed Annif from GitHub, use this instead:

    poetry install -E omikuji

# STWFSA backend

Install the optional feature:

    pip install annif[stwfsa]

If you have installed Annif from GitHub, use this instead:

    poetry install -E stwfsa

# YAKE backend

The yake backend is a wrapper around [YAKE library](https://github.com/LIAAD/yake), which is licended under GPLv3, while Annif is licensed under the Apache License 2.0. The licenses are compatible, but depending on legal interpretation, the terms of the GPLv3 (for example the requirement to publish corresponding source code when publishing an executable application) may be considered to apply to the whole of Annif+Yake if you decide to install the optional YAKE dependency.

Install the optional feature:

    pip install annif[yake]

If you have installed Annif from GitHub, use this instead:

    poetry install -E yake