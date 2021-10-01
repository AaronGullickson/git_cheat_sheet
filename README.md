# Git/GitHub Cheat Sheet

I am compiling here a useful list of commands and procedures for working with git and GitHub, particularly from RStudio using the [usethis](https://usethis.r-lib.org/) package (which must be installed first), but also directly from the command line. 

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
