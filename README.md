CRAN packages: devtools, bigmemory, lme4breeding, shiny, pedigreemm, pedigree, learnr

GitHub package: blupADC is on GitHub, not CRAN.

Also, I’m assuming lem4breeding was a typo for lme4breeding. That package is on CRAN.

Step 1: make an offline folder on the online computer

In R:

dir.create("C:/offline_packages", showWarnings = FALSE, recursive = TRUE)
dir.create("C:/offline_packages/cran", showWarnings = FALSE, recursive = TRUE)
dir.create("C:/offline_packages/source", showWarnings = FALSE, recursive = TRUE)
Step 2: download all CRAN packages as Windows binaries

This covers devtools, bigmemory, lme4breeding, shiny, pedigreemm, pedigree, and learnr, which are all available from CRAN.

Run:

pkgs <- c(
  "devtools",
  "bigmemory",
  "lme4breeding",
  "shiny",
  "pedigreemm",
  "pedigree",
  "learnr"
)

deps <- tools::package_dependencies(pkgs, recursive = TRUE)
all_pkgs <- sort(unique(c(pkgs, unlist(deps))))

download.packages(
  pkgs = all_pkgs,
  destdir = "C:/offline_packages/cran",
  repos = "https://cran.r-project.org",
  type = "win.binary"
)

That will save a pile of .zip files in C:/offline_packages/cran.

Step 3: prepare blupADC for offline install

blupADC is distributed from GitHub, and its install instructions use install_github().

For offline use, the cleanest approach is to build a source tar.gz on the online machine.

3A. Install the tools you need online
install.packages(c("remotes", "devtools"))
3B. Download the GitHub repo locally
remotes::install_github("TXiang-lab/blupSUP")
remotes::install_github("TXiang-lab/blupADC")

The blupADC repo notes that the latest version needs blupSUP first.

3C. Build local source packages

In R:

dir.create("C:/offline_packages/github_src", showWarnings = FALSE)

remotes::download_github("TXiang-lab/blupSUP", destdir = "C:/offline_packages/github_src")
remotes::download_github("TXiang-lab/blupADC", destdir = "C:/offline_packages/github_src")

If download_github() is unavailable in your setup, do this instead:

install.packages("gitcreds")

Or simpler: download the ZIPs from GitHub in your browser, unzip them, then build them:

devtools::build("C:/path/to/blupSUP-folder", path = "C:/offline_packages/source")
devtools::build("C:/path/to/blupADC-folder", path = "C:/offline_packages/source")

You want the result to be files like:

C:/offline_packages/source/blupSUP_x.y.z.tar.gz
C:/offline_packages/source/blupADC_x.y.z.tar.gz
Step 4: copy the whole folder to the offline computer

Copy this entire directory:

C:/offline_packages
Step 5: set up the offline computer first

Before installing anything offline, make sure the offline computer has:

Windows 64-bit

R 4.2.3

Rtools42

That matters especially for packages with compiled code like bigmemory, lme4breeding, pedigreemm, and blupADC.

Step 6: install the CRAN binaries offline

On the offline computer:

install.packages(
  list.files("C:/offline_packages/cran", full.names = TRUE),
  repos = NULL,
  type = "win.binary"
)
Step 7: install the GitHub source packages offline

Install blupSUP first, then blupADC:

install.packages(
  "C:/offline_packages/source/blupSUP_x.y.z.tar.gz",
  repos = NULL,
  type = "source"
)

install.packages(
  "C:/offline_packages/source/blupADC_x.y.z.tar.gz",
  repos = NULL,
  type = "source"
)
Step 8: test everything
library(devtools)
library(bigmemory)
library(lme4breeding)
library(shiny)
library(pedigreemm)
library(pedigree)
library(learnr)
library(blupADC)
One important caution

devtools pulls a very large dependency tree. learnr and shiny also pull quite a few packages. So the offline folder may get big. blupADC may also rely on external breeding tools or helper assets depending on which features you use; its repo explicitly mentions blupSUP and example software/data.

Best all-in-one script for your CRAN set

Use this on the online machine:

dir.create("C:/offline_packages/cran", showWarnings = FALSE, recursive = TRUE)

pkgs <- c(
  "devtools",
  "bigmemory",
  "lme4breeding",
  "shiny",
  "pedigreemm",
  "pedigree",
  "learnr"
)

deps <- tools::package_dependencies(pkgs, recursive = TRUE)
all_pkgs <- sort(unique(c(pkgs, unlist(deps))))

download.packages(
  pkgs = all_pkgs,
  destdir = "C:/offline_packages/cran",
  repos = "https://cran.r-project.org",
  type = "win.binary"
)

writeLines(all_pkgs, "C:/offline_packages/cran/package_list.txt")

If you want, I can give you the next script specifically for building blupSUP and blupADC into offline-installable .tar.gz files.
