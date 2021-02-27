#!/usr/bin/env python3

import os
import sys
import argparse
import logging
import subprocess
import re
from pathlib import Path
import datetime
import glob
import time
import pandas as pd
from shutil import copyfile
from itertools import groupby


parser = argparse.ArgumentParser(prog='merge_runs', 
description='Merge runs that where stopped and restarted, typically in case of a power cut. You can choose to  \
i) -r  automatically merge all folders fastq_pass in a run directory \n \
ii) -d select the fastq_pass directories you want to merge (in case you basecalled after the run, \
link that directory and not the initial fastq_pass directory)', 
formatter_class=lambda prog: argparse.HelpFormatter(prog,max_help_position=30,width=200))
parser.add_argument('--run','-r', action='store', dest='run', type=str, required=True, metavar='RUNID', help='Path to the run you want to merge. Example: /media/ngs/NAME_OF_STORAGE/RUN_NAME')
parser.add_argument('--dir','-d', action='store', dest='fastqPassDirs', nargs='*', help='Path to the fastq_pass directories you want to merge. Example: /media/ngs/NAME_OF_STORAGE/RUN_NAME/no_sample/FAL...../fastq_pass /media/ngs/NAME_OF_STORAGE/RUN_NAME/no_sample/FAL...../fastq_pass')

inputArgs = parser.parse_args()
RUN = inputArgs.run

os.makedirs(RUN + '/merge/fastq_pass',exist_ok=True)


FastqDirectories= list()
if not inputArgs.fastqPassDirs:
    FastqDirectories = glob.glob(RUN +"/*/*/fastq_pass/barcode*")
else:
    FastqDirectories = [glob.glob(fastqdir + '/barcode*') for fastqdir in inputArgs.fastqPassDirs]
    FastqDirectories = [val for sublist in FastqDirectories for val in sublist]

grouped = {}
keys = []
for elem in FastqDirectories:
    key = elem.split('/')[-1]
    grouped.setdefault(key, []).append(elem)
    keys.append(key)

for key in keys:
    dstDir = RUN + '/merge/fastq_pass/' + key 
    os.makedirs(dstDir,exist_ok=True)
    fastqFiles = glob.glob(RUN +'/*/*/fastq_pass/'+key+ '/*.fastq*')
    for fastq in fastqFiles:
        fileNameFastq =  fastq.split('/')[-1]
        if not (os.path.islink(dstDir + '/'+fileNameFastq)):
            os.symlink(fastq, dstDir + '/'+fileNameFastq)


print('Created links to fastq_pass files in ' + RUN + '/merge/fastq_pass')

##Find all sequencing summary files:
seqfiles = glob.glob(RUN + '/**/sequencing_summary_*', recursive=True)
seqfiles.remove(RUN + '/merge/sequencing_summary_merged.txt')
print(seqfiles)

with open(RUN + '/merge/sequencing_summary_merged.txt', 'w') as outfile:
        for seqfile in seqfiles:
            with open(seqfile) as infile:
                for line in infile:
                    outfile.write(line)

