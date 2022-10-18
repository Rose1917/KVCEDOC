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


`export_config_list` function also provides some useful optional arguments. For example, you can use the `write_file` argument to store the result to a file.

next time, you can load the file directly from the file.

.. code-block:: python

   import kvce
   config_list = kvce.export_config_list('./linux-5.10.22', write_file='./config_list')
   
   # here the config_list_from_file is the same as config_list
   config_list_from_file = kvce.load_config_file('./config_list')

For the detailed usage about this function, see the documentation part.(TODO:ADD REFERENCE)


Find the LINES that are affected by configuration variables
------------------------------------------------------------
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

In this part, we focuses the first case. The API is simple, we will put the emphasis on the explanation on the result of it.

To get lines affected by specific configuration list variable, just call the `analysis_lines` function.

.. code-block:: python

    import kvce
    config_list = ['CONFIG_CC_VERSION_TEXT', 'CONFIG_CC_IS_GCC', 'CONFIG_CC_IS_CLANG']
    kvce.analysis_lines('./linux-5.18.10', config_list)


You will get the result file like the structure as above:

.. code-block:: python

    '''
      1  [
      2   {
      3     "config_name": "CONFIG_CFG80211_CERTIFICATION_ONUS",
      4     "search_res": [
      5     ¦ {
      6     ¦   "path": "./linux-5.18.10/drivers/net/wireless/ath/dfs_pattern_detector.c",
      7     ¦   "extend": "source",
      8     ¦   "hits": [
      9     ¦   ¦ {
     10     ¦   ¦   "line_no": 359,
     11     ¦   ¦   "content": "\tif (!IS_ENABLED(CONFIG_CFG80211_CERTIFICATION_ONUS))"
     12     ¦   ¦ }
     13     ¦   ],
     14     ¦   "report": null
     15     ¦ },
     16     ¦ {
     17     ¦   "path": "./linux-5.18.10/drivers/net/wireless/ti/wl18xx/debugfs.c",
     18     ¦   "extend": "source",
     19     ¦   "hits": [
     20     ¦   ¦ {
     21     ¦   ¦   "line_no": 342,
     22     ¦   ¦   "content": "#ifdef CONFIG_CFG80211_CERTIFICATION_ONUS"
     23     ¦   ¦ },
     24     ¦   ¦ {
     25     ¦   ¦   "line_no": 563,
     26     ¦   ¦   "content": "#ifdef CONFIG_CFG80211_CERTIFICATION_ONUS"
     27     ¦   ¦ }
     28     ¦   ],
     29     ¦   "report": [
     30     ¦   ¦ [
     31     ¦   ¦   [
     32     ¦   ¦   ¦ 343,
     33     ¦   ¦   ¦ 405
     34     ¦   ¦   ],
     35     ¦   ¦   true,
     36     ¦   ¦   "+"
     37     ¦   ¦ ],
     38     ¦   ¦ [
     39     ¦   ¦   [
     40     ¦   ¦   ¦ 564,
     41     ¦   ¦   ¦ 564
     42     ¦   ¦   ],
     43     ¦   ¦   true,
     44     ¦   ¦   "+"
     45     ¦   ¦ ]
     46     ¦   ]
     47     ¦ }
     48     ]
     49   },...
       ]
    '''

