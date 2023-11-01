# molecule-ansible-poetry
Example project showing Ansible collections, Molecule delegated tests and Poetry as dependency management


## What it's about

After some time of absence I wanted to have a brief look into Ansible again: I had a deep look into it but didn't had the time to look into Collections, Ansible 2.10+ and the new Molecule with only Delegated drivers. Also I heard [that Poetry for dependency management is maybe slightly better then my beloved pipenv](https://plainenglish.io/blog/poetry-a-better-version-of-python-pipenv). So let's give these tools a short look.

Since I'm currently in the process of switching my go-to environment to Linux / Manjaro, I will also add the needed commands here.


## Install Python, pip, pipx and Poetry

First we need to be sure to have a current version of Python installed. On a Mac a great idea is to use homebrew (`brew install python`), on Manjaro Linux it's already present:

```
$ python --version
Python 3.11.5
```

On a Mac `pip` should be installed via the homebrew package also, on Manjaro I needed to leverage `pamac`:

```
pamac install python-pip
```

Also I wanted to have [Poetry](https://python-poetry.org/) installed. The [docs help us with the installation](https://python-poetry.org/docs#installation). There is a recommended installation script for Poetry, since there seem to be drawbacks using homebrew or other package managers like Manjaro's pamac: https://stackoverflow.com/a/73365831/4964553 But there's help! [pipx](https://github.com/pypa/pipx) is also fully supported by Poetry and acts like pip  while still isolating them in virtual environments. The [installation of pipx works like this](https://github.com/pypa/pipx#install-pipx):

```
# MacOS
brew install pipx

# Manjaro Linux
pamac install python-pipx
```

Also run `pipx ensurepath`. Something like this should appear:

```
$ pipx ensurepath
/home/jonashackt/.local/bin is already in PATH.

⚠️  All pipx binary directories have been added to PATH. If you are sure you want to proceed, try again with the '--force' flag.

Otherwise pipx is ready to go! ✨ 🌟 ✨
```

Now can finally [install Poetry via pipx](https://python-poetry.org/docs#installing-with-pipx) via `pipx install poetry`:

```
$ pipx install poetry                                                                                  ✔ 
  installed package poetry 1.6.1, installed using Python 3.11.5
  These apps are now globally available
    - poetry
done! ✨ 🌟 ✨
```

Now upgrading Poetry is as simple as `pipx upgrade poetry`.

Also tab completion could be added to your terminal: https://python-poetry.org/docs#enable-tab-completion-for-bash-fish-or-zsh

## Create 