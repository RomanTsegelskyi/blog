---
layout: post
title: Good Practices for Writing R Packages
desc: List of good practices that are used to create and maintain R packages
keywords: R, packages, practices, development, rstats, roman, Roman, Tsegelskyi, romantsegelskyi
comments: True
---

Recently I have finished working on my first R package - [yummlyr](https://github.com/RomanTsegelskyi/yummlyr), and decided to write a short post on best practices for creating R packages that I have encountered. There are many different manuals on creating R packages that can be found over the internet, with great [R packages](http://r-pkgs.had.co.nz/) book by [Hadley Wickham](http://had.co.nz/) for in depth study of the topic, while this post aims to give a short overview of best practices and tools for writing R packages, drawing some inspiration from similar [post](https://pascalhertleif.de/artikel/good-practices-for-writing-rust-libraries/) for [Rust](http://rust-lang.org). 

### What is an R package?

R packages was one of the most fundamental things of the language. Essentially, it's a well defined format for distributing R code typically through [CRAN](https://cran.r-project.org/) and [Bioconductor](https://www.bioconductor.org/). CRAN took it's idea from [CPAN](http://www.cpan.org/) for Perl and now holds over 7000 packages. 

### Best Practices 

#### Simplify development process with devtools

Hadley Wickham wrote an amazing [devtools](https://github.com/hadley/devtools) package, which simplifies all the process around development with R. 
For example, creating a package is as easy as calling 

```r
devtools::create()
```

Generally, I feel that the best piece of advice you can take from this article, is to maximize the usage of `devtools`. Whatever task you need to perform, first look into `devtools`, it's very likely that there is a function for that already.

#### Use Rcpp for compiled code

One of the things that distinguishes R from other languages is it's interfaces. It's deeply embedded into how language was designed thus providing great methods for interacting with other systems, in particular compiled code. R is great in executing compiled C/C++ code, especially with Rcpp. Unless you have some code that is already written and you just want to hook it up to R, strongly recommend using `Rcpp` for writing optimized code that is used in R. The simples way to start using `Rcpp` in your package is through `devtools`: 

```
devtools::use_rcpp()
```

Which will create `src/` directory and first file in it and update `DESCRIPTION` for you. Great overview on Rcpp can be found in [Advanced R book](http://adv-r.had.co.nz/Rcpp.html).

#### Test code with testthat/runit

There are two main packages used for testing throughout R community - [testthat](https://github.com/hadley/testthat) and [runit](https://cran.fhcrc.org/web/packages/RUnit/index.html). `testthat` draws inspiration from the xUnit family of testing packages, as well from many of the innovative ruby testing libraries, while `runit` aims to provide similar testing experience to [junit](http://junit.org/). Also `runit` seems to be one of the first testing frameworks for R, but recently `testhat` have been more popular and I rarely see new packages using `runit`. 

With `testthat` being a tool from HadleyVerse, it is also very easy to set it up with `devtools`

```
devtools::use_testthat()
```

A test file lives in `tests/testthat/`. Its name must start with test. Good example of using `testthat` for testing is [devtools test suite](https://github.com/hadley/devtools/tree/master/tests/testthat). More detailed information can be found on `testthat` [here](http://r-pkgs.had.co.nz/tests.html).

#### Put the package on GitHub with useful README.md

Putting the package on GitHub will allow the user of your package to get the development version with `devtools`, thus enabling you to deliver bug fixes/new features faster than going through formal process of submitting a package to CRAN/Bitbucket. Additionally I find it much easier to manage bug reports though GitHub issues. Also consider creating a detailed `README.md`, which in a lot of cases will serve the first introduction to your package. More and more now I find myself just going to the GitHub page of the package to find out how it works. And judging from [these comments](https://www.reddit.com/r/rstats/comments/3t2bni/good_practices_for_writing_r_packages/) it is a general trend in R community. 

#### Use Travis and AppVeyor for Continious Integration

During last year, I have seen a significant improvement of CI systems supporting R. Even though most of the tools are written by community, they are robust and make it relatively easy to setup. There are 2 systems, which I have seen primarily used in many packages (which are free for open source projects):

* [Travis](https://travis-ci.org) - arguably most popular CI system. Supports R almost as first class citizen, configuration as simple as adding 

```
language: r
sudo: required
```

to `travis.yml`. As with many tools here, this is automated with `devtools::use_travis()`.

* [AppVeyor](http://www.appveyor.com/) - CI systems for Windows and a must to have if you are using compiled code, otherwise there not that many cross-platform issues with R. Can be setup with `devtools::use_appveyov()`.

#### Measure code coverage with covr

Last year, Jim Hester created [covr](https://github.com/jimhester/covr/) package for measuring test coverage. Use

```
covr::package_coverage()
```

to get quick statistics for code coverage of your package and 

```
covr::shine(covr::package_coverage())
```

to inspect line by line code coverage inside shiny app.

Also `covr` is supported by [coveralls.io](https://coveralls.io) and [codecov.io](https://codecov.io) with allows a nice integration of code coverage into reporting into CI pipeline. In essence, to measure coverage with CI, you will only need to add `covr::coveralls` as part of your CI build. Just call,

```
devtools::use_coverage(type='coveralls')
```

which will do the job of updating `.travis.yml`. Replace `coveralls` with `codecov` if you want to use it instead.

#### Enforce coding style with lintr or formatR

The [formatR](http://yihui.name/formatR/) is a package created by [Yihui Xie](http://yihui.name/) which aims at reformating R code to improve readability; if you are familiar with [gofmt](https://golang.org/cmd/gofmt/) or [rustfmt](https://github.com/nrc/rustfmt), you will easily understand the appeal of the package.

Most obvious usage is 

```
formatR::tidy_dir()
```

which, will format all the files in the package. 

The [lintr](https://github.com/jimhester/lintr) package by [Jim Haster](http://www.jimhester.com/) takes a different approach. Rather than trying to fix the error by itself it simply reports them to the user. It is easy to use inside major editors. I prefer lintr since it allows to write custom lints and is a bit more configurable in my opinion.

Using `lintr` is also very simple, just call

```
lintr::lint_package()
```

For persistent project configuration create `.lintr` file as explained [here](https://github.com/jimhester/lintr#project-configuration).

#### Document objects with roxygen2

Standard way of documenting the objects and functions inside the packages is to create `.Rd` files and store them in `man/` directory. However, 
[Roxygen2](https://github.com/klutometis/roxygen) packages provides a more preferred solution to that. [Roxygen2](https://github.com/klutometis/roxygen) dynamically inspects the objects that it documents, and generates the `.Rd` files for you. Moreover, the package also generates the `NAMESPACE` file, which is mainly responsible for defining which functions should be exported from the package (more can be found [here](http://r-pkgs.had.co.nz/namespace.html#namespace)). It draws inspiration from [doxygen](http://www.stack.nl/~dimitri/doxygen/), and it will be simple to pick it up for people familiar with C/C++.

A simple example of function documentation looks like this:

```
#' Documentation title
#' 
#' More description
#' @param x parameter documentation.
#' @param y parameter documentation.
#' @return Description of the return value.
#' @examples
#' Some usage example
func <- function(x, y) {
  ...
}
```

After documenting the objects in this way, call

```
devtools::document()
```

or

```
roxygen2::roxygenize()
```

which will generate the `.Rd` files for you. 

For more information on possible tags and examples, take a look at [Object Documentation](http://r-pkgs.had.co.nz/man.html) chapter of [R packages book](http://r-pkgs.had.co.nz/).

#### Create Vignettes with knitr

Vignettes are the form of long-term documentation for R packages. They somewhat resemble usage manuals, papers or book chapters. Typically they are aimed at in-depth description of a particular use case or implementation details of the package. The easiest way to create vignettes now is to use [knitr](yihui.name/knitr/) packages.

To create your first vignette run 

```
devtools::use_vignette("my-vignette")
```

Which creates, `vignettes` folder and the `my-vignette.Rmd` file inside it. Rmd a file extension for [R markdown](http://rmarkdown.rstudio.com/), which is a flavor of markdown, designed to create reports with embedded R code. I won't go into much detail about markdown, which can be found [here](https://help.github.com/articles/markdown-basics/) for example, so I think it is more useful to give a short primer `knitr` usage instead. 

[knitr](yihui.name/knitr/) is an R package created by [Yihui Xie](http://yihui.name/) which allows intermingling of code, results and text to create documents of different types. `knitr` takes R code, runs it, captures the output, and translates it into formatted Markdown. For example, this snippet

```
```{r}
1:10
\`\`\`
```
and covered by `knitr` rendered like 

```r
1:10
## [1]  1  2  3  4  5  6  7  8  9 10
```

After you are done with creating vignettes, run `devtools::build_vignettes()`, which will build the vignette in different formats and copy them to `inst\doc`.

For more information and great [demos](http://yihui.name/knitr/demos/) refer to `knitr`'s [web-page](http://yihui.name/knitr/demos/). 

### P.S.

Thanks for reading this post. I hope it was useful and practices listed here will simplify creating R package for you. If you have any comments/suggestions, please reach out to me in [twitter](https://twitter.com/romantsegelskyi), this [reddit thread](https://www.reddit.com/r/rstats/comments/3t2bni/good_practices_for_writing_r_packages/) or this [HackerNews thread](https://news.ycombinator.com/item?id=10576942). 
