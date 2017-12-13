Contributing to googledrive
================

## Making a pull request

  - Uphold the design principles and package mechanics outlined below.
  - When in doubt, discuss in an issue before doing lots of work.
  - Make sure the package still passes `R CMD check` locally for you.
    It’s a good idea to do that before you touch anything, so you have
    a baseline.
  - Match the existing code style. Our intent is to follow
    <http://style.tidyverse.org>.
  - Tests: please *try* to run our tests or at least those that exercise
    your PR. Add tests, if relevant. If things go sideways, just say so.
    We are painfully aware that it’s not easy to test API-wrapping,
    auth-requiring packages like googledrive and are open to
    constructive feedback. More below.
  - Documentation: Update the documentation source, if your PR changes
    any behavior. We use
    [roxygen2](https://cran.r-project.org/package=roxygen2), so you must
    edit the roxygen comments above the function; never edit `NAMESPACE`
    or `.Rd` files by hand. More below.
  - Website: Do not update the pkgdown-created website, i.e. the files
    generated below `docs/`. We’ll take care of that.
  - If the PR is related to an issue, link to it in the description,
    with [the `#15`
    syntax](https://help.github.com/articles/autolinked-references-and-urls/)
    and the issue slug for context. If the PR is meant to close an
    issue, make sure one of the commit messages includes [text like
    `closes #44` or `fixes
    #101`](https://help.github.com/articles/closing-issues-using-keywords/).
    Provide the issue number and slug in the description, even if the
    issue is mentioned in the title, because auto-linking does not work
    in the PR title.
      - GOOD PR title: “Obtain user’s intent via mind-reading; fixes
        \#86”.
      - BAD PR title: “Fixes \#1043”. Please remind us all what issue
        \#1043 is about\!
      - BAD PR title: “Something about \#345”. This will not actually
        close issue \#345 upon merging. [Use the magic
        words](https://help.github.com/articles/closing-issues-using-keywords/).
  - Add a bullet to `NEWS.md` with a concise description of the change,
    if it’s something a user would want to know when updating the
    package. [dplyr’s
    `NEWS.md`](https://github.com/tidyverse/dplyr/blob/master/NEWS.md)
    is a good source of examples. Note the sentence format, the
    inclusion of GitHub username, and links to relevant issue(s)/PR(s).
    We will handle any organization into sub-sections just prior to a
    release. What merits a bullet?
      - Fixing a typo in the docs does not, but it is still awesome and
        deeply appreciated.
      - Fixing a bug or adding a new feature is bullet-worthy.

## Package philosophy

  - When in doubt, take a cue from the Unix file system commands or the
    Google Drive browser UI.
  - Have a reasonable default whenever humanly possible. This applies to
    auth, file name, file location, etc.
  - Be pipe-friendly.
  - If it’s not well-documented (e.g. working example\!), it doesn’t
    really exist.
  - Accommodate initial file specification via path or name, but
    constantly push downstream work to be based on file id.
  - Return a tidy tibble, probably a
    [`dribble`](https://tidyverse.github.io/googledrive/reference/dribble.html),
    whenever it makes sense.

There is a high-level interface for the typical user. These functions
help you accomplish the most common tasks, hopefully in a natural way.
Examples: `drive_find()`, `drive_upload()`, `drive_download()`. A few
hand-picked functions support passing extra parameters through to the
API request via `...`, but we don’t do this across the board.

There is also a low-level interface that is used internally. These are
functions like `generate_request()` and `process_response()`. These
functions are exported for use by programming-oriented users who are
willing to read [Drive API
docs](https://developers.google.com/drive/v3/web/about-sdk) and want to
do things we haven’t made available in the high-level interface.

## Package mechanics

### Documentation

We use [roxygen2](https://cran.r-project.org/package=roxygen2),
specifically with the [Markdown
syntax](https://cran.r-project.org/web/packages/roxygen2/vignettes/markdown.html),
to create `NAMESPACE` and all `.Rd` files. All edits to documentation
should be done in roxygen comments above the associated function or
object.

Use templates or inheritance to repeat documentation whenever it is
helpful, but without actually repeating its source.

Use internal and external links liberally, i.e. to other docs in
googledrive or to Drive API resources.

We encourage working examples that include any necessary setup and
teardown. In most cases, you’ll have to put them inside a `\dontrun{}`.

It’s nice if a pull request includes the result of running
`devtools::document()`, to update `NAMESPACE` and the `.Rd` files, but
that’s optional. A good reason to NOT `document()` is if you have a
different version of roxygen2 installed and that sprays minor formatting
changes across `.Rd` files that have nothing to do with the PR.

### Testing

We use [testthat](https://cran.r-project.org/package=testthat).

We have many tests that require authorization and that rely on the
existence of specific files and folders. Therefore, to fully test
googledrive, you’ll have to store a token in a specific place and you’ll
have to do some setup. We’ve tried to make it fairly easy to do this
setup and to clean up those files when you’re done.

TL;DR with more detail below:

``` r
## store an OAuth token
## TO DO: update this when dust settles re: gargle and service token!
token <- drive_auth(reset = TRUE, cache = FALSE)
saveRDS(token, rprojroot::find_testthat_root_file("testing-token.rds"))

## gather all the test setup and clean code from individual test files
source(rprojroot::find_testthat_root_file("driver.R"))
## leaves behind:
##   * all-test-setup.R
##   * all-test-clean.R

## "activate" by editing, e.g., SETUP <- TRUE vs SETUP <- FALSE

## source or, I prefer, render the setup script
rmarkdown::render(rprojroot::find_testthat_root_file("all-test-setup.R"))

## do all development work that requires tests HERE

## source or, I prefer, render the clean script
rmarkdown::render(rprojroot::find_testthat_root_file("all-test-clean.R"))
```

#### OAuth token for testing

1.  Obtain a new, non-caching token via browser flow.
    
    ``` r
    token <- drive_auth(reset = TRUE, cache = FALSE)
    ```

2.  Double-check that the user associated with the token is what you
    want.
    
    ``` r
    drive_user()
    ```

3.  Write this token to file, to the location expected by googledrive’s
    tests.
    
    ``` r
    saveRDS(token, rprojroot::find_testthat_root_file("testing-token.rds"))
    ```

#### R scripts for setup and clean

For speed reasons, the googledrive tests expect to find certain
pre-existing files and folders, i.e. we don’t do full setup and tear
down on each run. You do setup at the beginning of your googledrive
development and leave these files in place while you work. When you’re
done, e.g., when your PR is complete, you can clean up these files. Each
test run also creates and destroys files, both locally and on Drive, but
that is different and not what we’re talking about here.

1.  Source `tests/testthat/driver.R` to extract and aggregate the
    current setup and clean code across all test files.
      - This will create two R scripts:
        `tests/testthat/all-test-setup.R` and
        `tests/testthat/all-test-clean.R`. Inspect them.
2.  When you are truly ready to perform setup or clean, edit the code to
    set the `SETUP` or `CLEAN` variable to `TRUE` instead of `FALSE`.
    This friction is intentional, so you don’t accidentally create or
    delete lots of Drive files without meaning to.
3.  Render `all-test-setup.R` with the Knit button in RStudio or like
    so:

<!-- end list -->

``` r
rmarkdown::render("all-test-setup.R")
```

You could also just source it, but it’s nice to have a report that
records what actually happened.

You should now be able to run the tests via *Build \> Test Package* or
*Build \> Check Package* in RStudio or via `devtools::test()`.

You can leave the setup in place for as long as you’re working on
googledrive, i.e. you don’t need to do this for every test run. In fact,
that is the whole point\!

When your googledrive development is over, render the clean script:

``` r
rmarkdown::render("all-test-clean.R")
```

Again, read the report to look over what happened, in case anything was
trashed that should not have been (btw, let us know about that so we can
fix\!). Once you’re satisfied that your own files were not touched, you
can `drive_empty_trash()` to truly delete the test files.

#### Adding tests

If you’re going to add or modify tests, follow these conventions:

  - Test files are marked up with knitr chunk headers in comments, e.g.
    `# ---- clean ----` or `# ---- tests ----`. This is what enables the
    `driver.R` script to isolate the setup or cleaning code. Don’t break
    that.
  - Any file that is truly necessary and can be setup in advance and
    persist? Do it, in order to make future test runs faster. Put the
    associated setup and clean code at the top of the test file.
  - All test files should have a name that documents why they exist and
    who made them. Use the `# ---- nm_fun ----` chunk to define naming
    functions used in that test file (see existing files for examples).
    Always use one of these functions to generate file names. Use
    `nm_()` for test files that persist. Use `me_()` for ephemeral test
    files that are created and destroyed in one test run.

Example and structure of a self-documenting name for a persistent test
file:

    move-files-into-me-TEST-drive-mv
    <informative-slug>-TEST-<test-context>

Example and structure of a self-documenting name for an ephemeral test
file:

    DESCRIPTION-TEST-drive-upload-travis
    <informative-slug>-TEST-<test-context>-<user>

Note that the current user is appended\! This is so that concurrent test
runs do not attempt to edit the same files.

### Continuous integration

googledrive is checked on the current R release on Windows, via
[AppVeyor](https://ci.appveyor.com/project/tidyverse/googledrive), and
on several versions of R on Linux, via [Travis
CI](https://travis-ci.org/tidyverse/googledrive). We use
[codecov](https://codecov.io/github/tidyverse/googledrive?branch=master)
to track the test coverage. In general, the package is subjected to `R
CMD check`, unit tests, and test coverage analysis after every push to
GitHub. There are encrypted OAuth tokens on both AppVeyor and Travis CI,
so tests against the Drive API can be run.

Things are a bit different for pull requests from outside contributors,
however. These PRs do not have access to the encrypted tokens, therefore
many tests must be skipped. The PR will still be vetted via `R CMD
check` and tests that do not call the Drive API can still be run. After
you make a PR, it’s a good idea to check back after a few minutes to see
all of these results. If there are problems, read the log and try to
correct the problem proactively. We “squash and merge” most pull
requests, internal or external, so don’t agonize over the commit
history.

## Code of Conduct

Please note that this project is released with a [Contributor Code of
Conduct](CONDUCT.md). By participating in this project you agree to
abide by its terms.
