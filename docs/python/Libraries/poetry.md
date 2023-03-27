---
layout: default
title: Poetry
description: Poetry notes
has_toc: false
nav_order: 2
parent: Libraries
grand_parent: Python
permalink: /python-language/libraries/asyncio
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

We then need to create a virtual environment 

```bash
poetry env use python3
poetry env list
```