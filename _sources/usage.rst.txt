
Usage
=====


Source code
********************

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


Data processing
********************
The data processing procedure is essential for training and prediction. The raw FAST5 files need to be converted into feature files that can be used as input for the model. The data processing scripts are located in the ``./script`` directory. The following steps are required to process the raw FAST5 files.

1. Guppy basecalling
::

    guppy_basecaller -i demo_data/IVT_ac4C -s demo_data/IVT_ac4C_guppy --num_callers 40 --recursive --fast5_out --config rna_r9.4.1_70bps_hac.cfg

2. Multi-fast5 to single-fast5
::
    multi_to_single_fast5 -i demo_data/IVT_ac4C_guppy -s demo_data/IVT_ac4C_guppy_single -t 40 --recursive

3. Tombo resquiggling
::
    tombo resquiggle --overwrite --basecall-group Basecall_1D_000 demo_data/IVT_ac4C_single  demo_data/IVT_DRS_ac4C.reference.fasta --processes 40 --fit-global-scale --include-event-stdev

4. Mapping to reference
::
    cat demo_data/IVT_ac4C_guppy/pass/*.fastq >demo_data/IVT_ac4C.fastq
    minimap2 -ax map-ont demo_data/IVT_DRS_ac4C.reference.fasta demo_data/IVT_ac4C.fastq >demo_data/IVT_ac4C.sam

5. Feature extraction
::
    python script/feature_extraction.py --input demo_data/IVT_ac4C_guppy_single \
        --reference demo_data/IVT_DRS_ac4C.reference.fasta  \
        --sam demo_data/IVT_ac4C.sam \
        --output demo_data/IVT_ac4C.feature.tsv \
        --clip 10 \
        --motif NNCNN


Training from scratch
********************
The de novo training mode in modCnet enables users to train the model from scratch using their own datasets. To train a modCnet model, both modified and modification-free Direct RNA Sequencing (DRS) data are required.

Before training, the raw FAST5 files need to undergo the `data processing procedure <data_preprocessing>`_ . This process generates feature files specific to each modification type. The feature files should follow a naming convention that reflects the modification type they represent::

    |-- data
    |   |-- C.feature.tsv
    |   |-- ac4C.feature.tsv

In order to evaluate the performance during the training process, it is important to have a separate test dataset. Here's a script that randomly splits the feature file into training and test sets::

    python script/train_test_split.py --input_file data/C.feature.tsv --train_file data/C.feature.train.tsv --test_file data/C.feature.test.tsv --train_ratio 0.8
    python script/train_test_split.py --input_file data/ac4C.feature.tsv --train_file data/ac4C.feature.train.tsv --test_file data/ac4C.feature.test.tsv --train_ratio 0.8

To train the modCnet model using labelled training dataset, you can set the ``--run_mode`` argument to "train". This allows the model to be trained from scratch. Test data are required to evaluation the model performance.
::
    python script/modCnet.py --run_mode train \
          --model_type C/ac4C \
          --new_model model/C_ac4C.pkl \
          --train_data_C data/C.feature.train.tsv \
          --train_data_ac4C data/ac4C.feature.train.tsv \
          --test_data_C data/C.feature.test.tsv \
          --test_data_ac4C data/ac4C.feature.test.tsv \
          --epoch 100

The training process can be stopped manually based on the performance on the test set or by setting the maximum number of epochs. You can monitor the performance of the model on the test set during training and decide when to stop based on your desired criteria, such as reaching a certain accuracy or loss threshold. Alternatively, you can set a specific number of epochs as the maximum value for training using the ``-epoch`` argument. This allows the model to train for a fixed number of iterations, regardless of the performance on the test set. After the specified number of epochs, the training process will automatically stop. By providing these options, you have the flexibility to control the training process based on your specific requirements and preferences. The training process should be something like this::
    
    Epoch 2-2 Train acc: 0.853227, Test Acc: 0.801561, time: 0.684026
    Epoch 2-3 Train acc: 0.857492, Test Acc: 0.809284, time: 0.689912
    Epoch 2-4 Train acc: 0.859884, Test Acc: 0.810469, time: 0.695631
    Epoch 2-5 Train acc: 0.863527, Test Acc: 0.812851, time: 0.701268
    Epoch 2-6 Train acc: 0.865912, Test Acc: 0.814036, time: 0.701268





Prediction
********************
Pretained models were saved in directory ``./model``. You can load pretrained models to predict modification for new data by setting the ``--run_mode`` argument to "predict". Before prediction, the raw FAST5 files need to undergo the `data processing procedure <data_preprocessing>`_ ::

    python script/modCnet.py --run_mode predict \
          --pretrained_model model/C_ac4C.pkl \
          --feature_file data/WT.feature.tsv
          --predict_result data/WT.predict.tsv

The prediction result "data/WT.predict.tsv" has the following format::

    transcript_id   site    motif   read_id                                 prediction   probability
    NM_001349947.2  552     AACCA   320a1a8b-7709-4335-8f6a-84f09ba6592a    unmod        0.00014777448
    XM_006720125.3  2437    ACCAG   53dd21de-f74b-44db-baa3-06c68772b7e1    unmod        0.062309794
    NM_001321485.2  498     TGCTG   1f8ce6a2-5fac-4a2f-ae25-0abdb0de412e    unmod        0.17353779
    NM_001199673.2  2972    ATCAA   5781a0c4-ede0-452e-8789-9a43740451ab    unmod        0.26891512
    NM_014364.5     1233    GACAA   47f7b914-a51e-4eab-adb2-e500d8a46fd1    unmod        0.029849814
    NM_001321485.2  515     GCCTC   31fe54e8-7724-40c6-aaa2-025ab5de7754    unmod        0.004975981
    NM_001136267.2  1780    GACTA   62b6ab58-5ee0-4871-95d5-5db66a9c56c7    unmod        0.0018304548
    NM_001143883.4  714     TGCAG   4fb0be9b-9628-46aa-9ba4-40a6456d7d52    unmod        0.1989807
    NM_006012.4     1058    ATCTT   7c7ff067-1ead-4838-97c8-5fca91fdfe8a    unmod        0.06284212



