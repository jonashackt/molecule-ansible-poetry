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


## Add Molecule to the collection & run first test cycle

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


## Create local Molecule instance using Docker (via Delegated)

A great idea is to leverage containers for Molecule tests. In contrast to earlier Molecule versions, [Delegated is the default driver](https://ansible.readthedocs.io/projects/molecule/configuration/#driver) and it's the developers responsibility to implement it:

> Under this driver, it is the developers responsibility to implement the create and destroy playbooks.

To use Docker as a test instance in Molecule, we need to create a `requirements.yml` to hold the [community.docker](https://docs.ansible.com/ansible/latest/collections/community/docker/index.html) collection:

```yaml
collections:
  - community.docker
```

In order to have Molecule/Ansible recognize this dependency, we need to define it inside our [molecule.yml](collections/ansible_collections/jonashackt/moleculetest/extensions/molecule/default/molecule.yml):

```yaml
dependency:
  name: galaxy
  options:
    requirements-file: requirements.yml

platforms:
  - name: molecule-ubuntu
    image: ubuntu:23.04
```

Also we defined a Docker image to use inside the `platforms` section.

Now heading over to our [create.yml](collections/ansible_collections/jonashackt/moleculetest/extensions/molecule/default/create.yml) can create a Docker container instance (see also https://ansible.readthedocs.io/projects/molecule/docker/):

```yaml
---
- name: Create
  hosts: localhost
  gather_facts: false
  vars:
    molecule_inventory:
      all:
        hosts: {}
        molecule: {}
  tasks:
    - name: Create a container
      community.docker.docker_container:
        name: "{{ item.name }}"
        image: "{{ item.image }}"
        state: started
        command: sleep 1d
        log_driver: json-file
      register: container_result
      loop: "{{ molecule_yml.platforms }}"

    - name: Print some info
      ansible.builtin.debug:
        msg: "{{ container_result.results }}"

    - name: Add container to molecule_inventory
      vars:
        inventory_partial_yaml: |
          all:
            children:
              molecule:
                hosts:
                  "{{ item.name }}":
                    ansible_connection: community.docker.docker
      ansible.builtin.set_fact:
        molecule_inventory: >
          {{ molecule_inventory | combine(inventory_partial_yaml | from_yaml, recursive=true) }}
      loop: "{{ molecule_yml.platforms }}"
      loop_control:
        label: "{{ item.name }}" # display the container name only https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html#limiting-loop-output-with-label

    - name: Dump molecule_inventory
      ansible.builtin.copy:
        content: |
          {{ molecule_inventory | to_yaml }}
        dest: "{{ molecule_ephemeral_directory }}/inventory/molecule_inventory.yml"
        mode: "0600"

    - name: Refresh inventory to ensure new instances exist in inventory
      ansible.builtin.meta: refresh_inventory

    - name: Fail if molecule group is missing
      ansible.builtin.assert:
        that: "'molecule' in groups"
        fail_msg: |
          molecule group was not found inside inventory groups: {{ groups }}
      run_once: true # noqa: run-once[task]

# we want to avoid errors like "Failed to create temporary directory"
- name: Validate that inventory was refreshed
  hosts: molecule
  gather_facts: false
  tasks:
    - name: Check uname
      ansible.builtin.raw: uname -a
      register: check_uname_result
      changed_when: false

    - name: Display uname info
      ansible.builtin.debug:
        msg: "{{ check_uname_result.stdout }}"
```

We can try to create our first Molecule instance via:

```shell
molecule create
```

If you get the following error:

```shell
TASK [Create a container] ******************************************************
An exception occurred during task execution. To see the full traceback, use -vvv. The error was: ModuleNotFoundError: No module named 'requests'
failed: [localhost] (item={'image': 'ubuntu:23.04', 'name': 'molecule-ubuntu'}) => {"ansible_loop_var": "item", "changed": false, "item": {"image": "ubuntu:23.04", "name": "molecule-ubuntu"}, "msg": "Failed to import the required Python library (requests) on pikelinux's Python /home/jonashackt/.cache/pypoetry/virtualenvs/molecule-ansible-poetry-MP2KKXLA-py3.11/bin/python. Please read the module documentation and install it in the appropriate location. If the required library is installed, but Ansible is using the wrong Python interpreter, please consult the documentation on ansible_python_interpreter"}
```

we need to add the Python package `requests` to our [pyproject.toml](pyproject.toml) via `poetry add requests`.

If everything went fine, we should have a container running:

```shell
molecule create
WARNING  The scenario config file ('/home/jonashackt/dev/molecule-ansible-poetry/collections/ansible_collections/jonashackt/moleculetest/extensions/molecule/default/molecule.yml') has been modified since the scenario was created. If recent changes are important, reset the scenario with 'molecule destroy' to clean up created items or 'molecule reset' to clear current configuration.
INFO     default scenario test matrix: dependency, create, prepare
INFO     Performing prerun with role_name_check=0...
INFO     Running default > dependency
Starting galaxy collection install process
Nothing to do. All requested collections are already installed. If you want to reinstall them, consider using `--force`.
INFO     Dependency completed successfully.
WARNING  Skipping, missing the requirements file.
INFO     Running default > create

PLAY [Create] ******************************************************************

TASK [Create a container] ******************************************************
changed: [localhost] => (item={'image': 'ubuntu:23.04', 'name': 'molecule-ubuntu'})

TASK [Print some info] *********************************************************
ok: [localhost] => {
    "msg": [
        {
            "ansible_loop_var": "item",
            "changed": true,
            "container": {
                "AppArmorProfile": "docker-default",
                "Args": [
                    "1d"
                ],
                "Config": {
                    ...
                    "Image": "ubuntu:23.04",
                    "Labels": {
                        "org.opencontainers.image.ref.name": "ubuntu",
                        "org.opencontainers.image.version": "23.04"

                ...

TASK [Add container to molecule_inventory] *************************************
ok: [localhost] => (item=molecule-ubuntu)

TASK [Dump molecule_inventory] *************************************************
[WARNING]: Skipping unexpected key (molecule) in group (all), only "vars",
"children" and "hosts" are valid
changed: [localhost]

TASK [Refresh inventory to ensure new instances exist in inventory] ************

TASK [Fail if molecule group is missing] ***************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}

PLAY [Validate that inventory was refreshed] ***********************************

TASK [Check uname] *************************************************************
ok: [molecule-ubuntu]

TASK [Display uname info] ******************************************************
ok: [molecule-ubuntu] => {
    "msg": "Linux d0baf85f8169 6.5.9-1-MANJARO #1 SMP PREEMPT_DYNAMIC Wed Oct 25 13:14:27 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux\n"
}

PLAY RECAP *********************************************************************
localhost                  : ok=5    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
molecule-ubuntu            : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Running default > prepare
WARNING  Skipping, prepare playbook not configured.
```

You may have a look with `docker ps`, if the container is really running:

```shell
$ docker ps          
CONTAINER ID   IMAGE                  COMMAND                  CREATED         STATUS         PORTS                       NAMES
8768bfa8b2c8   ubuntu:23.04           "sleep 1d"               3 seconds ago   Up 2 seconds                               molecule-ubuntu
```

We also need to implement our [destroy.yml](collections/ansible_collections/jonashackt/moleculetest/extensions/molecule/default/destroy.yml):

```yaml
- name: Destroy molecule containers
  hosts: molecule
  gather_facts: false
  tasks:
    - name: Stop and remove container
      delegate_to: localhost
      community.docker.docker_container:
        name: "{{ inventory_hostname }}"
        state: absent
        auto_remove: true

- name: Remove dynamic molecule inventory
  hosts: localhost
  gather_facts: false
  tasks:
    - name: Remove dynamic inventory file
      ansible.builtin.file:
        path: "{{ molecule_ephemeral_directory }}/inventory/molecule_inventory.yml"
        state: absent
```

Running `molecule destroy` our Docker container should be removed. You can double check, if it got destroyed via `docker ps`.


## Run Molecule converge to our Docker container test instance

First we should enhance our [converge.yml](collections/ansible_collections/jonashackt/moleculetest/extensions/molecule/default/converge.yml) as it is stated in https://ansible.readthedocs.io/projects/molecule/docker/#converge-playbook to fail if molecule group is missing.

```yaml
---
- name: Fail if molecule group is missing
  hosts: localhost
  tasks:
    - name: Print some info
      ansible.builtin.debug:
        msg: "{{ groups }}"

    - name: Assert group existence
      ansible.builtin.assert:
        that: "'molecule' in groups"
        fail_msg: |
          molecule group was not found inside inventory groups: {{ groups }}

- name: Converge
  hosts: molecule
  gather_facts: false
  tasks:
    - name: Testing role install_python_pip
      ansible.builtin.include_role:
        name: jonashackt.moleculetest.install_python_pip
        tasks_from: main.yml
```

Also our `Converge` playbook should point to the `molecule` host, which represents our Docker container.

As we only have some debug printing inside our Ansible role under test right now, we should also enhance it inside [roles/install_python_pip/tasks/main.yml](collections/ansible_collections/jonashackt/moleculetest/roles/install_python_pip/tasks/main.yml):

```yaml
---
# tasks file for install_python_pip
- name: In order to get Ansible working, we need to install Python first (the snake bites itself)
  ansible.builtin.raw: apt update && apt install python3 -y

- name: Check Python was installed successfully
  ansible.builtin.raw: python3 --version
  register: python_result

- name: Print Python version installed
  ansible.builtin.debug:
    msg: "{{ python_result.stdout }}"

- name: Let's install the Python package manager pip
  ansible.builtin.apt:
    pkg:
      - python3-pip
    update_cache: yes
```

As we use a "naked" base image like ubuntu, we don't have python installed. So we need to install it before we can actually use our Ansible modules like `ansible.builtin.apt` as we're used to.