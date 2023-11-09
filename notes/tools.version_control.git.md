---
id: h0vdg2ngxj0yslp6b3iasir
title: Git
desc: ''
updated: 1699360926504
created: 1693376054163
---

## Resources

- [Intermediate GIT](https://www.youtube.com/watch?v=Uszj_k0DGsg&t=9s&ab_channel=freeCodeCamp.org)
- [Advanced GIT](https://www.youtube.com/watch?v=qsTthZi23VE&ab_channel=freeCodeCamp.org)
- [How GIT works under the hood](https://www.youtube.com/watch?v=RxHJdapz2p0&ab_channel=TechWithNikola)

```bash
# Adding to staging
git add 
    -p      # add based on patch level
    <file_name>

git merge
git rebase
git pull
git stash apply
git cherry-pick
```

## What is Git?

---

## Working with Git Branches

### Core Concepts

- The **HEAD** Branch: The currently "active" / "checked out" branch

- Local vs Remote Branches
  -

---

## The Perfect Commit

1. Add the **right** changes
2. Compose a **good** commit message

### Adding the right changes

- The bigger a commit gets and the more topics that are mixed into the commit, the harder it gets to understand the commits in the future
- Git staging area allows you to select specific files, or parts of those files for the next commit
- To add parts of files to a commit, use the `-p` flag i.e. `git add -p <file_name>`. It allows to `git add` on a patch level basis

### The Perfect Commit Message

1. Subject: concise summary of what happened.  
   If you have trouble with this, you might have included too many topics within the same commit
2. Body: More detailed explanation  
   - indicated by adding an empty line after the subject
   - What is now different that before?
   - What is the reason for the change  
   - Is there anything to watch out for / anything particularly remarkable?

###### Example

```text
Add captcha for email signup

Email signups now require a captcha to be completed:
- signup.php uses our captcha library
- invalid signup attempts are now blocked
```  

## Pull Requests

### Use Cases

- Pull requests invites reviewers to provide feedback before merging
- Allows contributing code to other repos you have no access to
  - Fork the repo -> make changes to it -> create a pull request for changes to be included

## Merge Conflicts: When, What and How to Solve them

- Occurs when integrating commits from Different Sources
- Most of the time, Git will figure things out of its own
- However, when contradictory changes occur (for e.g. same line is changed in two different ways), Git cannot know what's correct
- Note that you can always undo and start fresh

  ``` bash
  git merge --abort
  git rebase --abort
  ```

- To solve a conflict, simply clean up the files

## Merge vs Rebase

### How Merge Works

- When Git performs a merge, it looks for three commits
  1. common ancestor commit
  2. last commit on branch A
  3. last commit on branch B
- A Fast-Foward Merge: Both Branches share the exact same history
- In a most realistic scenario
  - Most branches move forward differently
  - Here, Git will have to create a new commit containing the difference between the two branches called a merge commit
  -
