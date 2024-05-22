
Usage
=====


Source code
********************

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



Training from scratch
********************
The de novo training mode in TandemMod enables users to train the model from scratch using their own datasets. To train a TandemMod model, both modified and modification-free Direct RNA Sequencing (DRS) data are required.

Before training, the raw FAST5 files need to undergo the `data processing procedure <data_preprocessing>`_ . This process generates feature files specific to each modification type. The feature files should follow a naming convention that reflects the modification type they represent::

    |-- data
    |   |-- A.feature.tsv
    |   |-- m6A.feature.tsv

In order to evaluate the performance during the training process, it is important to have a separate test dataset. Here's a script that randomly splits the feature file into training and test sets::

    python scripts/train_test_split.py --input_file A.feature.tsv --train_file A_train.feature.tsv --test_file A_test.feature.tsv --train_ratio 0.8
    python scripts/train_test_split.py --input_file m6A.feature.tsv --train_file m6A_train.feature.tsv --test_file m6A_test.feature.tsv --train_ratio 0.8

To train the TandemMod model using labelled training dataset, you can set the ``--run_mode`` argument to "train". This allows the model to be trained from scratch. Test data are required to evaluation the model performance.
::
    python scripts/TandemMod.py --run_mode train \
          --new_model model/m6A.pkl \
          --train_data_mod data/m6A_train.feature.tsv \
          --train_data_unmod data/A_train.feature.tsv \
          --test_data_mod data/m6A_test.feature.tsv \
          --test_data_unmod data/A_test.feature.tsv \
          --epoch 100

The training process can be stopped manually based on the performance on the test set or by setting the maximum number of epochs. You can monitor the performance of the model on the test set during training and decide when to stop based on your desired criteria, such as reaching a certain accuracy or loss threshold. Alternatively, you can set a specific number of epochs as the maximum value for training using the ``-epoch`` argument. This allows the model to train for a fixed number of iterations, regardless of the performance on the test set. After the specified number of epochs, the training process will automatically stop. By providing these options, you have the flexibility to control the training process based on your specific requirements and preferences. The training process should be something like this::
    
    Epoch 2-2 Train acc: 0.853227, Test Acc: 0.801561, time: 0.684026
    Epoch 2-3 Train acc: 0.857492, Test Acc: 0.809284, time: 0.689912
    Epoch 2-4 Train acc: 0.859884, Test Acc: 0.810469, time: 0.695631
    Epoch 2-5 Train acc: 0.863527, Test Acc: 0.812851, time: 0.701268
    Epoch 2-6 Train acc: 0.865912, Test Acc: 0.814036, time: 0.701268


Transfer learning
********************
In transfer learning mode, you can used a pretrained model to retrain the bottom layers to identify new modification. This mode leverages the knowledge acquired by the pretrained model on a large dataset and applies it to a specific target task with potentially limited data. To fine-tune the TandemMod model using other dataset, you can utilize the transfer run mode by setting the ``--run_mode`` argument to "transfer":
::
    python scripts/TandemMod.py --run_mode transfer \
          --pretrained_model model/m6A.pkl \
          --new_model model/m6Am.pkl
          --train_data_mod data/m6Am_train.tsv \
          --train_data_unmod data/A_train.tsv \
          --test_data_mod data/m6Am_test.tsv \
          --test_data_unmod data/A_test.tsv  \
          --epoch 100


Prediction
********************
Pretained models were saved in directory ``./models``. You can load pretrained models to predict modification for new data by setting the ``--run_mode`` argument to "predict". Before prediction, the raw FAST5 files need to undergo the `data processing procedure <data_preprocessing>`_ ::

    python scripts/TandemMod.py --run_mode predict \
          --pretrained_model model/m6A.pkl \
          --feature_file data/WT.feature.tsv
          --predict_result data/WT.predict.tsv

The prediction result "data/WT.predict.tsv" has the following format::

    transcript_id           site    motif   read_id                                 prediction   probability
    LOC_Os06g45000.1        797     AGATG   d6d2430d-c1f4-433b-8cee-e44534baab1e    A            3.0628286e-07
    LOC_Os06g45000.1        807     TGATA   d6d2430d-c1f4-433b-8cee-e44534baab1e    m6A          0.7467682
    LOC_Os06g45000.1        809     ATAGA   d6d2430d-c1f4-433b-8cee-e44534baab1e    A            0.083823845
    LOC_Os06g45000.1        811     AGAAC   d6d2430d-c1f4-433b-8cee-e44534baab1e    A            9.587409e-07
    LOC_Os06g45000.1        812     GAACC   d6d2430d-c1f4-433b-8cee-e44534baab1e    A            0.00015656528
    LOC_Os06g45000.1        815     CCATT   d6d2430d-c1f4-433b-8cee-e44534baab1e    A            0.052929
    LOC_Os06g45000.1        823     TGAAT   d6d2430d-c1f4-433b-8cee-e44534baab1e    A            0.022003165
    LOC_Os06g45000.1        824     GAATC   d6d2430d-c1f4-433b-8cee-e44534baab1e    A            0.020237297
    LOC_Os06g45000.1        834     TTAAT   d6d2430d-c1f4-433b-8cee-e44534baab1e    A            0.015113605
    LOC_Os06g45000.1        835     TAATG   d6d2430d-c1f4-433b-8cee-e44534baab1e    A            4.8967524e-10
    LOC_Os06g45000.1        838     TGAGC   d6d2430d-c1f4-433b-8cee-e44534baab1e    A            0.0012183546




