Contributing to {{{package}}}
================

## What is this?

A `CONTRIBUTING.md` document (such as this one) has instructions for
contributing to the project. These are guidelines the maintainers would
like contributors to adhere to, and exist to make the process flow more
smoothly.

As a contributor you should try to make accepting your code as easy as
you can, this greatly increases the chance your contribution will be
accepted.

## Prerequisites

  - For this guide to make sense, you’ll need to be acquainted with Git
    and GitHub. If you are not, see [Happy Git and GitHub for the
    useR](http://happygitwithr.com/) by Jenny Bryan.

  - Before you do a pull request, you should always file an issue and
    make sure someone from the tidyverse team agrees that it’s a
    problem, and is happy with your basic proposal for fixing it. If
    you’ve found a bug, first create a minimal
    [reprex](https://www.tidyverse.org/help/#reprex). We don’t want you
    to spend a bunch of time on something that we don’t think is a good
    idea.

There are lots of ways to contribute *other* than by writing code. See
[Contribute to the tidyverse](https://www.tidyverse.org/contribute/) for
more on this.

## Making a pull request

  - Uphold the design principles and package mechanics outlined below.  
  - When in doubt, discuss in an issue before doing lots of work.  
  - Make sure the package still passes `R CMD check` locally for you.
    It’s a good idea to do that before you touch anything, so you have
    a **baseline**.
  - Match the existing code style. For this purpose, you should follow
    the tidyverse style guide <http://style.tidyverse.org>.  
  - Documentation: Update the documentation source, if your PR changes
    any behavior. We use
    [**roxygen2**](https://cran.r-project.org/package=roxygen2), so you
    must edit the roxygen comments above the function; never edit
    `NAMESPACE` or `.Rd` files by hand.
  - Website: Do not update the pkgdown-created website, i.e. the files
    generated below `docs/`. We’ll take care of that.
      - To update the roxygen documentation *without* changing the
        pkgdown-created website, you can run `devtools::document()`.

### Style

For tidyverse projects read the [Style
Guide](http://style.tidyverse.org) and use the
[lintr](https://cran.r-project.org/package=lintr) package to find code
which does not adhere to the style guide.

``` r
# install.packages("lintr")
lintr::lint_package()
```

Remember to include style changes only in code you are contributing.

### Documentation

We use [roxygen2](https://cran.r-project.org/package=roxygen2),
specifically with the [Markdown
syntax](https://cran.r-project.org/web/packages/roxygen2/vignettes/markdown.html),
to create `NAMESPACE` and all `.Rd` files. All edits to documentation
should be done in roxygen comments above the associated function or
object.

### Testing

We use [testthat](https://cran.r-project.org/package=testthat).
Contributions with test cases are easier to accept because the tests
ensure the code does what it intends to do and nothing else. Without
tests the maintainer needs to check the new functionality by hand, a
burden you can lessen or remove by including tests. If you are not sure
what parts of your code is covered by tests
[covr](https://cran.r-project.org/package=covr) is a great tool to use
before submission. Just run the following to get a local coverage report
of the package so you can see exactly what lines are not covered in the
project.

``` r
# install.packages("covr")
co <- covr::package_coverage()
covr::report(co)
```

### View contributing as a relationship, not a transaction

The best way to be successful contributing to open source projects is to
do so repeatedly. This means cultivating trust between yourself and the
maintainer by multiple successful contributions. After a series of
smaller contributions the maintainer will be much more willing to review
and accept more substantial changes. As with any relationship being
polite and considerate throughout will go a long way to improve trust.
If you instead view the contribution as a solitary transaction to add
your pet feature you are much less likely to be successful.

### Code of Conduct

Please note that this project is released with a [Contributor Code of
Conduct](CONDUCT.md). By participating in this project you agree to
abide by its terms.

#### Acknowledgements/Confession

This document has been largely ~~plagiarized from~~ inspired by Jenny
Bryan’s [googledrive](http://googledrive.tidyverse.org/)
[`CONTRIBUTING.md`](https://github.com/tidyverse/googledrive/blob/master/CONTRIBUTING.md),
Hadley Wickham’s [Contributing to ggplot2
development](https://github.com/tidyverse/ggplot2/blob/92666ca8dd4cb5f96cbfcd4dcfcf157b599a6048/CONTRIBUTING.md),
and Jim Hester’s excellent [Contributing Code to the
Tidyverse](http://www.jimhester.com/2017/08/08/contributing/).
