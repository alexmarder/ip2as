---
title: ip2as Documentation
---

# Prerequisits
The only prerequisite is the [traceutils](https://github.com/alexmarder/traceutils/) package

# Installation
The package can be installed by pip (`pip install -U ip2as`) or from source.

To install from source, clone the repository, and from inside the repository run
```bash
python setup.py sdist bdist_wheel
pip install -e .
```

Both installations will produce the two scripts <tt>ip2as</tt> and <tt>rir2as</tt>.
Inside the python environement, both scripts can be run from the command line.

# Running
After installing, the next step is to create the prefix to AS mappings.
This process has 3 steps,
1. download prefix2as file or extract origins from RIBs,
2. (optional, but recommended) extract origins from RIR extended delegation files, and
3. create prefix to AS mappings.

# Extracting Origin ASes from RIBs
The easiest way to do this is to download a [Routeviews prefix2as file from CAIDA](http://data.caida.org/datasets/routing/).
If you do this, there is no need to extract origin ASes from RIB files, and you can skip this step.

Otherwise, first download RIB files from Routeviews or RIPE RIS, then run the prefixes.py script.
The file prefixes.py processes RIBs in parallel. It is run with the following arguments. First create a file with all of the filenames to use, then pass that file to prefixes.py.

Argument | Required | Description
:--- | :--- | :---
-o, --output | Optional | Output file. Defaults to stdout.
-p, --processes | Optional | Number of processes to use. Defaults to 1.
-r, --ribs | Required | File with RIB filenames, newline separated.

```bash
./prefixes.py -r ribs.files -p 4 -o ribs.prefixes
```
This creates a prefix to AS file in the CAIDA prefix2as format.

# Extract Origin ASes from RIR
This step is not required, but RIR extended delegation files can fill in prefixes that are missing from the BGP route announements used to create the prefix2as file.
First, download an RIR extended delegation file, one from each of the five RIRs.
The <tt>rir2as</tt> script processes RIR extended delegation files, with the arguments,

Argument | Required | Description
:--- | :--- | :---
-f, --files | Required | File with RIR filenames, newline separated.
-r, --rels | Required | AS relationships file retrieved from CAIDA.
-c, --cone | Required | AS customer cone file retrieved from CAIDA.
-o, --output | Required | Output file.

After creating the file with the list of RIR filenames, run
```bash
rir2as -f rir.files -r rels-file -c cone-file -o rir.prefixes
```

# Prefix to AS
The final step in creating the prefix to AS mappings is to run the <tt>ip2as</tt> script.

Argument | Required | Description
:--- | :--- | :---
-p, --prefixes | Required | RIB prefix to AS file.
-P, --peeringdb | Required | [PeeringDB json file](http://data.caida.org/datasets/peeringdb-v2/).
-r, –-rir | Optional, Recommended | RIR prefix to AS file.
-R, –-rels | Required | AS relationships file retrieved from CAIDA.
-c, –-cone | Required | AS customer cone file retrieved from CAIDA.
-o, –-output | Optional | Output file. Defaults to stdout.
-a, –-as2org | Required | [AS-to-organization mappings](http://data.caida.org/datasets/as-organizations/) retrieved from CAIDA.

[AS relationships and customer cones can be downloaded here](http://data.caida.org/datasets/as-relationships/serial-1/).
The relationships file are named as-rel.txt.bz2 and the customer cone files are named ppdc-ases.txt.bz2.

Run the script to produce the prefix to AS mappings
```bash
ip2as -p rib.prefixes, -r rir.prefixes -R rels-file -c cone-file \ -a as2org -file -P peeringdb.json -o ip2as.prefixes
```