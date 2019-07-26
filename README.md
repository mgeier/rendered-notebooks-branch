Getting Started from Scratch
----------------------------

... assuming that you have no Jupyter notebooks in your repository yet
or all notebooks are "clean" (i.e. stored without outputs).

1. Make sure there are no un-committed local changes
1. Create a new branch called `dev` (starting at the `master` branch)

That's it!

Now you can continue with the section "Making a Change".

Getting Started with Pre-executed Notebooks
-------------------------------------------

1. Make sure there are no un-committed local changes
1. Create a new branch called `dev` (starting at the `master` branch) and
   switch to the new branch: `git checkout -b dev master`
1. Do the steps listed in the section "Cleaning a Whole Repository"
1. Push the changes from the `dev` branch to the server:
   `git push origin dev`
1. Switch back to the `master` branch (`git checkout master`)
   and make a backup branch:
   `git branch backup`
1. Reset the `master` branch to point to the same commit as `dev`:
   `git reset dev --hard`

1. Get the executed version of all notebooks:
   `git checkout backup .`
1. Create a commit with a commit message like "Execute notebooks".
   If you are satisfied with the result, you can push your changes to the server,
   but note that you have to use `--force`, because you changed the Git history:
   `git push --force`

Making a Change
---------------

    git checkout dev

    # create one or more commits with new notebooks or changes to existing ones

    # push "dev" branch to the server

    git checkout master

    git rebase -X ours dev

    # manually (re-)run the changed (and any new) notebooks

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
