
Make the `rendered` branch the default on Github (or wherever you are hosting your repo).

##########

git checkout master
git checkout -b new-feature

# make changes
# commit
# PR, review, more changes, rebase, ...

# if accepted, merge feature branch into master (e.g. via fast-forward merge)

git checkout rendered

git rebase master

# manually re-run the changed notebooks

# TODO: get list of changed notebooks?

git commit -a --amend

# special care has to be taken when notebooks are removed

git checkout rendered
git rm the-deleted-notebook.ipynb
git commit --amend