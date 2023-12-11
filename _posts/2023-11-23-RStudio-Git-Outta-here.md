---
layout: post
title: "RStudio? Git Outta here"
description: "Adventures in keys and tokens"
date: 2023-11-23T07:00:00-07:00
tags: HPC R GIT
---
## 
Quickly jotting down notes to use AWS-CLI and PAT to resolve issues installing R packages from Github today...
```tsql
module load awscli/1.19.71
```
Using aws to access data from Serratus: https://github.com/ababaian/serratus/wiki/Access-Data-Release

Followed instructions to set up AWS account and access keys: https://github.com/ababaian/serratus/wiki/Running-Serratus
```tsql
aws configure
AWS Access Key ID [None]: XXXXXXXXXXX
AWS Secret Access Key [None]: YYYYYYYYYY
Default region name [None]: us-east-1
Default output format [None]:
```
And away with querying:
```tsql
aws s3 ls s3://public-repo/folder/ #list files
aws s3 cp s3://public-repo/folder/file.tsv . #download file locally
```
Round Two... swapping to R and trying to install the palmid package
```tsql
library(devtools)
options(download.file.method = "wininet")
install_github("ababaian/palmid")
---
Downloading GitHub repo ababaian/palmid@HEAD
Error: Failed to install 'palmid' from GitHub:
Git does not seem to be installed on your system
```
What eventually worked was 1) Installing the executable Git rather than the Portable Git version (to D drive) 2) During the install, allowing modification of the PATH by Git from the command line. "3) Adding the path to the executable to Tools > Global Options > Git/SVN (in RStudio)  
Bonus problem of exceeding my rate limit in my earnestness to troubleshoot the package woes. I could have waited 40 minutes, but instead I set up a PAT (Permanent Access Token) via Github which in theory, or rather practice, raises the rate limit for access/download attempts.
```tsql
usethis::create_github_token() #opens browser window for authentication and token generation
usethis::git_sitrep() #opens .renviron file - add "GITHUB_PAT=XXXXXXXXXXXXXXXX" here, save and reboot RStudio.
```

Bonus resource: https://happygitwithr.com/rstudio-see-git
