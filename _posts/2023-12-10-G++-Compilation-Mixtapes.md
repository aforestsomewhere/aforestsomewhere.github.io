---
layout: post
title: "(g++) Compilation Mixtape"
description: Compiling from source in a virtual conda env
date: 2023-12-10T07:00:00-07:00
tags: HPC Bash
---

## 
Trying to do a post per week, despite it being absolutely hectic these days, and needed to install Palmscan (https://github.com/rcedgar/palmscan) locally in the line of peer review.

This is in the grand scheme of things a non-event, but I am certain that the me-of-two-years-ago would definitely have abandoned the installation from source, so incremental progress maybe? Debateable... but in any case:
```tsql
#clone the repo
git clone https://github.com/rcedgar/palmscan
cd palmscan/src                                                          /
```
At this point, I recalled from *past trauma* that the current system-wide install of g++ would not cut the mustard. You can check the gcc version like so:
```tsql
g++ --version
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-44)
Copyright (C) 2015 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```
To get around this, a more modern version of g++ can be installed via conda-forge:
```tsql
conda create -n serratus_env
conda activate serratus_env
conda install -c conda-forge gxx
g++ --version
g++ (conda-forge gcc 12.2.0-19) 12.2.0
Copyright (C) 2022 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```
Yet, when I tried to compile using the Makefile (under /src), it was failing to locate the -lgomp library (OpenMP)
```tsql
make
g++  -O3 -fopenmp -pthread -lpthread -static o/abcxyz.o o/alignpalm.o o/align_msas.o o/alloc.o o/alpha.o o/alpha2.o o/annotate.o o/blosum.o o/blosum62.o o/buildpsm.o o/build_rdrp_model.o o/cal2fa.o o/calreader.o o/cddata.o o/cdinfo.o o/cdp_search.o o/cdsearcher.o o/cdp_train.o o/cdtemplate.o o/chainreader.o o/chains_subsample.o o/checkppc.o o/classify.o o/classifytm.o o/clusters2model.o o/cluster_motifs.o o/cluster_ppc.o o/cluster_ppm.o o/dali.o o/distmx.o o/fasta_xlat.o o/findcavity.o o/gspalign.o o/gspaligner.o o/gsprof.o o/gumbel.o o/hse.o o/hse_cmp.o o/hse_train.o o/palmprint_pssms.o o/pamerger.o o/pamerge.o o/palmcator.o o/pseudo_cb_angles.o o/kabsch.o o/palmcore.o o/palmcore_cmp.o o/palmcore_pssms.o o/pml.o o/ppcontactmap.o o/find_tri.o o/getchains.o o/cfilter.o o/pdbinfo.o o/pdb_pp.o o/ppcalignertm.o o/ppcontactmap_pssm.o o/ppcprofile.o o/ppcsearcher.o o/build_ppp.o o/ppc_score.o o/ppfeatures.o o/ppp.o o/ppsearcher_permuted.o o/cmp.o o/cmsearcher.o o/cm_search.o o/cmp_train.o o/cmp_search.o o/cmp_train_pssm.o o/readppc.o o/removehidden.o o/remove_dupes.o o/cluster_cl.o o/diagbox.o o/fastaseqsource.o o/fastqrec.o o/fileseqsource.o o/getss.o o/help.o o/invertmx.o o/linereader.o o/lockobj.o o/logaln.o o/model_strings.o o/model_strings_rdrp.o o/mx.o o/myutils.o o/ntmx.o o/objmgr.o o/one2three.o o/pssm_output.o o/outputfiles.o o/palmscan2_main.o o/palmsketch.o o/pdb2cal.o o/pdbchaincal.o o/ppcaligner.o o/readchains.o o/gddgateprob.o o/calpp2ppc.o o/scop40.o o/scop40_firstfp.o o/search3d_tri.o o/pdbchain.o o/psms.o o/pssm.o o/quarts.o o/rdrpsearcher.o o/search3d_cal.o o/searchaatop.o o/searchc.o o/search_ppp.o o/search_pssms.o o/searchgroup.o o/search3d_pssms.o o/searchparams.o o/search3d_ppc.o o/searchtriangle.o o/segs.o o/smooth.o o/spher.o o/split_train_test.o o/stock.o o/stock2fasta.o o/stocks2pssms.o o/structprof.o o/structprofheuristics.o o/sw.o o/test_pssms.o o/seqdb.o o/seqinfo.o o/seqsource.o o/sfasta.o o/sort.o o/rdrpmodel.o o/test.o o/test_sw.o o/timing.o o/tma.o o/tmhack.o o/tmscore.o o/tm_ppc.o o/tracebackbitmem.o o/triangle.o o/triangles.o o/trifinder.o o/triform.o o/trisearcher.o o/tshit.o o/tshitmgr.o o/tsoutput.o o/cmpsearcher.o o/validate.o o/viterbifastmem.o o/xalign.o o/xalign2.o o/xalign_calibrate.o o/xbasis.o o/xprof.o o/xprofdata.o o/xprof_train.o o/xtrainer.o  -o ../bin/palmscan2
/data/Food/analysis/R6564_NGS/Katie_F/conda/serratus_env/bin/../lib/gcc/x86_64-conda-linux-gnu/12.2.0/../../../../x86_64-conda-linux-gnu/bin/ld: cannot find -lgomp: No such file or directory
/data/Food/analysis/R6564_NGS/Katie_F/conda/serratus_env/bin/../lib/gcc/x86_64-conda-linux-gnu/12.2.0/../../../../x86_64-conda-linux-gnu/bin/ld: /data/Food/analysis/R6564_NGS/Katie_F/conda/serratus_env/bin/../x86_64-conda-linux-gnu/sysroot/usr/lib/../lib/libpthread.a(libpthread.o): in function `sem_open':
(.text+0x77cd): warning: the use of `mktemp' is dangerous, better use `mkstemp'
collect2: error: ld returned 1 exit status
make: *** [../bin/palmscan2] Error 1
```
 ![](https://media.giphy.com/media/l0MYB17ZzaTTpl8S4/giphy.gif)
 
Read a little, decided to try to check where the linker was looking for shared libraries:
```tsql
ld -lgomp --verbose
GNU ld version 2.27-44.base.el7_9.1
  Supported emulations:
   elf_x86_64
   elf32_x86_64
   elf_i386
   elf_iamcu
   i386linux
   elf_l1om
   elf_k1om
using internal linker script:
==================================================
*redacted for brevity*
==================================================
attempt to open //usr/x86_64-redhat-linux/lib64/libgomp.so failed
attempt to open //usr/x86_64-redhat-linux/lib64/libgomp.a failed
attempt to open //usr/lib64/libgomp.so failed
attempt to open //usr/lib64/libgomp.a failed
attempt to open //usr/local/lib64/libgomp.so failed
attempt to open //usr/local/lib64/libgomp.a failed
attempt to open //lib64/libgomp.so failed
attempt to open //lib64/libgomp.a failed
attempt to open //usr/x86_64-redhat-linux/lib/libgomp.so failed
attempt to open //usr/x86_64-redhat-linux/lib/libgomp.a failed
attempt to open //usr/local/lib/libgomp.so failed
attempt to open //usr/local/lib/libgomp.a failed
attempt to open //lib/libgomp.so failed
attempt to open //lib/libgomp.a failed
attempt to open //usr/lib/libgomp.so failed
attempt to open //usr/lib/libgomp.a failed
ld: cannot find -lgomp
```
Aha, incorrect prefix at play! This is probably fine for a sudo install, but once again, for my setup I need to point the linker towards the libraries in my virtual conda environment.
At this point I had a look at the Makefile and tried commenting out the lines setting the library paths
```tsql
BINDIR := ../bin
OBJDIR := o
BINPATH := $(BINDIR)/palmscan2

CPPFLAGS := $(CPPFLAGS) -DNDEBUG -pthread

CXX = g++
CXXFLAGS := $(CXXFLAGS) -O3 -fopenmp -ffast-math -msse -mfpmath=sse

#UNAME_S := $(shell uname -s)
LDFLAGS := $(LDFLAGS) -O3 -fopenmp -pthread -lpthread
#ifeq ($(UNAME_S),Linux)
#    LDFLAGS += -static
#endif
```
And finally, I set up environmental variables to point towards the library locations
```tsql
export LD_LIBRARY_PATH=/data/Food/analysis/R6564_NGS/Katie_F/conda/serratus_env/lib/
export PKG_CONFIG_PATH=/data/Food/analysis/R6564_NGS/Katie_F/conda/serratus_env/lib/
export PREFIX=/data/Food/analysis/R6564_NGS/Katie_F/conda/serratus_env
#note to future self who has the time (heheheh) - retry compilation excluding each of the above variables to determine which are precisely required
```
Now, recompiling from scratch gave a very satisfying log and resulted in an intact executable:
```tsql
make clean && make
cd ../bin
./palmscan2
palmscan2 v2.0.i86linux64
(C) Copyright 2022 Robert C. Edgar

Command:
    -search_pssms seqs.fasta    # input sequences, must be aa

Optional arguments:
    -report_pssms report.txt    # human-readable text report
    -tsv hits.tsv               # hits in tab-separated text
        -fev hits.fev               # hits in field-equals-value
    -threads N                  # threads, default min(number of cores, 10)
        -fasta pp.fa                # pamlmprints, FASTA
        -core core.fa               # palmcore, FASTA
        -minflanklen n              # minimum flank for palmcore (default 0)

Typical usage:
    palmscan -search_pssms seqs.fasta -tsv hits.tsv
```
 ![](https://media.giphy.com/media/xT0GqssRweIhlz209i/giphy.gif)

Realistically, a minor victory over a Minor Threat (https://www.youtube.com/watch?v=4lH9EV_Urxk) (obligatory mixtape reference)
