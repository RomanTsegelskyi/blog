---
layout: post
title: Update GH-pages automatically with Travis
comments: True
---

While working on [Pander](https://github.com/Rapporter/pander/) during [Google Summer of Code 2015](https://www.google-melange.com/gsoc/project/details/google/gsoc2015/romantsegelskyi/5738600293466112), I came across interesting task of automatically updating GH-pages and README using [travis](http://travis.com/) and decided to do a small write-up about my experience. Additional usage examples that I will give are more or less specific for repositories in [R](www.r-project.org) programming languages, but general setup and everything else is generic if notes otherwise.

### Github Credentials for Travis

To allow travis to seamlessly push the changes to and push to GitHub, you will need to set up [GitHub personal access token](https://github.com/settings/tokens) and tell travis about it. Essentially we will create an environment variable `GH_TOKEN` with your actual GitHub token, that is encrypted on the travis-ci servers. So this way you don't expose your actual GitHub token to anyone who looks at your `.travis.yml` file. Note that if you push the unencrypted token to the public repository on Github, it will be automatically revoked.  The rule of thumb is that travis won't show the ecrypted variables to pull requests from other repositories, which IMHO is for better. 

There are 2 ways to tell travis about your token without exposing it:

1) *Defining the environment variable using web interface*.

Travis allows to define environment variable directly. When you go to settings you should see the screen similar to his.

![](http://docs.travis-ci.com/images/settings-env-vars.png)

Add variable `GH_TOKEN` with token obtained before. Minor note is to keep `Display value in build log` option as off, to avoid exposing the key in the logs.

2) *Encrypt the key and put it in `.travis.yml`*.

travis gracefully provides a [Ruby gem](https://github.com/travis-ci/travis.rb), which can be installed using:

```
gem install travis
```

After installing, go to git repository and use:

```
  travis secure GH_TOKEN="yourgithubtoken" --add
```

Note the 2 spaces before command, so it will not be saved in `.bash_history`. You can never be too secure. Also we gonna use `--add` option to write the encrypted key to `.travis.yml`. If for some reason you don't want to use `--add` option (for example because it changes formatting of `.travis.yml`), just copy the encrypted key to `.travis.yml` so it looks the following way:

```
env:
  global:
  - secure: <string emmited by travis secure>
```

By default it will use the current repository, if you need to encrypt a key for different repository (for example if you are working on a forked copy, but want the functionality to be enabled for main repository), use `-r` option. Note that you need write access to the repository you are trying to specify.

### Development script

We want a script that does a local processes files to e.g. an out/ directory and after that pushes it to `gh-pages`. We will call it `ghp-updater.sh`, and start with simple example of converting `README.md` to `index.html` using [pandoc](http://pandoc.org/) and pushing it to `gh-pages` branch.

```
#!/bin/bash
GH_REPO="@github.com/USER/REPO.git"
FULL_REPO="https://$GH_TOKEN$GH_REPO"

mkdir out
cd out

# setup REPO and checkout gh-pages branch
git init
git remote add origin $FULL_REPO
git fetch
git config user.name "rapporter-travis"
git config user.email "travis"
git checkout gh-pages

# do useful work for gh-pages, for example convert README.md to index.html
pandoc ../README.md -o index.html

# commit and push changes
git add index.html
git commit -m "GH-Pages update by travis after $TRAVIS_COMMIT"
git push origin gh-pages
```

After creating the script, in case you are using `R`, don't forget to add it to `.Rbuildignore`. Also you will need to update `.travis.yml` to execute the script on build success.

```
after_success:
- bash ghp-updater.sh
```

Also using using `Travis` environment variable we can be smart about it and execute the script only on commits to `master` that are not pull-requests.

```
after_success:
- test $TRAVIS_PULL_REQUEST == "false" && test $TRAVIS_BRANCH == "master" && bash ghp-updater.sh
```

Travis build environment contains a lot of useful variable that can neatly used to tune the script or it's execution either for specific conditions (complete list can be found [here](http://docs.travis-ci.com/user/environment-variables/#Default-Environment-Variables)). The immediate uses that came to my mind was to build only commits to `master` and use commit id to know in updated commit message to ease debugging in case something breaks. Other obvious ideas is to only push on tags for examples.

Examples for deployments scripts I have created for [Pander](https://github.com/Rapporter/pander/blob/master/.gh-pages-update.sh) and [yummlyr](https://github.com/RomanTsegelskyi/yummlyr/blob/master/updater.sh).

#### Automatic vignette building

When working on R package, sometimes when changing vignettes written using `knitr`, it's easy to forget to actually build/copy them to `inst/doc`. While this is minor mistake that can be quickly fixed, why not to add some functionality that will do it automatically for you? Here is the code I use for that. 

```bash
CHANGED_FILES=`git show --stat $TRAVIS_COMMIT`
# check if vignettes were updated
if [[ $CHANGED_FILES =~ .*vignettes.*\.Rmd.* ]]
then
  R -e 'devtools::build_vignettes()'
  git add inst/doc
fi
```

Note that I use `git show --stat` instead of `diff-tree`, because for merge commits `diff-tree` only shows diff of files by merge commit, not by all commits that are being merged.

#### Updating README.Rmd with knitr

My favorite use for this beside automatically deploying GH-Pages is to automatically generate `README.md` for repository if the corresponding `README.Rmd` was changed. As this was primarily done with R packages is mind though not exclusively, I have shifted writing documentation for R to with knitr. For example, assume you have a `README.Rmd` and you want to knit markdown version on change. This can be easily done the following way:

```bash
CHANGED_FILES=`git show --stat $TRAVIS_COMMIT`
if [[ $CHANGED_FILES =~ .*README\.Rmd.* ]]
then
  R -e 'devtools::install_github("rstudio/rmarkdown"); rmarkdown::render("README.Rmd")'
  git add README.md
fi
```

One possible modification to that is to add installation of `rstudio/rmarkdown` to travis settings, but in that case it will be triggered on every build. 
