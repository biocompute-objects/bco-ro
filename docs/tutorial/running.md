---
title: Running the workflow
sort: 3
---



## Running the workflow

We'll run the workflow from within the `data/` directory that will contain the BagIt _Payload files_ 
with the outputs of the workflows:

```sh
mkdir -p chipseq_20200924/data
cd chipseq_20200924/data
```

We're going to run the workflow [nf-core/chipseq](https://nf-co.re/chipseq). Nextflow 
allows running of [nf-core](https://nf-co.re) workflows directly by their GitHub short-name.

### Recording

We'll use the `test` profile that runs with preconfigured test data, for using your own inputs
see the [workflow usage](https://nf-co.re/chipseq/1.2.2/docs/usage) instructions.

Because our BCO should be reproducible we need to be concious about which *version of the workflow*
we are using. At time of writing the latest version is `1.2.2`. 

If you are using Docker/Singularity containers instead of Conda, 
change the below `-profile test,conda` to 
`-profile test,docker` or `-profile test,singularity` accordingly.

```sh
$ nextflow run nf-core/chipseq -revision 1.2.2 -profile test,conda

N E X T F L O W  ~  version 20.07.1
Launching `nf-core/chipseq` [backstabbing_hawking] - revision: 6924b66942 [1.2.2]
----------------------------------------------------
                                        ,--./,-.
        ___     __   __   __   ___     /,-._.--~'
  |\ | |__  __ /  ` /  \ |__) |__         }  {
  | \| |       \__, \__/ |  \ |___     \`-._,-`-,
                                        `._,._,'
  nf-core/chipseq v1.2.2
----------------------------------------------------
Run Name            : backstabbing_hawking
Data Type           : Paired-End
Design File         : https://raw.githubusercontent.com/nf-core/test-datasets/chipseq/design.csv
Genome              : Not supplied
Fasta File          : https://raw.githubusercontent.com/nf-core/test-datasets/atacseq/reference/genome.fa
GTF File            : https://raw.githubusercontent.com/nf-core/test-datasets/atacseq/reference/genes.gtf
MACS2 Genome Size   : 1.2E+7
Min Consensus Reps  : 1
…
```

The workflow will take a while to run. If you previously skipped ahead, now go back to create the [skeleton BCO](#skeleton-bco)

We notice already nf-core reporting inputs of reference datasets. We'll record these in the BCO as part of the `execution_domain`:

```json
{"execution_domain": {

        "external_data_endpoints": [
          {"name": "Experiment design file for minimal test dataset",
           "url": "https://raw.githubusercontent.com/nf-core/test-datasets/chipseq/design.csv"
          },
          {"name": "iGenomes R64-1-1 Ensembl (Fasta sequence)",
           "url": "https://raw.githubusercontent.com/nf-core/test-datasets/atacseq/reference/genome.fa"
          },
          {"name": "iGenomes R64-1-1 Ensembl (GTF Genes)",
           "url": "https://raw.githubusercontent.com/nf-core/test-datasets/atacseq/reference/genes.gtf"
          }
    }
}
```

As the workflow progresses, Nextflow reports status per process:

```
executor >  local (97)
[a9/82ebb7] process > CHECK_DESIGN (design.csv)                      [100%] 1 of 1 ✔
[51/2105b9] process > BWA_INDEX (genome.fa)                          [100%] 1 of 1 ✔
[55/6ae158] process > MAKE_GENE_BED (genes.gtf)                      [100%] 1 of 1 ✔
[0f/984a21] process > MAKE_GENOME_FILTER (genome.fa)                 [100%] 1 of 1 ✔
[af/85c0f1] process > FASTQC (SPT5_T15_R2_T1)                        [100%] 6 of 6 ✔
[d1/34ec81] process > TRIMGALORE (SPT5_T15_R1_T1)                    [100%] 6 of 6 ✔
[17/ae37aa] process > BWA_MEM (SPT5_T15_R1_T1)                       [100%] 6 of 6 ✔
[25/530a51] process > SORT_BAM (SPT5_T15_R1_T1)                      [100%] 6 of 6 ✔
[76/3defb1] process > MERGED_BAM (SPT5_T15_R1)                       [100%] 6 of 6 ✔
[2e/3fcd78] process > MERGED_BAM_FILTER (SPT5_T15_R1)                [100%] 6 of 6 ✔
[ca/14e2bf] process > MERGED_BAM_REMOVE_ORPHAN (SPT5_T15_R1)         [100%] 6 of 6 ✔
[f1/1da873] process > PRESEQ (SPT5_T15_R1)                           [100%] 6 of 6, failed: 2 ✔
[99/6ff523] process > PICARD_METRICS (SPT5_T15_R1)                   [100%] 6 of 6 ✔
[b5/572a3a] process > BIGWIG (SPT5_T15_R1)                           [100%] 6 of 6 ✔
[0a/7b400e] process > PLOTPROFILE (SPT5_T15_R1)                      [100%] 6 of 6 ✔
[18/d0b673] process > PHANTOMPEAKQUALTOOLS (SPT5_T15_R1)             [100%] 6 of 6 ✔
[3a/d9d296] process > PLOTFINGERPRINT (SPT5_T15_R1 vs SPT5_INPUT_R1) [100%] 4 of 4 ✔
[29/2f9f0b] process > MACS2 (SPT5_T15_R1 vs SPT5_INPUT_R1)           [100%] 4 of 4 ✔
[f6/a50dd6] process > MACS2_ANNOTATE (SPT5_T15_R1 vs SPT5_INPUT_R1)  [100%] 4 of 4 ✔
[4e/ac1c7b] process > MACS2_QC                                       [100%] 1 of 1 ✔
[19/786250] process > CONSENSUS_PEAKS (SPT5)                         [100%] 1 of 1 ✔
[70/52a6a1] process > CONSENSUS_PEAKS_ANNOTATE (SPT5)                [100%] 1 of 1 ✔
[9d/4b5051] process > CONSENSUS_PEAKS_COUNTS (SPT5)                  [100%] 1 of 1 ✔
[7f/34cd6c] process > CONSENSUS_PEAKS_DESEQ2 (SPT5)                  [100%] 1 of 1 ✔
[a1/b86eb7] process > IGV                                            [100%] 1 of 1 ✔
[78/8d9786] process > get_software_versions                          [100%] 1 of 1 ✔
[6d/7aa2ec] process > MULTIQC (1)                                    [100%] 1 of 1 ✔
[12/a75060] process > output_documentation                           [100%] 1 of 1 ✔
-Warning, pipeline completed, but with errored process(es) -
-Number of ignored errored process(es) : 2 -
-Number of successfully ran process(es) : 95 -
-[nf-core/chipseq] Pipeline completed successfully-
WARN: To render the execution DAG in the required format it is required to install Graphviz -- See http://www.graphviz.org for more info.
Completed at: 23-Apr-2021 12:11:58
Duration    : 2h 4s
CPU hours   : 2.7 (0.6% failed)
Succeeded   : 95
Ignored     : 2
Failed      : 2
```

After execution our `data/results` folder will be populated with the result files, as marked as output in the Nextflow workflow definition. There will be additional files in the intermediate working directory under `data/work` which we'll not publish as part of our BCO or RO-Crate, as they are mostly duplicates of the results.


### Listing steps

The BCO should list the individual steps executed in the workflow, ideally with their individual inputs and outputs identified. As we see above, some of these steps are executed multiple times in iterations.

As a first step towards representing the workflow steps in BCO we list the `name` corresponding directly to the report above. 

        "pipeline_steps": [
            {"step_number": 1, "name": "CHECK_DESIGN", "description": "", "input_list": [], "output_list": []},
            {"step_number": 2, "name": "output_documentation", "description": "", "input_list": [], "output_list": []},
            {"step_number": 3, "name": "MAKE_GENE_BED", "description": "", "input_list": [], "output_list": []},
            {"step_number": 5, "name": "get_software_versions", "description": "", "input_list": [], "output_list": []},
            {"step_number": 6, "name": "BWA_INDEX", "description": "", "input_list": [], "output_list": []},
            {"step_number": 7, "name": "MAKE_GENOME_FILTER", "description": "", "input_list": [], "output_list": []},
            {"step_number": 8, "name": "FASTQC", "description": "", "input_list": [], "output_list": []},
            {"step_number": 9, "name": "TRIMGALORE", "description": "", "input_list": [], "output_list": []},
            {"step_number": 10, "name": "BWA_MEM", "description": "", "input_list": [], "output_list": []},
            {"step_number": 11, "name": "SORT_BAM", "description": "", "input_list": [], "output_list": []},
            {"step_number": 12, "name": "MERGED_BAM", "description": "", "input_list": [], "output_list": []},
            {"step_number": 13, "name": "PRESEQ", "description": "", "input_list": [], "output_list": []},
            {"step_number": 14, "name": "MERGED_BAM_FILTER", "description": "", "input_list": [], "output_list": []},
            {"step_number": 15, "name": "MERGED_BAM_REMOVE_ORPHAN", "description": "", "input_list": [], "output_list": []},
            {"step_number": 16, "name": "PHANTOMPEAKQUALTOOLS", "description": "", "input_list": [], "output_list": []},
            {"step_number": 17, "name": "BIGWIG", "description": "", "input_list": [], "output_list": []},
            {"step_number": 18, "name": "PICARD_METRICS", "description": "", "input_list": [], "output_list": []},
            {"step_number": 19, "name": "PLOTFINGERPRINT", "description": "", "input_list": [], "output_list": []},
            {"step_number": 20, "name": "MACS2", "description": "", "input_list": [], "output_list": []},
            {"step_number": 21, "name": "PLOTPROFILE", "description": "", "input_list": [], "output_list": []},
            {"step_number": 22, "name": "MACS2_ANNOTATE", "description": "", "input_list": [], "output_list": []},
            {"step_number": 23, "name": "CONSENSUS_PEAKS", "description": "", "input_list": [], "output_list": []},
            {"step_number": 24, "name": "CONSENSUS_PEAKS_COUNTS", "description": "", "input_list": [], "output_list": []},
            {"step_number": 25, "name": "CONSENSUS_PEAKS_ANNOTATE", "description": "", "input_list": [], "output_list": []},
            {"step_number": 26, "name": "MACS2_QC", "description": "", "input_list": [], "output_list": []},
            {"step_number": 27, "name": "CONSENSUS_PEAKS_DESEQ2", "description": "", "input_list": [], "output_list": []},
            {"step_number": 28, "name": "MULTIQC", "description": "", "input_list": [], "output_list": []},
            {"step_number": 29, "name": "IGV", "description": "", "input_list": [], "output_list": []},
            {"step_number": 30, "name": "but", "description": "", "input_list": [], "output_list": []},
            {"step_number": 31, "name": "errored", "description": "", "input_list": [], "output_list": []},
            {"step_number": 32, "name": "ran", "description": "", "input_list": [], "output_list": []}
        ]

```tip
Note the _partial order_ implied by `step_number`, here deliberately not listing any step `4`. You may have noticed during execution that multiple Nextflow steps executed concurrently, as they did not depend on each other. In BCO such parallelism can be more clearly indicated by setting the same number for `step_number`, e.g. if `FASTQC` ran at the same time as `TRIMGALORE` they can both have `"step_number": 8`: 

    {"step_number": 8, "name": "FASTQC", "description": "", "input_list": [], "output_list": []},
    {"step_number": 8, "name": "TRIMGALORE", "description": "", "input_list": [], "output_list": []},
```

```note
A more detailed listing of step execution can perhaps be made from Nextflow's [data/results/pipeline_info/execution_trace.txt](https://github.com/biocompute-objects/bco-ro-example-chipseq/blob/main/data/results/pipeline_info/execution_trace.txt), we here see each execution of `FASTQC` for different inputs:

    8	e1/a217d0	328841	FASTQC (SPT5_INPUT_R1_T1)	COMPLETED	0	2020-09-10 12:56:45.296	15.9s	13s	160.4%	237.9 MB	3.3 GB	38.1 MB	4.4 MB
    7	04/a99077	329934	TRIMGALORE (SPT5_INPUT_R1_T1)	COMPLETED	0	2020-09-10 12:56:50.451	23.5s	20.2s	163.8%	248.8 MB	3.1 GB	257.9 MB	213 MB
    11	bf/715e9e	331956	FASTQC (SPT5_T0_R1_T1)	COMPLETED	0	2020-09-10 12:57:01.183	16.9s	12.6s	166.3%	212.5 MB	3.3 GB	40.3 MB	4 MB

Conveniently Nextflow's incremental `task_id` could be used directly as a `step_number` - however notice how this log records tasks in order of their _completion_ of a task, while their `task_id` is assigned by their initial scheduling for execution. With several tasks executing concurrently they will not necessarily finish in the same order. As the partial ordering of the steps in BCO is only meant as a guide, it is up to you if you would like to list BCO steps by their "ideal" scheduled order (showing if they potentially could be concurrent), or list the steps in their actual started or completed order.
```


These programmatic step names like `TRIMGALORE` may seem a bit cryptic, and in many workflows have less descriptive names than shown in this nf-core example. Therefore it is good to provide a `description` explaining each step for humans. Luckily nf-core already [documents each step](https://nf-co.re/chipseq/1.2.2/ output#library-level-analysis), so we can explain each step, e.g.:

```
    {"step_number": 8, "name": "FASTQC", "description": "FastQC http://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/ gives general quality metrics about your reads. It provides information about the quality score distribution across your reads, the per base sequence content (%A/C/G/T). You get information about adapter contamination and other overrepresented sequences", "input_list": [], "output_list": []},
```

```tip
The field of `description` is free text, so the URL to [FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/) is not formally captured as linked data. We can provide further detail about each tool under `execution_domain` in the BCO, as well as in the RO-Crate.
```

For each step execution, `input_list` in BCO refers to a list of files consumed by that step, and likewise `output_list` can list the files produced. 

By looking for _Submitted process_ in the (very) detailed [.nextflow report](https://github.com/biocompute-objects/bco-ro-example-chipseq/blob/main/data/.nextflow.log#L228) that in reality `FASTQC` executed 6 times over different inputs:

```
Sep-10 12:56:45.296 [Task submitter] INFO  nextflow.Session - [e1/a217d0] Submitted process > FASTQC (SPT5_INPUT_R1_T1)
Sep-10 12:57:01.183 [Task submitter] INFO  nextflow.Session - [bf/715e9e] Submitted process > FASTQC (SPT5_T0_R1_T1)
Sep-10 12:57:40.582 [Task submitter] INFO  nextflow.Session - [f0/0c96b3] Submitted process > FASTQC (SPT5_INPUT_R2_T1)
Sep-10 12:57:54.885 [Task submitter] INFO  nextflow.Session - [61/31369d] Submitted process > FASTQC (SPT5_T0_R2_T1)
Sep-10 12:58:09.331 [Task submitter] INFO  nextflow.Session - [3e/c3dc21] Submitted process > FASTQC (SPT5_T15_R2_T1)
Sep-10 12:58:39.340 [Task submitter] INFO  nextflow.Session - [be/022ad6] Submitted process > FASTQC (SPT5_T15_R1_T1)
```

#### Step inputs

In this tutorial we've opted to describe `FASTQC` in BCO as a simplified single larger step, which can then be considered to consume all 6 inputs.  However we first have a challenge - the workflow started with a [single CSV file](https://github.com/nf-core/test-datasets/blob/chipseq/design.csv), yet it has been split into 6*2 actual FastQ files to be consumed by `FASTQ`. 

There is an [implied _shim_ step in Nextflow](https://github.com/nf-core/chipseq/blob/1.2.2/main.nf#L335) that has done this expansion and selected the paired end URLs from the CSV files, converting them to file objects connected to `FASTQC` and `TRIMGALORE`:

```groovy
    ch_design_reads_csv
        .splitCsv(header:true, sep:',')
        .map { row -> [ row.sample_id, [ file(row.fastq_1, checkIfExists: true), file(row.fastq_2, checkIfExists: true) ] ] }
        .into { ch_raw_reads_fastqc;
                ch_raw_reads_trimgalore }
```

While we could have formalized this CSV splitting as an implied `ch_design_reads_csv` intermediate step, the purpose of BCO is not to explain every such lower-level shim or data conversion built-in at the workflow execution level, but rather to explain the computational analysis. Therefore in this case we will rather list the data identifiers that data actually used, at the cost of them seemingly appearing out of nowhere.  Other workflow systems may have such steps explicit (e.g. calling a Python script to retrieve the files), in which case it may be more natural to also list them in the BCO.

The `CHECK_DESIGN` step has conveniently produced for us a [data/results/pipeline_info/design_reads.csv](https://github.com/biocompute-objects/bco-ro-example-chipseq/blob/main/data/results/pipeline_info/design_reads.csv) with `sample_id` that match what we see in the reports. We can thus report out the inputs directly into `input_list` which takes a list of URLs:

    {"step_number": 8, "name": "FASTQC", "description": "FastQC gives general quality…", 
    "input_list": [
      "https://raw.githubusercontent.com/nf-core/test-datasets/chipseq/testdata/SRR5204809_Spt5-ChIP_Input1_SacCer_ChIP-Seq_ss100k_R1.fastq.gz",
      "https://raw.githubusercontent.com/nf-core/test-datasets/chipseq/testdata/SRR5204810_Spt5-ChIP_Input2_SacCer_ChIP-Seq_ss100k_R1.fastq.gz",
      "https://raw.githubusercontent.com/nf-core/test-datasets/atacseq/testdata/SRR1822153_1.fastq.gz",
      "https://raw.githubusercontent.com/nf-core/test-datasets/atacseq/testdata/SRR1822154_1.fastq.gz",
      "https://raw.githubusercontent.com/nf-core/test-datasets/atacseq/testdata/SRR1822157_1.fastq.gz",
      "https://raw.githubusercontent.com/nf-core/test-datasets/atacseq/testdata/SRR1822158_1.fastq.gz",
      "https://raw.githubusercontent.com/nf-core/test-datasets/chipseq/testdata/SRR5204809_Spt5-ChIP_Input1_SacCer_ChIP-Seq_ss100k_R2.fastq.gz",
      "https://raw.githubusercontent.com/nf-core/test-datasets/chipseq/testdata/SRR5204810_Spt5-ChIP_Input2_SacCer_ChIP-Seq_ss100k_R2.fastq.gz",
      "https://raw.githubusercontent.com/nf-core/test-datasets/atacseq/testdata/SRR1822153_2.fastq.gz",
      "https://raw.githubusercontent.com/nf-core/test-datasets/atacseq/testdata/SRR1822154_2.fastq.gz",
      "https://raw.githubusercontent.com/nf-core/test-datasets/atacseq/testdata/SRR1822157_2.fastq.gz",
      "https://raw.githubusercontent.com/nf-core/test-datasets/atacseq/testdata/SRR1822158_2.fastq.gz"
    ], 
    "output_list": []},

Note that in this simplified approach of a single `FASTQC` step, with a flat list, we can no longer single out the individual pair-end reads. In the more verbose approach of using one `FASTQC` step for each row in [execution_trace.txt](https://github.com/biocompute-objects/bco-ro-example-chipseq/blob/main/data/results/pipeline_info/execution_trace.txt), we can be slightly more precise by only showing the reads for the given sample `SPT5_INPUT_R1_T1`:

    {"step_number": 8, "name": "FASTQC (SPT5_INPUT_R1_T1)", "description": "FastQC gives general quality…", 
    "input_list": [
      "https://raw.githubusercontent.com/nf-core/test-datasets/chipseq/testdata/SRR5204809_Spt5-ChIP_Input1_SacCer_ChIP-Seq_ss100k_R1.fastq.gz",
      "https://raw.githubusercontent.com/nf-core/test-datasets/chipseq/testdata/SRR5204809_Spt5-ChIP_Input1_SacCer_ChIP-Seq_ss100k_R2.fastq.gz",
    ], 
    "output_list": []},

This is still a simplification because in reality [this particular workflow step](https://github.com/nf-core/chipseq/blob/1.2.2/main.nf#L487) actually still executed `fastqc` separately for each file.

```
(bco-ro) root@05ce36630c51:/work/be/fc6003d5258941857baea391194fde# cat .command.sh 
#!/bin/bash -euo pipefail
[ ! -f  SPT5_INPUT_R1_T1_1.fastq.gz ] && ln -s SRR5204809_Spt5-ChIP_Input1_SacCer_ChIP-Seq_ss100k_R1.fastq.gz SPT5_INPUT_R1_T1_1.fastq.gz
[ ! -f  SPT5_INPUT_R1_T1_2.fastq.gz ] && ln -s SRR5204809_Spt5-ChIP_Input1_SacCer_ChIP-Seq_ss100k_R2.fastq.gz SPT5_INPUT_R1_T1_2.fastq.gz
fastqc -q -t 2 SPT5_INPUT_R1_T1_1.fastq.gz
fastqc -q -t 2 SPT5_INPUT_R1_T1_2.fastq.gz
```

Similarly note that in BCO we only provide input references as a _flat list_, we won't here document their role or parameter to the tool, nor the exact command line arguments, like for `TRIM_GALORE`:

    trim_galore --cores 1 --paired --fastqc --gzip      SPT5_INPUT_R1_T1_1.fastq.gz SPT5_INPUT_R1_T1_2.fastq.gz  

There are many reasons for not going into that level of detail, for instance in this NF-Core workflow each step also have pre- and post- steps that handle temporary file, record memory usage, capture the error log, etc. These details are usually not essential for explaining the computational analysis, and therefore do not need to be represented at BCO level.

#### Step outputs

Now let's go ahead and describe the `output_list` of the files this step produced, the `*.zip` and `*.html` reports, which are also workflow outputs under [data/results/fastqc](https://github.com/biocompute-objects/bco-ro-example-chipseq/tree/main/data/results/fastqc)

```
"output_list": [
  "results/fastqc/SPT5_INPUT_R1_T1_1_fastqc.html",
  "results/fastqc/SPT5_INPUT_R1_T1_2_fastqc.html"
  "results/fastqc/SPT5_INPUT_R1_T1_2_fastqc.html"
  "results/fastqc/zips/SPT5_INPUT_R1_T1_1_fastqc.zip"
  "results/fastqc/zips/SPT5_INPUT_R1_T1_2_fastqc.zip"
]
```

Note in this case we are referring to output files _contained by this RO-Crate_, they do not (yet) have any absolute URL, therefore we refer to them as _relative URI paths_ from the _RO-Crate Root_, the [`data/` folder](https://github.com/biocompute-objects/bco-ro-example-chipseq/tree/main/data/).

Some workflow systems produce workflow outputs directly web adressable, in which case you may elect to identify them by URL reference, instead of including them as part of the RO-Crate payload. However this means the BCO is more _fragile_, it refers to things on the web which may change or disappear.


### Identifying input/output files

As we saw above, the  `input_list` and `output_list` identify the workflow step's input and output files using URIs. 

One way to make a more lightweight BCO without all files bundled in, is to publish the workflow outputs on a hosting service. 

Here we try using GitHub to publish this BCO at <https://github.com/biocompute-objects/bco-ro-example-chipseq/>


The below form is valid, and use a HTTP _raw_ URI at GitHub. Note that the use of large files on GitHub might require the use of [Git LFS](https://git-lfs.github.com/) which could cause billable charges. The use of S3 bucket is **discouraged** as they are subject to change. Note that the below uses commit `ae950188ef874a9527f2c466354aa19a23ca0043` instead of `master` which again could be subject to change.
 
```json
{"step_number": 3, "name": "MAKE_GENE_BED", "description": "", "input_list": [], "output_list": [
    {"uri": "https://raw.githubusercontent.com/biocompute-object/bco-ro-example-chipseq/ae950188ef874a9527f2c466354aa19a23ca0043/data/results/genome/genes.bed",
     "filename": "data/results/genome/genes.bed"
    }
]},
```

We include the relative path under `filename`. It would not be valid to use that path as `uri`; 
the form below uses relative path, which currently is not valid according to the IEEE 2791 JSON Schema:

```json
{"uri": "data/results/genome/genes.bed"},
```

This form uses a `file:///` path which is valid and provides provenance of where the file was made locally, is not portable to other machines:

```json
{"uri": "file:///home/stain/src/bco-ro-example-chipseq/data/results/genome/genes.bed"},
```

This form uses [ARCP URIs inside the RO-Crate](https://www.researchobject.org/ro-crate/1.1/appendix/relative-uris.html#establishing-a-base-uri-inside-a-zip-file) based on the uuid in `bag-info.txt`, but is not valid because `,` wrongly is not permitted in the `uri` JSON Schema format for `authority` (it expects a hostname).

```json
{"uri": "arcp://uuid,9b309ebd-6dfb-4c6d-983b-56b91fca6e06home/data/results/genome/genome.fa.include_regions.bed"},
```