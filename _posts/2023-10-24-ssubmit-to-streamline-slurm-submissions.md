## ssubmit for streamlined slurm submissions
Job management systems like SLURM let you get your shiz computed quicker, but frequently will see you spending a lot of time drafting one-off submission scripts.

Came across this software this week, and wish it had been on my radar sooner:

https://github.com/mbhall88/ssubmit

Written in Rust, read - lightning fast install/exec

Installation was pretty straightforward: in my ecosystem, I just needed to modify the installation path to my local user bin (which should be available on your PATH)
```tsql
wget -nv -O - install.ssubmit.mbh.sh | sh -s -- -b /home/robert.plant/.local/bin
 ```
An example of a submission as a non-sudo HPC user, wherein you will need to chain your commands to load software modules/virutal environments:
```tsql
ssubmit gtdb "module load gtdb-tk/2.1.1 && gtdbtk classify_wf --genome_dir ISO_CV59_ONT --out_dir gtdb -x fasta --cpu 10 && module purge" -m 1000
```
This submits a job named "gtdb", which chains together 3 commands: loading the GTDBtk module, running the classification workflow, and unloading the module, requesting 1000kb of memory (yes, I know..)

Adding the "-n" flag gives you a dry run so you can preview the script before submission:
```tsql
ssubmit gtdb "module load gtdb-tk/2.1.1 && gtdbtk classify_wf --genome_dir ISO_CV59_ONT --out_dir gtdb -x fasta --cpu 10 && module purge" -m 1000 -n
```
et voila':
```tsql
INFO ssubmit Dry run requested. Nothing submitted
sbatch <script>
=====<script>=====
#!/usr/bin/env bash
#SBATCH --job-name=gtdb
#SBATCH --mem=1K
#SBATCH --time=168:0:0
#SBATCH --error=%x.err
#SBATCH --output=%x.out
set -euxo pipefail

module load gtdb-tk/2.1.1 && gtdbtk classify_wf --genome_dir ISO_CV59_ONT --out_dir gtdb -x fasta --cpu 10 && module purge
=====<script>=====
```
Aside from trying not to estimate how many hours of coding this could have saved me over the past few years (;´༎ຶД༎ຶ`)... I am looking forward to incorporating this into my day-to-day work, mainly to replace shorter scripts with <=3 commands (more than that, the chaining becomes unwieldly I think).
