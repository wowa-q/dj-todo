# [Monorepo Basics](https://gafni.dev/blog/cracking-the-python-monorepo/) 

## Tools 
The monorepo is built with [uv](https://docs.astral.sh/uv/getting-started/) and [dagger](https://docs.dagger.io/ci/quickstart/daggerize/)

> UV-Git: [uv](https://github.com/astral-sh/uv)

## Setup monorepo

```
mkdir uv-dagger-dream
cd uv-dagger-dream
uv init
uv add --group dev ruff pyright             (adds dependencies to the root project)
mkdir projects
uv init --package --lib projects/lib-one
uv init --package --lib projects/lib-two
uv lock
```
This results in:
```
.
├── README.md
├── projects
│   ├── lib-one
│   │   ├── pyproject.toml
│   │   └── src
│   │       └── lib_one
│   │           └── __init__.py
│   └── lib-two
│       ├── pyproject.toml
│       └── src
│           └── lib_two
│               └── __init__.py
├── pyproject.toml
└── uv.lock
```

To create dependency between the projects:
```
uv add --package lib-two lib-one
```
The `--package` flag tells _uv_ to execute the command in the context of the _lib-two_ package. The lib-one package is added as a dependency to lib-two’s pyproject.toml and the root uv.lock file is updated automatically.

After that we have **monorepo** - two different packages in one repo.

