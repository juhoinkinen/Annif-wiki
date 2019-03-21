The core of Annif can be installed from PyPI with a single command. It consists of pure Python code and the only requirement with native code dependencies is NumPy.

There are some optional features that depend on external native code libraries:

# voikko analyzer

Assuming you are using Ubuntu, you fill first need to install the `libvoikko1` and `voikko-fi` packages:

    sudo apt install libvoikko1 voikko-fi

Then install the optional feature:

    pip install annif[voikko]

# fastText backend

Using the fastText backend requires installing the fastText Python wrapper, which compiles into native code and is not included by default when installing Annif. We use the [fasttextmirror](https://pypi.org/project/fasttextmirror/) package from PyPI. You can install the optional dependencies like this:

    pip install annif[fasttext]

Alternatively (e.g. if you have installed Annif from GitHub), you can just install the required `fasttextmirror` package directly:

    pip install fasttextmirror

# Vowpal Wabbit based backends

Using the `vw_multi` backend requires installing the Vowpal Wabbit bindings for Python, which is not included by default when installing Annif. The bindings require building VW from source, so you need to install some libraries first (see [Dependencies](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Dependencies) in the VW wiki for more details if necessary). On a typical Ubuntu 16.04 or 18.04 system this should be enough:

    sudo apt install libboost-program-options-dev libboost-python-dev zlib1g-dev

You can install the optional dependencies like this:

    pip install annif[vw]

Alternatively (e.g. if you have installed Annif from GitHub), you can just install the required `vowpalwabbit` package directly:

    pip install vowpalwabbit

If the build still fails and you get an error like this:

    ImportError: /usr/lib/x86_64-linux-gnu/libboost_python-py27.so.1.58.0: undefined symbol: PyClass_Type

the likely reason is that the VW code is being built with the wrong (Python 2) version of libboost-python. You can fix it by fiddling with symlinks like this:

    sudo ln -sf /usr/lib/x86_64-linux-gnu/libboost_python-py35.a /usr/lib/x86_64-linux-gnu/libboost_python.a
    sudo ln -sf /usr/lib/x86_64-linux-gnu/libboost_python-py35.so /usr/lib/x86_64-linux-gnu/libboost_python.so
