---
title: BagIt manifest
sort: 10
---

# BagIt

The role of the BagIt is mainly ensure all files are present and not modified or corrupted in transfer. The roles of the files are as follows:

[bagit.txt](bagit.txt), [bag-info.txt](bag-info.txt), [manifest-sha512.txt](manifest-sha512.txt) and [tagmanifest-sha512.txt](tagmanifest-sha512.txt) conform to [BagIt (RFC8493)](https://www.rfc-editor.org/rfc/rfc8493.html).

* [bagit.txt](bagit.txt) — BagIt [declaration](https://www.rfc-editor.org/rfc/rfc8493.html#section-2.1.1)
* [bag-info.txt](bag-info.txt) — BagIt [metadata](https://www.rfc-editor.org/rfc/rfc8493.html#section-2.2.2)
* [manifest-sha512.txt](manifest-sha512.txt) — [Payload manifest](https://www.rfc-editor.org/rfc/rfc8493.html#section-2.1.3), checksums of all `data/` files
* [tagmanifest-sha512.txt](tagmanifest-sha512.txt) — [Tag manifest](https://www.rfc-editor.org/rfc/rfc8493.html#section-2.2.1), checksums of other files

## BagIt declaration

[bagit.txt](bagit.txt) should have this fixed content:

```
BagIt-Version: 1.0
Tag-File-Character-Encoding: UTF-8
```

The main role of this file is to mark the folder as a bag according to [RFC8493](https://www.rfc-editor.org/rfc/rfc8493.html). The base directory containing `bagit.txt` can have any name, and can be transferred in any way, e.g. [ZIP](https://github.com/stain/bco-ro-example-chipseq/archive/master.zip), SFTP or even be exposed [on the web](https://raw.githubusercontent.com/stain/bco-ro-example-chipseq/master/bagit.txt). 

## Payload manifest

[manifest-sha512.txt](manifest-sha512.txt) contain [SHA-512 checksums](https://en.wikipedia.org/wiki/SHA-2) of **all** files under [data/](data/) directory, e.g.:

```
41846747…ee71  data/ro-crate-metadata.json
e1105ed0…5e13  data/chipseq_20200910.json
37fd3a02…bb95  data/results/pipeline_info/design_reads.csv
…
```

Creating this file without using BagIt tools/libraries can be done as:

```sh
$ find data -type f -print0 | xargs -0 sha512sum > manifest-sha512.txt
```

Similarly checking the file:

```sh
$ sha512sum --quiet -c manifest-sha512.txt 
data/chipseq_20200910.json: FAILED
data/ro-crate-metadata.json: FAILED
sha512sum: WARNING: 2 computed checksums did NOT match
```

Notice how the BagIt _payload manifest_ list checksums of all individual data outputs in [data/results], as well as the RO-Crate [data/ro-crate-metadata.json](data/ro-crate-metadata.json) and the IEEE 2791 BCO JSON [data/chipseq_20200910.json](data/chipseq_20200910.json). Although these files are strictly speaking _metadata_ we can consider them part of the `data/` _payload_ of transfering an RO-Crate-described BioCompute Object, as [recommended by RO-Crate 1.0](https://w3id.org/ro/crate/1.0#example-of-adding-ro-crate-to-bagit).

Alternative checksums algorithm are allowed [according to RFC8493](https://www.rfc-editor.org/rfc/rfc8493.html#section-2.4), e.g. `manifest-sha256.txt` - as long as each manifest file is complete - here we use SHA-512 by default as recommended by RFC8493.

## Tag manifest

In BagIt it is optional to also checksum files outside `data/`, so-called [tag files](https://www.rfc-editor.org/rfc/rfc8493.html#section-2.2.1), as in [tagmanifest-sha512.txt](tagmanifest-sha512.txt). Because we have included the RO-Crate under `data/` these files are in examples are mainly about creating the BagIt BCO, such as this README file, and the BagIt files like `bagit.txt` `manifest-sha512.txt`

```
b0556450…8802  bag-info.txt
1abe59bd…969a  Makefile
b5598554…256d  README.md
000b27e3…c52e  manifest-sha512.txt
…
```

Unlike the _payload manifest_ above this file does not need to be complete (e.g. it does not list itself!), however we recommend it minimally includes `bag-info.txt` and `manifest-sha512.txt` to ensure these files are complete.

Creating the tag manifest without BagIt files is best achieved by listing individual files:

```sh
$ sha512sum bagit.txt bag-info.txt manifest-sha512.txt > tagmanifest-sha512.txt
```

## BagIt metadata

[bag-info.txt](bag-info.txt) contains [Bag-It metadata](https://www.rfc-editor.org/rfc/rfc8493.html#section-2.2.2) in a loose key-value based textual format to the describe the bag, primarily for the purpose of transfer.


Our example is quite minimal:

```
ROCrate_Specification_Identifier: https://w3id.org/ro/crate/1.0/
External-Description: Workflow run of a ChIP-seq peak-calling, QC and differential analysis pipeline
Bagging-Date: 2020-09-10T19:27:45Z
Payload-Oxum: 414893243.372
Bag-Size: 396MB
```

`ROCrate_Specification_Identifier` is an additional key used by [RO-Crate Describo](https://uts-eresearch.github.io/describo/) to indicate the version of [data/ro-crate-metadata.json](data/ro-crate-metadata.json). This string SHOULD match the [`conformsTo`](data/ro-crate-metadata.json#L7) URI.

`Payload-Oxum` is a compound field listing the total size of `data/` files in bytes, and the number of payload files (excluding directories). These numbers could be obtained with:

```sh
$ du --apparent-size -b -s data
414893243	data
$ du --apparent-size --human-readable -s data
396M	data
$ find data -type f | wc -l
372
```

(Note the use of `--apparent-size` as in this case actual disk-usage is 129M due to ZFS compression)

This example from RFC8493 lists other keys that MAY be used:

```
Source-Organization: FOO University
Organization-Address: 1 Main St., Cupertino, California, 11111
Contact-Name: Jane Doe
Contact-Phone: +1 111-111-1111
Contact-Email: example@example.com
External-Description: Uncompressed greyscale TIFF images from the
        FOO papers colle...
Bagging-Date: 2008-01-15
External-Identifier: university_foo_001
Payload-Oxum: 279164409832.1198
Bag-Group-Identifier: university_foo
Bag-Count: 1 of 15
Internal-Sender-Identifier: /storage/images/foo
Internal-Sender-Description: Uncompressed greyscale TIFFs created
        from microfilm and are...
```

However, as metadata would mainly be covered by the [bco](data/chipseq_20200910.json) and [RO-Crate](data/ro-crate-metadata.json) we recommend keeping `bag-info.txt` minimal for transfer-level metadata. 