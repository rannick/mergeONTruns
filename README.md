# mergeONTruns
Small program to merge Oxford Nanopore Technologies sequencing runs that were stopped and restarted so all fastq files are symlinked into a merged directory and sequencing summaries files are concatenated

#Usage:

If all fastq_pass directories in a run directory should be merged:
merge_runs.py -r /path/to/run 

If only specific fastq_pass directories should be merged:
merge_run.py -r /path/to/run -d /path/to/fastq_passA /path/to/fastq_passB ... (space separated paths)
