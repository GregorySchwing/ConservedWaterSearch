ConservedWaterSearch
====================

.. image:: https://readthedocs.org/projects/conservedwatersearch/badge/?version=latest
    :target: https://conservedwatersearch.readthedocs.io/en/latest/?badge=latest
.. image:: https://badge.fury.io/py/conservedwatersearch.svg
    :target: https://badge.fury.io/py/conservedwatersearch
.. image:: https://img.shields.io/conda/vn/conda-forge/conservedwatersearch.svg
    :target: https://anaconda.org/conda-forge/conservedwatersearch

The ConservedWaterSearch (CWS) Python library uses density based clustering approach to detect conserved waters from simulation trajectories. First, positions of water molecules are determined based on clustering of oxygen atoms belonging to water molecules(see figure below for more information). Positions on water molecules can be determined using Multi Stage Re-Clustering (MSRC) approach or Single Clustering (SC) approach (see :cite:`conservedwatersearch2022` for more `information on clustering procedures <https://doi.org/10.1021/acs.jcim.2c00801>`_).

.. image:: figs/Scheme.png
  :width: 700

Conserved water molecules can be classified into 3 distinct conserved water types based on their hydrogen orientation: Fully Conserved Waters (FCW), Half Conserved Waters (HCW) and Weakly Conserved Waters (WCW) - see  :cite:`conservedwatersearch2022`  and figure below for examples and more information or see :ref:`conservedwaters:theory, background and methods`.

.. image:: figs/WaterTypes.png
  :width: 700

Both, MSRC and SC can be used with either OPTICS (via `sklearn <https://scikit-learn.org/stable/modules/generated/sklearn.cluster.OPTICS.html>`_) and `HDBSCAN <https://hdbscan.readthedocs.io/en/latest/index.html>`_. MSRC approach using either of the two algorithms produces better quality results at the cost of computational time, while SC approach produces lowe quality results at a fraction of the computational cost.

Important links
===============
	- `Documentation <https://conservedwatersearch.readthedocs.io/en/latest/>`_: hosted on Read The Docs
	- `GitHub repository <https://github.com/JecaTosovic/ConservedWaterSearch>`_: source code/contribute code
	- `Issue tracker <https://github.com/JecaTosovic/ConservedWaterSearch/issues>`_: Report issues/request features

Related Tools
=============
	- `WaterNetworkAnalysis <https://github.com/JecaTosovic/WaterNetworkAnalysis>`_: prepare trajectories for identification of conserved water molecules.

Citation
========
See :cite:`conservedwatersearch2022`.

Installation
============
The easiest ways to install **ConservedWaterSearch** is to install it from conda-forge using :code:`conda`:

.. code:: bash

   conda install -c conda-forge ConservedWaterSearch

CWS can also be installed from PyPi (using :code:`pip`). However, hdbscan which is a hard dependency, requires a C++ compiler to be installed for :code:`pip` installation to work (see :ref:`installation:Installation`). To install via :code:`pip` use:

.. code:: bash

   pip install ConservedWaterSearch

Pymol is the only requirement missing on PyPi and has to be installed either from source or conda. For more information see :ref:`installation:Installation`.

Example
=======
The easiest way to use CWS is by calling :code:`WaterClustering` class. The starting trajectory should be aligned first, and coordinates of water oxygen and hydrogens extracted. See `WaterNetworkAnalysis  <https://github.com/JecaTosovic/WaterNetworkAnalysis>`_ for more information and convenience functions.

.. code:: python

   # imports
   from ConservedWaterSearch.water_clustering import WaterClustering
   from ConservedWaterSearch.utils import get_orientations_from_positions
   # Number of snapshots
   Nsnap = 20
   # load some example - trajectory should be aligned prior to extraction of atom coordinates
   Opos = np.loadtxt("tests/data/testdataO.dat")
   Hpos = np.loadtxt("tests/data/testdataH.dat")
   wc = WaterClustering(nsnaps=Nsnap, save_intermediate_results=False, save_results_after_done=False)
   wc.multi_stage_reclustering(*get_orientations_from_positions(Opos, Hpos))
   print(wc.water_type)
   # "aligned.pdb" should be the snapshot original trajectory was aligned to.
   wc.visualise_pymol(aligned_protein = "aligned.pdb", output_file = "waters.pse")

Sometimes users might want to explicitly classify conserved water molecules. A simple python code can be used to classify waters into categories given an array of 3D oxygen coordinates and their related relative hydrogen orientations:

.. code:: python

   import ConservedWaterSearch.hydrogen_orientation as HO
   # load some example
   orientations = np.loadtxt("tests/data/conserved_sample_FCW.dat")
   # Run classification
   res = HO.hydrogen_orientation_analysis(
        orientations,
   )
   # print the water type
   print(res[0][2])


For more information on preprocessing trajectory data, please refer to the `WaterNetworkAnalysis  <https://github.com/JecaTosovic/WaterNetworkAnalysis>`_.
