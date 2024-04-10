---
id: tyea49snlt3h1kvjcazg9ft
title: Basics
desc: ''
updated: 1702350151274
created: 1699597350676
---

```raw
A---B---C---F  (target_branch with merge commit)
         \     /
          D---E  (source_branch)
```




### Branches

-   Branches allow us to create a completely separate working directory i.e. an entire copy of the current state of your master branch.
-   As more files changes are added at different commits, all these commits are stored inside the master branch.
-   This means you have an entire history of the project that you created inside this master branch.
    -   Conceptually, you can think about the master branch as kind of a folder inside your Git project that holds all the different versions of your project (so that you can always jump back to previous versions)
-   Suppose the need to implement a new feature into your website.
-   While it is possible to commit additional changes to this master branch, it's not advisable to do so because working on the existing master version of your website, so on the live version of the website,

    -   Might end up break this project if you accidentally introduce some bugs or things like that.
    -   Better to have a separate copy of the existing version and work on that new feature there
    -   Once this new feature is finished, to implement that feature into the master branch. This is called a so-called merge.

-   The big advantage here is that developers can work independently from each other, work on dedicated features, and then bring back new features to the actual running version of the website in the master branch.
