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
usethis:git_sitrep()
```

### Setting up a GitHub Personal Access Token



## Initialize a git repo and sync it with GitHub
