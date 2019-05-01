1. Create a new branch called `dev`
1. Make sure all notebooks are "clean", i.e. their cell outputs are not stored.
   See the section about "Cleaning" below for how to automate this.
1. Commit the cleaned notebooks to the `dev` branch, push to server
1. Reset the `master` branch to point to the same commit as `dev`
1. Switch to `master` branch
1. Execute all your notebooks.  See the section about "Executing" below for how to
   automate this.
1. Create a commit with a commit message like "Execute notebooks", push to server

---------------

    git checkout master

    # create one or more commits (e.g. by fast-forward merging a feature branch)

    git checkout rendered

    git rebase -X ours master

    # manually re-run the changed notebooks (TODO: get list of changed notebooks?)

    git commit -a --amend

Special care has to be taken when notebooks are removed:

    git checkout rendered
    git rm the-deleted-notebook.ipynb
    git commit --amend


Executing All Notebooks
-----------------------

    python3 -m nbconvert --execute --inplace **/*.ipynb

To disable the timeout, add `--ExecutePreprocessor.timeout=-1` to the command.
This should actually be the default, but it's not,
see https://github.com/jupyter/nbconvert/issues/791.

Cleaning All Notebooks
----------------------

    #python3 -m nbconvert --clear-output **/*.ipynb

`--clear-output` doesn't work: https://github.com/jupyter/nbconvert/issues/822

    python3 -m nbconvert --ClearOutputPreprocessor.enabled=True --inplace **/*.ipynb

Cleaning a Whole Repository
---------------------------

Make sure you don't have any local changes.

Create a new `dev` branch:

    git checkout -b dev master

Clean the whole Git history of the new branch:

    git filter-branch --tree-filter "python3 -m nbconvert --ClearOutputPreprocessor.enabled=True --inplace **/*.ipynb"

If there are some commits without Jupyter notebook in them, you might want to
extend the command a bit (to ignore any errors):

    git filter-branch --tree-filter "python3 -m nbconvert --ClearOutputPreprocessor.enabled=True --inplace **/*.ipynb || true"
