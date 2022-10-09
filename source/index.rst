.. KVCE2 documentation master file, created by
   sphinx-quickstart on Fri Oct  7 15:57:50 2022.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

KVCE Introduction
=================================
**KVCE** is a linux kernel configuration variable analysis tool which provides the function of kernel configuration extraction and finding the configuration-related code or file.

This project is written in pure python(although it also utilizes some existing advanced binary tools). it is powered by `ripgrep <https://github.com/BurntSushi/ripgrep>`_, `kconfiglib <https://github.com/ulfalizer/Kconfiglib>`_, `cpip <https://cpip.readthedocs.io/en/latest/readme.html>`_.

Features
=================================
* Fast: KVCE has a built-in C/C++preprocessor that has been cut to filter out the logic not required by the task. For a kernel configuration item, it needs no more than 1s to analyze and export the 60w line+kernel source code; In addition, in the process of searching the kernel, we used rg, which is currently the fastest search tool under Linux, and the search speed is an order of magnitude higher than GREP.
* Fullly-featured: The line level accurate analysis can reasonably handle macro nesting, blank lines, comments and other logic, and can output the logical relationship between specific code lines and configuration items.
* Accuracy: For the export of kernel configuration items, KVCE does not use the traditional search method, but uses Kconfiglib for accurate analysis (no need to worry, no need to install Kconfiglib additionally); For analysis logic, CPIP is a preprocessor implemented in strict accordance with ISO C99 standard.
* Clear: KVCE interfaces at different levels provide both end-to-end interfaces and low-level interfaces to obtain intermediate results. The output results are provided in the most common data types, which are clear.

.. note::
   This project is under active development.

Installation
==================================

Prerequisites
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
* python3 python2
* make
* ripgrep

Download
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Directly clone the source from github.

.. code-block:: console

   $git clone https://github.com/Rose1917/KVCE2

File Structure
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The souce structure is as follow:

.. code-block:: console

   ├── build //intermediate files
   ├── cpip  //preprocessor for this task
   ├── issue.txt 
   ├── kvce.py  //main logic
   ├── README.md
   ├── requirements.txt //python libraries
   ├── scripts //some utilities scripts
   ├── utils //util function

.. toctree::
   :maxdepth: 2
   :caption: Contents:


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
