---
permalink: /projects/bayes/
title: "Bayesian inference in R"
---

<small>Use of the Bayes factor (BF) has become more popular in cognitive training studies during the recent years. It allows researchers to examine the likelihood of their data both under null hypothesis (e.g., no effect following an experimental manipulation) and the alternative hypothesis (e.g., an effect following an experimental manipulation). In this project I will demonstrate how to conduct Bayesian inference in an experimental study design. For those who are interested to take a look on the whole code related to this project, see https://osf.io/q7xe2/).</small>
<p>
```r
# Libraries
library(shiny)
library(ggplot2)
library(rsconnect)
library(tidyverse)

# Load dataframe
df_wmc <- read.csv("df_wmc.csv")
```
