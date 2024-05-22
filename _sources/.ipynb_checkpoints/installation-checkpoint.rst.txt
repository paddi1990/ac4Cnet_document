.. _installation:

Installation
==================================
The following modules are needed to run TandemMod.


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

After installing all the essential packages,  reset the environment's state by deactivating and reactivating the environment:
::
    conda deactivate
    conda activate TandemMod

We have also provided a yaml file in the repository so you can install the dependencies through the configuration file::

    conda env create -f TandemMod.yaml


The source code and data processing scripts are available on `GitHub <https://github.com/yulab2021/TandemMod>`_. You can download them by using the git clone command::

    git clone https://github.com/yulab2021/TandemMod.git

TandemMod offers three modes: de novo training, transfer learning, and prediction. Researchers can train from scratch, fine-tune pre-trained models, or apply existing models for predictions. It provides a user-friendly solution for studying RNA modifications.

In the provided repository, the pretrained models are located under the ``./models`` directory, and the data processing scripts and the main script are located under the ``./scripts`` directory:: 

    .
    ├── data
    │   ├── A_test.tsv
    │   ├── A_train.tsv
    │   ├── m5C
    │   ├── m6A
    │   ├── m6A_test.tsv
    │   └── m6A_train.tsv
    ├── demo
    │   ├── fast5
    │   │   └── batch_0.fast5
    │   ├── files.txt
    │   ├── guppy
    │   │   ├── fail
    │   │   │   └── fastq_runid_71d544d3bd9e1fe7886a5d176c756a576d30ed50_0_0.fastq
    │   │   ├── guppy_basecaller_log-2023-06-06_09-58-28.log
    │   │   ├── pass
    │   │   │   └── fastq_runid_71d544d3bd9e1fe7886a5d176c756a576d30ed50_0_0.fastq
    │   │   ├── sequencing_summary.txt
    │   │   ├── sequencing_telemetry.js
    │   │   └── workspace
    │   │       └── batch_0.fast5
    ├── models
    │   ├── hm5C_transfered_from_m5C.pkl
    │   ├── m1A_train_on_rice_cDNA.pkl
    │   ├── m5C_train_on_rice_cDNA.pkl
    │   ├── m6A_train_on_rice_cDNA.pkl
    │   ├── m7G_transfered_from_m5C.pkl
    │   ├── psU_transfered_from_m5C.pkl
    │   ├── test.model
    │   └── test.pkl
    ├── plot
    ├── README.md
    ├── scripts
    │   ├── extract_feature_from_signal.py
    │   ├── extract_signal_from_fast5.py
    │   ├── __init__.py
    │   ├── models.py
    │   ├── TandemMod.py
    │   ├── train_test_split.py
    │   ├── transcriptome_loci_to_genome_loci.py
    │   └── utils.py
    └── TandemMod.yaml

