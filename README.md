# Bug report code

This repository reproduces an apparent bug on `pytest-mypy` Python package.

To imediately see the bug in action clone this repository, and from the root
run:

    pytest --mypy

    error: Duplicate module named "conftest" (also at "/home/lviana/repos/pytest-mypy-conftest-bug/test/conftest.py")
    note: See https://mypy.readthedocs.io/en/stable/running_mypy.html#mapping-file-paths-to-modules for more info
    note: Common resolutions include: a) using `--exclude` to avoid checking one of them, b) adding `__init__.py` somewhere, c) using `--explicit-package-bases` or adjusting MYPYPATH


## Longer explanation

Pytest execution can be configured usign files called `conftest.py`. Such file
may be placed in several locations in a nested directory structure, and work
in a cascading manner -- this is by design.

The `pytest-mypy` plugin collects all `.py` and passes them along to `mypy`,
who in turn sees multiple `conftest.py` files placed in seemingly randome
locations and reports an error.

When running `mypy` without pytest it is possible to use the `exclude`
configuration or the `--exclude` flag.  This configuration option is
(unintentionally?) ignored by `pytest-mypy`, because `mypy` does not ignore
files that are explicitly passed to it.


## Solution

Ideally the `exclude` option should be read from `mypy.ini`, interpreted by
`pytest-mypy` and taken into account durint file collection. It would also
be nice to have the `--exclude` flag passed along via something like
`--mypy-exclude`.
