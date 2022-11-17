Get Started
======================

In this section, we will introduce the main the features of this project step by step. 

we arrange this section by functions. That is, in each part of this section, we finish a task concerning with the configuration of the linux. 

import
---------------------
before everything begins, import the package.

.. code-block:: python

   import kvce


please make sure you can import the lib successfully.


linux kernel configuration variable
------------------------------------------------------------
The Linux kernel configuration is usually found in the kernel source in the file: /usr/src/linux/.config . It is not recommended to edit this file directly but to use one of these configuration options:

* make config - starts a character based questions and answer session

* make menuconfig - starts a terminal-oriented configuration tool (using ncurses)

* make xconfig - starts a X based configuration tool

After you run the commands above, you will get .config file which contains a set of configuration varibles set. In other words, the configuration variables are just like "switches" which control what will be compiled into the linux kernel or kernel module.

For more information about the linux configuration variable. See `here <https://tldp.org/HOWTO/SCSI-2.4-HOWTO/kconfig.html>`_





export the linux kernel configuration items
------------------------------------------------------------
One of the most basic functions provided by KVCE is to export all configuration items of a certain kernel version which is required by many downstream tasks. 

.. note::
   If you want to know where to download the source code of the Linux kernel, you can refer to this `website <https://mirrors.edge.kernel.org/pub/linux/kernel/>`_



.. code-block:: python

   import kvce
   config_list = kvce.export_config_list('./linux-5.10.22')

the `export_config_list` function return a list of string.

.. code-block:: python
   
   # the code above is omitted
   config_list = kvce.export_config_list('./linux-5.10.22')
   print(config_list)
   '''output:
   ['CONFIG_GCC','CONFIG_IS_GCC','CONFIG_STACKINIT'...]
   '''

.. note::
   This function will automatically download the kbuildlib, so make sure the network is connected.


`export_config_list` function also provides some useful optional arguments. For example, you can use the `write_file` argument to store the result to a file.

next time, you can load the file directly from the file.

.. code-block:: python

   import kvce
   config_list = kvce.export_config_list('./linux-5.10.22', write_file='./config_list')
   
   # here the config_list_from_file is the same as config_list
   config_list_from_file = kvce.load_config_file('./config_list')

For the detailed usage about this function, see the documentation part.(TODO:ADD REFERENCE)


How the Linux Kernel Configuration Affect The Process Of Kernel Build
------------------------------------------------------------------------
This part focuses how these CONFIG_XXX configuration variables affect the process of kernel process?

Generally these configuration variables affect the kernel in two ways:

* it affect the C files preprocess:

.. code-block:: c

   #ifdef CONFIG_FOO
   //some statements here 
   #endif

* it decides what files will be passed to compiler:

.. code-block:: makefile

   #drivers/isdn/i4l/Makefile
   # Makefile for the kernel ISDN subsystem and device drivers.
   # Each configuration option enables a list of files.
   obj-$(CONFIG_ISDN_I4L)         += isdn.o
   obj-$(CONFIG_ISDN_PPP_BSDCOMP) += isdn_bsdcomp.o

In-Line Analysis
--------------------------------------------------------------------------

In this part, we focuses the first case. The API is simple, we will put the emphasis on the explanation on the result of it.

To get lines affected by specific configuration list variable, just call the `analysis_in_line` function.

.. code-block:: python

    import kvce
    config_list = ['CONFIG_CC_VERSION_TEXT', 'CONFIG_CC_IS_GCC', 'CONFIG_CC_IS_CLANG']
    kvce.analysis_in_line('./linux-5.18.10', config_list)


You will get the result file like the structure as above:

.. code-block:: python


  '''
  {
      "CONFIG_FOO":{
      ¦   "config_name":"CONFIG_FOO",
      ¦   "SEARCH_RES":[
      ¦   ¦   {
      ¦   ¦   ¦   "PATH":"path/to/specific/file",
      ¦   ¦   ¦   "EXTEND":"source"
      ¦   ¦   ¦   "REPORT":[
      ¦   ¦   ¦   ¦   ((LINE_BEGIN,LINE_END), FULLLY, "DEP_STR"),
      ¦   ¦   ¦   ¦   ((line_begin,line_end), True, "DEP_STR"),
      ¦   ¦   ¦   ¦   ...
      ¦   ¦   ¦   ]
      ¦   ¦   },
      ¦   ¦   {
      ¦   ¦   ¦   "path":"path/to/another/file",
      ¦   ¦   ¦   "extend":"source"
      ¦   ¦   ¦   "report":[
      ¦   ¦   ¦   ¦   ((line_begin,line_end), True, "dep_str"),
      ¦   ¦   ¦   ¦   ((line_begin,line_end), True, "dep_str"),
      ¦   ¦   ¦   ¦   ...
      ¦   ¦   ¦   ]
      ¦   ¦   },
      ¦   ]
      },

      "CONFIG_BAR":...
  }
  '''

Now, I will try to explain the code snippest above.
* SEARCH_RES: a list of analysis results, each result of it is a file object.

* EXTEND: the file format extension of the file.

* REPORT: a list of analysis results for file PATH, each result of it is a code snippest analysis information. 
 
* LINE_BEGIN, LINE_END: the code snippest range, [LINE_BEGIN, LINE_END]

* FULLY: a boolean value which denotes if the code snippest is only affected by the current configuration.

* DEP_STR: if the fully value is True, this field will be "+" or "-", which means the positive correlation and negative correlation respectively. 




File-Level Analysis
--------------------------------------------------------------------------

In this part, we focuses the second case, that is, try to decide what files are affected by specific configuration items in file-level. In this level, the configuration usually decides if to include specific files to the kernel. 

Similarly, to get file-level analysis affected by specific configuration list variable, just call the `analysis_makefile` function.

.. code-block:: python

    import kvce
    config_list = ['CONFIG_CC_VERSION_TEXT', 'CONFIG_CC_IS_GCC', 'CONFIG_CC_IS_CLANG']
    kvce.analysis_makefile('./linux-5.18.10', config_list)


You will get the result as below. The structure of the result json is clear, but it is worth noting that the result is a dictionary  which uses the CONFIGURATION ITEM string as key.

.. code-block:: python

  '''
  CONFIG_BASED:
  {
      "CONFIG_FOO":['./linux-5.8.10/mm/x.c', './linux-5.8.10/mm/y.c'...  ],
      "CONFIG_BAR":['./linux-5.8.10/mm/x.c', './linux-5.8.10/mm/y.c'...  ],
  }
  '''


Top-Level API
--------------------------------------------------------------------------

KVCE also provides top-level API which combines the result of in-line result and makefile result.

To get top-level analysis result, just call  `analysis` function.

.. code-block:: python

    import kvce
    config_list = ['CONFIG_CC_VERSION_TEXT', 'CONFIG_CC_IS_GCC', 'CONFIG_CC_IS_CLANG']
    kvce.analysis('./linux-5.18.10', config_list)


.. code-block:: python

   '''
   {
      "CONFIG_FOO":{
      ¦   "config_name":"CONFIG_FOO",
      ¦   "search_res":[
      ¦   ¦   {
      ¦   ¦   ¦   "path":"path/to/specific/file",
      ¦   ¦   ¦   "extend":"source"
      ¦   ¦   ¦   "report":[
      ¦   ¦   ¦   ¦   ((line_begin,line_end), True, "dep_str"),
      ¦   ¦   ¦   ¦   ((line_begin,line_end), True, "dep_str"),
      ¦   ¦   ¦   ¦   ...
      ¦   ¦   ¦   ]
      ¦   ¦   },
      ¦   ¦   {
      ¦   ¦   ¦   "path":"path/to/another/file",
      ¦   ¦   ¦   "extend":"source"
      ¦   ¦   ¦   "report":[
      ¦   ¦   ¦   ¦   ((line_begin,line_end), True, "dep_str"),
      ¦   ¦   ¦   ¦   ((line_begin,line_end), True, "dep_str"),
      ¦   ¦   ¦   ¦   ...
      ¦   ¦   ¦   ]
      ¦   ¦   },
      ¦   ],
      ¦   "file_level":["/path/to/specific/file", "path/to/another/file  "]

      },

      "CONFIG_BAR":...
  }
  '''

File-Based
--------------------------------------------------------------------------

The result above are all set "CONFIG_XXX" as the dictionary key. KVCE also provides the FILE-Based result.

To get file-based  analysis result, just call the functions above with key argument `file_based` set True. 

For example, when getting the makefile analysis result, if you want file-based format(that is, using the file path as key)

.. code-block:: python

    import kvce
    config_list = ['CONFIG_CC_VERSION_TEXT', 'CONFIG_CC_IS_GCC', 'CONFIG_CC_IS_CLANG']
    kvce.analysis_makefile('./linux-5.18.10', config_list, file_based=True)


The result format is as follow

.. code-block:: python

  '''
  {
    "path/to/specific/file":{
     ¦   ¦   "configs":['CONFIG_XXX', 'CONFIG_BAR', 'CONFIG_REP']),
     ¦   ¦   "configs_str":"CONFIG_XXX && CONFIG_BAR || CONFIG_REP"
     },

    "path/to/specific/file":{
     ¦   ¦   "configs":['CONFIG_XXX', 'CONFIG_BAR', 'CONFIG_REP']),
     ¦   ¦   "configs_str":"CONFIG_XXX && CONFIG_BAR || CONFIG_REP"
     },

   }

  '''
 
Similarly, the `analysis_inline` function will return the object as below when you set `file_based` as True.

.. code-block:: python


  '''
  {
      "./linux-5.10.12/mm/backing-dev.c":{
      ¦   "inline_level":
      ¦   ¦   [
      ¦   ¦   ¦   {
      ¦   ¦   ¦   ¦   "line_range":[begin, end]
      ¦   ¦   ¦   ¦   "configs":['CONFIG_XXX', 'CONFIG_YYY']
      ¦   ¦   ¦   ¦   "reports":['xxx', 'xxx']
      ¦   ¦   ¦   },
      ¦   ¦   ¦   {
      ¦   ¦   ¦   ¦   "line_range":[begin, end]
      ¦   ¦   ¦   ¦   "configs":['CONFIG_XXX', 'CONFIG_YYY']
      ¦   ¦   ¦   ¦   "reports":['xxx', 'xxx']
      ¦   ¦   ¦   },
      ¦   ¦   ]
      },
      ...
  }
 '''

And call the top-level function with `file_based` set True.


.. code-block:: python

  '''
  {
      "./linux-5.10.12/mm/backing-dev.c":{
      ¦   "file_level":['CONFIG_XXX', 'CONFIG_YYY']
      ¦   "file_level_str":"CONFIG_A && CONFIG_B"
      ¦   "inline_level":
      ¦   ¦   [
      ¦   ¦   ¦   {
      ¦   ¦   ¦   ¦   "line_range":[begin, end]
      ¦   ¦   ¦   ¦   "configs":['CONFIG_XXX', 'CONFIG_YYY']
      ¦   ¦   ¦   ¦   "reports":['xxx', 'xxx']
      ¦   ¦   ¦   },
      ¦   ¦   ¦   {
      ¦   ¦   ¦   ¦   "line_range":[begin, end]
      ¦   ¦   ¦   ¦   "configs":['CONFIG_XXX', 'CONFIG_YYY']
      ¦   ¦   ¦   ¦   "reports":['xxx', 'xxx']
      ¦   ¦   ¦   },
      ¦   ¦   ]
      },
      ...
  }
  '''

Output Format Choice
-----------------------------------------------------------
For some reason(like to store the intermediate result), you may want to get the different output format. We have provided the json format result with an optional argument json_format(Boolean value). By default, it is false, you can set it True to get the json format result.

For instance:

.. code-block:: python

    import kvce
    config_list = ['CONFIG_CC_VERSION_TEXT', 'CONFIG_CC_IS_GCC', 'CONFIG_CC_IS_CLANG']
    kvce.analysis_lines('./linux-5.18.10', config_list, json_format=True)


You will get the result of json string format. You can easily to store it to a file.
 
This optional argument is supported by the `analysis/analysis_lines/analysis_makefile` apis.
