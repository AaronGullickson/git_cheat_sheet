# Git/GitHub Cheat Sheet

I am compiling here a useful list of commands and procedures for working with git and GitHub, particularly from RStudio using the [usethis](https://usethis.r-lib.org/) package (which must be installed first), but also directly from the command line. I preface all command line entries with `$`.

Feel free to for this for your own use.

**Table of Contents**

- [Git set up](#git-set-up)
- [Initialize a git repo and sync it with GitHub](#initialize-a-git-repo-and-sync-it-with-github)
- [Clone a GitHub repository locally](#clone-a-github-repository-locally)
- [Basic git workflow](#basic-git-workflow)
- [Branching and merging](#branching-and-merging)
- [Undoing stuff in git](#undoing-stuff-in-git)
- [The .gitignore file](#the-gitignore-file)
- [Useful links](#useful-links)

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

Either way, I would also suggest that if you are on a MacOS system, you also set the `core.editor` to "nano" to avoid being stuck in [vim hell](https://qz.com/990214/a-million-people-have-visited-this-web-page-explaining-how-to-close-vim-a-notoriously-difficult-text-editing-program/).

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

### Amending a commit

Sometimes, you forget to put something into a commit. The most common case is that you had some unsaved changes in a file because you hadn't saved it recently and realize that after doing the commit. If you realize this before making other commits, you can amend your most recent commit to include the changes.

You generally should only do this if you have not yet pushed to a remote. If you have pushed to a remote, then the commit histories in the two repositories will not be aligned and you will have to deal with some issues when you next try to pull. 

Assuming you have not pushed, then to amend the previous commit, stage the changes you want to add and then type from the command line:

```bash
$ git commit --amend -CHEAD
```

This will amend the previous commit to include the newly staged changes. Alternatively, In RStudio, you can use the "Amend previous commit" checkbox below the commit message in the commit dialog.

## Branching and merging

To create a new branch in RStudio, just click the purple branch button in the upper right of the git tab and provide a name. The advantage of this approach is that will create a corresponding branch on the remote reposistory as well. Next to this button, you will see a drop-down menu that shows you all branches on your local and remote repository and allows you to select the current branch. Always make sure you are on the right branch before committing!

To switch between existing branches from the command line just use `git checkout`:

```bash
$ git checkout branch_name
```

You can also use `git branch` to see all existing branches locally.

To create a new branch from the command line, just use the `git checkout` command with the `-b` argument:

```bash
git checkout -b new_branch_name
```

This will not create a corresponding branch on the remote, however. To add this branch to the remote, you must make a commit and then push that to the remote repository (assuming here that it is named "origin") using the `-u` option:

```bash
git push -u origin new_branch_name
```

The `-u` option tells git to use this remote repository as the default for all push commands from this branch, so in the future you should be able to just use `git push` to push changes to the remote repository.

To merge changes from a branch back into the main branch (or any other branch), you must first switch back to the branch you want to merge changes into. Then you can use the `git merge` command with the name of the branch you want to merge:

```bash
$ git checkout main
$ git merge new_branch_name
```

This will checkout the main branch and then merge all off the commits from the `new_branch_name` into `main`.

Although you can merge from the command line, I highly recommend using [GitHub pull requests](https://docs.github.com/en/github/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests) to handle merging branches back into main in most cases.

## Undoing stuff in git

You made some changes to files and now realize that you don't like those changes. How do you go back?

### Situation 1: You did not commit the changes yet

If you have not yet committed the changes, then you can checkout the file again from the most recent commit. This will wipe all changes to that file since you last committed. To checkout a file called some_script.R from the command line:

```bash
$ git checkout -- some_script.R
```

From RStudio, just right click the file in the git tab and choose "Revert...", although this is technically not a revert (see below).

### Situation 2: You committed the change but did not yet push it

If you are in this situation, then you can reset back to a good commit before the bad commit. Under a normal "soft" reset all of the work in commits since the reset will be left as uncommitted changes, so you will not lose other work, but you will have to recommit it.

As an example, lets say your last good commit before the commit with the work you want to remove is `ce65533`, then:

```bash
$ git reset ce65533
```

All of the work since `ce65533` (including the work you want to remove) will then be left as uncommitted changes which you can selectively recommit.

If you are ok with losing all of the work in commits since commit `ce65533`, then you can do a "hard" reset. This will delete all of the changes in subsequent commits after `ce65533`. Be **very careful** with this option as that work will be gone permanently. If your bad commit is buried pretty deep in your commit history, this is almost certainly not a good option. You might want to instead consider `git revert`, discussed in the next section.

```bash
$ git reset --hard ce65533
```

### Situation 3: You committed the change and pushed it

In this case, instead of removing the change, we will do a `git revert` which will add a new commit that "inverts" the original commit. This is generally the safest option for undoing things, but also adds another commit. Lets say that the changes you want to revert are in commit `39c398c`. To revert these changes:

```bash
$ git revert 39c398c
```

This will add a new commit that basically does the opposite of whatever is done in commit `39c398c`. Keep in mind that given other change to your code since that commit, this inversion might break your code (if for example, other code depends on an object that this commit created).


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

If a file has already been committed to version control, then ignoring that file or its type will have no affect on that particular file. To get git to stop tracking changes to a file called some_script.R, you must tell git to stop tracking it:

```bash
$ git rm --cached some_script.R
```

You can then delete the file locally and commit the deletion and git will no longer track this file.

## Useful links

* [GitHub-flavored markdown cheat sheet](https://guides.github.com/pdfs/markdown-cheatsheet-online.pdf)
* [GitHub's git cheat sheet](https://education.github.com/git-cheat-sheet-education.pdf)
* [Happy Git and GitHub for the useR by Jenny Bryan](https://happygitwithr.com/)
* [Why Jenny Bryan will burn down your computer, eg don't use absolute paths](https://www.tidyverse.org/blog/2017/12/workflow-vs-script/)
* [Kieran Healy's The Plain Person’s Guide to Plain Text Social Science](https://kieranhealy.org/publications/plain-person-text/)
* [Code and Data for the Social Sciences, by Matthew Gentzkow and Jesse M. Shapiro](https://web.stanford.edu/~gentzkow/research/CodeAndData.xhtml)
