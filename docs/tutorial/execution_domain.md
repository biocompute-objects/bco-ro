---
title: BCO Execution domain
sort: 5
---

### execution_domain

The `execution_domain` should refer to actual the workflow script being executed. 

This is a bit of a challenge in this example as we have not bundled the `*.nf` file in the BCO, but ran it by refernece `nf-core/chipseq` which Nextflow then retrieved from GitHub. The web page <https://nf-co.re/chipseq> gives great information for humans, but is in HTML and not executable by workflow engines. 

Taking into consideration the `-revision 1.2.1` we then navigate from <https://nf-co.re/chipseq> to <https://github.com/nf-core/chipseq>, select the [tag 1.2.1](https://github.com/nf-core/chipseq/tree/1.2.1) and find <https://github.com/nf-core/chipseq/blob/1.2.1/main.nf> - but again this is HTML, so we use the **Raw** button to find <https://raw.githubusercontent.com/nf-core/chipseq/1.2.1/main.nf>.

This can then be described in the BCO in the `script` array, for `script_driver` we use `nextflow` as it matches the command line (Note: there is currently no registry of known `script_driver` values).

```json
    "execution_domain": {
        "script": ["https://raw.githubusercontent.com/nf-core/chipseq/1.2.1/main.nf"],
        "script_driver": "nextflow"
    }
```

We can annotate the other URIs like <https://nf-co.re/chipseq> in the RO-Crate, as they give additional information for the user.

A challenge here is that we have not indicated how the workflow engine itself should be invoked on the command line. Should we instead have listed our [run.sh](run.sh) that invokes Nextflow? (We would need to move `run.sh` into `data/` to make it part of the RO-Crate)

```json
    "execution_domain": {
        "script": ["run.sh"],
        "script_driver": "shell"
    }
```

In one way this is more useful, as it directly executable - at least if the Conda [environment.yml](environment.yml) has been activated. On the other side `run.sh` provides absolutely no details about the data analysis performed, and as the purpose of the BCO is to submit a workflow, we instead show the `main.nf` that lists the individual steps, matching the `pipeline_steps` section of the BCO.