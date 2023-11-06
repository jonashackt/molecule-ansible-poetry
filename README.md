# molecule-ansible-poetry
Example project showing Ansible collections, Molecule delegated tests and Poetry as dependency management


## What it's about

After some time of absence I wanted to have a brief look into Ansible again: I had a deep look into it but didn't had the time to look into Collections, Ansible 2.10+ and the new Molecule with only Delegated drivers. Also I heard [that Poetry for dependency management is maybe slightly better then my beloved Pipenv](https://plainenglish.io/blog/poetry-a-better-version-of-python-pipenv) and [that it's kind of a successor to it](https://www.warp.dev/blog/prose-about-poetry):

> In 2022, with the standardization of the pyproject.toml manifest, poetry stands out as the most robust and easy-to-use package manager, even for a newcomer to the python universe. In all aspects, it is better than pipenv with a developer experience that I find more complete and enjoyable (see [this post](https://dev.to/farcellier/i-migrate-to-poetry-in-2023-am-i-right--115))

And [it's supported by Renovate](https://docs.renovatebot.com/modules/manager/poetry/). So let's give these tools a short look.

Since I'm currently in the process of switching my go-to environment to Linux / Manjaro, I will also add the needed commands here.


## Install Python, pip, pipx and Poetry

First we need to be sure to have a current version of Python installed. On a Mac a great idea is to use homebrew (`brew install python`), on Manjaro Linux it's already present:

```
$ python --version
Python 3.11.5
```

On a Mac `pip` should be installed via the homebrew package also, on Manjaro I needed to leverage `pamac` (which should apply to other Linux OSses also, using a different package manager):

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

## Initialize Poetry: create a pyproject.toml

Using [Poetry](https://python-poetry.org/docs/basic-usage/) is straight forward. For example there's `poetry new` to create a new Poetry project. But as we want to use `ansible-galaxy` to create a new Ansible collection and role, we'll leverage the `poetry init` command. This will only create a `pyproject.toml` file in an interactive fashion:

```
$ poetry init

This command will guide you through creating your pyproject.toml config.

Package name [molecule-ansible-poetry]:  
Version [0.1.0]:  
Description []:  Example project showing Ansible collections, Molecule delegated tests and Poetry as dependency management
Author [Jonas Hecht <jonas.hecht@codecentric.de>, n to skip]:  
License []:  
Compatible Python versions [^3.11]:  

Would you like to define your main dependencies interactively? (yes/no) [yes] no
Would you like to define your development dependencies interactively? (yes/no) [yes] no
Generated file

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


Do you confirm generation? (yes/no) [yes] yes

```

The `pyproject.toml` is where everything Poetry related comes together - especially Python dependencies for our Ansible collection/roles.


## Add Ansible & Molecule via Poetry

Now we can [issue our Molecule installation](https://ansible.readthedocs.io/projects/molecule/installation/) leverageing Poetry via `poetry add ansible-core molecule`:

```
$ poetry add ansible-core molecule
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

If we only use the Delegated driver, that's it for now.

If we want to use other drivers, we need to install them separately by using `poetry add "molecule-plugins[docker]"`.

> Molecule uses the \"delegated\" driver by default. Other drivers can be installed separately from PyPI, most of them being included in molecule-plugins package: https://github.com/ansible-community/molecule-plugins

```
$ poetry add "molecule-plugins[docker]"
```


## Create Ansible collection & role via ansible-galaxy

But running `ansible` or other commands doesn't seem to work right now. Why is that?

In order to be able to use the installed packages, we need to spin up a Python virtual environment. That's handled by Poetry for us and we only need to run:

```
poetry shell
```

Now our executable should be available. Just try to run `ansible` or `ansible-galaxy`. They should work now as expected!

The [crucial point here](https://ansible.readthedocs.io/projects/molecule/getting-started/) is this:

> One of the recommended ways to create a collection is to place it under a `collections/ansible_collections` directory. If you don't put your collection into a directory named `ansible_collections`, molecule won't be able to find your role.

If we don't create the directory accordingly, we'll run into errors like this later:

```shell
TASK [Testing role install_python_pip] *****************************************
ERROR! the role 'jonashackt.moleculetest.install_python_pip' was not found in /home/jonashackt/dev/molecule-ansible-poetry/jonashackt/moleculetest/extensions/molecule/default/roles:/home/jonashackt/dev/molecule-ansible-poetry/jonashackt/moleculetest/extensions/roles:/home/jonashackt/dev/molecule-ansible-poetry/jonashackt/moleculetest/extensions/molecule/default

The error appears to be in '/home/jonashackt/dev/molecule-ansible-poetry/jonashackt/moleculetest/extensions/molecule/default/converge.yml': line 10, column 15, but may
be elsewhere in the file depending on the exact syntax problem.

The offending line appears to be:

      ansible.builtin.include_role:
        name: jonashackt.moleculetest.install_python_pip
              ^ here

PLAY RECAP *********************************************************************
```

So __BEFORE__ proceeding any further, we need to create a directory `collections/ansible_collections` ([`mkdir -p` will help to create the subdirectory from one command](https://stackoverflow.com/a/1731775/4964553)):

```shell
$ mkdir -p collections/ansible_collections
```

We have everything to create our first Ansible collection via `ansible-galaxy collection init my.collection.name`. Just be sure to `cd` into the `collections/ansible_collections` dir before:

```shell
$ cd collections/ansible_collections
$ ansible-galaxy collection init jonashackt.moleculetest
```

After this we can cd into `roles` dir of our newly created collection and create a new Ansible role inside via `ansible-galaxy role init`

```shell
$ cd jonashackt/moleculetest/roles
$ ansible-galaxy role init install_python_pip
```


## Add Molecule to the collection

Now we can finally add Molecule to the game.

Therefore create a new directory `extensions` in the root of the collection:

```shell
$ mkdir extensions && cd extensions
```

Inside the `extensions` directory we can now create our Molecule scenario using `molecule init scenario`:

```shell
$ molecule init scenario
INFO     Initializing new scenario default...

PLAY [Create a new molecule scenario] ******************************************

TASK [Check if destination folder exists] **************************************
changed: [localhost]

TASK [Check if destination folder is empty] ************************************
ok: [localhost]

TASK [Fail if destination folder is not empty] *********************************
skipping: [localhost]

TASK [Expand templates] ********************************************************
changed: [localhost] => (item=molecule/default/create.yml)
changed: [localhost] => (item=molecule/default/converge.yml)
changed: [localhost] => (item=molecule/default/destroy.yml)
changed: [localhost] => (item=molecule/default/molecule.yml)

PLAY RECAP *********************************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Initialized scenario in /home/jonashackt/dev/molecule-ansible-poetry/jonashackt/moleculetest/extensions/molecule/default successfully.
```

[The docs](https://ansible.readthedocs.io/projects/molecule/getting-started/#molecule-scenarios) have a great starting point what Molecule scenarios are:

> Scenarios are the starting point for a lot of powerful functionality that Molecule offers. For now, we can think of a scenario as a test suite for roles or playbooks within a collection. You can have as many scenarios as you like and Molecule will run one after the other.

Another way of thinking about the created playbooks `create.yml`, `converge.yml`, `molecule.yml` & `destroy.yml` is to use the analogy of the BDD-style inspired way of structuring tests using `Given`, `When`, `Then`. See also this blog post for more info https://www.codecentric.de/wissens-hub/blog/test-driven-infrastructure-ansible-molecule 

In order to tell Molecule that it should execute and test our previously generated role, we need to change the [converge.yml](jonashackt/moleculetest/extensions/molecule/default/converge.yml):

```yaml
---
- name: Converge
  hosts: all
  gather_facts: false
  tasks:
    - name: Testing role install_python_pip
      ansible.builtin.include_role:
        name: jonashackt.moleculetest.install_python_pip
        tasks_from: main.yml
```

Now we are already able to run a full Molecule test cycle via `molecule test`:

> It can be particularly useful to pass the `--destroy=never` flag when invoking molecule test so that you can tell Molecule to run the full sequence but not destroy the instance if one step fails.

```shell
$ molecule test
```



