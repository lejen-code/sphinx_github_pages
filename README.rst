######################################################
How to Host reStructured Text on GitHub Pages
######################################################

.. figure:: https://github.com/lejen-code/sphinx_github_pages/workflows/github%20pages/badge.svg 
.. image:: https://readthedocs.org/projects/sphinx-github-pages/badge/?version=latest


******************************************************
01: Project structure
******************************************************

.. important:: 
    
    Project folder structure with actual source code hosted in repository.
    Workflow is placed in the **docsource** directory.
    GitHub Pages in the **docs** directory.

.. code:: 

  project-name
  ├── README.rst
  ├── .gitignore
  └── docs
  │   └── .gitkeep
  └── docsource
      └── .gitkeep

.. important:: 
    
    Requirements:
    ``anaconda navigator (v1.10.0)``,
    ``python (v3.8.5)``,
    ``sphinx (v3.4.1)``,
    ``poetry (v1.1.0)``

******************************************************
02: sphinx-quickstart
******************************************************

**Sphinx** documentation `sphinx-doc.org: Getting Started <https://www.sphinx-doc.org/en/master/usage/quickstart.html>`_
**sphinx-quickstart** command to quickly generate documentation `sphinx-quickstart <https://www.sphinx-doc.org/en/master/man/sphinx-quickstart.html?highlight=sphinx-quickstart>`_

.. code:: bash

    cd docsource && sphinx-quickstart --no-sep

--no-sep    if specified, create build directory under source directory.

.. code::

  project-name
  ├── README.rst
  ├── .gitignore
  ├── docs
  │   └── .gitkeep
  └── docsource
      ├── _build
      │   └── .gitkeep
      ├── _static
      │   └── .gitkeep
      ├── _templates
      │   └── .gitkeep
      ├── .gitkeep
      ├── conf.py
      ├── index.rst
      ├── make.bat
      └── Makefile


******************************************************
03: Test
******************************************************

.. tip::

    **python** built-in **http.server** to serve local files. More information on **python http.server** 
    can be found here `docs.python.org: http.server <https://docs.python.org/3/library/http.server.html>`_

**one line command**

.. code:: bash

    make -C ./docsource html && python3 -m http.server 8000 --bind 127.0.0.1 --directory ./docsource/_build/html

**or using the step by step approach**

.. code:: bash

    cd ./docsource
    make html
    cd ./_build/html
    python3 -m http.server 8000 --bind 127.0.0.1

Now navigate to `localhost:8000 <http://localhost:8000>`_. 

******************************************************
04: add python module/package
******************************************************

.. code::

    project-name
    ├── .gitignore
    ├── docs/
    │   └── .gitkeep
    ├── docsource/
    │   ├── .gitkeep
    │   ├── _autosummary/           # sphinx `autosummary` extension generated directory/content
    │   ├── _build/
    │   │   ├── doctrees/           # sphinx generated directory/content during the `make html` command
    │   │   └── html/               # sphinx generated directory/content during the `make html` command
    │   ├── _static/
    │   │   └── .gitkeep
    │   ├── _templates/             # templates needed by `autosummary` and `autodocs`
    │   │   ├── .gitkeep
    │   │   ├── class.rst
    │   │   └── module.rst
    │   ├── conf.py
    │   ├── index.rst
    │   ├── make.bat
    │   └── Makefile
    ├── mytoolbox/                  # example python module/package (by JamesALeedham/Sphinx-Autosummary-Recursion)
    │   ├── .DS_Store
    │   ├── __init__.py
    │   ├── mymodule1.py
    │   ├── mymodule2.py
    │   └── mysubpackage/
    │       ├── __init__.py
    │       ├── mymodule3.py
    │       └── mysubsubpackage/
    │           ├── __init__.py
    │           └── mymodule4.py
    └── README.rst


05: Uncomment ``sys.path``
======================================================

From `conf.py` uncomment the following code to allow ``sphinx`` to look in the correct directory 
where the python packages have been saved:

File: `conf.py`

.. code:: python

    import os
    import sys
    sys.path.insert(0, os.path.abspath('..')) 
    # the `..` will move up one directory
    # make sure you are pointing it to the correct relative/absolute path

06: add ``autodoc`` and ``autosummary`` extensions
======================================================

Add the built-in sphinx extensions: **autodoc** and **autosummary** by replacing:
`autodoc` `sphinx-doc.org: autodoc <https://www.sphinx-doc.org/en/master/usage/extensions/autodoc.html>`_
`autosummary` `sphinx-doc.org: autosummary <https://www.sphinx-doc.org/en/master/usage/extensions/autosummary.html>`_


.. code:: python 

    # -- General configuration ---------------------------------------------------

    # Add any Sphinx extension module names here, as strings. They can be
    # extensions coming with Sphinx (named 'sphinx.ext.*') or your custom
    # ones.
    extensions = [
    ]

to:

.. code:: python 

    # -- General configuration ---------------------------------------------------

    # Add any Sphinx extension module names here, as strings. They can be
    # extensions coming with Sphinx (named 'sphinx.ext.*') or your custom
    # ones.
    extensions = [
        'sphinx.ext.autodoc',  # Core Sphinx library for auto html doc generation from docstrings
        'sphinx.ext.autosummary',  # Create neat summary tables for modules/classes/methods etc
    ]
    # Turn on sphinx.ext.autosummary
    autosummary_generate = True


07: create ``autosummary`` templates
======================================================

Sphinx `autosummary <https://www.sphinx-doc.org/en/1.6/ext/autosummary.html>`_ uses Jinja stub page templates files to generate the documentation: 

- autosummary/base.rst – fallback template
- autosummary/module.rst – template for modules
- autosummary/class.rst – template for classes
- autosummary/function.rst – template for functions
- autosummary/attribute.rst – template for class attributes
- autosummary/method.rst – template for class methods

. code:: 

    project-name
    ├── docsource/
    │   ├── _templates/             # templates needed by `autosummary` and `autodocs`
    │   │   ├── class.rst
    │   │   └── module.rst

08: add the ``autosummary`` directive
======================================================

This has been added to the ``index.rst`` page but can be added to any other page.

.. code:: 

    .. autosummary::
    :toctree: _autosummary
    :caption: API Reference
    :template: module.rst
    :recursive:

    mytoolbox

09: modify the `make` file and remove built cache
======================================================

.. code:: bash

    %: Makefile
	rm -rf _build               # remove _build folder cache during the local build test process
	rm -rf _autosummary         # remove _autosummary folder cache during the local build test process
	@$(SPHINXBUILD) -M $@ "$(SOURCEDIR)" "$(BUILDDIR)" $(SPHINXOPTS) $(O)

10: build the docs locally
======================================================

sing **python** built-in ``http.server`` module create a new **make** file in the **root** of the project and add the following command:

.. code:: bash

    # using the makefile inside the docs folder
    # clean the current build then
    # test build docs in local environment and 
    # start python http.server
    live_docs:
        @cp -a ./README.rst ./docsource/README.rst
        make -C ./docsource html && python3 -m http.server 8000 --bind 127.0.0.1 --directory ./docsource/_build/html  

This will:

- copy the latest README.rst file (guide) into the ``docsource`` directory
- run ``make html`` to build the documentation into the ``_build`` directory
- the ``make html`` command is actually saved in the ``./docsource/Makfile``
- open a new python ``http.server`` to show the static files saved in the previous step

To test the new setup you can run ``make live_docs`` from the **root** of the project.

11: Host to GitHub Pages
======================================================

Add the following to the **root Makefile** and run ``make github_docs``:

.. code:: bash

    # manual 
    github_docs:
        rm -rf docs
        mkdir ./docs && touch ./docs/.nojekyll
        @cp -a ./README.rst ./docsource/README.rst
        @make -C ./docsource html
        @cp -a ./docsource/_build/html/. ./docs

    # automatic github action push or pull request
    github_action_docs:
        rm -rf docs
        mkdir docs && touch docs/.nojekyll
        @cp -a README.rst docsource/README.rst
        rm -rf docsource/_build && mkdir docsource/_build 
        rm -rf docsource/_autosummary
        pipx run poetry run sphinx-build -b html docsource docsource/_build/html
        @cp -a docsource/_build/html/* docs

This will:

- remove docs folder cache that might have been previously built to allow for a fresh version 
- add a ``.nojekyll`` file to `github.blog: Bypassing Jekyll on GitHub Pages <https://github.blog/2009-12-29-bypassing-jekyll-on-github-pages/>`_
- copy the latest README.rst file (guide) into the ``docsource`` directory
- run ``make html`` to build the documentation into the ``_build`` directory
- the ``make html`` command is actually saved in the ``./docsource/Makefile``
- copy the newly built ``_build`` static files into ``docs`` required by GitHub Pages

.. important:: 

    After the repository and the **docs** directory have been committed to GitHub go to the repository
    settings and select it as the **Source** for GitHub Pages. 


12: github actions to auto build github pages
******************************************************

.. important:: 

    Use a GitHub Action saved in ``.github/workflows/publish.yml``

.. code:: yaml

    name: github pages  🚀

    on:
    push:
        branches: [main]
    pull_request:
        branches: [main]

    jobs:
    build:
        runs-on: ubuntu-20.04

        steps:
        - uses: actions/checkout@v2
        - name: Set up Python 3.8.5
            uses: actions/setup-python@v2
            with:
            # Semantic version range syntax or exact version of a Python version
            python-version: "3.8.5"
            # Optional - x64 or x86 architecture, defaults to x64
            architecture: "x64"
        # You can test your matrix by printing the current Python version
        - name: Install pipx
            run: python3 -m pip install --user pipx==0.16.0.0
        - name: pipx ensurepath
            run: /home/runner/.local/bin/pipx ensurepath
        - name: pipX PATH
            run: PATH=/home/runner/.local/bin:$PATH
        - name: Install poetry
            run: pipx install poetry==1.1.0
        - name: Test environment
            run: python3 --version ; pip --version ; pipx --version ; pipx run poetry --version ; ls -a ; ls docsource -a
        - name: Install dependencies
            run: pipx run poetry install
        - name: Build website
            run: make github_action_docs
        - name: Commit and Push
            run: |
            git config --global user.name "github-actions[bot]"
            git config --global user.email "41898282+github-actions[bot]@users.noreply.github.com"
            # use account 'github-actions[bot]' to set the git config
            git add docs
            git commit -m "new github pages"
            git push


References
******************************************************

This project has been made possible by using/reading:

- `github.com: JamesALeedham/Sphinx-Autosummary-Recursion <https://github.com/JamesALeedham/Sphinx-Autosummary-Recursion>`_
- `docslikecode.com: Yes You Can Use GitHub Pages with Python Sphinx <https://www.docslikecode.com/articles/github-pages-python-sphinx/>`_
- `github.com: annegentle/create-demo <https://github.com/annegentle/create-demo>`_

Project fork from: https://github.com/foolish-dev/ply_sphinx_docs_github_pages/

