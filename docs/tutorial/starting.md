---
title: Starting the BCO RO-Crate
sort: 2
---


## Starting the BCO RO-Crate

We'll create the BCO RO-Crate inside a new folder. 

```sh
mkdir chipseq_20200924
cd chipseq_20200924
mkdir data
```

Here we use a name `chipseq_20200924` as we ran the workflow `chipseq` on 2020-09-24 - you can use any reasonable name.

The `data/` folder will contain our [RO-Crate](https://w3id.org/ro/crate/1.1) according to its [recommendation for combining with BagIt](https://www.researchobject.org/ro-crate/1.1/appendix/implementation_notes.html#combining-with-other-packaging-schemes).  If you will not be using BagIt you can skip the `data/` subfolder.

Note that running the workflow can take a while, so this might be a good point to [skip ahead](running.md) and start the Nextflow run in a separate window.
 
### Skeleton BCO 

We'll start our editor to create skeleton JSON files for the BCO and RO-Crate.

The BCO JSON can have any name - to make its relative paths easy we'll put it in the root of `data/` and we'll name it like the folder `chipseq_20200924.json` which we'll also use as our `object_id`:

**data/chipseq_20200924.json**
```
{
    "object_id": "chipseq_20200924",
    "spec_version": "https://w3id.org/ieee/ieee-2791-schema/",
    "etag": "abcd",
    "provenance_domain": {
        "name": "",
        "version": "",
        "created": "2020-09-24T15:38:00Z",
        "modified": "2020-09-24T15:38:00Z",
        "contributors": [
            {"contribution": ["createdBy"],
             "name": ""
            }   
        ],
        "license": ""
    },
    "usability_domain": [
    ],
    "description_domain": {
        "keywords": [
        ],
        "pipeline_steps": [
        ]
    },
    "execution_domain": {
		"script": [],
        "script_driver": "",
        "software_prerequisites": [
        ],
        "external_data_endpoints": [
        ],
        "environment_variables": {}
    },
    "io_domain": {
      "input_subdomain": [],
      "output_subdomain": []     
    }
}

While the above is valid according to the [IEEE 2791 schemas](https://w3id.org/ieee/ieee-2791-schema/) it has obviously not got much information yet.  

**Tip:** While editing the BCO JSON, have it open as **Input JSON** in <https://www.jsonschemavalidator.net/> with the below schema:

```json
{ "$ref": "https://w3id.org/ieee/ieee-2791-schema/2791object.json"}
```

### ETag

From [IEEE 2791-2020](https://doi.org/10.1109/IEEESTD.2020.9094416):

> Everything except for the `etag`, `object_id`, and `spec_version` shall be included in the generation of an [ETag](https://tools.ietf.org/html/rfc7232#section-2.3) - which can be ["strong" or "weak"](https://tools.ietf.org/html/rfc7232#section-2.1). It is recommended that the ETag be deleted or updated if the object file is changed (except in cases using weak ETags in which the entirety of the change comprises a simple re-writing of the JSON).

To calculate the `etag` value we need some kind of checksum of the BCO's JSON. 

Below is a poor man's "weak" attempt of a JSON checksum by ignoring any lines containing `etag`, `object_id`, and `spec_version` when calculating a new etag, as well as ignoring any newlines, space and tab characters:

```sh
$ egrep -v '"(etag|object_id|spec_version)"' chipseq_20200924.json  | 
   sed -e ':a' -e 'N' -e '$!ba' -e 's/\n/ /g' -e 's/[ ]*//g' | 
   sed -e $'s/\t/|/g' | sha256sum
cfb77c3e5c9b0e937de215190106dadcc9af9f40fa19a4deacf9a5a3a42f5de6  -

We can from this checksum generate and insert the `etag` field for the BCO:

```json
"etag": "cfb77c3e5c9b0e937de215190106dadcc9af9f40fa19a4deacf9a5a3a42f5de6"
```

```note
The above etag calculation can be considered "weak", e.g. it would give a different checksum also for non-structural changes like moving key-value lines around, while ignoring some structural changes like `"lots of space.txt"` to `"lotsofspace.txt"`. [RFC7232](https://tools.ietf.org/html/rfc7232#section-2.3) says such Etag weakness should be indicated with a `W/` prefix, however that is [not currently permitted](https://opensource.ieee.org/2791-object/ieee-2791-schema/-/merge_requests/1) in the JSON Schema for the BCO `etag` field.
```

We'll need to update the `etag` whenever we substantially change the BCO JSON - so we'll do this again towards the end of this tutorial. Notice how the above `egrep` command-line ignores the `"etag"` line to avoid circularly calculating a new checksum.


```tip
Performing a "strong" `etag` calculation would need to be done at tree level by parsing the BCO JSON, e.g. ignoring order of fields within an object. A strong calculation may also want to assign new `etag` values if any of files referenced from the BCO have changed.
```

### Provenance domain

The `provenance_domain` describe this BCO overall. Because in this case we are primarily submitting the `nf-core/chipseq` workflow with supporting data we'll reflect the workflow's original creation date and contributors, even though they were not involved in making the workflow's description as a BCO. 

Luckily nf-core has provided [citation info](https://nf-co.re/chipseq#citation) where we can find the authors ([`authoredBy`](http://purl.org/pav/html#http://purl.org/pav/authoredBy)) of the workflow from <https://doi.org/10.5281/zenodo.3240506>.  

We add a `name` with the title from the `nf-core/chipseq` website, as well as their indicated [latest release](https://nf-co.re/chipseq/releases) - at time of writing `"version": "1.2.2`.  If your workflow does not currently have a version number, consider using [Semantic Versioning](https://semver.org/spec/v2.0.0.html) as recommended by IEEE 2791.

Here `created` reflects the original release date the workflow (which did not include a BCO), we'll set `modified` to current date to reflect when we last updated the BCO JSON (and presumably also changed its `etag`). 

In many cases your workflow will be considered "released" at the same time as the BCO JSON is created by its own authors, in which case this situation is simplified.


```json
"provenance_domain": {
    "name": "nf-core/chipseq: ChIP-seq peak-calling, QC and differential analysis pipeline",
    "version": "1.2.2",
    "created": "2019-06-06T15:15:00.676813+00:00Z",
    "modified": "2021-04-23T00:27:42.542985Z",
    "contributors": [
        {"contribution": ["authoredBy"], "name": "Harshil Patel" },
        {"contribution": ["authoredBy"], "name": "Chuan Wang" },
        {"contribution": ["authoredBy"], "name": "Phil Ewels" },
        {"contribution": ["authoredBy"], "name": "Tiago Chedraoui Silva" },
        {"contribution": ["authoredBy"], "name": "Alexander Peltzer" },
        {"contribution": ["authoredBy"], "name": "Drew Behrens" },
        {"contribution": ["authoredBy"], "name": "Maxime Garcia" },
        {"contribution": ["authoredBy"], "name": "mashehu" },
        {"contribution": ["authoredBy"], "name": "Rotholandus" },
        {"contribution": ["authoredBy"], "name": "Sofia Haglund" },
        {"contribution": ["authoredBy"], "name": "Winni Kretzschmar" },
    ],
    "license": "https://github.com/nf-core/chipseq/blob/1.2.2/LICENSE"
},
```

To find the `license` of the workflow we have to hunt into its GitHub source code <https://github.com/nf-core/chipseq> where we find a [LICENSE](https://github.com/nf-core/chipseq/blob/1.2.1/LICENSE) file - indicated as an _MIT License_. As this particular license includes a copyright statement _Copyright (c) Philip Ewels_ we need to refer to the license in its source repository, rather than the generic SPDX identifier <https://spdx.org/licenses/MIT>. 

For open source licenses like the _Apache License version 2.0_ which are not modified depending on copyright holder, you should instead use their SPDX identifier like <https://spdx.org/licenses/Apache-2.0> (removing `.html`). If your workflow has its own license that is not on the web (e.g. non-open license), use `"license": "LICENSE.txt"` and add the file `data/LICENSE.txt` with your copyright information.

In this case Stian has _created_ the BCO and performed the workflow run, so we'll also add this as a separate contribution to the `contributors` array, ideally including a <https://orcid.org/> identifier to disambiguate from other people with the same name.

```json
{ "contribution": ["createdBy"],
  "name": "Stian Soiland-Reyes",
  "orcid": "https://orcid.org/0000-0001-9842-9718",
} 
```

[PAV contributor relations](http://purl.org/pav/html) [supported by IEEE 2791](https://w3id.org/ieee/ieee-2791-schema/2791object.json) include:

* [authoredBy](http://purl.org/pav/html#http://purl.org/pav/authoredBy) - who originated or gave existence to the work that is expressed by this BCO (who designed the workflow)
* [createdBy](http://purl.org/pav/html#http://purl.org/pav/createdBy) - person who made the representation as saved to disk (JSON of this BCO)
* [createdWith](http://purl.org/pav/html#http://purl.org/pav/createdWith) - software used to create the representation (e.g. [BCO Editor](https://github.com/biocompute-objects/bco_editor))
* [curatedBy](http://purl.org/pav/html#http://purl.org/pav/curatedBy) - who shaped the expression (workflow) into the appropriate format (BCO) or who ensured the quality of the representation (see also `review` key of `provenance_domain`)
* [importedBy](http://purl.org/pav/html#http://purl.org/pav/importedBy) - software that transcribed the work (workflow) to the current form (BCO)
* [providedBy](http://purl.org/pav/html#http://purl.org/pav/providedBy) - original provider of the encoded information (e.g. _nf-core_)
* [retrievedBy](http://purl.org/pav/html#http://purl.org/pav/retrievedBy) - software that retrieved data from external source
* [sourceAccessedBy](http://purl.org/pav/html#http://purl.org/pav/sourceAccessedBy) - someone who consulted an external source of information
* [contributedBy](http://purl.org/pav/html#http://purl.org/pav/contributedBy) - someone who provided any sort of help in conceiving the work (if none of the above match)

These additional PAV relations can also be provided, however we would need to be flexible with their `name` and `orcid` field to give their URIs:

* [derivedFrom](http://purl.org/pav/html#http://purl.org/pav/derivedFrom) - where knowledge was derived from but substantially changed, e.g. a different workflow (see also `derived_from` key of `provenance_domain` if previous was a BCO)
* [importedFrom](http://purl.org/pav/html#http://purl.org/pav/importedFrom) - where knowledge was transcribed from, e.g. <https://nf-co.re/chipseq>
* [retrievedFrom](http://purl.org/pav/html#http://purl.org/pav/retrievedFrom) - where the BCO was directly retrieved from, e.g. <https://raw.githubusercontent.com/stain/bco-ro-example-chipseq/master/data/chipseq_20200910.json>
* [createdAt](http://purl.org/pav/html#http://purl.org/pav/createdAt) - a location the BCO was created at, e.g. `"Manchester, UK"`

### Skeleton RO-Crate

Similarly let's create the high-level [RO-Crate](https://w3id.org/ro/crate/1.1). According to the [RO-Crate structure](https://www.researchobject.org/ro-crate/1.1/structure.html) the RO-Crate Metadata File always have the name `ro-crate-metadata.json` which we'll create inside `data/`:

**data/ro-crate-metadata.json**

```json
{ "@context": "https://w3id.org/ro/crate/1.1/context", 
  "@graph": [
    {
        "@type": "CreativeWork",
        "@id": "ro-crate-metadata.json",
        "conformsTo": {"@id": "https://w3id.org/ro/crate/1.1"},
        "about": {"@id": "./"}
    },
    
    {
      "@id": "./",
      "@type": "Dataset"
    }
  ]
}
```

This preamble  declares the version of the RO-Crate specification used, it's equivalent to BCO's `spec_version`. The remaining RO-Crate elements will be added to the `@graph` array as [data entities](https://www.researchobject.org/ro-crate/1.1/data-entities.html) (files and directories) or [contextual entities](https://www.researchobject.org/ro-crate/1.1/contextual-entities.html) (e.g.  people, organizations)

The [root of the RO-Crate](hhttps://www.researchobject.org/ro-crate/1.1/root-data-entity.html) represents the whole _dataset_, in this case our `data/` folder, but also any external references like the nf-core workflow and reference data. We're going to use the RO-Crate metadata to provide further details on resources and their origin in the world, while the BCO provides finer-grained details of the workflow execution. 

In both cases we will mainly use relative paths within the `data/` directory, where also both metadata files reside. We notice thus that `./` for the root Dataset reflects the `data/` directory.

We'll start by describing the RO-Crate itself under the `./` Dataset, including as a minimum the [core properties of the Root Data Entity](https://www.researchobject.org/ro-crate/1.1/root-data-entity.html#direct-properties-of-the-root-data-entity), although any of the <http://schema.org/Dataset> properties can be utillized:

```json
{
    "@id": "./",
    "@type": "Dataset",
    "name": "Workflow run of nf-core/chipseq",
    "description": "Workflow run of a ChIP-seq peak-calling, QC and differential analysis pipeline",
    "author": {
    "@id": "https://orcid.org/0000-0001-9842-9718"
    },
    "datePublished": "2020-09-24T23:00:00.000Z",
    "distribution": {
    "@id": "https://github.com/stain/bco-ro-example-chipseq/archive/master.zip"
    },
    "hasPart": [
    {
        "@id": "chipseq_20200910.json"
    }
    ],
    "license": {
    "@id": "https://spdx.org/licenses/CC0-1.0"
    }
}
```

Already you will notice some differences from the BCO. The `name` could match the `provenance_domain/name` of the BCO - but as the BCO focus more on the workflow and the Dataset includes all the files we've changed it to include `"Workflow run of.."`. However if your RO-Crate did not include workflow results, then the two could have the same title. `description` allow us to provide a longer description - comparable to BCO's `usability_domain` which we'll populate later, but again decribing the whole dataset.

The reason these fields are mainly at dataset level is that we can further describe individual files and resources later as separate [data entities](hhttps://www.researchobject.org/ro-crate/1.1/data-entities.html). Therefore here the `author` of the dataset is  <https://orcid.org/0000-0001-9842-9718>, the ORCID identifier for Stian, as he ran the workflow and gathered (most of) the files, and `license` of the dataset (the whole folder) can be different from the license of the workflow. If need be `license`, `author` etc. can be different on the `ro-crate-metadata.json` entity if someone else made this JSON.

The `hasPart` lists the content of the RO-Crate, currently just the BCO JSON file. We'll describe it as a [data entity](https://www.researchobject.org/ro-crate/1.1/data-entities.html) and indicate that it is following the IEEE 2791 schemas using `conformsTo` and adding to the `@graph` array:

```json
{
    "@id": "chipseq_20200910.json",
    "@type": "File",
    "name": "chipseq_20200910.json",
    "description": "IEEE 2791 description (BioCompute Object) of nf-core/chipseq",
    "conformsTo": {
        "@id": "https://w3id.org/ieee/ieee-2791-schema/"
    }
}
```

Given the above you can identify programmatically the BCO file `chipseq_20200910.json` 
independent of its filename: Parse `ro-crate-metadata.json` as JSON, then iterate 
over `@graph` array until you find a matching `conformsTo`, then look up its `@id` to
find its (URI escaped) file path within the `data/` folder.


### Assigning unique identifiers to BCOs

If the BCO has a `object_id` that is globally unique, e.g. a 
randomly generated [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) 
then these can be reflected as `identifier` URIs in the RO-Crate if 
combined with the registered `urn:uuid:` prefix.

This can be useful when looking up or matching 
BCOs later; RO-Crate Metadata files are valid
[JSON-LD](https://json-ld.org/) and can be loaded into a 
knowledge graph using a _triple store_ like 
[Apache Jena Fuseki](https://jena.apache.org/documentation/fuseki2/index.html) for 
detailed [querying](https://jena.apache.org/tutorials/sparql.html).

```
$ echo urn:uuid:`uuidgen`
urn:uuid:dc308d7c-7949-446a-9c39-511b8ab40caf
```

So we can change our BCO JSON's self-identity from the not-so-unique `chipseq_20200924` to:

```json
"object_id": "urn:uuid:dc308d7c-7949-446a-9c39-511b8ab40caf"
```

and, now that we have a URI, add correspondingly to the `chipseq_20200910.json` entry in `ro-crate-metadata.json`:

```json
"identifier": "urn:uuid:dc308d7c-7949-446a-9c39-511b8ab40caf"
```

