## Shrinking a Repository

## Preparation

### Determining Files / Folders to Remove

#### Listing All Files

```
git log --all --pretty=format: --name-only | sort -u
```

#### Identifying Large Files

```
while read -r largefile; do
    echo $largefile | awk '{printf "%s %s ", $1, $3 ; system("git rev-list --all --objects | grep " $1 " | cut -d \" \" -f 2-")}'
done <<< "$(git rev-list --all --objects | awk '{print $1}' | git cat-file --batch-check | sort -k3nr | head -n 20)"
```

Source: [How to find out which files take up the most space in git repo?](https://stackoverflow.com/a/32506324/)

#### Identifying File Renames

Checking for file renames.

```
git log --name-only --follow --all -- FILENAME \
  | grep '\.' | grep -v "^Author:" | grep -v "^ " | sort -u
```



## Removing Files / Folders from a Git Repository

Checkout all remote branches:

```
for remote in `git branch -r | grep -v /HEAD`; do git checkout --track $remote ; done
```

Remove unwanted files / folders from commit history - do not forget renamed files:

```
git filter-branch --force --index-filter \
  "git rm --quiet -r --cached --ignore-unmatch FILE_1 FILE_2 FOLDER_1/ FOLDER_2/" \
  --prune-empty --tag-name-filter cat -- --all
```

Remove the original refs backed up by git-filter-branch - stored in .git/refs/original:

```
git for-each-ref --format="%(refname)" refs/original/ | xargs -n 1 git update-ref -d
```

Expire all reference logs - stored in .git/logs:

```
git reflog expire --expire=now --all
```

Garbage collect - count objects before / after with `git count-objects -vH`:

```
git gc --prune=now --aggressive
```

**BEWARE** - Only push to the remote when you are sure everything is fine:

```
git push origin --force --all
git push origin --force --tags
```

#### References

- [Removing sensitive data from a repository](https://help.github.com/en/github/authenticating-to-github/removing-sensitive-data-from-a-repository)

- [Remove folder and its contents from git/GitHub's history](https://stackoverflow.com/a/32886427)

- [How to remove/delete a large file from commit history in Git repository?
](https://stackoverflow.com/a/2158271)


## Splitting a Folder into New Repository

Checkout all remote branches:

```
for remote in `git branch -r | grep -v /HEAD`; do git checkout --track $remote ; done
```

Apply filter:

```
git filter-branch --subdirectory-filter FOLDER-NAME \
  --prune-empty --tag-name-filter cat -- --all
```

Remove the original refs backed up by git-filter-branch - stored in .git/refs/original:

```
git for-each-ref --format="%(refname)" refs/original/ | xargs -n 1 git update-ref -d
```

Expire all reference logs - stored in .git/logs:

```
git reflog expire --expire=now --all
```

Garbage collect - check before / after with `git count-objects -vH`:

```
git gc --prune=now --aggressive
```

Set new remote - count objects before / after with `git remote -v`:

```
git remote set-url origin NEW-REPOSITORY-URL
```

**BEWARE** - Only push to the remote when you are sure everything is fine:

```
git push -u origin --all
git push origin --tags
```

#### References

- [Splitting a subfolder out into a new repository](https://help.github.com/en/github/using-git/splitting-a-subfolder-out-into-a-new-repository)



## Splitting a Repository into Multiple Repositories

### Creating the New Repository

#### Preparation

Create a clean project structure:

```
git clone git@github.com:USERNAME/PROJECT
cd PROJECT
```

Identify files to be deleted by listing all files and identifying renames as described earlier.

#### Remove Files / Folders from the Git Repository

Checkout all remote branches:

```
for remote in `git branch -r | grep -v /HEAD`; do git checkout --track $remote ; done
```

Remove unwanted files / folders from commit history - do not forget renamed files:

```
git filter-branch --force --index-filter \
  "git rm --quiet -r --cached --ignore-unmatch \
    FILE_1 \
    FILE_2 \
    FOLDER_1/ \
    FOLDER_2/ \
  " \
  --prune-empty --tag-name-filter cat -- --all
```

Remove the original refs backed up by git-filter-branch - stored in .git/refs/original:

```
git for-each-ref --format="%(refname)" refs/original/ | xargs -n 1 git update-ref -d
```

Expire all reference logs - stored in .git/logs:

```
git reflog expire --expire=now --all
```

Garbage collect - count objects before / after with `git count-objects -vH`:

```
git gc --prune=now --aggressive
```

#### Check the Result

Check what files are still in the Git repository:

```
git log --all --pretty=format: --name-only | sort -u
```

Note: This list will be used as the list of files to remove from the original project.

#### Change the Remote

Set new remote - checking before / after with `git remote -v`:

```
git remote -v
git remote set-url origin NEW-REPOSITORY-URL
git remote -v
```

#### Push to the Remote

**BEWARE** - Only push to the remote when you are sure everything is fine:

```
git push -u origin --force --all
git push origin --tags
```



### Pruning the Existing Repository

Use exactly the same process as above, except use the files in the other project as the list of files to be removed.



## Manual References

### filter-branch

Reference - [git-filter-branch](https://git-scm.com/docs/git-filter-branch)

Checking for file renames

```
git log --name-only --follow --all -- FILENAME
```

Filter all refs when calling `git filter-branch` (update all tags and all branches):

```
--tag-name-filter cat -- --all
```

Expire all reflogs:
```
git reflog expire --expire=now --all
```

Garbage collection:

```
git gc --prune=now
```

