
--- 
title: "rTorch + PyTorch"
author: "Alfonso R. Reyes"
date: "2019-09-20"
site: bookdown::bookdown_site
documentclass: book
bibliography: [book.bib, packages.bib]
biblio-style: apalike
link-citations: yes
description: "This is a minimal tutorial of using the `rTorch` package to have fun while doing machine learning. This book was produced with `bookdown`."
---

# Prerequisites {-}

You need two things to get `rTorch` working:

1. Install Python [Anaconda](). Preferrably, for 64-bits, and above Python 3.6+.

2. Install [R](), [Rtools]() and [RStudio]().

3. Install `rTorch` from CRAN or GitHub.


> Note. It is not mandatory to have a previously created `Python` environment  with `Anaconda`, where `PyTorch` and `TorchVision` have already been installed. This step is optional. You could also get it installed directly from the `R` console, in very similar fashion as in [R-TensorFlow]() using the function `install_pytorch`.


This book is available online via [GitHub Pages](), or you can also build it from source from its [repository]().


## Installation {-}

`rTorch` is available via CRAN or GitHub.

The **rTorch** package can be installed from CRAN or Github.

From CRAN:


```r
install.packages("rTorch")
```


From GitHub, install `rTorch` with: 


```r
devtools::install_github("f0nzie/rTorch")
```


## Python Anaconda {-}
Before start running `rTorch`, install a Python Anaconda environment first. 

### Example {-}

1. Create a `conda` environment from the terminal with `conda create -n myenv python=3.7`

2. Activate the new environment with `conda activate myenv`

3. Install the `PyTorch` related packages with:  

`conda install python=3.6.6 pytorch-cpu torchvision-cpu matplotlib pandas -c pytorch`

The last part `-c pytorch` specifies the conda channel to download the PyTorch packages. Your installation may not work if you don't indicate the channel.


Now, you can load `rTorch` in R or RStudio.

### Automatic installation {-}
I use the idea from automatic installation in `r-tensorflow`, to create the function `rTorch::install_pytorch()`. This function will allow you to install a `conda` environment complete with all `PyTorch` requirements.


>**Note.** `matplotlib` and `pandas` are not really necessary for `rTorch` to work, but I was asked if `matplotlib` or `pandas` would work with `PyTorch`. So,  I decided to install them for testing and experimentation. They both work.



