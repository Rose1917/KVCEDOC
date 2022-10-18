.. KVCE2 documentation master file, created by
   sphinx-quickstart on Fri Oct  7 15:57:50 2022.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

KVCE Introduction
=================================
**KVCE** is a linux kernel (Kbuild) configuration analysis tool which mainly provides the relationship between the kernel configuration and linux source code or file.

This project is written in pure python(although it also utilizes some existing advanced binary tools). it is powered by `ripgrep <https://github.com/BurntSushi/ripgrep>`_, `kconfiglib <https://github.com/ulfalizer/Kconfiglib>`_, `cpip <https://cpip.readthedocs.io/en/latest/readme.html>`_.

Features
=================================
* 🚀Fast: KVCE has a set of built-in tools to speed up the analysis process. A C/C++preprocessor that has been cut to filter out the logic not required by the task. For a kernel configuration item, it needs no more than 1s to analyze and export the 60w line+kernel source code; In addition, in the process of searching the kernel, we used rg, which is currently the fastest search tool under Linux, and the search speed is an order of magnitude higher than GREP.
* 💎Fullly-featured: The line level accurate analysis can reasonably handle macro nesting, blank lines, comments and other logic, and can output the logical relationship between specific code lines and configuration items.
* ✨Accuracy: For the export of kernel configuration items, KVCE does not use the traditional search method, but uses Kconfiglib for accurate analysis (no need to worry, no need to install Kconfiglib additionally); For analysis logic, CPIP is a preprocessor implemented in strict accordance with ISO C99 standard.
* 🌲Clear: KVCE interfaces at different levels provide both end-to-end interfaces and low-level interfaces to obtain intermediate results. The output results are provided in the most common data types, which are clear.

.. note::
   This project is under active development.


   
   


Indices and tables
==================
Below is the overview of this documentation. In the install secion, we mainly introduce the prerequistes and some points of attention. In the get started part, the most important apis are described. For a full description of the api, see the documentation part.

.. toctree::

    Installation
    Get Started
    Documentation
