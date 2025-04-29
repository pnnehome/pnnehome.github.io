:html_theme.sidebar_secondary.remove:

:parenttoc: True

.. _data:

Data
============

We share four sample datasets on which one can apply the pre-trained NNE.  You can find them in Matlab formats at `GitHub directory <https://github.com/pnnehome/code_matlab>`_. These datasets are used in :ref:`Wei and Jiang (2025) <papers_data>` and come from sources with direct public access. More detailed descriptions of these datasets can be found in the paper too.


Information of the datasets
''''''''''''''''''''''''''''''


Expedia - destination 1
""""""""""""""""""""""""
This dataset comes from `a Kaggle contest <https://www.kaggle.com/competitions/expedia-personalized-sort/overview>`_ that studies hotel searches and bookings on Expedia.com. It has been used in several papers to study consumer online search behaviors. This dataset here consists of the search sessions for the largest travel destination in this contest. There are :math:`n` = 1258 sessions. There are 3 product attributes, 2 consumer attributes, and 1 advertising attribute.


Expedia - destination 2
""""""""""""""""""""""""
This dataset consists of the search sessions for the second largest travel destination in the same contest as above. There are :math:`n` = 897 sessions, slightly smaller than required by the pre-trained NNE (see :ref:`code <code>`). Nevertheless, in this case the pre-trained NNE seems to work OK despite this small shortfall.


Trivago - desktop channel
"""""""""""""""""""""""""""
This dataset comes from the `ACM RecSys Challenge <https://recsys.trivago.cloud/challenge/dataset/>`_ that analyzes user sessions on Trivago.com. This dataset here consists of the search sessions made on the desktop channel. The setting of Trivago (a meta-search engine) does not exactly fit the standard sequential search model. Nevertheless, we find it a good place to try out the pre-trained NNE.


Trivago - mobile channel
"""""""""""""""""""""""""""
This dataset consists of the search sessions made on the mobile channel from the same source as above.


|

|

.. _papers_data:

Papers
---------------

Wei, Yanhao 'Max' and Zhenling Jiang (2025), "Pretraining Estimators for Structural Models: Application to Consumer Search."

|

|

|

|

