# Reflections: Exeter Oncology Model

The aim of WP3 is to:

* Provide real-world applications of the framework
* While applying the framework, track:
    * Barriers and enablers to applying it
    * Shortcomings of the framework
    * Contrasts between applying the framework retrospectively and prospectively
    * The approximate time, work and rework required to apply the framework
* Potentially amend the framework based on these reflections
* Conduct reproducibility tests on the new repositories, adopting the same testing methodology as in WP1, by a member of the team not involved in the case study, sharing the results as a separate report with a short RCR report similar to those used in ACM journals. The results of these tests will be mapped to the ACM and NICO badge systems. 

This markdown file aims to address the aims of:

* Tracking barriers and enablers to applying the framework
* Tracking shortcomings of the framework
* Tracking approximate time, work and rework required to apply the framework

This is based on the logbook kept whilst applying the framework, `eomrcc_logbook.md`.

## Rough notes

### What I did or did not implement from the framework (plus any extras!)

| Component | Implemented? Any details? Why/Why not? |
| - | - |
| **Essential components** |
| Open license: Free and open-source software (FOSS) license (e.g. MIT, GNU Public License (GPL)) | ✅ Already in place. | 
| Dependency management: Specify software libraries, version numbers and sources (e.g. dependency management tools like virtualenv, conda, poetry) | ✅ Add `renv` when I first got set-up and attempted to run the model. |
| FOSS model: Coded in FOSS language (e.g. R, Julia, Python) | ✅ Already the case. |
| Minimum documentation: Minimal instructions (e.g. in README) that overview (a) what model does, (b) how to install and run model to obtain results, and (c) how to vary parameters to run new experiments | ✅ |
| ORCID: ORCID for each study author | ✅ |
| Citation information: Instructions on how to cite the research artefact (e.g. CITATION.cff file) | ✅ |
| Remote code repository: Code available in a remote code repository (e.g. GitHub, GitLab, BitBucket) | ✅ Already the case. |
| Open science archive: Code stored in an open science archive with FORCE11 compliant citation and guaranteed persistance of digital artefacts (e.g. Figshare, Zenodo, the Open Science Framework (OSF), and the Computational Modeling in the Social and Ecological Sciences Network (CoMSES Net)) | ❌ For the PenTAG team to decided, they were open to it but required approval |
| **Optional components** |
| Enhanced documentation: Open and high quality documentation on how the model is implemented and works  (e.g. via notebooks and markdown files, brought together using software like Quarto and Jupyter Book). Suggested content includes:<br>• Plain english summary of project and model<br>• Clarifying license<br>• Citation instructions<br>• Contribution instructions<br>• Model installation instructions<br>• Structured code walk through of model<br>• Documentation of modelling cycle using TRACE<br>• Annotated simulation reporting guidelines<br>• Clear description of model validation including its intended purpose | ✅ More details below... |
| Documentation hosting: Host documentation (e.g. with GitHub pages, GitLab pages, BitBucket Cloud, Quarto Pub) | ✅ |
| Online coding environment: Provide an online environment where users can run and change code (e.g. BinderHub, Google Colaboratory, Deepnote) | ❌ Due to how computationally expensive the analysis is |
| Model interface: Provide web application interface to the model so it is accessible to less technical simulation users | ✅ |
| Web app hosting: Host web app online (e.g. Streamlit Community Cloud, ShinyApps hosting) | ✅ |

Delving into each aspect of the detailed documentation...

| Component | Implemented? Any details? Why/Why not? |
| - | - |
| Plain english summary of project and model | ✅ |
| Clarifying license | ✅ |
| Citation instructions | ✅ |
| Contribution instructions | ✅ Just email |
| Model installation instructions | ✅ |
| Structured code walk through of model | ✅ I only did this for one of the R scripts - not for all the functions thought, and not for other R scripts like probabilistic or scenario analysis.<br>Examples of how to do this could be helpful? Or maybe not, as no one right way, and perhaps just a matter of mentioning e.g. notebooks |
| Documentation of modelling cycle using TRACE | ❌ Not possible **retrospectively** |
| Annotated simulation reporting guidelines | ❌ No because felt like duplication with information in NICE reports which are very detailed and follow standardised reporting requirements |
| Clear description of model validation including its intended purpose | ❌ Not done as my knowledge is limited purely to this description from the [assessment report](https://www.nice.org.uk/guidance/ta964/documents/assessment-report):<br>"Within the model results and validation addendum which will follow this report, model outputs will be compared to the data used as model inputs (for example visual comparison to Kaplan Meier data) to ensure the appropriateness of model structure and data derivation. The model will then be compared to the projections from other models previously used for NICE STAs in the same decision point. Clinical expert input will be used to ensure that the model retains clinical face validity."

Extra things I did that weren't in the framework...

| Extra item | Any details? Why? |
| - | - |
| Acronyms | Important as there are acronyms used elsewhere with no definitions given and found that quite confusing when I first was looking through things<br><br>Including acronyms used in the code itself... not random variable names, but like meaningful ones in file names or variable names, or acronyms used in comments |
| Context (e.g. NICE appraisals, papers, reports) (i.e. related documents) | This felt important! To mention. It's that clear link from there to the code (like for paper in STRESS), but in this case, also vice versa, making sure to link from code to paper/s |
| Detailed summary | This felt like one of the most important pages to me! A more technical explanation of the model. I'm not sure if you would cover it by including annotated reporting guidelines? I would argue not, as that could be as simple as yes/no, and very little info. And this is about having a clear, easy to read narrative of what the model does and how it works, in detail, which is fundamental to actually understanding a model, and therefore to reuse. |
| CHANGELOG | This was add to repository itself (although I did also display in the site but don't think that is essential/too important). Main thing is just having in repo, as it is just a nice thing to do! |

**Overlap** between hosted docs and README not a bad thing... but does everything need to be in both?

I found **diagrams** SUPER IMPORTANT for making things clearer in the detailed and plain english summaries as there was so much interlinked analysis.

**Version control**. Version control history and CHANGELOG are both important. Version control history comes up alot in WP1 (ie. things just uploaded). In this case that did happen but partly by necessity of it needing to be kept private.

It took a **long time** to understand what the code did

* What had been done before vs within code was crucial, particularly with lots of survival analyses
* The NICE reports were key for me in understanding this and were my main resource. The detailed explanation of everything. Although still difficult as those are very complex and I had to understand/learn about techniques and how they work
    * Would have been handy to link to that from repo as I had to find myself
* Still don't fully understand!
    * This was only getting enough understanding to write documentation and not full reuse
* This was necessary for (a) model overview, and (b) code walkthrough
    * Unlike reproductions, where I often just tried running code after reading paper, with no deeper understanding than that
* This was greatly aided by looking what the code output at each step, which would not have been possible if I hadn't been able to run the model.
* Main barrier was code complexity (lots of analyses, lots of interlinked functions) - inescapable given the large ambitions of this project and I don't have any suggestion for how it could be less complex

They had packages listed and R version, but I had difficulty trying to backdate to work with their R version and just ended up on latest of everything. I made renv which may help address it as that has R version and package version and all dependencies, but also maybe not, as I know some people struggle to restrore renv, though I can't speak from experience on this.

Model slow ot run. Found it was quickest with no parallel processing. Once did longest bit, from then on for that bit, I just loaded the pre-run results.

Legacy code is confusing, best to remove it if possible.

* Removing legacy file as well as legacy code (e.g. blank sub folder READMEs)
* But also within file legacy code, inc. eg. exact duplicate lines of code, or inputs for functions that are no longer used, and not just like unused functions or lines of code
* And whole files with old code

CHANGELOG and releases are not mentioned in framework but feel important as handing at demarking specific points in time. In context of reproduction, that could be about e.g. specific version matching with paper, and allowing changes since. In context of reuse, that could just be generally, about changes made, showing model development, particularly if start using then gets worked on more.

Enabler: Already familiar with Quarto... although, less familiar with Shiny, and still got on fine with that.

Enabler: Code already structured into functions and seperate folders

Timings:

* Essential = speedy
* Docs = very long time
* Hosting = speedy
* App = pretty quick, although I only did one aspect, in this case it would be very complex to do all inputs modified in app and then code run, but would require consideration of how best to implement in this case, given computation time, number of inputs, complexity of inputs. E.g. selection of pre-run models with specific scenarios
* Online code = not suitable du to computation time. although would there be any argument for it? to run part of model? or perhaps not?

ORCID: identified from online serach

Citation: created CFF with cffinit

Minimal docs: I did license type and funding, but that is not currently in framework. Its something we often do though. How important is it?

Minimal docs: "how to vary parameters to run new experiments" - initially I found this requirement quite daunting but then I realised it can be quite a simple statement referring to the scripts and/or functions as you would need to change (rather than delving into the specifics)

* Essential STARS doesn't require deeper understanding from you, or to be giving deeper understand to the reader, and can just be about guiding them on to where to get started

I improved the README clarity, structure and apeparance. Banner image with logos, badges, table of contents, info from the word document

* Looking at compiled examples of good READMEs helped with this
* This was quick (and fun) to do
* Improves clarity and readability but no more deeper understanding

When applying the framwork, I didn't refer to the STARS paper, but instead to my own notes on each part of framework from paper, which I made for evaluating articles for WP1. I found it important/handy to have framework available as list with information needed about each part of framework incorporated into that list.

Had planned to change to package as: (a) standardised structure of code, and (b) standardised documentation (e.g. docstrings, function descriptions)

* From this, noticed convention differences. E.g. R packages want `NEWS.md` rather than `CHANGELOG.md`. They also want a `CITATION` file rather than a `CITATION.cff`. Although there is no one right way to do things, and you could argue reasons either way to use one or the other.
    * This does point to benefit of including citation info in README too
* It is not easy to just switch a complicated fully deevloped model into R package structure, particularly if unfamiliar with R packages
    * This reflection feels specific to the retrospective application of the framework. I think it would have been easier / more straightforward, if had wanted, to start with package structure from early on and then build it up as build up complexity
    * However, it is not impossible. I did it retrospectively for IPACS. So I think a part of this is complexity, and not just been reotrpsective. To turn IPACS into a package, it required a super deeper understanding of what code was all doing and how every line worked. ANd although I managed to improve my understanding of this model enough to make the documentation, to change it to package would've required way more (e.g. for example, knowing how to write each docstring and parameter inputs and outputs and so on)
* Package adds alot of procedure and requirements that, on reflection, might nto actually be that beneficial. I ended up focusing on understanding code and developing documentation
    * Would be handy to know guidance on when package structure can be handy, or necessary, or unnecessary
        * There are varying thoughts and recommendations on how best to structure code, and its not one size fits all. Could be nice to refer to these examples? Like that nice R research compendium one?

Regarding the web application, scope is important, and capacity, and what and why you make an app. In this case, computationally intensive and loads of inputs and complicated inputs. Not as simple as just popping into into an app.

* They are hoping for grant to explore this. They will need to consider if users can sit and wait through wrun time, or if it can be sped up somehow, or if you can have some elements pre run, or limited to certain part of analysis.
* Regardless of what level, it is still possible to achieve their stated aims to varying degrees
    * `Model_Structure.R` -
        * "During Phase 2 a Shiny front-end will be added to the model which will allow an alternative mechanism to upload these types of inputs" - which is in reference to the inputs from Excel
        * "Note that draw_plots will be a switch in the shiny application" - which is in reference to the draw_plots input of `f_surv_runAllTSD14()`
    * [Final analysis plan](https://www.nice.org.uk/guidance/gid-ta11186/documents/assessment-report-3) -
        * "Within Phase 4 of the project, the health economic model will be embedded into an interactive web browser using R-Shiny functionality to provide an easy-to-understand user-interface. This is intended to test the use of such functionality to support the development, critique and understanding of the model structure (and underlying R code) for decision makers and other stakeholders in future models."
    * [Assessment report](https://www.nice.org.uk/guidance/ta964/documents/assessment-report) -
        * "A later stage of this pilot following the evaluation of cabozantinib + nivolumab will involve the incorporation of a Shiny front-end to the R model. Shiny is an open source R package enables the user to build web applications using R. 221 This will allow model users to interact via an easyto-understand user-interface operating via their web browser."

Any differences doing this than when implement for DES?

* Can't comment on the comparison itself as I have not tried to do this for DES
* STARS as very applicable. This can be articulated by WHAT I did and WHY I did it (was the reason due to it not being DES, or another reason)

My changes to the repository should aid reuse, but the reality is that a massive factor relates to model complexity

* Although I can't necessarily speak to this from a health economics perspective... perhaps that is a faciliator? Although there is a mention of it in their article - "...the sheer scale of the decision problem in terms of the systematic review, network meta-analyses, and clinical consultation work required. This in turn led to a complex, computationally expensive model"

## Sorting notes into sections...

### Things to mention to Tom

I didn't try to **reuse** this model, so its hard for me to speak to that purpose. If we had time/need for it/in hindsight otherwise, it would be beneficial to:

* (a) read paper
* (b) plan scope of things to reproduce
* (c) plan new use for and reuse of the model - i.e. can i get these three new scenarios I came up with to run, that are similar or maybe slightly different to the paper

It could be something I could do retrospectively on a WP1, or retro or pro on a WP3? Or not at all?

It could just be something I do informally for myself? If we do have another WP3 that I do like this - just as part of helping me develop and understand differences when actually trying to reuse code

* Although catch to that is people don't necessarily reuse code in its full code, but might just adapt or use chunks
* What did people find when trying to reuse IPACS? That's a real practical applied example of the issue, could be worth mentioning?

### Things to mention to Dawn and Darren

If get funding to continue working on it, one suggestion would be to remove legacy code within files, and legacy files themselves (e.g. old scripts for pre-processing).

I wasn't able to run the survival analysis, got an error, might be a me problem, but could be something they could try and check if works for them?

### Working retrospectively

These are reflections specific to the retrospective application of the framework - identifying what was relatively easy to do at the end, versus what would have been better to do as you went along.

**Dependency management**. Although possible to do at the end, it is good practice to do this from the start, so you avoid troubles for yourself if you change versions, or if you're collaborating with others in development.

### Barriers and enablers to applying the framework

### Shortcomings of the framework

### Approximate time, work and rework required to apply the framework


