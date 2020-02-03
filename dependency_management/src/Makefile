# This Makefile is based on the Makefile defined in the Python Best Practices repository:
# https://git.datapunt.amsterdam.nl/Datapunt/python-best-practices/blob/master/dependency_management/
#
# VERSION = 2020.01.29

PYTHON = python3

help:                                   ## Show this help.
	@fgrep -h "##" $(MAKEFILE_LIST) | fgrep -v fgrep | sed -e 's/\\$$//' | sed -e 's/##//'

pip-tools:
	pip install pip-tools

## Install requirements and sync venv with expected state as defined in requirements.txt
install: pip-tools
	pip-sync requirements.txt requirements_dev.txt

## Upgrade requirements (in requirements.in) to latest versions and compile requirements.txt
requirements: pip-tools
	pip-compile --upgrade --output-file requirements.txt requirements.in
	pip-compile --upgrade --output-file requirements_dev.txt requirements_dev.in

## Run 'requirements' and 'install' targets
upgrade: requirements install
