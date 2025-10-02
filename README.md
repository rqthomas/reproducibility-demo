# Challenges with reproducing analyses

1.  Data are not available
2.  Data are in a format that is not readable (Lotus123 spreadsheet?)
3.  Code is not available to run analysis (only the method section is available)
4.  Correct software is not available to read data or run analysis (i.e, \|\> requires R version 4.1 or higher)
5.  R packages used in analysis have changed and are not backward compatible (how long will dplyr support spread and gather?)
6.  Computer architecture doesn't exist anymore
7.  The world as we know it doesn't exist anymore

# The reproducibility highway

The following are steps on the reproducibility highway. You do not need to drive the full length of the highway for all projects (in fact #7 above suggests that the highway never ends - how do we prepare for the end of the world?). It is important to figure out which highway exit is suitable for your project.

## Exit 1: Reproductiblity from GitHub

Add your code to GitHub and have others clone and run your code. Since you don't know the computational environment that other are using you have to be careful about R versioning and package dependencies. For straightforward analyses that use common R packages that maintain backward compatibility, then putting your code on GitHub is a solid foundation for reproducibility.  

It is best if you make your GitHub repo an R project so that Rstudio understand relative pathing better.

You would then archive your GitHub repo on Zenodo (<https://zenodo.org>) so that it persists. Here is information about linking your GitHub repo to Zenodo and then creating a release of your code that gets automatically upload to Zenodo. You then got into Zenodo to update the metadata. See more here: <https://coderefinery.github.io/github-without-command-line/doi/>

## Exit 2: Reproducibility from GitHub + renv

Renv is a package manager for R (<https://rstudio.github.io/renv/articles/renv.html>) that controls for the version of R packages. I have yet to have a project that didn't involve me throwing a can, bottle, or other nearby object at my computer screen because an `renv.lock` file gets cross wired. Renv is awesome in principle but not in practice.

## Exit 3: Reproducibility using Rocker + GitHub

Starting from a Docker container is the closest thing you can do to starting from a fresh computer that isn't filled with all the packages and data sets that you already have on your computer. A fresh computer forces you to be explicit about package installs, etc. and will make it easier for others to successfully reproduce your analysis.

A Rocker is a Docker container with R (potentially also with Rstudio and other R packages). The one with Rstudio makes it easy to use a Docker container in a familiar interface.

1.  Download and install Docker Desktop on your computer. The instructions are at <https://www.docker.com/>.

2.  Launch Docker by starting the Docker application.

3.  Find the command line for your computer (terminal in Mac or command prompt in Windows)

4.  In the command line, try to run `docker run --rm -ti -e PASSWORD=yourpassword -p 8787:8787 rocker/rstudio` that is documented here: <https://rocker-project.org/>

5.  Point your browser to `localhost:8787`. Log in with user = rstudio, passwoard = yourpassword.

6. Start a new project in your rocker container (Exits 3 & 4) from your forked GitHub repo. (new project -\> Version Control -\> Git)

7. Test your code

## Exit 4: Reproductiblity using Docker + GitHub

In the above example, you start from the rstudio rocker and then install packages on top of it. To be more explicit about the users environment, you can provide your own docker container. You would do this if you have a particular Rocker version that you want to start with or you are worried about packages changing.

1.  Update the Dockerfile in this repo to match your repo

2.  Create user account on hub.docker.com

3.  At the terminal navigate to your github repo on your computer. Then run the following code to create, tag, and push your Docker container

```         
docker build https://github.com/rqthomas/reproducibility-demo.git#main -t thomas_demo#main -t thomas_demo
docker image tag thomas_demo rqthomas/thomas_demo:latest
docker image push rqthomas/thomas_demo:latest 
```

Someone performing a code review or reproducing your analysis will follow the steps in Exit 3, except for changing the docker "image" that is used from "rocker/rstudio" to the custom image ("rqthomas/thomas_demo" in the example) 

```         
docker run --rm -ti -e PASSWORD=yourpassword -p 8787:8787 thomas_demo:latest
```

5.  Point your browser to `localhost:8787`. Log in with user = rstudio, passwoard = yourpassword.

6. Start a new project in your rocker container (Exits 3 & 4) from your forked GitHub repo. (new project -\> Version Control -\> Git)

7. Test your code

# Performing a code review

1.  On GitHub.com, create a fork of the repo that you will review into your GitHub organization.  For this example the GitHub is this one:  <https://github.com/rqthomas/reproducibility-demo>.  Find the fork button, click it, and then select your GitHub organization.

2.  Start a new project in the on your computer (Exit 1) or rocker container (Exits 3 & 4) from your forked GitHub repo. (new project -\> Version Control -\> Git)

3.  The following checklist is a list of things to review in the code:

-   Does the GitHub repo have a Readme?
-   Does the Readme tell you how to run the analysis?
-   Can you run the analysis successfully out-of-the-box?
-   Does the repo have an install.R script that includes all the necessary packages beyond the recommended Rocker container?
-   Is there a clear step for downloading data that works?
-   Are custom function in an R subdirectory?
-   Is there extra code that isn't used in the analysis
-   Is there a ton of commented-out code in the analysis script that others will be looking at? That should be cleaned up!
-   Does the Readme list the authors and manuscript title? Does it have a license? (GNU General Public License v2.0 is one that may be appropriate, see: https://www.cmu.edu/cttec/forms/opensourcelicensegridv1.pdf)
-   Is there repeated code that should be in functions?
-   Are there single-use functions in R subdirectory that would be better in the main analysis script for improving read ability?

5.  If you find any issues that you can fix, fix them in the code!

6.  Commit and Push the updates to your fork.  If you are using a Docker container you will need to set up your GitHub credentials on the container following: <https://github.com/frec-3044/git-rmd-intro-template/blob/main/assignment/instructions.md>

7.  Go to your fork on GitHub.com and select "Contribute". Open a PR. In the discussion of the PR, describe the key fixes that you to addressed.

# Guidelines when requesting a code review from a collaborator

Consider that requesting a collaborator to review your code is similar to asking that person to review a manuscript. As a result, similar to when you are drafting a manuscript, it's best practice to make sure your code is well-organized, concise, and generally interpretable by someone else before you ask for a review, as a professional courtesy. Below are provided a few guidelines to consider when requesting a code review from a collaborator.

1. **Clarify your review objectives when you request a review.** Is your primary objective merely to ensure that the code runs with no errors? Or are there sections of it that you would like the reviewer to screen more carefully, such as a particular analysis that you want to make sure is free of mistakes? Again, similar to when you request a manuscript review, giving the reviewer some guidance about the type of feedback you are seeking can both make the reviewer's life simpler and help you accomplish your goals.

2. **Ensure your code repository is well-organized.** Is it clear to the reviewer where to begin, and which scripts need to be run in what order? Is there an informative README.md? Are scripts well-annotated so the reviewer can quickly navigate the code and follow along?

3. **Omit needless code.** Similar to how you generate an initial draft of text and then revisit and revise it to improve the wording, consider that revising your code to make sure it is concise and efficient can make a reviewer's life much easier. I doubt any of us want to read 1000 lines of code if the same tasks can be executed in 100 lines! While we are ecologists first, not programmers, a little code etiquette can really help a reviewer (and you later on!). If you have code that isn't currently needed but that you aren't ready to part with, consider storing it in an archive folder that can be ignored by the reviewer.

4. **Consider using functions for repeated tasks.** The benefits to this approach are many! To name a few: 1) once you get beyond a few hundred lines of code in a script, writing functions makes it easier to navigate your code, as you can "follow the trail" of functions back to the line of code you are looking for rather than scrolling through thousands of lines of code; 2) following on (1), it makes code easier to troubleshoot, as you can pin down which function is misbehaving; 3) it helps avoid errors due to repeated copying and pasting of code. If you find that you are repeatedly copying and pasting very similar code with just a few modifications (e.g., only changing the year of the dataset, the study site, the study organism, the value of the model parameter, etc.), think about writing a function that takes the thing(s) you need to change (year, site, etc.) as arguments.

5. **Scan your code for 'readability'.** Just as a page of densely formatted text in 10 pt font with no paragraph breaks can be a little intimidating to tackle, code that is written with little spacing or extremely long lines running off the page can be difficult to read. Have a heart for your reviewer's eyes (and your own!).

Additional resources (please add to this list!):

https://www.browserstack.com/guide/coding-standards-best-practices
https://www.michaelagreiler.com/code-review-best-practices/

