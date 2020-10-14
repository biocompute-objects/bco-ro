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
see the [workflow usage](https://nf-co.re/chipseq/1.2.1/docs/usage) instructions.

Because our BCO should be reproducible we need to be concious about which version of the workflow
we are using. At time of writing the latest version is `1.2.1`.

If you are using Docker/Singularity containers instead of Conda, 
change the below `-profile test,conda` to 
`-profile test,docker` or `-profile test,singularity` accordingly.


```sh
$ nextflow run nf-core/chipseq -revision 1.2.1 -profile test,conda

N E X T F L O W  ~  version 20.07.1
Launching `nf-core/chipseq` [small_legentil] - revision: 0f487ed76d [1.2.1]
----------------------------------------------------
                                        ,--./,-.
        ___     __   __   __   ___     /,-._.--~'
  |\ | |__  __ /  ` /  \ |__) |__         }  {
  | \| |       \__, \__/ |  \ |___     \`-._,-`-,
                                        `._,._,'
  nf-core/chipseq v1.2.1
----------------------------------------------------
Run Name            : small_legentil
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

We notice already nf-core reporting inputs of reference datasets. We'll record these in the BCO as:

```json
{"description_domain": {

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
```


### Listing steps

(..)

### Identifying input/output files

The  `input_list` and `output_list`

This form is valid, and use a HTTP _raw_ URI at GitHub. Note that the use of large files on GitHub might require the use of [Git LFS](https://git-lfs.github.com/) which could cause billable charges. The use of S3 bucket is **discouraged** as they are subject to change. Note that the below uses commit `ae950188ef874a9527f2c466354aa19a23ca0043` instead of `master` which again could be subject to change.
 
```json
{"step_number": 3, "name": "MAKE_GENE_BED", "description": "", "input_list": [], "output_list": [
    {"uri": "https://raw.githubusercontent.com/stain/bco-ro-example-chipseq/ae950188ef874a9527f2c466354aa19a23ca0043/data/results/genome/genes.bed",
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

This form uses [ARCP URIs inside the RO-Crate](https://www.researchobject.org/ro-crate/1.1-DRAFT/appendix/relative-uris.html#establishing-a-base-uri-inside-a-zip-file) based on the uuid in `bag-info.txt`, but is not valid because `,` wrongly is not permitted in the `uri` JSON Schema format for `authority` (it expects a hostname).

```json
{"uri": "arcp://uuid,9b309ebd-6dfb-4c6d-983b-56b91fca6e06home/data/results/genome/genome.fa.include_regions.bed"},
```