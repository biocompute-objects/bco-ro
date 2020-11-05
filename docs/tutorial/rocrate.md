---
title: RO-Crate metadata file
sort: 4
---

# RO-Crate

[RO-Crate](https://www.researchobject.org/ro-crate/) is a method to package research outputs with their structured metadata and contextual descriptions; reusing existing Linked Data standards and providing best practice guides. 

In our example [data/ro-crate-metadata.json](https://github.com/biocompute-objects/bco-ro-example-chipseq/blob/main/data/ro-crate-metadata.json) is a JSON-LD file conforming to [RO-Crate Metadata specification 1.1](https://www.researchobject.org/ro-crate/1.1/). This section of the tutorial explains how RO-Crate should be used when packaging IEEE 2791-2020 BCOs. Note that RO-Crate is independent of the type of research outputs packaged and is used by multiple other domains beyond the scope of BCOs.  Here we will frequently refer to the RO-Crate specification using deep links to provide further guidance for the components of the RO-Crate.

For our purposes the role of the RO-Crate is to type, relate and describe the individual files in the packaged BCO. This includes external references, licenses and attributions to authors. This may seem like overlap from the BCO, but as RO-Crate details per file and URL, it can handle more complicated situations such as the reference datasets. 

```tip
An important principle in this more open-ended metadata model is to focus on what is _necessary_ and _sufficient_ to explain the BCO's content beyond what the BCO already captures. It is easy to become captivated by the possibilities of describing in detail nested referred resources (e.g. organizations, instruments); care should be taken to mainly model metadata that is informative for this particular RO-CRate, and that is also possible to describe consistently without too many assumptions, simplifications
or elaborations.
```

## RO-Crate Root

Working within a directory, the [RO-Crate Root](https://www.researchobject.org/ro-crate/1.1/structure.html), we've already started our [skeleton RO-Crate](starting.md#skeleton-ro-crate), and used it to flag `chipseq_20200910.json` as a IEEE 2791-2020 BCO JSON file. The RO-Crate Root and its descendent files & directories are, together with referenced and contextual entities, what we consider as the [research object](https://www.researchobject.org/) - a unit of discourse.

```tip
In this tutorial we show how a RO-Crate is created around a single BCO and its related files, which then should reside under the RO-Crate Root. We will also show how the RO-Crate can include external references. As this tutorial will also create [BagIt manifests](bagit.md) our _RO-Crate Root_ is the payload directory [data/](https://github.com/biocompute-objects/bco-ro-example-chipseq/tree/main/data), however other packaging of RO-Crate may use any directory name; any RO-Crate Root can be identified by the presence of `ro-crate-metadata.json`.
```

```note
Our `ro-crate-metadata.json` JSON file will use `@id` identifiers uwith relative paths 
based within `data/`. This folder is also self-described as the
[root data entity](https://www.researchobject.org/ro-crate/1.1/root-data-entity.html) `./`
which we created in the [skeleton RO-Crate](starting.md#skeleton-ro-crate).
```

Now, after [running the workflow](running.md), we should complete the RO-Crate, in particular to fill in metadata we could not find a slot for in the BCO, but which may still be important for understanding the data, workflow and its context. 

While the RO-Crate is not required to list _every_ file in the directory structure (rather fulfilled by the [BagIt manifest](bagit.md)), RO-Crates should be [self-described and self-contained](https://www.researchobject.org/ro-crate/1.1/structure.html#self-describing-and-self-contained) - here we should document the directories sufficiently for navigation and understanding, considering a domain scientist who has just received the BCO, perhaps evaluating it for regulatory purposes.

## Data entities

The bread and butter of most RO-Crates are _files_ and _directories_, collectively described as [data entities](https://www.researchobject.org/ro-crate/1.1/data-entities.html). In `ro-crate-metadata.json` we list the contained data using `hasPart` from the root data entity. From the [workflow run](running.md) we've now got an additional folder and a log file which we'll add to the root dataset `@id: ./`:

```json
      "hasPart": [
        { "@id": "chipseq_20200910.json" },
        { "@id": "results/" },
        { "@id": "nextflow.log" }
```

These can then be explained just like `chipseq_20200910.json` using additional [data entities](https://www.researchobject.org/ro-crate/1.1/data-entities.html#referencing-files-and-folders-from-the-root-data-entity) which we'll add to `@graph` - for the file [nextflow.log]():

```json
    {
      "@id": "nextflow.log",
      "@type": "File",
      "dateModified": "2020-09-10T13:10:50.246Z",
      "name": "nextflow log",
    }
```

While the folder [results/]() is a directory with content per step, so we'll detail each of the subfolders further:

```json
    {
      "@type": "Dataset",
      "author": {
        "@id": "https://orcid.org/0000-0001-9842-9718"
      },
      "creator": {
        "@id": "#db65dfb7-4867-400e-a12f-a1652d46a333"
      },
      "dateModified": "2020-09-10T13:20:49.143Z",
      "description": "Nextflow outputs from examplar run of nf-core/ pipeline workflow.",
      "hasPart": [
        {
          "@id": "results/bwa/"
        },
        {
          "@id": "results/fastqc/"
        },
        {
          "@id": "results/genome/"
        },
        {
          "@id": "results/igv/"
        },
        {
          "@id": "results/multiqc/"
        },
        {
          "@id": "results/pipeline_info/"
        },
        {
          "@id": "results/trim_galore/"
        }
      ],
      "license": {
        "@id": "https://github.com/nf-core/test-datasets/blob/atacseq/LICENSE"
      },
      "name": "results",
      "@reverse": {
        "hasPart": [
          {
            "@id": "./"
          }
        ]
      },
      "@id": "results/"
    },
    ```

**TODO**: Simplify above
