# Git/GitHub Cheat Sheet

I am compiling here a useful list of commands and procedures for working with git and GitHub, particularly from RStudio using the [usethis](https://usethis.r-lib.org/) package (which must be installed first), but also directly from the command line. I preface all command line entries with `$`.

Feel free to for this for your own use.

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

If you prefer to do this from the command line, you will have to create the new repository on GitHub first and copy the https address for that repository. You can then type into the command line (from the right working directory):

```bash
$ git init #initialize the repo
$ git add .  #stage all files (you can also pick specific ones instead)
$ git commit -m "initial commit" #make the initial commit
$ git remote add origin <https address> #add the GitHub remote as origin
$ git branch -M main #change default branch name to main
$ git push -u origin main #push changes to the GitHub repository
```
