---
id: ywglohxkjwnd01t2cruql5h
title: commit
desc: ''
updated: 1702349526302
created: 1702349501854
---

## Commits
### How Git Works

-   Git is a system made of commit objects and pointers.
-   Commits are connected through the parent-child relationship.
-   Each commit points to a previous commit as its parent.
    -   Initial commit i.e. snapshot stores a copy of initial files in the working directory
    -   With consequent commits, Git compares the previous commit with the new commit we want to make and only stores the changes
-   By default, all commits are stored in the master branch

### Commits: Tracking Changes

-   The Working Directory is the folder containing the actual project files, that is managed by Git.
-   Whenever we turn a normal project folder into a Working Directory, we automatically create another hidden folder, the .git folder, the Git Repository.
    -   The Repository is the area where all the version management happens
    -   The Repository contains two different areas.
        -   The Staging Area, which is basically an Index File.
        -   The Objects Folder, which contains the different commits, the different snapshots.
-   With every commit, we first add the changes made to this Staging Area, to be part of the next commit. You can think about the Staging Area as a draft area.
-   And once all your changes are added, which should be part of this next commit, then you create this commit, you commit your changes, and the file changes are tracked in the next commit in the next snapshot.

> #### The Perfect Commit
>
> 1.  Add the **right** changes
> 2.  Compose a **good** commit message
>
> ##### Adding the right changes
>
> -   The bigger a commit gets and the more topics that are mixed into the commit, the harder it gets to understand the commits in the future
> -   Git staging area allows you to select specific files, or parts of those files for the next commit
> -   To add parts of files to a commit, use the `-p` flag i.e. `git add -p <file_name>`. It allows to `git add` on a patch level basis
>
> ##### The Perfect Commit Message
>
> 1.  Subject: concise summary of what happened.  
>     If you have trouble with this, you might have included too many topics within the same commit
> 2.  Body: More detailed explanation
>     -   indicated by adding an empty line after the subject
>     -   What is now different that before?
>     -   What is the reason for the change
>     -   Is there anything to watch out for / anything particularly remarkable?
>
>   ```text
>   Add captcha for email signup
>
>   Email signups now require a captcha to be completed:
>   - signup.php uses our captcha library
>   - invalid signup attempts are now blocked
>   ```

### The `.gitignore` file

-   In real world project, you might have files which you might need or want to have in your project folder but not as part of your version management.. i.e. log files, config files
-   For such cases, we have the option to tell Git that it should ignore some files or folders by creating a `.gitignore` file.
-   Typically created manually in your root folder of your project.
-   In the file, we specify which data, which files, which directors should be ignored by Git.
-   Must be added and also committed, that's important.

    ```raw
    *.log       # ignore all files with .log extension
    !test.log   # ignore all files except test.log

    web-app/*   # ignore all files in web-app folder

    ```