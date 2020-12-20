# Development Guide

...

## Initial setup


### Initialize the project

On the local machine install dependencies for prettier and black. These tools
are needed to autoformat the changed code before committing.
Create a python virtual environment with `python3 -m venv .` .

```bash
# Setup to use a virtual python environment
python3 -m venv .
source bin/activate;

# Install black to format python code when developing
pip install black;
```

## Regenerating with cookiecutter

Get the latest changes from the cookiecutter that initially generated the
project
([jkenlooper/cookiecutter-website-infrastructure](https://github.com/jkenlooper/cookiecutter-website-infrastructure))
by uninstalling the app, running the cookiecutter in a different directory, and
then rsync the changed files back in. Use `git` to then patch files as needed.

```bash
now=$(date --iso-8601 --utc)
# In the project directory of {{ cookiecutter.repo_name }}.
cd ../;
mkdir {{ cookiecutter.repo_name }}--${now};
cd {{ cookiecutter.repo_name }}--${now};

# Regenerate using last entered config from initial project.
cookiecutter \
  --config-file ../{{ cookiecutter.repo_name }}/.cookiecutter-regen-config.yaml \
  gh:jkenlooper/cookiecutter-website-infrastructure renow=${now};

# Back to parent directory
cd ../;

# Create backup tar of project directory before removing all untracked files.
tar --auto-compress --create --file {{ cookiecutter.repo_name }}-${now}.bak.tar.gz {{ cookiecutter.repo_name }};
cd {{ cookiecutter.repo_name }};
git clean -fdx --exclude=.env;
cd ../;

# Use rsync to copy over the generated project
# ({{ cookiecutter.repo_name }}--${now}/{{ cookiecutter.repo_name }})
# files to the initial project.
# This will delete all files from the initial project.
rsync -a \
  --itemize-changes \
  --delete \
  --exclude=.git/ \
  --exclude=.env \
  {{ cookiecutter.repo_name }}--${now}/{{ cookiecutter.repo_name }}/ \
  {{ cookiecutter.repo_name }}

# Remove the generated project after it has been rsynced.
rm -rf {{ cookiecutter.repo_name }}--${now};

# Patch files (git add) and drop changes (git checkout) as needed.
# Refer to the backup {{ cookiecutter.repo_name }}-${now}.bak.tar.gz
# if a file or directory that was not tracked by git is missing.
cd {{ cookiecutter.repo_name }};
git add -i .;
git checkout -p .;
```
