# molecule-ansible-poetry
Example project showing Ansible collections, Molecule delegated tests and Poetry as dependency management


## What it's about

After some time of absence I wanted to have a brief look into Ansible again: I had a deep look into it but didn't had the time to look into Collections, Ansible 2.10+ and the new Molecule with only Delegated drivers. Also I heard [that Poetry for dependency management is maybe slightly better then my beloved pipenv](https://plainenglish.io/blog/poetry-a-better-version-of-python-pipenv) and [that it's kind of a successor to Pipenv](https://www.warp.dev/blog/prose-about-poetry):

> In 2022, with the standardization of the pyproject.toml manifest, poetry stands out as the most robust and easy-to-use package manager, even for a newcomer to the python universe. In all aspects, it is better than pipenv with a developer experience that I find more complete and enjoyable (see [this post](https://dev.to/farcellier/i-migrate-to-poetry-in-2023-am-i-right--115))

And [it's supported by Renovate](https://docs.renovatebot.com/modules/manager/poetry/). So let's give these tools a short look.

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



# Create a Ansible Collection, Role and Playbook via Poetry and Molecule

## Create a Poetry-based Python project

Using [Poetry](https://python-poetry.org/docs/basic-usage/) is straight forward. Let's create a new Poetry project:

```
poetry new molecule-ansible-poetry
```

This will create a directory `molecule-ansible-poetry` with the following structure:

```
$ tree
.
├── molecule_ansible_poetry
│   └── __init__.py
├── pyproject.toml
├── README.md
└── tests
    └── __init__.py
```

This includes the new file `pyproject.toml`, where everything Poetry related comes together:

```
[tool.poetry]
name = "molecule-ansible-poetry"
version = "0.1.0"
description = "Example project showing Ansible collections, Molecule delegated tests and Poetry as dependency management"
authors = ["Jonas Hecht <jonas.hecht@codecentric.de>"]
readme = "README.md"

[tool.poetry.dependencies]
python = "^3.11"


[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```


## Add Molecule via Poetry

Now we can [issue our Molecule installation](https://ansible.readthedocs.io/projects/molecule/installation/) leverageing Poetry via `poetry add molecule`:

```
$ poetry add molecule                                                                            ✔ 
Creating virtualenv molecule-ansible-poetry-MP2KKXLA-py3.11 in /home/jonashackt/.cache/pypoetry/virtualenvs
Using version ^6.0.2 for molecule

Updating dependencies
Resolving dependencies... Downloading https://files.pythonhosted.org/packages/ec/1a/610693ac4ee14fcdf2d9bf3c493370e4f2ef7ae2e19217d7a237ff42367d/packaging-Resolving dependencies... Downloading https://files.pythonhosted.org/packages/96/06/4beb652c0fe16834032e54f0956443d4cc797fe645527acee59e7deaa0a2/PyYAML-6.0Resolving dependencies... (4.4s)

Package operations: 27 installs, 0 updates, 0 removals

  • Installing attrs (23.1.0)
  • Installing pycparser (2.21)
  • Installing rpds-py (0.10.6)
  • Installing cffi (1.16.0)
  • Installing markupsafe (2.1.3)
  • Installing mdurl (0.1.2)
  • Installing referencing (0.30.2)
  • Installing cryptography (41.0.5)
  • Installing jinja2 (3.1.2)
  • Installing jsonschema-specifications (2023.7.1)
  • Installing markdown-it-py (3.0.0)
  • Installing packaging (23.2)
  • Installing pygments (2.16.1)
  • Installing pyyaml (6.0.1)
  • Installing resolvelib (1.0.1)
  • Installing ansible-core (2.15.5)
  • Installing bracex (2.4)
  • Installing click (8.1.7)
  • Installing jsonschema (4.19.2)
  • Installing rich (13.6.0)
  • Installing subprocess-tee (0.4.1)
  • Installing ansible-compat (4.1.10)
  • Installing click-help-colors (0.9.2)
  • Installing enrich (1.2.7)
  • Installing pluggy (1.3.0)
  • Installing wcmatch (8.5)
  • Installing molecule (6.0.2)

Writing lock file
```

The new dependecy `molecule` should appear inside the [`pyproject.toml`](pyproject.toml).

If we only use the Delegated driver, that's it. If we want to use other drivers, we need to install them separately by using `poetry add "molecule-plugins[docker]"`.

> Molecule uses the \"delegated\" driver by default. Other drivers can be installed separately from PyPI, most of them being included in molecule-plugins package: https://github.com/ansible-community/molecule-plugins

```
$ poetry add "molecule-plugins[docker]"
Using version ^23.5.0 for molecule-plugins

Updating dependencies
Resolving dependencies... Downloading https://files.pythonhosted.org/packages/08/dc/28c668097edfaf4eac4617ef7adf081b9cf50d254672fcf399a70f5efc41/pywin32-30Resolving dependencies... Downloading https://files.pythonhosted.org/packages/08/dc/28c668097edfaf4eac4617ef7adf081b9cf50d254672fcf399a70f5efc41/pywin32-30Resolving dependencies... Downloading https://files.pythonhosted.org/packages/08/dc/28c668097edfaf4eac4617ef7adf081b9cf50d254672fcf399a70f5efc41/pywin32-30Resolving dependencies... Downloading https://files.pythonhosted.org/packages/08/dc/28c668097edfaf4eac4617ef7adf081b9cf50d254672fcf399a70f5efc41/pywin32-30Resolving dependencies... Downloading https://files.pythonhosted.org/packages/08/dc/28c668097edfaf4eac4617ef7adf081b9cf50d254672fcf399a70f5efc41/pywin32-30Resolving dependencies... Downloading https://files.pythonhosted.org/packages/08/dc/28c668097edfaf4eac4617ef7adf081b9cf50d254672fcf399a70f5efc41/pywin32-30Resolving dependencies... Downloading https://files.pythonhosted.org/packages/08/dc/28c668097edfaf4eac4617ef7adf081b9cf50d254672fcf399a70f5efc41/pywin32-30Resolving dependencies... Downloading https://files.pythonhosted.org/packages/08/dc/28c668097edfaf4eac4617ef7adf081b9cf50d254672fcf399a70f5efc41/pywin32-30Resolving dependencies... Downloading https://files.pythonhosted.org/packages/08/dc/28c668097edfaf4eac4617ef7adf081b9cf50d254672fcf399a70f5efc41/pywin32-30Resolving dependencies... Downloading https://files.pythonhosted.org/packages/08/dc/28c668097edfaf4eac4617ef7adf081b9cf50d254672fcf399a70f5efc41/pywin32-30Resolving dependencies... Downloading https://files.pythonhosted.org/packages/08/dc/28c668097edfaf4eac4617ef7adf081b9cf50d254672fcf399a70f5efc41/pywin32-30Resolving dependencies... Downloading https://files.pythonhosted.org/packages/08/dc/28c668097edfaf4eac4617ef7adf081b9cf50d254672fcf399a70f5efc41/pywin32-30Resolving dependencies... Downloading https://files.pythonhosted.org/packages/08/dc/28c668097edfaf4eac4617ef7adf081b9cf50d254672fcf399a70f5efc41/pywin32-30Resolving dependencies... Downloading https://files.pythonhosted.org/packages/08/dc/28c668097edfaf4eac4617ef7adf081b9cf50d254672fcf399a70f5efc41/pywin32-30Resolving dependencies... Downloading https://files.pythonhosted.org/packages/08/dc/28c668097edfaf4eac4617ef7adf081b9cf50d254672fcf399a70f5efc41/pywin32-30Resolving dependencies... (2.6s)

Package operations: 10 installs, 0 updates, 0 removals

  • Installing certifi (2023.7.22)
  • Installing charset-normalizer (3.3.2)
  • Installing idna (3.4)
  • Installing urllib3 (2.0.7)
  • Installing distro (1.8.0)
  • Installing requests (2.31.0)
  • Installing websocket-client (1.6.4)
  • Installing docker (6.1.3)
  • Installing selinux (0.3.0)
  • Installing molecule-plugins (23.5.0)

Writing lock file
```