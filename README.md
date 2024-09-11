![MS-BioGraphs Validation](https://blogs.qub.ac.uk/dipsa/wp-content/uploads/sites/319/2023/08/11.jpg)


# Validation Code for [MS-BioGraphs](https://blogs.qub.ac.uk/dipsa/MS-BioGraphs)

## Links

- MS-BioGraphs: https://blogs.qub.ac.uk/DIPSA/MS-BioGraphs
- MS-BioGraphs on IEEE DataPort (Open Access dataset): https://dx.doi.org/10.21227/gmd9-1534 .
- [![DOI](gmd9-1534.svg)](https://doi.org/10.21227/gmd9-1534)
- Downloading from DataPort: https://blogs.qub.ac.uk/DIPSA/MS-BioGraphs-on-IEEE-DataPort/
- MS-BioGraphs Validation: https://blogs.qub.ac.uk/DIPSA/MS-BioGraphs-Validation/ 

## Explanation

We provide a Shell script, `validation.sh`, and a Java program, `EdgeBlockSHA.java`, to verify the the correctness 
of the graphs. Each graph has a `.ojson` file whose `shasum` is verified by the 
value retreived from our server.
Files such as  `offsets.bin`,`wcc.bin`, `n2o.bin`,`trans_offsets.bin`, and `edges_shas.txt` 
have shasum records in the `ojson` file which is used for validation of these files.

The graph in WebGraph format has been compressed in  `MS??-underlying.*` and `MS??-weights.*` 
files. In order to validate the compressed graph, the `EdgeBlockSHA.java` is used. 
It is  a parallel Java code that uses the WebGraph library
to traverse the graph and calculates shasum of the blocks of edges (endpoints and weights).
Then, the calculated results are matched with the `edges_shas.txt` file of the graph.

It is also possible to validate some particular blocks by matching
the calculated shasum with the relevant row in the `edges_shas.txt` file. This file has
a format such as the following. Each block contains 64 Million consecutive edges. 
The start of each block is identified by
a vertex ID and its edge index. The Column `endpoint_sha` is the `shasum` of the 64 Million
endpoints when stored as an array of 4-Bytes elements in the binary format 
and in the little endian order. 
Similarly, Column `weights_sha` shows the `shasum` of weights (labels). 
We have separated weights from endpoints as in some applications
weights are not needed and therefore it is not necessary to read and validate them. 

```
 64MB blk#;     vertex; edge index;                             endpoint_sha;                              weights_sha;
         0;          0;          0; 509784b158cb9404241afb21d0ceaf590b88d2f2; 57da4ad7bb89c5922e436b0535d791fa8f40dffd;
         1;    2315113;        705; fafc118563c1d7b5fbff64af56edd6a56524f479; 13b7a9ca60bfb0715d563218d0a1cd787b00a07c;
         2;    4521625;        597; 4ed65aa07c8062a151166ef2e9bdb93e41d19357; 8158276bec426ee46eca9912759eb9bd57fcc957;
         3;    6347361;        112; d02e8913c807c3f4ecde9c638e0ded5ab80ba819; 26bc3296de65cba6ac539cd96b79ae6f7a4d37be;
         4;    8447869;         15; 61513c84db40124496cdf769516118b63598914f; 781b9f4372ac614e94d097017c756d015234deb6;
```

## Requirements
  - `JDK` with version > 15
  - `jq`
  - `wget`

## WebGraph Framework
The shell script `validation.sh` downloads the WebGraph framework as one of its step. 
For further details about the WebGraph framework, please visit https://webgraph.di.unimi.it.

## Downloading Datasets From IEEE DataPort
Please visit https://blogs.qub.ac.uk/DIPSA/MS-BioGraphs-on-IEEE-DataPort .
```
dsname="MS1"   # Set the name of dataset 
html_file="dp.html"

urls=`cat $html_file  | sed  -e 's/\&amp;/\&/g'  | grep -Eo "(http|https)://[a-zA-Z0-9./?&=_%:-]*" | grep amazonaws  | sort | uniq | grep -E "$dsname[-_\.]"`

for u in $urls; do
    wget $u
    if [ $? != 0 ]; then break; fi
done

# Removing query strings from file names
for f in $(find $1 -type f); do
    if [ $f = ${f%%\?*} ]; then continue; fi
    mv "${f}" "${f%%\?*}"
done

# Optional: liking offsets.bin to be found by ParaGrapher
ln -s ${dsname}_offsets.bin ${dsname}-underlying_offsets.bin
```

## License
Licensed under the GNU v3 General Public License, as published by the Free Software Foundation. You must not use this Software except in compliance with the terms of the License. Unless required by applicable law or agreed upon in writing, this Software is distributed on an "as is" basis, without any warranty; without even the implied warranty of merchantability or fitness for a particular purpose, neither express nor implied. For details see terms of the License (see attached file: LICENSE). 

#### Copyright 2022-2023 The Queen's University of Belfast, Northern Ireland, UK

## Sample Execution
```
$ PATH=~/jdk-17.0.1/bin/:$PATH ./validation.sh ~/sharedscratch/1/MS1-underlying  | tee MS1-sample.txt

*** MS-Biographs Validation Script ***

path: /users/2635474/sharedscratch/1/MS1
graph_name: MS1

Checking shasum of MS1.ojson (/users/2635474/sharedscratch/1/MS1.ojson)
ojson shasum: c60afa0652955fd46f1bb8056380523504d69fa6
local shasum: c60afa0652955fd46f1bb8056380523504d69fa6
shasum matches.

JSON:
{
  "name": "MS1",
  "directed": 0,
  "edge_weighted": 1,
  "vertices_count": 43144218,
  "edges_count": 2660495200,
  "max_weight": 634925,
  "max_degree": 14212,
  "zero_degrees_count": 0,
  "cp_first_degree": 6,
  "cp_last_degree": 1,
  "cp_first_neighbour": 0,
  "cp_first_weight": 74811,
  "cp_last_neighbour": 43144217,
  "cp_last_weight": 3714,
  "min_weight": 3680,
  "largest_component_size": 124003393,
  "cc_count": 15746208,
  "wg_size_underlying_ver_1": 6378487341,
  "wg_size_weights_ver_1": 8282238556,
  "offsets_ver_1_shasum": "0abedde32e1ac7181897f82d10d40acfe14f2022",
  "wcc_ver_1_shasum": "4c491dd96e3582b70a203ae4a910001381278d75",
  "n2o_ver_1_shasum": "b163320b6349fed7a00fb17c4a4a22e7d124b716",
  "edges_shas_ver_1_shasum": "27974edb4bf8f3b17b00ff3a72a703da18f3807a"
}


Checking file offsets.bin:
file-path: /users/2635474/sharedscratch/1/MS1_offsets.bin
json shasum:  0abedde32e1ac7181897f82d10d40acfe14f2022
local shasum: 0abedde32e1ac7181897f82d10d40acfe14f2022
shasum matches.


Checking file wcc.bin:
file-path: /users/2635474/sharedscratch/1/MS1-wcc.bin
json shasum:  4c491dd96e3582b70a203ae4a910001381278d75
local shasum: 4c491dd96e3582b70a203ae4a910001381278d75
shasum matches.


Checking file n2o.bin:
file-path: /users/2635474/sharedscratch/1/MS1-n2o.bin
json shasum:  b163320b6349fed7a00fb17c4a4a22e7d124b716
local shasum: b163320b6349fed7a00fb17c4a4a22e7d124b716
shasum matches.


Checking file trans_offsets.bin:
File does not exist.


Checking file edges_shas.txt:
file-path: /users/2635474/sharedscratch/1/MS1_edges_shas.txt
json shasum:  27974edb4bf8f3b17b00ff3a72a703da18f3807a
local shasum: 27974edb4bf8f3b17b00ff3a72a703da18f3807a
shasum matches.


*** Files' shasum verified. ***


JLIBS_FOLDER: jlibs
--2023-08-11 13:19:53--  http://78.46.92.120/MS-BioGraphs/jlibs.zip
Connecting to 78.46.92.120:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 48263234 (46M) [application/zip]
Saving to: ‘jlibs.zip’

100%[=================================================================================================>] 48,263,234  12.2MB/s   in 3.9s   

2023-08-11 13:19:57 (11.7 MB/s) - ‘jlibs.zip’ saved [48263234/48263234]

Archive:  jlibs.zip
   creating: jlibs/
  inflating: jlibs/mg4j-4.0.3.jar    
  inflating: jlibs/stax-api-1.0.1.jar  
  inflating: jlibs/fastutil-8.5.6.jar  
  inflating: jlibs/commons-io-2.4.jar  
  inflating: jlibs/log4j-1.2.17.jar  
  inflating: jlibs/commons-collections-20040616.jar  
  inflating: jlibs/mg4j-big-4.0.4.jar  
  inflating: jlibs/jung-algorithms-2.0.1.jar  
  inflating: jlibs/lama4j-1.1.1.jar  
  inflating: jlibs/commons-lang-2.6.jar  
  inflating: jlibs/guava-31.0.1-jre.jar  
  inflating: jlibs/concurrent-1.3.4.jar  
  inflating: jlibs/commons-configuration-1.8.jar  
  inflating: jlibs/logback-classic-1.1.6.jar  
  inflating: jlibs/collections-generic-4.01.jar  
  inflating: jlibs/sux4j-5.2.3.jar   
  inflating: jlibs/slf4j-api-1.7.14.jar  
  inflating: jlibs/fastutil-core-8.5.6.jar  
  inflating: jlibs/wstx-asl-3.2.6.jar  
  inflating: jlibs/jung-io-2.0.1.jar  
  inflating: jlibs/colt-1.2.0.jar    
  inflating: jlibs/joda-time-2.5.jar  
  inflating: jlibs/commons-lang3-3.4.jar  
  inflating: jlibs/law-2.7.2.jar     
  inflating: jlibs/jung-api-2.0.1.jar  
  inflating: jlibs/webgraph-3.6.10.jar  
  inflating: jlibs/fastutil-extra-8.5.4.jar  
  inflating: jlibs/logback-core-1.1.6.jar  
  inflating: jlibs/law-library-2.6.0.jar  
  inflating: jlibs/webgraph-big-3.6.6.jar  
  inflating: jlibs/jsap-2.1.jar      
  inflating: jlibs/commons-math3-3.6.jar  
  inflating: jlibs/commons-logging-1.1.1.jar  
  inflating: jlibs/dsiutils-2.6.17.jar  
Java libararies downloaded.

SHA Calculator for MS-BioGraphs Arc-Labeled Graphs Using WebGraph Library
  graph_file (args[0]):  /users/2635474/sharedscratch/1/MS1
  output_file (args[1]): MS1_local_edges_shas.txt
  offsets_file:  /users/2635474/sharedscratch/1/MS1_offsets.bin
  threads_count: 128
  Graph Init. Time: 5.25 seconds
  RandomAccess: true
  Arc labeled: Yes
  #Nodes: 43,144,218
  #Arcs: 2,660,495,200
  block_size:   67,108,864
  blocks_count: 40

  2023/08/11 13:20:05.050 Step 0: Started.
  2023/08/11 13:20:33.455 Step 0: Done.
  2023/08/11 13:20:33.456 Step 0: Setting blocks' start points completed.

  2023/08/11 13:20:33.457 Step 1: Started.
  2023/08/11 13:21:16.487 Step 1: Done.
  processed_edges: 2,660,495,200
  2023/08/11 13:21:16.503 Step 1: Writing SHAs to files completed.


        Exec. Time for creating sha of edge blocks: 76.71 seconds


The shasums of edge blocks matches.

Total exec. time: 85,709.526 (ms)
```

