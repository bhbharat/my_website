---
title: "Github"
linkTitle: "Git remove large files and amend"
weight: 2
description: >-
     Git remove large files and amend.
---

## Git remove large files and amend 


```python
#first copy the data from somewhere
git filter-branch --force --index-filter "git rm --cached --ignore-unmatch News/input/vendor_data/*" HEAD
git push origin --force --all
git rm -r --cached . # if gitignore not working
git submodule add --branch <branch_name> <repository_url> <path_in_your_repo>
git submodule status

# Amend to last commit
git add .
git commit --amend --no-edit
git push origin --force --all

## only checkout specific folder file
git checkout -b releases1
git rm -r --cached .
git checkout main .gitignore dist README.md CHANGELOG.md
git add .gitignore dist README.md CHANGELOG.md
git commit -m "Added new branch"
git push origin releases1
## git stash and drop

git branch -D releases1
git push origin --delete releases1

git tag
git tag -d v0.1.0
git push origin --delete v0.1.0
git tag v0.1.0
git push origin --tags

```

## Other Github Commands

```python
#!pip install -q seedir
import os
print(os.getcwd()+'\n')
import seedir as sd
sd.seedir('.',style='emoji',exclude_folders=[f for f in os.listdir('.') if f[0] in ['.','_','~']],depthlimit=1)

# Check the details of local git repository

print('\033[1m' +"Git Remote Location --->" +'\033[0m')
#!git remote -v  # don't print if pushing to same git location (token issue)
print('\033[1m' +"Status --->" +'\033[0m')
!git status
print('\033[1m' +"Branches --->" +'\033[0m')
!git branch
print('\033[1m' +"Git Log --->" +'\033[0m')
!git log -n 5 --oneline
print('\033[1m' +"Gitignore file --->" +'\033[0m')
!cat .gitignore
print('\033[1m' +"Git lfs tracked files --->" +'\033[0m')
!git lfs ls-files

# Create .gitignore and lfs tracking

%%writefile .gitignore
.*
~*
*.log
Github.ipynb

!git rm -r --cached . # if gitignore not working

!sudo apt-get install git-lfs
!git lfs track input/*
!git lfs track output/*
!git add -f .gitattributes

# Check the Git difference if untracked files

!git diff commit1 commit2 file
!git diff branch1..branch2

!git diff HEAD ./Github.ipynb

# Create and merge new branch

!git checkout -b text_classification
!git checkout master
!git merge newbranch

## Initialize Git and add details

# get tokens from here https://github.com/settings/tokens
!git init

!git config --global user.email "mebharat123@gmail.com"
!git config --global user.name "Bharat"

username='bharatb964'
repository='Kaggle'
git_token='paste token from above link' # make sure Github.ipynb is in .gitignore
github = 'github.com'

!git remote set-url https://{git_token}@{github}/{username}/{repository}

br="\""+ input("Name of the branch to push: ")+"\""
!git add .
!git commit -m "First Commit"
!git push origin $br


## Clone

!git clone https://{git_token}@{github}/{username}/{repository}
%cd {repository}
!git checkout branch name

## Pull

!git stash
br="\""+ input("Name of the branch to pull: ")+"\""
!git pull origin $br

## Push

message="\""+ input("Type the commit message: ")+"\""
br="\""+ input("Name of the branch to push: ")+"\""
!git add .
!git commit -m $message
!git push origin $br

```
