######################################################
Test Sphinx docs on GitHub Pages
######################################################

******************************************************
Step 01: Project folder
******************************************************

.. important:: 
    
    The project folder structure is based on the concept that actual source code will be hosted 
    inside the repository and therefore in order to achieve separation of concern the documentation 
    and all associated workflow is placed in the **docsource** directory while the built site that GitHub Pages
    will serve is saved in the **docs** directory.

.. code:: 

  project-name
  ├── README.rst
  ├── .gitignore
  └── docs
  │   └── .gitkeep
  └── docsource
      └── .gitkeep

.. important:: 
    
    In order to replicate this project the following have been used: 
    ``anaconda navigator (v1.10.0)``,
    ``python (v3.8.5)``,
    ``sphinx (v3.4.1)``,
    ``poetry (v1.1.0)``
