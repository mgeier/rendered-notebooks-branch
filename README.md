Getting Started
---------------

1. Create a new branch called `dev`
1. Make sure all notebooks are "clean", i.e. their cell outputs are not stored.
   See the section about "Cleaning" below for how to automate this.
1. Commit the cleaned notebooks to the `dev` branch, push to server
1. Reset the `master` branch to point to the same commit as `dev`
1. Switch to `master` branch
1. Execute all your notebooks.
   See the section about "Executing" below for how to automate this.
1. Create a commit with a commit message like "Execute notebooks",
   force-push to server

Making a Change
---------------

    git checkout dev

    # create one or more commits (e.g. by fast-forward merging a feature branch)

    # push "dev" branch to the server

    git checkout master

    git rebase -X ours dev

    # manually re-run the changed notebooks

    # to get list of changed notebooks:

    git diff --name-only dev $(git merge-base dev origin/master)

    # when all changed notebooks have been executed:

    git commit -a --amend

Special care has to be taken before rebasing when notebooks are removed:

    git checkout master
    git rm the-deleted-notebook.ipynb
    git commit --amend

In the end, the changes to `master` have to be force-pushed:

    git push --force


Executing All Notebooks
-----------------------

    python3 -m nbconvert --execute --inplace *.ipynb **/*.ipynb

To disable the timeout, add `--ExecutePreprocessor.timeout=-1` to the command.
This should actually be the default, but it's not,
see https://github.com/jupyter/nbconvert/issues/791.

Please note the two *globbing* patterns used here.
The second pattern (`**/*.ipynb`) is collecting all the notebooks recursively,
but it doesn't include the files in the current directory.
That's what the first pattern (`*.ipynb`) is used for.
If you don't have notebooks in the main directory, you should omit this pattern.

Cleaning All Notebooks
----------------------

    #python3 -m nbconvert --clear-output *.ipynb **/*.ipynb

`--clear-output` doesn't work: https://github.com/jupyter/nbconvert/issues/822

    python3 -m nbconvert --ClearOutputPreprocessor.enabled=True --inplace *.ipynb **/*.ipynb

Cleaning a Whole Repository
---------------------------

Make sure you don't have any local changes.

Create a new `dev` branch:

    git checkout -b dev master

Clean the whole Git history of the new branch:

    git filter-branch --tree-filter "python3 -m nbconvert --ClearOutputPreprocessor.enabled=True --inplace *.ipynb **/*.ipynb"

If there are some commits without Jupyter notebook in them, you might want to
extend the command a bit (to ignore any errors):

    git filter-branch --tree-filter "python3 -m nbconvert --ClearOutputPreprocessor.enabled=True --inplace *.ipynb **/*.ipynb || true"
