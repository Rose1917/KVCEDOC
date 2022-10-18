Installation
==================================

This part introduce how to install this python library to your system.

Prerequisites
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
* python3.6 python2
* make
* ripgrep

.. note:: 
  is python3.6 necessary? if higher or lower version is acceptable?

  no, sorry to say that. under the current version, some thrid parties are only available in 3.6. we will fix that though. 

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
   ├── requirements.txt //python libraries
   ├── scripts //some utilities scripts
   ├── utils //util module

all the apis you may need are in the kvce.py
