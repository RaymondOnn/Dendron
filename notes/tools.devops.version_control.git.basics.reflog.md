---
id: uas3agnsllrxf3g4j9f08ah
title: reflog
desc: ''
updated: 1700930968341
created: 1700930950826
---


### Reference Logs: `git reflog`

-   Git uses a system called reference log, or simply "reflog," to keep track of updates to the branches' tips to the local repo.
    -   A reference, often known as a "ref," is a pointer to a commit or branch that many Git commands accept as a parameter.
    -   `git checkout`, `git reset`, and `git merge` are examples of some common git commands that accept refs as parameters.
-   Whenever the branch tip is updated for any reason (by switching branches, pulling new changes, rewriting history, or adding new commits), a new record will be added
-   By default, reflogs keep track of each HEAD position throughout the last 90 days.
-   Furthermore, the reflog history is exclusive to the repository and is not accessible remotely.
-   Apart from branch tip reflogs, there is a separate reflog for the Git stash.
-   Reflogs are stored in specific directories under the local repository's .git directory. Can be found at
    -   `.git/logs/refs/heads/.`
    -   `.git/logs/HEAD`
    -   `.git/logs/refs/stash` if the git stash has been used on the repository.

#### References to Reflog

-   By default, git reflog outputs the HEAD ref's reflog.
-   The symbol HEAD denotes the currently active branch.
-   There are also reflogs available for other refs. The syntax used to access a git ref is name@{qualifier} , for example - otherbranch@{0}. In addition to HEAD refs, other branches, tags, remotes, and Git stash can be referenced as well.
-   To see the complete reflog of all refs, you can execute:
    ```bash
    git reflog show --all
    ```
-   Other than the ordered indexes, each reflog entry has a timestamp attached to it that can also be leveraged as a qualifier token for the Git ref pointer syntax.
-   This is what enables the filtering of these reflog entries by time.
    -   Examples of commonly used time qualifiers include:
        -   @{0}
        -   @{6.minutes.ago}
        -   @{2.hour.ago}
        -   @{3.day.ago}
        -   @{5.weeks.ago}
        -   @{8.years.ago}
        -   @{today}
        -   @{2022-01-23.08:30:00}
        -   @{1.day.10.hours.ago}
-   These time qualifiers can be combined (e.g. 1.day.3.hours.ago). Plural forms of these time qualifiers are also accepted (e.g. 5.minutes.ago).
-   They can be used along with the git reflog command as follows:

    ```bash
    git reflog show develop@{3.days.ago}
    ```

#### Git reflog as your safety net
- Git reflog can be used as a safety net during development as you can't lose data from your repo once it's been committed if you understand the concept of reflog correctly. 
- You can use the reflog to see where you were before and `git reset --hard` to get back to that ref to restore your prior state if you unintentionally reset to an older commit, rebase incorrectly, or perform any other operation that visibly "removes" commits.