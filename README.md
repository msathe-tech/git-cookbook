#Git cookbook

## Git basics
Stage and commit
```shell script
git add s1
git commit -m "message"
```
Every commit has a SHA1 hash.

Add and commit files
```shell script
```
Check logs
`git log`

## Git branching and merging
HEAD normally points to a branch.
By default it is a `master` branch.

### show graph of commits and branches
`git log --all -decorate --oneline --graph`

### branch
* Commit: 1
* Commit: 2 <- (master <- HEAD)

New branch

`git branch branchA`

`git branch branchB`

* Commit: 2 <- (master <- HEAD), branchA, branchB

show all branches
`git branch`

move HEAD to a branch
`git checkout branchA`
`git add; git commit`

* Commit: 3 <- (branchA <- HEAD)
* Commit: 2 <- master, branchB

move HEAD to another branch
`git checkout branchB`
* Commit: 3 <- branchA
* Commit: 2 <- (branchB <- HEAD), master

commit some changes
`git add; git commit`
* Commit: 3 <- branchA
* Commit: 2 <- master
* Commit: 4 <- (branchB <- HEAD)

## Fast forward merge
When there is a direct path between master and a branch fast forward merge is possible.
In this case the master just moves to the (latest) commit of the branch.

`git checkout master`
* Commit: 3 <- branchA
* Commit: 2 <- (master <- HEAD)
* Commit: 4 <- branchB

### difference in two branches
`git diff master..branchA`

### merge
`git merge branchA`
* Commit: 3 <- (master <- HEAD), branchA
* Commit: 2
* Commit: 4 <- branchB
Now we don't need `branchA` because it has been merged with master, and both point to same commit.

Check which commits are already merged with branch we are on.
`git branch --merged`

`git branch -d branchA`
* Commit: 3 <- (master <- HEAD)
* Commit: 2
* Commit: 4 <- branchB

## Three way merge
When there is no direct path between two branches.
In this example, `master` is now on commit 3, and `branchB` is on commit 4.
Both have common base, commit 2. But `branchB` doesn't have `master` branch's commit as base.
In this case we will need three way merge.

`git merge branchB`
This results in a separate commit.
* Commit: 3
* Commit: 2
* Commit: 4
* Commit: 5 <- (master <- HEAD), branchB
When there is no conflict you just continue the merge.
If there is conflict you need to resolve the conflict.

Now you can delete `branchB`.
`git branch -d branchB`
* Commit: 3
* Commit: 2
* Commit: 4
* Commit: 5 <- (master <- HEAD)

## Merging with conflict
`git checkout -b dev`
* Commit: 3
* Commit: 2
* Commit: 4
* Commit: 5 <- (dev <- HEAD), master
`git add; git commit`

`git checkout master`
Make changes to file on same lines as done in `dev` branch causing conflict.
Or delete a line that `dev` inherited from base.
`git add; git commit`
* Commit: 3
* Commit: 2
* Commit: 4
* Commit: 5 <- dev
* Commit: 6 <- (master <- HEAD)
Now, `dev` is not in direct path of `master`.
And the changes done to `master` are conflicting.
That means Git can't automatically resolve it.
A human being has to step in to merge the files.
So this will be a three way merge with conflicts .

dev......master

.......\\..../

.......base

`git merge dev`
...-> You hve unmerged paths.
A file with conflict is merged, adding changes from both branches.
And a human being is supposed to resolve and then commit.
The merged file will have Git markers such as
```
<<<<<<< HEAD
  some text
======
  some other text
>>>>>>> dev
```
You delete the Git markers and keep the text you want to finally go in merge.
`git add; git commit`
This creates merge commit.
* Commit: 3
* Commit: 2
* Commit: 4
* Commit: 5 <- dev
* Commit: 6
* Commit: 7 <- (master <- HEAD)

### Abort merge
`git merge --abort`


## Detached HEAD
Usually head points to a branch which points to a commit.
But if HEAD points directly to a commit it is called detached HEAD.
`git status`
`git checkout <commit-hash>`

You can handle it by simply
`git checkout <branch-name>`

Or you can create a branch on commit where HEAD is pointing.
`git checkout <commit-hash>`
`git branch stage`
This will make `stage` and HEAD point to same commit. Still, the HEAD
is not yet pointing to a branch. So this is still a Detached HEAD.
`git checkout stage`
Now HEAD points to same commit as `stage`.

## Stash
When you are in a branch and change a file, you have a file in your working tree.
Now if you want to switch to another branch then you either `commit` the change or `stash` it.
`git stash save "message"`
`git stash list -p`

To bring in the changes for a stash
`git stash apply`
This will get the changes of the last stash.
This will keep the stash still in the list.
To remove the stash
`git stash pop`

To get changes of a particular stash
`git stash list -p`
`git stash apply <stash-id>`
















