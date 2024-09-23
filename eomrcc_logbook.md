# Logbook: Exeter Oncology Model

Author: **Amy Heather**

This logbook relates to the **Exeter Oncology Model: Advanced Renal Cell Carcinoma Edition (EOM-RCC)**. It will describe:

* Modifications made to the model repository as part of STARS work package 3
* Any observed barriers and enablers to applying the framework
* Any observed shortcomings of the framework

Relevant links:

* Model code: <https://github.com/nice-digital/NICE-model-repo>
* Paper describing model and experiences from the pathways pilot: <https://doi.org/10.1007/s41669-024-00490-x>
* NICE pathways pilot appraisal ID6186: <https://www.nice.org.uk/guidance/indevelopment/gid-ta11186/documents>
* NICE appraisal of cabozantinib plus nivolumba ID6184: <https://www.nice.org.uk/guidance/ta964>

## Background

### NICE pathway pilots

The model was produced by the Peninsula Technology Assessment Group (PenTAG) at the University of Exeter in collaboration with the National Institute for Health and Care Excellence (NICE).

In 2022, NICE announced a programme aimed at taking a proportionate approach to technology appraisals -

> "We simplified, removed, or reconfigured parts of the appraisals process. This means we applied light-touch, faster approaches to simpler, low-risk treatments allowing us to produce rapid guidance for some topics." - [NICE](https://www.nice.org.uk/about/what-we-do/proportionate-approach-to-technology-appraisals)

Phase two of this programme included a **pathways pilot** which involves "the production of reusable reference models for each disease area to reduce repetition and improve consistency in decision making". The rationale for this that almost half of health technology assessments are within 10 disease areas. ([Lee et al. 2024](https://doi.org/10.1007/s41669-024-00490-x))

Pilot models were/are being created in:

* Renal cell carcinoma (the model discussed within this document)
* Non-small-cell lung cancer (ongoing, expected in Q2 2024, [link to NICE project page](https://www.nice.org.uk/guidance/indevelopment/gid-ta11289))

### Description of EOM-RCC from the paper

The description of the model from the paper is as follows:

> "The pilot involved (1) the production of a sequencing model capable of handling a multi-comparator decision space with 12 possible treatments, three different risk subgroups, the possibility of prior adjuvant therapy or not, and four possible lines of active therapy; and (2) using that model to appraise a first-line treatment for RCC: cabozantinib plus nivolumab."
>
> "When run, the model would:
> * extract cost, resource use, utility, relative effectiveness, and treatment sequence setting inputs from Excel front-end;
> * compute possible treatment sequences for each population;
> * load patient-level data and conduct survival analyses;
> * load network meta-analyses;
> * populate and propagate relative efficacy network for all treatments at all lines;
> * compute patient flow for all possible sequences at all possible lines and apply cost and utility weights;
> * for the live appraisal, compute weighted average patient flow by first-line treatment and calculate the impact of patient access schemes (confidential discounts on the published list price) in increments of 1 from 1 to 100% for all treatments;
> * output results as files to store and as a fully automated Word document following formatting requirements for NICE."
>
> "Previous appraisals in RCC highlighted issues with sub- sequent treatments in trials not being available in UK practice and difficulties in matching cost and effectiveness data when trying to compensate. Consequently, we built a state transition model with tunnel states to incorporate time-dependency at later lines. This approach simplified incorporation of the sequencing features that arose within the scope of the pilot. For prudence, we incorporated a partitioned-survival (PartSA) modelling approach in parallel. This allowed comparison with models following implementation precedent in advanced RCC HTA." ([Lee et al. 2024](https://doi.org/10.1007/s41669-024-00490-x))

## Logbook

### July 2024: Running the model

Before making any changes, the first step was to make sure I was able to run the model.

#### Set up environment

Installed 4.3.1

```
R_VERSION=4.3.1
sudo curl -O https://cdn.posit.co/r/ubuntu-2204/pkgs/r-${R_VERSION}_1_amd64.deb
sudo apt-get install gdebi-core
sudo gdebi r-${R_VERSION}_1_amd64.deb
```

Opened R project in that version

```
export RSTUDIO_WHICH_R=/opt/R/4.3.1/bin/R
rstudio
```

Installed `renv`: `install.packages('renv')`

Initialised renv: `renv::init()`. This began `resolving missing dependencies`. Then restarted R session and prompted that project is out of sync.

Instead of going through each script uncommenting the installations, I ran `renv::install()` to install more dependencies that it has identified as being used but not installed. Finished with error: `Error: package 'Matrix' is not available`. Ran `install.packages("Matrix", quiet = TRUE)` and this returned the message:

```
Warning message:
package ‘Matrix’ is not available for this version of R
‘Matrix’ version 1.7-0 is in the repositories but depends on R (>= 4.5)
‘Matrix’ version 1.7-0 is in the repositories but depends on R (>= 4.4.0)

A version of this package for your version of R might be available elsewhere,
see the ideas at
https://cran.r-project.org/doc/manuals/r-patched/R-admin.html#Installing-packages 
```

Hence, this is an issue with trying to install the latest version of Matrix on their version of R. If we backdate Matrix to, for example, Matrix 1.6-5 (from 11th Jan 2024), it is fine.

`renv::install("Matrix@1.6-5")`

Then error to install SAVI - but can see from code that this was installed from GitHub. Switched from `devtools` to `renv` to install -

`renv::install("Sheffield-Accelerated-VoI/SAVI-package")`

Once done, saved to lock file with `renv::snapshot()`.

#### Updating environment

Then ran the model. Apx. start 14.58.

The slow part is:

```
pf <- f_pf_computePF(
  p           = p,
  struct      = p$basic$structure,
  verbose     = FALSE,
  plots       = FALSE,
  just_pop    = p$basic$pops_to_run,
  just_nlines = NULL,
  just_seq    = NULL
)
```

It has a nice output that shows progress. However, after about a minute, RStudio crashed and the computer essentially restarted itself.

Tried running from command line:

`/opt/R/4.3.1/bin/R -e 'renv::run("2_Scripts/Model_Structure.R")'`

However, this also crashed the computer at 3m 43s.

I created a new directory, and tried instead working with R 4.4.1...

* Opened `.Rproj`
* Ran `renv::init()`
* Ran `renv::install("Sheffield-Accelerated-VoI/SAVI-package")`
* Ran `renv::snapshot()`

Then ran `Model_Structure.R`. Without touching anything else on the machine, it ran for about 50 minutes and reached this point, at which point the machine crashed:

* 99% memory usage (using apx. 30GB RAM)
* `f_pf_computerPF()` - 3 active treatment lines - 16% 1|6|10|999

#### Four cores

Ran on my machine on four cores, like Darren suggested. Did keep using machine for other things. Ran much faster than before - in 12 minutes, had reached the same point as above.

In 1 hour 43 minutes, made it to Population 3 pathways with 4 active treatments 68%.

#### Remote machine

Tried using Tom's pop-os (22.04) remote machine 13th Gen Intel(R) Core(TM) i9-13900K 81GB RAM.

* `R --version` returned 4.4.1 (same as me)
* `Rscript -e 'renv::restore()'`

Received errors, and installed the required dependencies prompted from each error when re-running `renv::restore()`...

* `sudo apt-get install -y libfontconfig1-dev`
* `sudo apt-get install -y libcairo2-dev`
* `sudo apt-get install -y libharfbuzz-dev`
* `sudo apt-get install -y libfribidi-dev`
* `sudo apt-get install -y libfreetype6-dev libpng-dev libtiff5-dev libjpeg-dev`

Then:

* `Rscript -e 'renv::run("2_Scripts/Model_Structure.R")'`

At 53 minutes, it was still on Population 1 with 3 pathways. Given that the default script was much slower on my machine than lowering the number of cores, I decided to cancel it and amend the number of cores on the remote machine too.

I ran:

* `R`
* `library(future.apply)`
* `availableCores()` - which returned 32
* `quit`

Despite there being 32 cores, I decided to try it with 12, as Darren states that on 24GB or 32GB RAM, 8-14 is best, so I'll start with that first (despite this being much higher).

I used nano to amend the `.R` file, then reran. Recorded time to get to **complete 3 active treatment lines for population 1**, then repeated with other cores, to understand what is faster / slower:

* 28 cores: > 53m (still on 3)
* 12 cores: 35m
* 8 cores: < 19m
* 4 cores: 7m
* Sequential: 3m

To run full script on that machine sequentially, it took 19m.

### Friday 16 August 2024

#### CHANGELOG and release

Created `CHANGELOG.md` and made first release on GitHub. Used `v1.0.0` as this was a finalised/stable version of the model, and not initial development (and so no starting from `v0.1.0`).

<details>
<summary><b>STARS framework reflections</b></summary>

* Doesn't include CHANGELOG or releases.

</details>

#### Essential STARS components

Some of the essential STARS components are already satisfied:

* License
* FOSS model
* Dependency management (with addition of renv in July)

The archiving of the model would be a decision for the HTA team.

The remaining components can be implemented though:

* ORCID IDs
	* IDs identified from online search 
* Citation information
	* Created CITATION.cff with cffinit. Only included emails if they were already publicly available online.
	* Add citation information to README
* Minimum documentation
	* Add license type to README
	* Add funding statements to README
	* Minimal instructions that overview:
		* (a) what model does
		* (b) how to install and run model to obtain results
		* (c) how to vary parameters to run new experiments

<details>
<summary><b>STARS framework reflections</b></summary>

* Doesn't mention including the source of funding and the license in the README
* "how to vary parameters to run new experiments" feels quite daunting initially, although realise it can be quite a simple statement referring to the scripts and/or functions that you would need to change (rather than delving too much into the specifics).
* Reflection: Was quite quick to implement essential components

</details>

#### README

I improved the README clarity, structure and appearance. This included adding banner image with logos, extra badges, table of contents, all the information from `ID6184 Using the R decision model..` (e.g. installation guide with images, overview of input files, future versions)

<details>
<summary><b>STARS framework reflections</b></summary>

* Whether anything from how changed README can be of relevance

</details>

### Tuesday 20 August 2024

#### Tidying

Removed empty READMEs from folders, although the README in `1_Data` remains as it seems to have some example text but also potentially some actual relevant text (`gpop adjustment`). May later incorporate elsewhere, as currently a little confusing.

#### Getting started with package

The required dependencies are already in the environment. As from [this guide](https://sahirbhatnagar.com/rpkg/), these are:

* `usethis` - create package
* `devtools` - build, install and check package
* `roxygen2` - document functions
* `rmarkdown` - create vignettes

Ran the command `usethis::use_description()` to create DESCRIPTION file. Had to modify the folder name from `stars-eom-rcc` to `StarsEomRcc` (as the former was not a suitable package name). Modified the `DESCRIPTION` file. I guessed at likely reasonable answers for `author` and `role` but this would need review.

Add each package to imports of `DESCRIPTION` with `usethis::use_package("", min_version=TRUE)`. I used minimum version to indicate what versions of the package I used, following recommendations from the [r-pkg documentation](https://r-pkgs.org/description.html). I worked through each listed to install in `Model_Structure.R`. For tidyverse, an error message appeared:

> Error: 'tidyverse' is a meta-package and it is rarely a good idea to depend on it. Please determine the specific underlying package(s) that offer the function(s) you need and depend on that instead. For data analysis projects that use a package structure but do not implement a formal R package, adding 'tidyverse' to Depends is a reasonable compromise. Call `use_package("tidyverse", type = "depends")` to achieve this.

On the assumption that we are doing the latter, I have add it to depends.

Then, continuing to follow [this tutorial](https://sahirbhatnagar.com/rpkg/), ran:

* `usethis::use_namespace()`: created empty namespace file
* `base::dir.create("R")`: create empty `R/`
* `usethis::use_package_doc()`: created `R/StarsEomRcc-package.R` so can run `?StarsEomRcc`
* `usethis::use_readme_rmd()` (but then deleted)

Restarted RStudio. However, `renv::status()` is showing all packages as uninstalled? I ran `renv::restore()` and this resolved the issue.

In `.Rproj`, set `Restore .RData into workspace at startup` to `No`. However, it didn't have the ROxygen option from the tutorial.

Then tried to use devtools, but it said it wasn't installed despite it being in `renv/`? Tried `renv::install("devtools")` which worked quick. The only difference now for `renv::status()` was that I'd upgraded shiny.

Trying an example function, I ran `use_r("excel_extract")` and then copied `f_excel_extract` and `f_excel_cleanParams` from `3_Functions/excel/extract.R`. Then ran `devtools::load_all()`, and `f_excel_extract()` (to see it was available, as auto complete popped up), and then `exists("f_excel_extract", where = globalenv(), inherits = FALSE)` (to confirm I hadn't sourced it).

Then ran `check()`. This highlighted error in `DESCRIPTION` which I resolved. It requires a creator, which I had put as Darren, but it also requires an email for the creator. As Darren's email is not publicaly available online, I switched this over to Dawn, but would require review. When running `check()`, it add "dependency on R >= 3.5.0 because serialized objects in serialize/load version 3 cannot be read in older versions of R." I then switched this to my version (4.4). Lots of errors and warnings were returned from the check.

#### Addressing warnings from check()

```
❯ checking package subdirectories ... NOTE
  Found the following CITATION file in a non-standard place:
    CITATION.cff
  Most likely ‘inst/CITATION’ should be used instead.
```

Tried moving to `inst/`, as per [docs](https://r-pkgs.org/R-CMD-check.html#package-structure).

```
❯ checking DESCRIPTION meta-information ... NOTE
  Licence components which are templates and need '+ file LICENCE':
    MIT
```

Tried adding `+ file LICENSE` to `DESCRIPTION`.

```
❯ checking for hidden files and directories ... NOTE
  Found the following hidden files and directories:
    .github
  These were most likely included in error. See section ‘Package
  structure’ in the ‘Writing R Extensions’ manual.
```

Tried running `use_build_ignore(".github/")` to add regular expression to `.Rbuildignore` to ignore `.github/`.

```
❯ checking R code for possible problems ... NOTE
  f_excel_cleanParams: no visible global function definition for
    ‘as.data.table’
  f_excel_cleanParams : <anonymous>: no visible binding for global
    variable ‘Parameter.name’
  f_excel_extract: no visible global function definition for
    ‘loadWorkbook’
  f_excel_extract: no visible global function definition for
    ‘getNamedRegions’
  f_excel_extract : <anonymous>: no visible global function definition
    for ‘read.xlsx’
  Undefined global functions or variables:
    Parameter.name as.data.table getNamedRegions loadWorkbook read.xlsx
```

Add the functions and their sources using `@importFrom`

```
❯ checking top-level files ... NOTE
  File
    LICENSE
  is not mentioned in the DESCRIPTION file.
  Non-standard files/directories found at top level:
    ‘1_Data’ ‘2_Scripts’ ‘3_Functions’ ‘4_Output’ ‘CHANGELOG.md’
    ‘CITATION.cff’ ‘ID6184 Using the R decision model (EAG instructions)
    noACIC 30.07.2024.docx’ ‘img’ ‘test.Rmd’
```

**Changelog**: In R, the convention with packages seems to be to keep a `NEWS.md` file rather than a `CHANGELOG`. I created one using `usethis::use_news_md()`, and transferred the information from `CHANGELOG.md`.

**Img**: Images should be saved in `man/figures/README` for an R package ([as here](https://r-pkgs.org/other-markdown.html#sec-readme))

I then renamed the folder from `StarsEomRCC` to `StarsEOMRCC` and same renv issue as before, so again, `renv::restore()` and `renv::install("devtools")`.

<details>
<summary><b>STARS framework reflections</b></summary>

* Convention differences in R like `NEWS.md` and `CITATION` location (specific to package, or in general?)
* That it's not a super easy task to switch into R package structure, particularly if unfamiliar with how R packages should be structured and work

</details>

I've not addressed all the items from `check()` but decided to run again.

#### Re-running check

There was an error with my addition of R version to Depends in DESCRIPTION so removed that.

```
❯ checking package subdirectories ... NOTE
  Found the following CITATION file in a non-standard place:
    inst/CITATION.cff
  Most likely ‘inst/CITATION’ should be used instead.
```

Since this remains, it seems R convention is to use `CITATION` instead of `CITATION.cff`. I ran `usethis::use_citation()` which created a template `CITATION` file. I did not find any conversion options. Instead of deleting my file, I filled out the other file with the similar information.

Moreover, moving `CITATION.cff` to `inst/` has meant it is now no longer recognised by GitHub.

I looked at [one of Rob's packages](https://github.com/bristol-vaccine-centre/tableone) as an example, and could see he had `CITATION.cff` in the main directory, so just returned it.

Looking at the template `CITATION`, it just appears to be a `.bib` file. I tried adding a modified version of my GitHub action to convert `CITATION.cff` to `.bib`, but naming it `inst/CITATION`, but this didn't work and decided to remove.

#### Should it be a package?

As I was working on this, I started to wonder if converting it into an R package was actually beneficial or not. I reflected on what our ambitions were, which were to:

* Improve open science credentials of repository, such as by the addition of hosted documentation
* Improve the clarity of the repository, such as by the addition of documentation and docstrings, and tidying up little bits and bobs

Converting into a package adds alot of procedure and requirements that, on reflection, might not actually necessarily be that beneficial? I decided for now that, instead of converting it into a package now, I would focus on understanding the code and developing documentation and docstrings, and that we could always then return to the package idea later if, at that point, we still feel it would be beneficial. I moved the "package" activities from main into a dev branch, and reset main back to just after the release v1.1.1, prior to making "package" changes.

<details>
<summary><b>STARS framework reflections</b></summary>

Varying thoughts and recommendations on best ways to structure code, not one size fits all.

</details>

#### Quarto site

Created a basic quarto site with acronyms and publications.

<details>
<summary><b>STARS framework reflections</b></summary>

What end up adding / not adding, and how that compares to the recommendations for detailed documentation from the STARS framework

</details>

### Wednesday 21 August 2024

Working on the quarto site to add:

* Description of NICE appraisals
* Listing out all the web pages, reports and publications that pertain to the model

I then started working on the model overview and code walk through, but the main first task was just to work through the model and make sure I understand what it is doing at each step.

### Thursday 22 August 2024

Continuing to work through the model code.

Did note that I was unable to run the survival analysis. If I set `i$dd_run_surv_reg="Yes"`, it would run until hitting an error:

```
Error in optim(method = "BFGS", par = c(mu = 5.82584217367559, sigma = -0.208272130470619,  : 
  non-finite finite-difference value [1]
```

This is an error generated by `stats::optim()`, which is an optimisation function. Here, `BFGS` means its using a variable matrix algorithm (Broyden et al. 1970). The issue is that the function being optimised has returned `NaN` or `Inf` for some parameter values.

### Friday 23 August 2024

Continuing to work through the model code.

Noticed a duplicate line: `p$releff$fp_dest <- i$FPNMA$destinations[!is.na(Molecule), ]`

`lookup` not used but input for: `f_NMA_generateNetwork`

### Wednesday 11 September 2024

Resuming working through the model code.

<details>
<summary><b>STARS framework reflections</b></summary>

The biggest impact here is that this is a really big and complicated piece of work, so most my time is spent in understanding what the model is doing.

And so, whilst my changes will help aid reuse, reality is that a massive factor in reuse relates to model complexity - although, I can't necessarily speak to this, as from a health economics perspective, perhaps that is a facilitator.

The complexity is commented on in their article about developing the model: "...the sheer scale of the decision problem in terms of the systematic review, network meta-analyses, and clinical consultation work required. This in turn led to a complex, computationally expensive model"
</details>

### Thursday 12 September

Continuing to work on understanding what they have done (before and during code).

<details>
<summary><b>STARS framework reflections</b></summary>

(1) Implementing R package was something I'd hoped to do as its a nice way to tidy up code and integrate functions with docs and a standard way to share something, but I haven't when I realised that it's just too complex and is already set up in a way that is fairly clear (in the sense that it seperates out data and scripts and outputs and so on). This is a specific reflection to it being retrospective, that with it all done, it's not worth changing things, and its very complex.

(2) Acronyms very important to be in documentation for this one - and that includes acronyms used in the code and not just in the docs (although not just like random variable names, but more like comments in the code and so on)
</details>

### Friday 13 September

Finished the `code_walkthrough.qmd`.

Started working on the docs, beginning with the basic and complex overview.

### Monday 16 September

Finished up complex and basic overviews.

<details>
<summary><b>STARS framework reflections</b></summary>

Importance of the diagrams in making it clearer
</details>

### Tuesday 17 September

Working on the doc pages that walk through input data, model code and results.


<details>
<summary><b>STARS framework reflections</b></summary>

Overlap between detailed model documentation and the README. Does everything need to be in both?
</details>

### Wednesday 18 September

Continuing to work on the doc pages walking through model code. Issue of splitting split and wanting to reuse global environment from prior script.

Trying saving environment using `save.image(file.path(q_path, "code1.RData"))` but:

* Doesn't include libraries (so have to repeat `library()` calls each time)
* Concerned this will get too big for the later pages - it's already nearly 1GB just from that first page.

Might be simpler to run the prior page at the start of each script, but that would have longer run time.

Another alternative could be to save the image to a temporary file.

Or saving .qmd to html and then splitting that and including it.

Problem seems similar to people wanting "merge and knit" functionality that is available with Bookdown.

Played around with rendering .qmd to .md and then extracting from .md. Made a .sed file run with `sed -nf extract_md.sed code_walkthrough.html.md`

```
# Extract code1 content between SPLITMD_CODE1_START and SPLITMD_CODE1_END
/SPLITMD_CODE1_START/,/SPLITMD_CODE1_END/ {
    /SPLITMD_CODE1_START/d
    /SPLITMD_CODE1_END/d
    w code1.md
}

# Extract code2 content between SPLITMD_CODE2_START and SPLITMD_CODE2_END
/SPLITMD_CODE2_START/,/SPLITMD_CODE2_END/ {
    /SPLITMD_CODE2_START/d
    /SPLITMD_CODE2_END/d
    w code2.md
}
```

And trying

```
pre-render:
  - knitr::knit("pages/walkthrough/code_walkthrough.Rmd")
  - sed -nf pages/walkthrough/extract_md.sed pages/walkthrough/code_walkthrough.md
```

### Thursday 19 September

Continuing to work on the walkthrough in the docs.

### Friday 20 September

Finish code walkthrough for docs.

### Monday 23 September

Wrote walkthrough of the output report, as well as pages on probabilistic analysis and scenario analysis.

#### Annotated reporting guidelines

STARS suggests that detailed documentation includes annotated reporting guidelines, with an example like STRESS-DES for discrete event papers. As this is a different context (health economics paper where economic model is partitioned survival or markov, and includes other modelling e.g. network meta-analysis), it would require different reporting guidelines.

  For economic evaluations, the equator network has the Consolidated Health Economic Evaluation Reporting Standards (CHEERS) 2022, <https://www.equator-network.org/reporting-guidelines/cheers/>.

  In the [assessment report](https://www.nice.org.uk/guidance/gid-ta11186/documents/assessment-report-3), they note that in their search for published cost-effectiveness studies, the studies will be critically appraised using:

  * CHEERS for economic evaluations
  * The Phillips 2004 checklist for decision analytical models
  * The AMSTAR checklist for systematic reviews

  Hence, CHEERS appears the relevant checklist (although also maybe Phillips).

However, given the context, wherein the study has had to follow standardised reporting requirements from NICE, I wonder if this would serve as "duplication", and that instead, it would be more appropriate in this context to refer to the NICE documentation?

#### Model validation

My understanding of the validation undertaken is limited to this description from the [assessment report](https://www.nice.org.uk/guidance/ta964/documents/assessment-report):

"Within the model results and validation addendum which will follow this report, model outputs will be compared to the data used as model inputs (for example visual comparison to Kaplan Meier data) to ensure the appropriateness of model structure and data derivation. The model
will then be compared to the projections from other models previously used for NICE STAs in the same decision point. Clinical expert input will be used to ensure that the model retains clinical face validity."

## TODO list

* Add which files to run to do which analyses (as doesn't currently mention filenames) and it took a little while to understand which scripts you can run or not

`2_Scripts/`

* `standalone scripts` - are they used? what are they?
* Add comments and doc strings to files without (note: `Model_Structure.R` and `probablistic_model.R` has great amount of comments and detail)
* Files:
	* `Model_Structure.R` - model
	* `probablistic_model.R` - run model probabilistically, uses HPC
	* `process PSA output.R` - think this could be the equivalent of `output_script.R` but for the probablistic model? PSA not defined, but assuming it is "probablistic safety assessment"
	* `output_script.R`- some hard-coded file paths (e.g. "C:/Users/dl556"), don't *think* this runs the model (?) but just processes results - although the final function that produces the word document matches up to that from Model_Structure, so assuming this is duplication of that but allowing to produce report from having data already (?) - would be simpler to define that as part of model script, and have a way of preventing the model running (?) although it might be different?
* Lists commented out `install.packages()` - could switch to DESCRIPTION (with those packages listed) and renv? Also, specific versions that are compatable with R 4.3.1.

`3_Functions/`

* Add comments and docstrings (some have very detailed docstrings, some partial, and some without)
* Appears that output save location might be Downloads? `subsequent_tx_weighted_averages.R`
* There is a file in `3_Functions/excel` called `excel extraction example.xlx` that looks like a file from development that might no longer be needed. <mark>check if can remove</mark>

`Tests/`

* Add comments

Recommendations from STARS for detailed docs: Open and high quality documentation on how the model is implemented and works (e.g. via notebooks and markdown files, brought together using software like Quarto and Jupyter Book). Suggested content includes:

* Plain english summary of project and model
* Clarifying license
* Citation instructions
* Contribution instructions
* Model installation instructions
* Structured code walk through of model
* Documentation of modelling cycle using TRACE
* Annotated simulation reporting guidelines (BUT Stress-DES is not relevant)
* Clear description of model validation including its intended purpose

