---
id: h0vdg2ngxj0yslp6b3iasir
title: git
desc: ''
updated: 1702350247218
created: 1693376054163
---

## Resources

- [Intermediate GIT](https://www.youtube.com/watch?v=Uszj_k0DGsg&t=9s&ab_channel=freeCodeCamp.org)
- [Advanced GIT](https://www.youtube.com/watch?v=qsTthZi23VE&ab_channel=freeCodeCamp.org)
- [How GIT works under the hood](https://www.youtube.com/watch?v=RxHJdapz2p0&ab_channel=TechWithNikola)



### What is Git?

-   Git is a free and open source distributed version control system designed to handle everything from small to very large projects with speed and efficiency.
-   It allows you to manage your code history very efficient, and it also tracks code changes over time.
-   This means you don't only have all the different versions of your code, you can also compare versions and see what changed over time.
-   Git has one or two big disadvantages.
    -   Git is a local tool.
    -   It's installed on your machine and therefore, only you can use it as the user of this computer.
    -   the code you have managed in Git can only be accessed from that computer and not from anywhere else.

---
### Commands


-   `git bisect`

#### General

-   `git --version`: check if git is available
-   `git config <DETAIL>`: set up account identity
    -   `--global`: for global settings. Omit this to set for current repo only
    -   `alias.<ALIAS>`:
    -   `user.<PROPERTY>`:
-   `git init`: initialize the repository
-   `git log`: display commit history of current branch. Can get commit ID here.
    -   `--merge`: see commits involved in the merge
    -   `--graph`:
    -   `--decorate`
    -   `--oneline`
    -   `-S`: Search
-   `git reflog`: shows the logs of a specific reference (default: HEAD). See [Reference Logs](#reference-logs-git-reflog) below
    -   `show`: shows the logs of a specific reference (default: HEAD). Same as `git log -g --abbrev-commit --pretty=oneline.`
    -   `expire`: prune older reflog entries
        -   `-n, --dry-run`: do a dry run and inform what will be deleted
    -   `delete`: delete single entries from reflog history
    -   `exists`: check whether a ref (branch / tag) has reflog entries
-   `git status`: gives information about your working directory.
-   `git ls-files`: check which files are in staging
-   `git tag <TAG>`: Add tags to commits
    -   `-d`: delete tag
    -   `-a`: indicate tag add is an annotated tag
    -   `-m`: attach message to tag
-   `git show`: View expanded details on git objects (blobs, trees, tags, commmits)

#### Commit Creation & Access

-   `git add <FILE>`: add file to the staging area
    -   `-A`:
-   `git commit`: add it as a commit in our objects folder in a repository
    -   `-m`: add a commit message
    -   `-a`
    -   `--amend`: Modify the most recent commit
-   `git checkout <COMMIT_HASH / BRANCH_NAME / TAG>`: 'check out' a certain commit. Goes back to the latest commit of branch/file specified if COMMIT_HASH is unspecified

#### Branches

-   `git branch <BRANCH_NAME> <BRANCH_NAME>`: Creates new branch. List all available branches if BRANCH_NAME is unspecified. Coloured branch indicate the branch you are currently on.
    -   `-d`: delete only branches that have been merged
    -   `-D`: delete branches regardless merged or not
    -   `-vv`: list local tracking branches and their remotes
    -   `-a`: list all branches
    -   `-r`: show remote tracking branches
    -   `--track <BRANCH_NAME> ORIGIN/<BRANCH_NAME`: create local tracking branches
    -   `--delete`
    -   `--remotes`: working with remote tracking branches
-   `git checkout <BRANCH_NAME>`: Switch to another branch
    -   `-b <BRANCH_NAME`: create and switch to new branch
-   `git switch <BRANCH_NAME>`: Switch to another branch. from Git v2.23 onwards.
    -   `-c`: Create branch first before switching to it
-   `git merge <OTHER_BRANCH>`: merge specified branch with current branch
    -   `--squash`:
    -   `--no-ff`:
    -   `--abort`: abort merge
-   `git rebase <BRANCH_NAME>`
-   `git cherry-pick <COMMIT_HASH>`

#### Deleting Data

-   `git rm`: remove files from Git Repo
-   `git restore <FILE>`: discard changes in working directory until latest commit. From v2.2 onwards.
    -   `--staged`: discard staged changes in staging area
-   `git clean`: Discards untracked files in working directory
    -   `-d`: clean both files and directories
    -   `-f`: force, execute without confirmation
    -   `-n, --dry-run`: have Git do a dry run and tell you what it would have removed
-   `git reset HEAD-<N>`: go back N commits. both commit and staging are undone.
    -   `--soft`: Soft reset: Only the commit is undone, staging is unaffected
    -   `--hard`: Hard reset: Changes in commits, staging and working directory removed
        -   `origin/main`:
-   `git revert`:

#### `git stash`

-   Saves the uncommitted changes locally and revert back to last committed state
-   `git stash` options:
    -   `-u, --include-untracked`: stash untracked files
    -   `-a, --all`: stash untracked and ignored files
-   subcommands:
    -   `apply <STASH_INDEX>`: Apply the locally saved uncommiteed changes. The last 'stash' is applied if unspecified
    -   `list`: Lists all the stashes previously saved
    -   `push`: Same as `git stash`
        -   `-m`: attach message to stash
    -   `show <STASH_INDEX`: show the diff of a stash
        -   `-p, --patch`: View in more detail
    -   `drop <STASH_INDEX>`: Delete specified stash
    -   `pop <STASH_INDEX>`: Apply stash and remove stash from stash list
    -   `clear`: Clear stash list by removing all stashes
    -   `branch`:
    -   `create`:

---


### The `HEAD` and the detached `HEAD`

-   HEAD is a pointer to your current working commit. It also defines the current state of your project.
-   Usually, HEAD points to a branch.
-   When HEAD points to a branch, it points to the latest commit under that branch.
    -   Branches are, in essence, labels for commits.
    -   When you push to a branch, that label now points to the new commit.
    -   When you run `git checkout <BRANCH_NAME>`, Git checks out the latest commit the branch points to:
        ```raw
        HEAD --> BRANCH --> LATEST COMMIT
        ```

#### The detached `HEAD`

-   The HEAD is detached when the HEAD of the project is not pointing to a branch anymore. Instead, it’s pointing to a specific commit.
-   It happens when you run `git checkout <COMMIT_HASH>` rather than a branch name.
-   As a side-note, you can get hashes of your commits using `git log`

#### Reasons to detach the `HEAD`

-   There are a couple of reasons to check out a specific commit.
    -   One of them is to debug or fix issues. In that case, rolling back to a certain moment can be helpful.
    -   Another reason is to experiment with alternative solutions to any given problem. Since the state is simple to correct, that’s a great way to play around with your code.

#### How to fix

-   A detached HEAD is not an error but a feature in Git.
-   With that said, there are two ways to make things go back to “normal.”

    1. Check out a different branch
        - Git will discard your uncommitted code, and your HEAD will point to a branch again.
    2. Create a new branch and commit your code.

        - If you have some code changes you don’t want to lose, create a new branch and commit your code. Here are the Git commands to do so:

        ```bash
        git branch <branch-name> && git checkout <branch-name>
        ```

        - Once you do that, you can commit and push your code to the new branch. As long as you do that before returning to your regular branch, you’ll be fine.



---
## Working with Git Branches

### Core Concepts

- The **HEAD** Branch: The currently "active" / "checked out" branch

- Local vs Remote Branches
  -

---



