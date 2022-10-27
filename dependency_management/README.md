
# Dependency Management

The goal of this topic to get all Python projects in line and to handle dependency management in a predictive and robust manner.

## Setup

1. Makefile: Entrypoint containing the same base targets in each project
2. requirements.in: Base requirements file
3. requirements_dev.in: Base requirements file for development requirements
4. requirements.txt: Compiled requirements with pinned versions. Includes sub-requirements.
5. requirements_dev.txt: Compiled development requirements with pinned versions. Includes sub-requirements.

### Makefile

Contains the following targets at minimum:

| Target            |  Description                             |
|:------------------|:------------------------------------------|
| `install`         | Install all requirements as defined in requirements_dev.txt and requirements.txt and synchronize the current virtualenv with the expected state (will uninstall dependencies if not defined in requirements).|
| `requirements`    | Updates the requirements to their latest version and compile into requirements.txt. |
| `upgrade`         | Run `requirements` and `install` targets. |

### requirements.in

This is the source file of the requirements. It defines all requirements needed to run the project. Note that it does not contain any development requirements, these are defined in requirements_dev.txt.

Most dependencies in this file will not be pinned, meaning we'll update them everytime the `requirements` or `upgrade` make targets are run. If a specific version is needed, pin it here and add a comment explaining why the version is pinned.

Example:
```
certifi
django==2.2.*   # Using 2.2 for now because library x does not work with newer versions. See ticket githubissue.com
```

### requirements_dev.txt

This file contains all requirements that are needed during development, but not in production (e.g. django-extensions, django-debug-toolbar, etc). Versions can be pinned if necessary, but can also be open.

### requirements.txt

This is the generated file containing all dependencies (including sub-dependencies). Each dependency will be pinned, and comments exist explaining what library installed the dependency.

Never change this file by hand, because it will be overridden. If a specific version of a library is needed, pin its version in requirements.in, or requirements_dev.txt.

Example:
```
attrs==19.3.0             # via pytest
certifi==2019.11.28       # via requests, sentry-sdk
chardet==3.0.4
coverage==5.0.3
django-filter==2.2.0
django==2.2.8
```

# Getting started

To start a new project, copy all files in the src/ folder, and start adding dependencies to requirements.in or requirements_dev. After creating and entering a virtualenv, run `make install` to install all packages.
