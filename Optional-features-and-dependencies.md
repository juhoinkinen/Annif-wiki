The core of Annif can be installed from PyPI with a single command. It consists of pure Python code and the only requirement with native code dependencies is NumPy.

There are some optional features that depend on external native code libraries:

# voikko analyzer

Assuming you are using Ubuntu, you fill first need to install the `libvoikko1` and `voikko-fi` packages:

    sudo apt install libvoikko1 voikko-fi

Then install the optional feature:

    pip install annif[voikko]

If you have installed Annif from GitHub, use this instead:

    pip install .[voikko]
    pip install -e .  # make sure the Annif installation remains in editable mode

# fastText backend

Using the fastText backend requires installing the fastText Python wrapper, which compiles into native code and is not included by default when installing Annif. We use the [fasttextmirror](https://pypi.org/project/fasttextmirror/) package from PyPI. First you need to ensure that the Python development files are available:

    sudo apt install libpython3.6-dev

(adapt this to your Python 3 version)

You can install the optional dependencies like this:

    pip install annif[fasttext]

If you have installed Annif from GitHub, use this instead:

    pip install .[fasttext]
    pip install -e .  # make sure the Annif installation remains in editable mode

# Neural network backend (nn_ensemble)

Using the nn_ensemble backend requires TensorFlow 2. PyPI provides pre-built packages of TensorFlow so no compilation is necessary. You can install the optional dependencies like this:

    pip install annif[nn]

If you have installed Annif from GitHub, use this instead:

    pip install .[nn]
    pip install -e .  # make sure the Annif installation remains in editable mode

If this fails with an error like `Could not find a version that satisfies the requirement tensorflow==2.0.*`, you may need to upgrade your `pip` first, like this:

    pip install -U pip

# Vowpal Wabbit based backends

Using the `vw_multi` or `vw_ensemble` backends requires installing the Vowpal Wabbit bindings for Python, which is not included by default when installing Annif. The bindings require building VW from source, so you need to install some libraries first (see [Dependencies](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Dependencies) in the VW wiki for more details if necessary). On a typical Ubuntu 16.04 or 18.04 system this should be enough:

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

# Omikuji backend

Omikuji is implemented in Rust, but generally it doesn't have to be built from Rust sources as binary packages are available on PyPI. See the [omikuji README](https://github.com/tomtung/omikuji#python-binding) for details if you have issues.

Install the optional feature:

    pip install annif[omikuji]

If you have installed Annif from GitHub, use this instead:

    pip install .[omikuji]
    pip install -e .  # make sure the Annif installation remains in editable mode