# molecule-ansible-poetry
[![delegated-docker-ubuntu](https://github.com/jonashackt/molecule-ansible-poetry/workflows/delegated-docker-ubuntu/badge.svg)](https://github.com/jonashackt/molecule-ansible-poetry/actions)
[![docker-ubuntu](https://github.com/jonashackt/molecule-ansible-poetry/workflows/docker-ubuntu/badge.svg)](https://github.com/jonashackt/molecule-ansible-poetry/actions)
[![License](http://img.shields.io/:license-mit-blue.svg)](https://github.com/jonashackt/molecule-ansible-poetry/blob/master/LICENSE)
[![renovateenabled](https://img.shields.io/badge/renovate-enabled-yellow)](https://renovatebot.com)

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



# Create an Ansible Collection, Role and Playbook via Poetry and Molecule

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

> The command `ansible-galaxy role init` creates a strange directory `tests` inside the new role's directory. After doing some research in [the official role docs about the structure of roles](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html#role-directory-structure) and the [official ansible-galaxy docs about the `ansible-galaxy role init` command](https://docs.ansible.com/ansible/latest/galaxy/dev_guide.html#creating-roles-for-galaxy) it became clear, that this is a non-standard folder which can be ignored. See also [this so q&a](https://stackoverflow.com/questions/70898834/which-tools-use-the-tests-directory-in-an-ansible-role-by-default), which makes it clear that the `tests` folder [was originally meant for Travis CI only](https://github.com/ansible/ansible/pull/13489).

> The correct directory for `tests` should be directly in the root of the collection `collections/ansible_collections/jonashackt/moleculetest`, where also the tool `ansible-test` put's it's outputs. For example run a `ansible-test sanity` and the dir should be generated correctly (don't forget to add the `collections/ansible_collections/jonashackt/moleculetest/tests/output` to your [.gitignore](.gitignore)).


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
  become: false
  roles:
    - role: jonashackt.moleculetest.install_python_pip
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
  become: false
  roles:
    - role: jonashackt.moleculetest.install_python_pip
```

Also our `Converge` playbook should point to the `molecule` host, which represents our Docker container.



As we only have some debug printing inside our Ansible role under test right now, we should also enhance it inside [roles/install_python_pip/tasks/main.yml](collections/ansible_collections/jonashackt/moleculetest/roles/install_python_pip/tasks/main.yml):

```yaml
---
# tasks file for install_python_pip
- name: Let's install the Python package manager pip
  ansible.builtin.apt:
    pkg:
      - python3-pip
    update_cache: yes
```

As we use a "naked" base image like ubuntu, we don't have python installed. So we need to install it before we can actually use our Ansible modules like `ansible.builtin.apt` as we're used to. Therefore we can leverage a new file [`prepare.yml`](collections/ansible_collections/jonashackt/moleculetest/extensions/molecule/default/prepare.yml):

```yaml
---
- name: Prepare
  hosts: all
  gather_facts: false
  tasks:
    - name: In order to get Ansible working, we need to install Python first (the snake bites itself)
      ansible.builtin.raw: apt update && apt install python3 -y
      register: install_python
      changed_when: install_python.stdout is search("The following NEW packages will be installed:") # implemeted for idempotence tests, see https://datamattsson.tumblr.com/post/186523898221/idempotence-and-changedwhen-with-ansible

    - name: Make sure python3 is installed
      ansible.builtin.package:
        name: python3
        state: present
```

We also need to take the `idempotence` check into account using `changed_when: install_python.stdout is search("The following NEW packages will be installed:")`, otherwise `molecule test` will fail. See https://datamattsson.tumblr.com/post/186523898221/idempotence-and-changedwhen-with-ansible


## Verify if our Ansible role is working using Testinfra

As with the default driver, [the default verifier for Molecule is not also Ansible](https://ansible.readthedocs.io/projects/molecule/configuration/#advanced-testing). But as I know Testinfra, I wanted to have a look, if I can use it with Molecule v6 as expected.

Testinfra is based on pytest and is also referred to in the docs: https://ansible.readthedocs.io/projects/molecule/configuration/#testinfra

In order to be able to use testinfra, we need to install it into our Python environment via `poetry add testinfra`.

Now let's configure testinfra inside our [molecule.yml](collections/ansible_collections/jonashackt/moleculetest/extensions/molecule/default/molecule.yml):

```yaml
...
verifier:
  name: testinfra
  directory: ../tests/
  env:
    # get rid of the DeprecationWarning messages of third-party libs,
    # see https://docs.pytest.org/en/latest/warnings.html#deprecationwarning-and-pendingdeprecationwarning
    PYTHONWARNINGS: "ignore:.*U.*mode is deprecated:DeprecationWarning"
  options:
    # show which tests where executed in test output
    v: 1
```

Also we need to add a new directory `tests` in our `extensions/molecule` directory and create a file [test_pip.py](collections/ansible_collections/jonashackt/moleculetest/extensions/molecule/tests/test_pip.py):

```python
import os

import testinfra.utils.ansible_runner

testinfra_hosts = testinfra.utils.ansible_runner.AnsibleRunner(
    os.environ['MOLECULE_INVENTORY_FILE']).get_hosts('all')


def test_is_pip_installed(host):
    package_pip = host.package('python3-pip')

    assert package_pip.is_installed
```

Here we levarage the power of testinfra using the class [`testinfra.utils.ansible_runner.AnsibleRunner`](https://github.com/pytest-dev/pytest-testinfra/blob/main/testinfra/utils/ansible_runner.py) and can simply check via `host.package().is_installed`, if our package `python3-pip` has been installed.

Let's run our testinfra tests using Molecule via `molecule verify`:

```shell
molecule verify                                                                        1 ✘  molecule-ansible-poetry-MP2KKXLA-py3.11  
WARNING  The scenario config file ('/home/jonashackt/dev/molecule-ansible-poetry/collections/ansible_collections/jonashackt/moleculetest/extensions/molecule/default/molecule.yml') has been modified since the scenario was created. If recent changes are important, reset the scenario with 'molecule destroy' to clean up created items or 'molecule reset' to clear current configuration.
INFO     default scenario test matrix: verify
INFO     Performing prerun with role_name_check=0...
INFO     Running default > verify
INFO     Executing Testinfra tests found in /home/jonashackt/dev/molecule-ansible-poetry/collections/ansible_collections/jonashackt/moleculetest/extensions/molecule/default/../tests//...
/home/jonashackt/.cache/pypoetry/virtualenvs/molecule-ansible-poetry-MP2KKXLA-py3.11/lib/python3.11/site-packages/_testinfra_renamed.py:5: DeprecationWarning: testinfra package has been renamed to pytest-testinfra. Please `pip install pytest-testinfra` and `pip uninstall testinfra` and update your package requirements to avoid this message
  warnings.warn((
============================= test session starts ==============================
platform linux -- Python 3.11.5, pytest-7.4.3, pluggy-1.3.0 -- /home/jonashackt/.cache/pypoetry/virtualenvs/molecule-ansible-poetry-MP2KKXLA-py3.11/bin/python
rootdir: /home/jonashackt
plugins: testinfra-9.0.0, testinfra-6.0.0
collecting ... collected 1 item

../tests/test_pip.py::test_is_pip_installed[ansible:/molecule-ubuntu] PASSED [100%]

============================== 1 passed in 0.57s ===============================
INFO     Verifier completed successfully.
```


## Add GitHub Actions workflow

Let's create a simple GitHub Actions workflow [.github/workflows/molecule-test.yml](.github/workflows/molecule-test.yml):

```yaml
on: [push]

jobs:
  run-molecule:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v4.1.1

    - name: Cache Poetry virtualenvs incl. all pip packages (see https://python-poetry.org/docs/configuration/#listing-the-current-configuration)
      uses: actions/cache@v3.3.2
      with:
        path: ~/.cache/pypoetry/virtualenvs
        key: ${{ runner.os }}-poetry-${{ hashFiles('**/Pipfile.lock') }}
        restore-keys: |
          ${{ runner.os }}-poetry-

    - uses: actions/setup-python@v4.7.1
      with:
        python-version: '3.11'

    - name: Install required dependencies with Poetry
      run: |
        docker info
        pipx install poetry
        poetry install

    - name: Molecule testing GHA-locally with Docker
      run: |
        poetry run molecule test
      working-directory: ./collections/ansible_collections/jonashackt/moleculetest/extensions
```


# Using Molecule Plugins

Earlier version of Molecule featured more sophisticated Drivers than just Delegated, where the Molecule user needs to implement the `create.yml` and `destroy.yml` for themselves. Although Delegated is the default Ansible Driver, there are still so called Molecule plugins https://github.com/ansible-community/molecule-plugins that feature more high level Drivers like Docker, EC2, Vagrant, GCE, Azure etc.

But be beware that this is an Ansible Community project, so the community is expected to maintain the drivers. The docs tell us in "Molecule Plugins in 2023" about the movement of maintained drivers to the new GitHub repo (and the archiving of the unmaintained ones): https://ansible.readthedocs.io/projects/devtools/stats/molecule-plugins/


## Using the docker Driver from the Molecule Plugins

In order to see the differences, let's have a look into the Docker driver. [The docs state](https://github.com/ansible-community/molecule-plugins):

> Installing molecule-plugins does not install dependencies specific to each, plugin. To install these you need to install the extras for each plugin, like pip3 install 'molecule-plugins[docker]'.

> Before installing these plugins be sure that you uninstall their old standalone packages, like pip3 uninstall molecule-azure. If you fail to do so, you will end-up with a broken setup, as multiple plugins will have the same entry points, registered.

Using Poetry we can install the new Molecule Docker driver using:

```shell
poetry add 'molecule-plugins[docker]==23.5.0'
```

As you can see we can also install Poetry packages [with pinned exact versions](https://nadeauinnovations.com/post/2023/01/pinning-package-versions-with-poetry-a-short-guide-to-optimizing-python-development-and-projects/).

To have a look onto all installed versions, just run `poetry show`.

Now we should be able to init our new Molecule scenario using `molecult init --scenario-name`:

```shell
molecule init scenario --driver-name docker docker-ubuntu
```

Besides the `molecule.yml` inside the new directory `docker-ubuntu` also other already known files are created: `create.yml` and `destroy.yml`. But with the docker driver there is no need for them, since the Driver handles creation and destruction of our Molecule test instances.

So we could easily leave the `molecule init` command, create the `docker-ubuntu` directory ourselves and copy the [`molecule.yml`](collections/ansible_collections/jonashackt/moleculetest/extensions/molecule/docker-ubuntu-2304/molecule.yml):

```yaml
---
driver:
  name: docker

platforms:
  - name: docker-ubuntu
    image: ubuntu:23.04
    pre_build_image: false

verifier:
  name: testinfra
  directory: ../tests/
  env:
    # get rid of the DeprecationWarning messages of third-party libs,
    # see https://docs.pytest.org/en/latest/warnings.html#deprecationwarning-and-pendingdeprecationwarning
    PYTHONWARNINGS: "ignore:.*U.*mode is deprecated:DeprecationWarning"
  options:
    # show which tests where executed in test output
    v: 1
```

Compared to the Delegated variant, we don't need to define the `dependency` keyword, but instead use the `driver` keyword to configure the Docker driver.

We still need a [`converge.yml`](collections/ansible_collections/jonashackt/moleculetest/extensions/molecule/docker-ubuntu-2304/converge.yml), which triggers our role execution:

```yaml
---
- name: Converge
  hosts: all
  gather_facts: false
  become: false
  roles:
    - role: jonashackt.moleculetest.install_python_pip
```

Now that's already everything we need to using the docker driver!

Let's test our new Scenario. First let's create our Molecule Docker container:

```shell
molecule create --scenario-name docker-ubuntu
```

And then fire our role onto it:

```shell
molecule converge --scenario-name docker-ubuntu
```

Works really nice! And compared to the Delegated driver we have much lesser configuration files:

```shell
$ tree
.
└── molecule
    ├── default
    │   ├── converge.yml
    │   ├── create.yml
    │   ├── destroy.yml
    │   ├── molecule.yml
    │   ├── prepare.yml
    │   └── requirements.yml
    ├── docker-ubuntu
    │   ├── converge.yml
    │   └── molecule.yml
```

I also created another GitHub Actions workflow for our new scenario and driver.