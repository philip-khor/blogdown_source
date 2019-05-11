---
title: 'My first package: bnmr'
author: "Philip Khor"
date: '2019-05-04'
slug: my-first-package-bnmr
tags: []
categories: []
---

[bnmr](/bnmr) is a wrapper for `httr` to access BNM (Malaysia's central bank)'s [recently released API](api.bnm.gov.my). I wrote this to practice writing R packages, with lots of help from the [R Packages](http://r-pkgs.had.co.nz/) book. 

Using bnmr is really simple, e.g. to get interest rate data in January 2019: 

```r
interest_rate(year = 2019, month = 1)
```

## Exception handling 

I appreciate [Daniel's](https://github.com/DanielYuo) assistance in adding some exception handling to the `base_rate()` function.

I realised how little I know about exception handling in R while writing this package. 
Specifically, I learned along the way that you should use NULL for optional arguments [instead of missing()](https://www.youtube.com/watch?v=dwS67RPq37Q). 

## Vectorise

The BNM API allows the user to access some variables with daily measures. Ideally, bnmr functions should be vectorised, so that I could simply call `interest_rate(year = 2019)` and obtain interest rates throughout 2019. However, the daily API endpoints only have three options - do not specify anything, specify date, or specify year/month combination. To obtain interest rates in a given year, you would have to make the following GET requests:

- GET interest-rate/year/2019/month/1
- GET interest-rate/year/2019/month/2
- GET interest-rate/year/2019/month/3
- GET interest-rate/year/2019/month/4

Right now `interest_rate(year = 2019)` works. The API seems to have a very low request limit. After a few attempts I run into a HTTP 429 error.

I sloppily included some preliminary vectorisation code in `opr()`, which returns the overnight policy rate. However, place too many simultaneous API calls and the API will stop responding. Right now, I add a short time stop in between calls.

Moving forward, I'll be working on [exposing errors within vectorised operations](https://gist.github.com/stephlocke/dc72be42e39997bef894dfc95b67fd8a).

## Returning tidy data 

Right now the functions in this package sometimes return lists, and sometimes data frames, in accordance with `jsonlite`'s simplifying rules. I do not just want to return a tibble - the data should be returned in a tidy format. Currently I'm performing the reshaping using `tidyr::gather()` and `tidyr::spread()` in the `renminbi_tbl()` function.

## pkgdown and documentation

pkgdown makes it very simple to create a [website](http://philip-khor.github.io/bnmr) for my package, which runs on GitHub Pages. Along the way, I found it very hard to write useful documentation - so the documentation, like the package, is also a work in progress. 

## testing

I've added test coverage and continuous integration with Travis using the usethis package. However, I have my reservations about testing too much in the case of bnmr - currently, if the API fails, the tests necessarily fail. As I'm concerned that too much testing may cause the API to reject calls, I'm currently using the in-documentation examples as my test mechanism. 

## Interested? 

If you want to try out bnmr, you'll need the devtools package. Run the following code: 

```r
devtools::install_github("philip-khor/bnmr")
```

Alternately, use pak:

```r
pak::pkg_install("philip-khor/bnmr")
```

