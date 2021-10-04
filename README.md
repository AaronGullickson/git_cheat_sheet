# Git/GitHub Cheat Sheet

I am compiling here a useful list of commands and procedures for working with git and GitHub, particularly from RStudio using the [usethis](https://usethis.r-lib.org/) package (which must be installed first), but also directly from the command line. I preface all command line entries with `$`.

Feel free to for this for your own use.

**Table of Contents**

- [Git set up](#git-set-up)
- [Initialize a git repo and sync it with GitHub](#initialize-a-git-repo-and-sync-it-with-github)
- [Clone a GitHub repository locally](#clone-a-github-repository-locally)
- [Basic git workflow](#basic-git-workflow)

## Git set up

To set up your your basic git configuration, you can `use_git_config`. From an R console:

```r
usethis::use_git_config(user.name = "Jane Doe", user.email = "jane@example.com")
```

If you prefer to do this from the command line:

```bash
$ git config --global user.name "Jane Doe"
$ git config --global user.email jane@example.com
```

Either way, I would also suggest that if you are on a MacOS system, you also the `core.editor` which is the default text editor to "nano" to avoid being stuck in [vim hell](https://qz.com/990214/a-million-people-have-visited-this-web-page-explaining-how-to-close-vim-a-notoriously-difficult-text-editing-program/).

If using the `usethis` package, I would also recommend running:

```r
usethis::git_vaccinate()
```

This will add several items to your global .gitignore file that will be carried over when you initialize new git projects from RStudio.

You can always check on the current status of your git in R with:

```r
usethis::git_sitrep()
```

### Setting up a GitHub Personal Access Token

Working with GitHub remotes now requires a personal access token (PAT) rather than just your GitHub password. This will include any push commands. You can create a appropriate on GitHub by going to your global user settings (not repository settings) and then Developer > Personal access token. However, I would recommend that instead you type the following from the R console:

```r
usethis::create_github_token()
```

This will take you to the same page on GitHub, but has the advantage that it will already check all the most common scopes that you need to work with GitHub. If you do it manually, you will need to check the appropriate scopes.

Copy the PAT (you only get once chance!). Once you have your PAT, you can use it when you are prompted for your "password" when running git operations. To avoid this nuisance, you can instead cache your PAT so that it will be run automatically. From the R console:

```r
gitcreds::gitcreds_set()
```

Follow the prompts on-screen and paste your PAT when it is asked for. You should now be able to push to your GitHub repositories without authenticating every time.

## Initialize a git repo and sync it with GitHub

I assume you have a local directory with a project that you would like to turn into a git repository synced to GitHub. With the `usethis` package, this process is straightforward.

Before beginning, make sure that R is in the correct working directory:

```r
getwd()
```

The working directory should be the same as the top-level of the directory that you want to make into a git repository. Once you are sure that this is correct, simply type the following into the R console:

```r
# Create an R project in the current working directory. Note that this command
# will open up a new RStudio window with your project loaded.
usethis::create_project(".")

# Initialize the current directory as a git repository. This command will give 
# you a couple of prompts. Most importantly, it will ask you if you want to
# make an initial commit of all the files in the directory. Only do this if 
# you are sure all the files should be part of your repository. You can always
# make the initial commit manually by choosing specific files later.
usethis::use_git()

# Create a repository on GitHub and push everything in this repo to that new
# repository. Note that you need to change `private=TRUE` if you want this to
# be a private repository (public by default)
usethis:use_github(private=FALSE)
```

You are now fully set up and should be able to push and pull between the repositories.

If you prefer to do this from the command line, you will have to create the new repository on GitHub first and copy the https address for that repository (given to you when you set up the repository for the first time). You can then type into the command line (from the right working directory):

```bash
$ git init #initialize the repo
$ git add .  #stage all files (you can also pick specific ones instead)
$ git commit -m "initial commit" #make the initial commit
$ git remote add origin <https address> #add the GitHub remote as origin
$ git branch -M main #change default branch name to main
$ git push -u origin main #push changes to the GitHub repository
```

You can always run `usethis::git_sitrep()` from R to see the status of your current git repository.

## Clone a GitHub repository locally

Instead of turning a local repository into a GitHub repository, you may want to clone an already existing GitHub repository to your local machine. To do this, you will need to copy the https address for the repository (given under the big green "Code" button on the main page of the repository).

Once you have the https address, you can clone from the R console with:

```r
usethis::create_from_github("<https address>", destdir="path/to/destination")
```

If you do not set the `destdir`, then the repository will most likely be cloned to your desktop. If you would like to place the repository in the current working directory, then you can use `destdir="."`.

Note that if you run this from RStudio, this repository will be initialized as an RStudio project as well. You can control this behavior with the argument `rstudio=TRUE/FALSE`.

You can also clone from the new project wizard in RStudio. Just go to File > New Project. Then select Version Control > Git. From the final wizard, enter the https address of your GitHub repository and the "Create Project" button.

If you prefer to clone from git itself, then from the command line (in the correct working directory):

```bash
$ git clone <https address>
```

Note that this will not set up your git repository as an R Project. You can do this manually from the new project wizard by by selecting the "Existing Directory" option.


## Basic git workflow

The basic git workflow is:

1. Pull in changes from the remote.
2. Do your work.
3. Commit changes in small logical chunks.
4. Push changes to the remote.

In RStudio, you can use the git tab to accomplish all of these tasks. The commit dialog will allow you to make commits and the push and pull buttons will do pushing and pulling. 

Note that the git tab will only show up in an R project. So if you cloned your repository outside of RStudio, you will need to use the new project wizard to make the existing directory an R project (basically adds an *.Rproj text file). Also, if you did not open a specific project through RStudio, you will not see the git tab even if you are in the correct working directory. To open the project, use the project menu in the upper right or just double-click the *.Rproj file in your system's file viewer. 

If you want to use the basic git workflow from the command line, the first command you should know is:

```bash
$ git status
```

This will tell you the current status of your git repository and identify files with uncommitted changes and untracked files.

You can stage and commit files with:

```bash
$ git add fileA fileb some_directory/fileC ...
$ git commit -m "write a commit message"
```

To push and pull:

```bash
$ git push <remote name> <branch name>
$ git pull <remote name> <branch name>
```

Assuming youre remote is named `origin` and your branch is the default `main` branch, you can push with:

```bash
$ git push origin main
```

Note that since `origin` is already set up as the default upstream repository for `main`, you can probably just type `git push` and `git pull` and be fine when on the `main` branch.

## The .gitignore file

The .gitignore file is a text file in you top-level directory that will tell git what files to ignore when indicating what files should be put under version control. This file will be created automatically if you create the repository through RStudio. If you initialize git from the command line, you will have to create it manually. 

If using RStudio, I recommend you run:

```r
usethis::git_vaccinate()
```

This will create a global default .gitignore file in your home directory which will be used as the default when you create any new git repository.

You can use [globbing parameters](https://linux.die.net/man/7/glob) to identify types of files you might want to ignore. Some common cases are:

```gitignore
# ignore all files anywhere in your repository that end in .log
*.log 

# ignore all directories anywhere in your repository that are 
# named logs
**/logs

# ignore all files ending in .log except thisone.log
*.log
!thisone.log

# ignore any filed named debug log anywhere in the logs directory 
# including subdirectories of that directories
logs/**/debug.log
```

What should go into your gitignore file? Here are some possibilities:

* Any system junk files like .DS_Store
* Any files with sensitive/private information
* Any built files that are created by the scripts

Here is a starter gitignore file for data science:

```gitignore
# files added by usethis::git_vaccinate
.httr-oauth
.Rproj.user
.Rhistory
.Rdata
.DS_Store
# built files
*.html
*.pdf
*.log
# Ignore all the junk created by LaTeX
*.aux
*.out
*.bbl
*.ent
```

### Removing a file from version control

If a file has already been committed to version control, then ignoring that file or its type will have no affect on that particular file. To get git to stop tracking changes to it, you must tell git to stop tracking it:

```bash
$ git rm --cached specific_file_path
```

You can then delete the file locally and commit the deletion and git will no longer track this file.
