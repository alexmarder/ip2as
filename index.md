---
title: ip2as Documentation
---

After installing traceutils, the next step is to create the prefix to AS mappings. This process has 3 steps,
1. extract origins from RIBs,
2. extract origins from RIR extended delegation files, and
3. create prefix to AS mappings.

The source code resides at [https://github.com/alexmarder/ip2as/tree/gh-pages](https://github.com/alexmarder/ip2as/tree/gh-pages).

Argument | Required | Description
:--- | :--- | :---
-o, --output | Optional | Output file. Defaults to stdout.
-p, --processes | Optional | Number of processes to use. Defaults to 1.
-r, --ribs | Required | File with RIB filenames, newline separated.

# Extracting Origin ASes from RIBs
The file prefixes.py processes RIBs in parallel. It is run with the following arguments. First create a file with all of the filenames to use, then pass that file to prefixes.py.
```bash
./prefixes.py -r ribs.files -p 4 -o ribs.prefixes
```
This creates a prefix to AS file in the CAIDA prefix2as format.

# Extract Origin ASes from RIR
The file rir_delegations.py processes RIR extended delegation files, with the arguments,

Argument | Required | Description
:--- | :--- | :---
-f, --files | Required | File with RIR filenames, newline separated.
-r, --rels | Required | AS relationships file retrieved from CAIDA.
-c, --cone | Required | AS customer cone file retrieved from CAIDA.
-o, --output | Required | Output file.

After creating the file with the list of RIR filenames, run
```bash
./rir_delegations.py -f rir.files -r rels-file -c cone-file -o rir.prefixes
```

# Prefix to AS
The final step in creating the prefix to AS mappings is to run the ip2as.py script.

Argument | Required | Description
:--- | :--- | :---
-p, --prefixes | Required | RIB prefix to AS file.
-P, --peeringdb | Required | PeeringDB json file.
-r, –-rir | Required | RIR prefix to AS file.
-R, –-rels | Required | AS relationships file retrieved from CAIDA.
-c, –-cone | Required | AS customer cone file retrieved from CAIDA.
-o, –-output | Optional | Output file. Defaults to stdout.
-a, –-as2org | Required | AS-to-organization mappings retrieved from CAIDA.

Run the script to produce the prefix to AS mappings
```bash
./ip2as.py -p rib.prefixes, -r rir.prefixes -R rels-file -c cone-file \ -a as2org -file -P peeringdb.json -o ip2as.prefixes
```