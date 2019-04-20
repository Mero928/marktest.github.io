---
title: "Economic Model"
author: "AmrMoslim"
date: "2019-04-20"
output:
  prettydoc::html_pretty:
    theme: tactile
    highlight: github
    toc: yes
    keep_md: yes
    fig_caption: yes
---




## Creating Economic Model for Oil and Gas Projects _**EcoMan**_
### Writing The Economic Functions


```r
### Writing the Economic Functions to calculate NPV, IRR and ROP

dcf <- function(x, r, t0=FALSE){
  # calculates discounted cash flows (DCF) given cash flow and discount rate
  #
  # x - cash flows vector
  # r - vector or discount rates, in decimals. Single values will be recycled
  # t0 - cash flow starts in year 0, default is FALSE, i.e. discount rate in first period is zero.
  if(length(r)==1){
    r <- rep(r, length(x))
    if(t0==TRUE){r[1]<-0}
    }
  x/cumprod(1+r)
 }

npv <- function(x, r, t0=FALSE){
  # calculates net present value (NPV) given cash flow and discount rate
  #
  # x - cash flows vector
  # r - discount rate, in decimals
  # t0 - cash flow starts in year 0, default is FALSE
  sum(dcf(x, r, t0))
 }

pbp <- function(x, ...){
  # calculates payback period (PBP)
  #
  # x - cash flows vector
  # ... - ignored
  i <- match(1, sign(cumsum(x)))
  i-2+(-cumsum(x)[i-1]/x[i])
 }

dpbp <- function(x, r, t0=FALSE){
  # calculates discounted payback period (DPBP) given cash flow and discount rate
  #
  # x - cash flows vector
  # r - discount rate, in decimals
  # t0 - cash flow starts in year 0, default is FALSE
  pbp(dcf(x, r, t0))
 }

irr <- function(x, t0=FALSE, ...){
  # calculates internal rate of return (IRR) given cash flow 
  #
  # x - cash flows vector
  # t0 - cash flow starts in year 0, default is FALSE
tryCatch(uniroot(f=function(i){sum(dcf(x, i, t0))}, 
                 interval=c(0,1))$root,
         error=function(e) return(NA)
        )
  }
```
## The Input data
We have two different projects to be evaluated based on the folowing economic parameters:  
- Net Present Value _**NPV**_  
- Internal Rate of Return _**IRR**_  
- Pay back Period _**PBP**_  
- Discounted Pay back Period _**DPBP**_  
- Discounted Cash Flow _**DCF**_  


```r
### the Two Project that needed to be evaluated
# To evaluate the two projects they should have the same project time
#together to evaluate them together or you should evaluate them separately
  Time.Evaluation <-c(1:6)
  Project1 <- c(-1000, 1250, 10, 10, 20, 20)
  Project2 <- c(-1000, -10, 0, 10, 20, 2000)
  Projects.data <- data.frame(Time.Evaluation, Project1 , Project2)
  knitr::kable(Projects.data)
```



 Time.Evaluation   Project1   Project2
----------------  ---------  ---------
               1      -1000      -1000
               2       1250        -10
               3         10          0
               4         10         10
               5         20         20
               6         20       2000

## The Method of Evaluations

