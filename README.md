<!-- README.md is generated from README.Rmd. Please edit that file -->
plater
======

[![Travis-CI Build Status](https://travis-ci.org/seaaan/plater.svg?branch=master)](https://travis-ci.org/seaaan/plater)

plater makes it easy to work with data from experiments performed in plates. It is aimed at scientists and analysts who deal with microtiter plate-based instruments.

Installation
------------

plater is not currently on CRAN. You can install the latest GitHub version like this:

``` r
install.packages("devtools") # if you don't already have it
devtools::install_github("seaaan/plater")
```

Getting your data in
--------------------

Many scientific instruments (such as plate readers and qPCR machines) produce data in tabular form that mimics a microtiter plate: each cell corresponds to a well as physically laid out on the plate. For experiments like this, it's often easiest to keep records of what was what (control vs. treatment, concentration, sample type, etc.) in a similar plate layout form.

But data in those dimensions aren't ideal for analysis. That's where `read_plate()` and `add_plate()` come in.

-   `read_plate()` takes data in plate layout form and converts it to a data frame, with one well per row, identified by well name.
-   `add_plate()` does the same thing, but merges the new columns into an existing data frame you provide.

In other words, these functions seamlessly convert plate-shaped data (easy to think about) into tidy data (easy to analyze).

To make it even easier, if you have multiple plates in an experiment, use `read_plates()` to read them all in and combine them into a single data frame.

Seeing your data
----------------

Sometimes it's useful to map your data back onto a plate (are the weird outliers all from the same corner of the plate?). For that, there's `view_plate()`, which takes a data frame with one well per row, and lays it out like it's on a plate.

Vignette
--------

For a detailed example of how to use `plater`, check out <https://github.com/seaaan/plateR/blob/master/inst/doc/plater-basics.md>

Code of conduct
---------------

`plater` is developed under a [Contributor Code of Conduct](CONDUCT.md). To contribute to its development, you must agree to abide by its terms.
