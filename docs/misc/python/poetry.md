---
layout: default
title: Poetry
description: Poetry notes
has_toc: false
nav_order: 7
parent: Python
grand_parent: Miscellaneous
permalink: /misc/python-language/libraries/poetry
---

# Poetry
{: .no_toc }

<details closed markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

# Working with Poetry
Poetry is a tool for dependency management and packaging in Python. Here is how to use Poetry


## Creating a Poetry environment

```bash
poetry new rp-poetry --name realpoetry
```

Poetry will create a new folder named `rp-poetry/`, you will see this structure inside the folder...

```
rp-poetry/
│
├── rp_poetry/
│   └── __init__.py
│
├── tests/
│   ├── __init__.py
│   └── test_rp_poetry.py
│
├── README.rst
└── pyproject.toml
```

The `pyproject.toml` is most important here. This orchestrates your project and its dependencies

To create a Poetry environment from an existing project use

```bash
cd pre-existing-project
poetry init
```

---
A useful command to have all of your `.venv` saved in the project directory is `poetry config virtualenvs.in-project true`

{: .important}
If you already have created a project, you need to re-create the virtualenv to make it appear in the correct place...

```bash
poetry env list  # shows the name of the current environment
poetry env remove <current environment>
poetry install  # will create a new environment using your updated configuration
```

---


## Creating the virtual environment
We then need to create a virtual environment 

```bash
poetry env use python3 # My recommendation is to use pyenv
poetry shell # Actually creates the virtual environment
poetry env list
```



## Running our Project
We can then run our project in two ways through the virtual environment or by using a simple command

Activate the virtual environment with `poetry shell` and the run your project with `python projectName.py`, or we can run the following command in order to run within Poetry(allowing for dependincies to work) with

```bash
poetry run python projectName.py
```

## Specifying dependencies
Dependincies can be specified in a few ways

1. Using `poetry add requests`      
This will add a suitable version of the requests library __and install__ as a dependency in `pyproject.toml`

2. Using `poetry install`     
You can manually add dependencies in the `pyproject.toml` and the run `poetry install` in order to install it. One of two things will happen

### Installing without `poetry-lock`
If you have never run the command before and there is also no `poetry.lock` file present, Poetry simply resolves all dependencies listed in your `pyproject.toml` file and downloads the latest version of their files.

When Poetry has finished installing, it writes all the packages and their exact versions that it downloaded to the `poetry.lock` file, locking the project to those specific versions. You should commit the `poetry.lock` file to your project repo so that all people working on the project are locked to the same versions of dependencies

### Installing with `poetry-lock`
This brings us to the second scenario. If there is already a `poetry.lock` file as well as a `pyproject.toml` file when you run poetry install, it means either you ran the install command before, or someone else on the project ran the install command and committed the `poetry.lock` file to the project (which is good).

Either way, running install when a `poetry.lock` file is present resolves and installs all dependencies that you listed in `pyproject.toml`, but Poetry uses the exact versions listed in `poetry.lock` to ensure that the package versions are consistent for everyone working on your project. As a result you will have all dependencies requested by your `pyproject.toml` file, but they may not all be at the very latest available versions (some dependencies listed in the `poetry.lock` file may have released newer versions since the file was created). This is by design, it ensures that your project does not break because of unexpected changes in dependencies

In short `poetry add` immediately installs the dependency to the project, much like `pip install`, `poetry install` looks at the dependencies listed in either `poetry-lock` or `pyproject.toml` and installs the packages, whilst creating the virtual environment

{: .important}
I learned when using a poetry virtual environment in vscode you need to specify the path to that virtual environments interpreter in order for imports to resolve. Add the following to your interpreter path in vscode `~/ProjectName/.venv/bin/python`

