:html_theme.sidebar_secondary.remove:

.. pNNE documentation master file, created by
   sphinx-quickstart on Mon Sep 18 12:08:20 2023.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

.. toctree::
   :maxdepth: 3

.. toctree::
   :hidden:

    Home <home/home>
    Code <code/code>
    Data <data/data>
    Contact <contact/contact>

Welcome to Pre-trained NNE
==========================

.. role:: note-text

(:note-text:`Note\: This website is still under construction. Last update on May 20, 2024.`)

This is the companion website for :ref:`Wei and Jiang (2024b) <papers_home>`. It provides a pre-trained estimator for a consumer search model used in economics & marketing. It is based on a neural net that recognizes search model parameters from data patterns. We call it pre-trained neural net estimator (NNE). The approach extends :ref:`Wei and Jiang (2024a) <papers_home>` and is generally applicable to structural models. But here we focus on a consumer search model.

Below is a brief overview of applying pre-trained NNE to your search data. For Matlab code and documentation, see :ref:`code <code>` page. For examples to try out, see :ref:`data <data>` page.

Overview
------------------

The main function to execute estimation is ``nne_estimate.m``, as shown below.

.. code-block:: console

   result = nne_estimate(nne, Y, Xp, Xa, Xc, consumer_idx)

Explanation for inputs and output: 

* Input ``nne`` stores the trained neural net and some pre-defined settings, available from the file ``trained_nne.mat``.
* Inputs ``Xp``, ``Xa``, ``Xc``, ``Y``, and ``consumer_index`` store your data (more on them below).
* Output ``result`` is a table storing the parameter estimate for the search model.

Below shows an example. The total execution time is 0.78 sec on a laptop. The time includes overheads such as some data sanity checks.

.. code-block:: console

   >>  load('trained_nne.mat', 'nne')
   >>  tic 
   result = nne_estimate(nne, Y, Xp, Xa, Xc, consumer_idx);
   toc
   Elapsed time is 0.784314 seconds.
   >> result
   result = 8×2 table
       name           val            
    ----------      -------
    "\alpha_0"      -6.4546
    "\alpha_1"     -0.11333
    "\eta_0"          5.929
    "\eta_1"      -0.048687
    "\eta_2"        0.22004
    "\eta_3"       0.052894
    "\beta_1"       0.25836
    "\beta_2"      -0.53811

The rest of this page gives more details on the search model, how to format your data, and how to get standard errors.


1) The search model
""""""""""""""""""""""
Sequential search models have been commonly applied in economics & marketing. A consumer faces :math:`J` products (or options). She decides which options to search and which option to buy. Searching an option incurs a search cost but also reveals some utility for that option.  The search model estimated by the pre-trained NNE here is a standard one. The exact specification of it is given in :ref:`Wei and Jiang (2024b) <papers_home>`. Here we give a high-level description:

* A consumer searches and chooses among a list of products, plus an outside good. A free search is endowed (because in most data each consumer searches at least once).
* The list shows some **product attributes** that affect consumer's utility for products. The effects of these attributes are described by coefficients :math:`\beta`.
* There may be some **consumer attributes** that affect consumer's outside utility. The effects of these attributes are described by coefficients :math:`\eta`.
* There may also be **advertising attributes** that affect search costs (but not utility). The effects of these attributes are described by coefficients :math:`\alpha`. 

For a survey on empirical applications of search models, see :ref:`Ursu, Steiler, & Honka (2022) <papers_home>`.


2) Data format
""""""""""""""""""""""
To use the pre-trained NNE, organize data into five arrays: ``Y``, ``Xp``, ``Xa``, ``Xc``, and ``consumer_index``. Respectively, they store search & purchase outcomes, product attributes, advertising attributes, consumer attributes, and consumer identifiers.

Below, we illustrate with an example of shopping at an online retailer. There are :math:`n` = 10,000 consumers and :math:`J` = 15 options per consumer. Below shows a preview of the top rows of  ``consumer_index``, ``Y``, ``Xp``, and ``Xa``. Note there are :math:`nJ` rows in total. We see:

* ``consumer_idx`` is a column vector. Each value indicates the consumer.
* ``Y`` has two columns; the 1st column indicates search and 2nd column indicates purchase. In this example, we see the first consumer searched the 1st, 3rd, and 8th options. She bought the 3rd option. 
* ``Xp`` stores product attributes. In this example, there are two product attributes: a 1-5 product rating and log price. 
* ``Xa`` stores advertising attributes. In this example, there is one advertising attribute: whether the product is highlighted on the retailer website.
* ``Xc`` stores consumer attributes. It is not previewed below. It has only :math:`n` rows, one for each consumer.

.. code-block:: console

   >>  table(consumer_idx, Y, Xp, Xa)
   ans = 150000×4 table
    consumer_idx      Y             Xp          Xa
    ____________    ______    ______________    __
           1        1    0    4      0.67743    1 
           1        0    0    5       1.1052    1 
           1        1    1    1     -0.24542    1 
           1        0    0    5      0.78452    0 
           1        0    0    4      0.10519    0 
           1        0    0    5     0.068463    0 
           1        0    0    4      0.35691    0 
           1        1    0    5      0.61307    0 
           1        0    0    3       1.1809    0 
           1        0    0    5      0.91391    0 
           1        0    0    5     0.054537    0 
           1        0    0    3       1.0015    0 
           1        0    0    5      0.73938    0 
           1        0    0    3    -0.020808    0 
           1        0    0    5      0.23587    0 
           2        0    0    3      0.72159    1 
           2        1    0    5     -0.39847    1 
           2        0    0    5      0.73669    1 
         :            :             :           : 

Below show the dimensions of each variable. Outside this example, if your data do not feature advertising or consumer attributes, you can let ``Xa`` or ``Xc`` be an empty matrix.

.. code-block:: console

   >>  whos Xp Xa Xc Y consumer_idx

       Name               Size             Bytes         Class      Attributes

       Xa                150000x1         1200000        double 
       Xc                 10000x3          240000        double 
       Xp                150000x2         2400000        double              
       consumer_idx      150000x1         1200000        double              
       Y                 150000x2         2400000        double 


3) Bootstrap standard errors
""""""""""""""""""""""""""""""
The ``nne_estimate.m`` function has standard error calculation built in. Simply add ``se = true`` option as shown below. The output will include an additional column of standard errors. The calculation bootstraps 50 samples so execution time will be somewhat longer, but it can take advantage of parallel computing toolbox if installed.

We see :math:`\alpha_1` is negative, indicating that the products highlighted by the retailer enjoy a lower search cost (so they are more likely to be searched). We see :math:`\beta_1` is positive, indicating that consumer utility increases with product rating. We see :math:`\eta_1` is negative, indicating that the outside utility decreases with the first consumer attribute.

.. code-block:: console

    >> result = nne_estimate(nne, Y, Xp, Xa, Xc, consumer_idx, se = true)
    result = 8×3 table

       name          val          se   
    __________    _________    ________
    "\alpha_0"      -6.4546    0.071848
    "\alpha_1"     -0.11333    0.044997
    "\eta_0"          5.929    0.061318
    "\eta_1"      -0.048687    0.011513
    "\eta_2"        0.22004    0.029282
    "\eta_3"       0.052894    0.023437
    "\beta_1"       0.25836    0.004951
    "\beta_2"      -0.53811    0.011032
    
    
|

|

.. _papers_home:

Papers
---------------

Wei, Yanhao 'Max' and Zhenling Jiang (2024a), "Estimating Parameters of Structural Models with Neural Networks," Accepted at Marketing Science. 

Wei, Yanhao 'Max' and Zhenling Jiang (2024b), "A Pre-trained Estimator for Consumer Search Models."

Ursu, Raluca, Stephan Steiler, and Elisabeth Honka (2022), "The Sequential Search Model: A Framework for Empirical Research." 

|

|

|

|
