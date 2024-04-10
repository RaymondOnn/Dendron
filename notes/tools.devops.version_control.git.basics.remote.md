---
id: ujjh8o8vxw5s999fmcix7ru
title: remote
desc: ''
updated: 1702350258348
created: 1700466369823
---

>### Commands
>
> #### Interaction with remote repo
>
> -   `git remote`: Show remote servers
>   - `add origin URL`: Establish connection with remote repo in cloud
>   - `show origin`: show detailed configuration
> -   `git push origin`: push data from local repo to remote repo in cloud
>   - `--delete <NRACH_NAME`: delete branch in remote repo. Respective remote tracking branch will be removed as well
> -   `git pull <REMOTE> <BRANCH>`: get data from remote repo to local repo
> -   `git fetch <REMOTE>`: Update local copy of remote repo
> -   `git ls-remote`: See all branches in remote repo
> -   `git clone`:

### What is GitHub?

-   Git repository hosting provider

    -   A Git repository is basically a Git-managed project in simple terms.
    -   Cloud hosting provider that allows you to store your data in the Cloud,enabling us to collaborate on our projects with other people.

-   So with that capabilities, so these local capabilities of Git with managing that code efficiently, with managing the history and also with tracking changes, and the capabilities of GitHub with being able to host the code on a Cloud service and to enable that great collaboration capabilities, well, this is how Git and GitHub are connected.

#### Connecting Git and GitHub.

-   To bring this local information to the cloud we first have to establish a connection between these two different repositories.
-   For this, we run a git command locally: `git remote add origin URL`.
    -   `remote add`: add remote connection between the local repo and the remote repo.
    -   `origin`: alias of the URL of the remote repository that can be changed
    -   `URL`: unique URL to remote repository on a remote server.

### Tracking Branches

-   By default, branches in Git have nothing to do with each other.
-   A tracking branch is a Git branch that is used to build a connection between local and remote branches for pushing and pulling data.
-   When you tell a local branch to "track" a remote branch, you create a connection between these two branches. Your local branch now has a "counterpart" on the remote server.

#### Remote Tracking Branches

-   In essence, remote-tracking branches are references to the state of remote branches
-   A local branch is created automatically by Git and named as `remotes/<REMOTE NAME>/<BRANCH_NAME>` when we run `git push origin master`, storing a local copy of your remote repo's master branch.
-   It works like an image of the remote branch that helps us to assess the status of the local branch comparing to the remote.
-   Since it meant to just reflects the status of the remote branch, the remote-tracking branch cannot be edited locally.
-   Instead, Git automatically updates them when you communicate with the remote branch (e.g when you execute git fetch or git pull and so on).
-   As the Pro Git book suggests, “think of them as bookmarks, to remind you where the branches in your remote repositories were the last time you connected to them”.

#### When a remote-tracking branch is used ?

1. When we execute `git status`, is used to compare the local to the remote branch, based on the information that is container in the remote-tracking branch.
    - Since they represent the state of the remote repository the last time you connected to them, this information may not be up to date and might not reflect the current situation of the remote branch (i.e. The remote repo may be updated by other members of the project)
    ```bash
    $ git status
    # On branch branch1
    # Your branch is ahead of 'origin/branch1' by 1 commit.
    ```
2. When we do `git push` or `git pull` without specifying remote or branch, it will use the relation defined by the remote-tracking branch to select the appropriate remote and branch.

#### When are remote-tracking branches created ?

1. `git clone`

    - When you clone a remote repository all the remote branches are tracked and set as upstream branch for the new checked out master, then git-pull(1) will appropriately merge from the starting point branch.

    - Running `git branch -a`, you’ll see something like this:

        ```bash
        git branch -a
        >>> * master
        >>> remotes/origin/HEAD
        >>> remotes/origin/develop
        >>> remotes/origin/master
        ```

    - When we try to checkout one of these remote branches, git will create the necessery local branch.

2. `git push -u <remote> <branch>`
    - If we try to push a local branch to a remote repository without having a remote-tracking branch, we have to specify the remote name and the branch name it will be pushed to.
    - If the branch name does not exist in the remote repository, it will be created.
    - But a remote-tracking branch will not be created locally. And that’s because a temporary relation between the local and remote is assumed.
    - To create a remote-tracking branch during the pushing we need to add the “-u” option. This option is a shorthand for “–set-upstream”.

#### Updating the remote-tracking branch can be done by:

1. `git fetch`

    - it updates the remote-tracking branches by fetching branches and/or tags (collectively, “refs”) from one or more other repositories, along with the objects necessary to complete their histories.
    - it may be by more than one remotes, but, if not specified, only the default remote is used. You can see the default remote by using “git branch -vv”

2. `git pull`

    - it’s in fact a git fetch followed by a git merge. So, it will merge any pulled commits into the branch you are currently working.

3. `git push`
    - of course, an action that changes the remote repository could not leave untouched the remote-tracking branch

#### Remove a tracking relationship

```bash
git branch –unset-upstream
```

--- 
## Pull Requests

### Use Cases

- Pull requests invites reviewers to provide feedback before merging
- Allows contributing code to other repos you have no access to
  - Fork the repo -> make changes to it -> create a pull request for changes to be included
