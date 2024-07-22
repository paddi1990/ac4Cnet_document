.. _installation:

Installation
==================================
The following modules are needed to run modCnet.


.. list-table:: Required modules
   :widths: 50 50
   :header-rows: 1

   * - module
     - version
   * - minimap2
     - 2.17-r941
   * - python 
     - 3.7.12
   * - h5py
     - 3.7.0
   * - statsmodels
     - 0.10.0
   * - joblib 
     - 0.16.0
   * - scikit-learn
     - 0.22
   * - torch
     - 1.9.1
   * - guppy
     - 6.1.5
   * - ont-tombo
     - 1.5.1
   * - ont_vbz_hdf_plugin
     - 1.0.1
   * - ont-fast5-api
     - 4.1.1
   * - numpy
     - 1.19.5

Conda is recommended for package management, you can create a new conda environment and then install the packages. Here's an example of how you can do it. Create a new conda environment::
    
    conda create -n TandemMod python=3.7.12

Activate the newly created environment::

    conda activate TandemMod

Install the required modules::

    conda config --add channels conda-forge
    conda config --add channels bioconda

    conda install -c conda-forge scipy=1.7.0
    conda install -c bioconda minimap2=2.17
    conda install -c conda-forge numpy=1.19.5
    conda install -c anaconda h5py=3.7.0
    conda install -c conda-forge joblib=0.16.0
    conda install -c anaconda scikit-learn=0.22
    conda install -c bioconda ont-tombo=1.5.1
    conda install -c bioconda ont_vbz_hdf_plugin=1.0.1
    conda install -c bioconda ont-fast5-api=4.1.1
    conda install -c conda-forge statsmodels=0.10.0
    pip install torch==1.9.1

Or, some of the modules can be installed by pip::

    pip install numpy==1.19.5
    pip install h5py==3.7.0
    pip install statsmodels==0.10.0
    pip install joblib==0.16.0
    pip install scikit-learn==0.22
    pip install ont-tombo==1.5.1
    pip install ont-fast5-api==4.1.1
    pip install scipy==1.7.0

Guppy can be obtained from `Oxford Nanopore Technologies <https://nanoporetech.com/>`_ or from this `mirror <https://mirror.oxfordnanoportal.com/software/analysis/ont-guppy-cpu-6.1.5-1.el7.x86_64.rpm>`_. Install Guppy using dpkg::

    alien ont-guppy-cpu-6.1.5-1.el7.x86_64.rpm
    dpkg -i ont-guppy-cpu-6.1.5-1.el7.x86_64.deb

``libhdf5`` and ``libcrypto`` are required for running guppy.

The entire installation will take about 10 minutes. After installing all the essential packages,  reset the environment's state by deactivating and reactivating the environment:
::
    conda deactivate
    conda activate modCnet

We have also provided a yaml file in the repository so you can install the dependencies through the configuration file::

    conda env create -f modCnet.yaml


The source code and data processing scripts are available on `GitHub <https://github.com/yulab2021/modCnet>`_. You can download them by using the git clone command::

    git clone https://github.com/yulab2021/modCnet.git


In the provided repository, the pretrained models are located under the ``./model`` directory, and the data processing scripts and the main script are located under the ``./script`` directory:: 


  .
  ├── modCnet.yaml
  ├── data
  │   ├── event_level_features_C_base_quality.csv
  │   ├── event_level_features_C_length.csv
  │   ├── event_level_features_C_mean.csv
  │   ├── event_level_features_C_median.csv
  │   ├── event_level_features_C_std.csv
  │   ├── IVT_transcripts_ac4C.csv
  │   ├── IVT_transcripts_C.csv
  │   ├── IVT_transcripts_m5C.csv
  │   ├── qPCR_curve_4.13_1.csv
  │   ├── qPCR_curve_4.13_2.csv
  │   └── Rn_cycle_curve.csv
  ├── demo_data
  │   ├── ac4C.feature.test.tsv
  │   ├── ac4C.feature.train.tsv
  │   ├── C.feature.test.tsv
  │   ├── C.feature.train.tsv
  │   ├── GRCh38_subset_reference.fa
  │   ├── HeLa
  │   ├── IVT_DRS.reference.fasta
  │   ├── IVT_fast5
  │   ├── IVT_fast5_guppy
  │   ├── IVT_fast5_guppy_single
  │   ├── IVT.fastq
  │   ├── IVT.feature
  │   ├── IVT.sam
  │   ├── m5C.feature.test.tsv
  │   ├── m5C.feature.train.tsv
  │   ├── model
  │   └── test.feature.tsv
  ├── docs
  │   └── test
  ├── model
  │   ├── C_ac4C.pkl
  │   ├── C_m5C_ac4C.pkl
  │   ├── C_m5C.pkl
  │   └── m5C_ac4C.pkl
  ├── README.md
  ├── results_reproduce
  │   └── figure1_script.ipynb
  └── script
      ├── modCnet.py
      ├── feature_extraction.py
      ├── __init__.py
      ├── model.py
      ├── models.py
      ├── __pycache__
      ├── read_level_prediction_to_site_level_prediction.py
      ├── transcriptome_location_to_genome_location.py
      └── utils.py


