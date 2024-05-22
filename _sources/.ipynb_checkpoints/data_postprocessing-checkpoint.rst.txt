.. _data_postprocessing:

Data postprocessing
==================================

Transcriptome location to genome location
********************
In modification detection tasks that utilize Direct RNA Sequencing (DRS) data, where sequences are mapped to transcripts, it is often necessary to convert transcriptome locations to genome locations for comparison with other tools or experimental results. The script ``scripts/transcriptome_location_to_genome_location.py`` will take TandemMod prediction file as input and convert the transcriptome locations to genome locations.
::
    usage: transcriptome_location_to_genome_location.py [-h] -i INPUT -o OUTPUT -g GFF
    Convert transcriptome location to genome location.

    optional arguments:
      -h, --help                   show this help message and exit
      -i INPUT, --input INPUT      Input file,transcriptome location.
      -o OUTPUT, --output OUTPUT   Output file, genome location.
      -g GFF, --gff GFF            Annotation file.

    python scripts/transcriptome_location_to_genome_location.py \
        --input predictions.tsv \
        --output predictions_genome_loc.tsv \
        --gff /path/to/your/genome_annotation.gtf

In the above command, replace "predictions.tsv" with the actual TandemMod prediction file you have, and specify the desired output file name using ``--output``. The script will read the input prediction file, convert the transcriptome locations to genome locations, and save the converted predictions in the specified output file. Additionally, for the conversion, the script requires a genome annotation file in GFF (General Feature Format) format. The GFF file contains genomic coordinates and annotations for various features such as genes, exons, and transcripts.

Here is an example to illustrate the transcriptome locations format:
::
    transcript_id           site    motif   read_id                                 prediction   probability
    LOC_Os08g44480.1        218     GACCA   b32995bf-9975-4ff6-86c6-29c900f321d6    C            0.11261273
    LOC_Os08g44480.1        219     ACCAG   b32995bf-9975-4ff6-86c6-29c900f321d6    m5C          0.95669556
    LOC_Os08g44480.1        223     GGCCA   b32995bf-9975-4ff6-86c6-29c900f321d6    C            4.225098e-14
    LOC_Os08g44480.1        224     GCCAC   b32995bf-9975-4ff6-86c6-29c900f321d6    C            6.3074664e-11
    LOC_Os08g44480.1        226     CACCT   b32995bf-9975-4ff6-86c6-29c900f321d6    C            5.365696e-05
    LOC_Os08g44480.1        227     ACCTA   b32995bf-9975-4ff6-86c6-29c900f321d6    C            1.9726056e-06
    LOC_Os08g44480.1        230     TACGA   b32995bf-9975-4ff6-86c6-29c900f321d6    C            0.00010113342
    LOC_Os08g44480.1        233     GACAA   b32995bf-9975-4ff6-86c6-29c900f321d6    C            4.4892527e-11
    LOC_Os08g44480.1        237     AGCTG   b32995bf-9975-4ff6-86c6-29c900f321d6    C            5.053165e-10
    LOC_Os08g44480.1        240     TGCTC   b32995bf-9975-4ff6-86c6-29c900f321d6    C            3.4177435e-16
    LOC_Os08g44480.1        242     CTCTC   b32995bf-9975-4ff6-86c6-29c900f321d6    C            0.031539176
    LOC_Os08g44480.1        244     CTCCG   b32995bf-9975-4ff6-86c6-29c900f321d6    C            0.08256312
    LOC_Os08g44480.1        245     TCCGA   b32995bf-9975-4ff6-86c6-29c900f321d6    C            0.0004769674
    LOC_Os08g44480.1        252     TGCCC   b32995bf-9975-4ff6-86c6-29c900f321d6    C            2.2711574e-30
    LOC_Os08g44480.1        253     GCCCA   b32995bf-9975-4ff6-86c6-29c900f321d6    C            7.629347e-21

Here are the converted results in genome locations.
::
    transcript_id           site    chr     site            motif   prediction   probability
    LOC_Os06g36590.1        826     Chr6    21495252        ATCGG   C            4.780567e-05
    LOC_Os06g36590.1        831     Chr6    21495257        ATCTT   C            3.1403553e-09
    LOC_Os06g36590.1        836     Chr6    21495262        GTCGT   m5C          0.998418
    LOC_Os06g36590.1        850     Chr6    21495276        GTCTG   C            2.6889346e-08
    LOC_Os06g36590.1        853     Chr6    21495279        TGCTA   C            7.5095416e-11
    LOC_Os06g36590.1        856     Chr6    21495282        TACAT   C            0.013431426
    LOC_Os06g36590.1        871     Chr6    21495297        TGCCT   C            2.3783017e-05
    LOC_Os06g36590.1        872     Chr6    21495298        GCCTT   C            8.105786e-09
    LOC_Os06g36590.1        877     Chr6    21495303        TGCTT   C            1.7306347e-12
    LOC_Os06g36590.1        881     Chr6    21495307        TGCCT   C            1.315495e-09
    LOC_Os06g36590.1        882     Chr6    21495308        GCCTT   C            6.700999e-10
    LOC_Os06g36590.1        888     Chr6    21495314        GGCAA   C            4.613933e-09
    LOC_Os06g36590.1        891     Chr6    21495317        AACCT   C            6.412733e-07
    LOC_Os06g36590.1        892     Chr6    21495318        ACCTG   C            2.1830398e-05
    LOC_Os06g36590.1        901     Chr6    21495445        TACAG   C            0.009539205
    LOC_Os06g36590.1        912     Chr6    21495456        TACTT   C            0.3794193
    LOC_Os06g36590.1        916     Chr6    21495460        TGCAG   C            0.08652508
    LOC_Os06g36590.1        922     Chr6    21495466        ATCTA   C            5.3866494e-05



Read-level predictions to site-level predictions
********************
TandemMod provided single-read predictions as well as their modification probabilities. By aggregating all the predictions for each site, we can derive a consensus or summary prediction for that specific genomic location. The script is located under ``scripts/read_level_prediction_to_site_level_prediction.py``
::
    usage: read_level_prediction_to_site_level_prediction.py [-h] -i INPUT -o  OUTPUT
    Convert read-level predictions to site-level predictions.

    optional arguments:
      -h, --help                     show this help message and exit
      -i INPUT, --input INPUT        Input file,transcriptome location.
      -o OUTPUT, --output OUTPUT     Output file, genome location.

    python scripts/read_level_prediction_to_site_level_prediction.py \
        --input read_level_prediction.tsv \
        --output site_level_prediction.tsv

In the given command, please replace "read_level_prediction.tsv" with the converted results obtained from the ``transcriptome_location_to_genome_location.py`` script. Specify the desired output file name using the ``--output`` option. The script will then aggregate the read-level predictions to derive site-level predictions. The resulting predictions will include the count of modified bases, considering the predictions with probability values ranging from 0.5 to 0.95 as the cutoff range. The total base count is located in the last column.
::
    transcriptome_id        site    chr     site            motif   p_0.5     p_0.6     p_0.7     p_0.8     p_0.9     p_0.95    total
    LOC_Os05g41060.1        445     Chr5    24059817        TGCGC   2         2         2         2         2         1         63
    LOC_Os05g41060.1        447     Chr5    24059815        CGCCA   1         0         0         0         0         0         63
    LOC_Os05g41060.1        448     Chr5    24059814        GCCAG   4         4         4         4         2         1         63
    LOC_Os05g41060.1        451     Chr5    24059811        AGCGG   10        8         7         6         3         2         63
    LOC_Os05g41060.1        454     Chr5    24059808        GGCAC   15        14        14        11        10        8         63
    LOC_Os05g41060.1        456     Chr5    24059806        CACTG   2         2         2         2         2         1         63
    LOC_Os05g41060.1        462     Chr5    24059800        TACAT   0         0         0         0         0         0         63
    LOC_Os05g41060.1        465     Chr5    24059797        ATCCA   6         6         6         5         5         3         63
    LOC_Os05g41060.1        466     Chr5    24059796        TCCAG   2         2         2         1         1         0         63
    LOC_Os05g41060.1        471     Chr5    24059791        AGCAC   11        8         7         5         3         2         63
    LOC_Os05g41060.1        473     Chr5    24059789        CACAT   1         1         1         0         0         0         63
    LOC_Os05g41060.1        479     Chr5    24059783        TGCTA   1         1         1         1         0         0         63
    LOC_Os05g41060.1        482     Chr5    24059780        TACCT   5         5         5         4         2         2         63
    LOC_Os05g41060.1        483     Chr5    24059779        ACCTC   5         5         5         5         4         4         63
    LOC_Os05g41060.1        485     Chr5    24059777        CTCTG   4         4         4         3         2         1         63
    LOC_Os05g41060.1        508     Chr5    24059754        GGCTT   2         2         2         1         1         1         63


Futher analysis
********************
The other data processing and plot scripts are located under the `plot <https://github.com/yulab2021/TandemMod/tree/master/results_reproduce>`_  directory.