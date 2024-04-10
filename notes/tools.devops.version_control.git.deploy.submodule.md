---
id: agu1hxem3o0n85lunpvnljo
title: submodule
desc: ''
updated: 1710414734549
created: 1710411662542
---

> ### Reference
>
> #### Submodule: `git submodule`
>
> -   `docker version`: check your version and that docker is working
> -   `docker info`: shows most config values for the docker engine
> -   `docker login <SERVER_URL>`: Defaults to logging in DockerHub, but can overrider by adding server url
> -   `docker logout <SERVER_URL>`: Always logout from shared machines/servers when done to protect your acct
> -   `ps aux`: show me all running processes

### What is a Submodule?
- A Git submodule is a repository that is nested in another repository. 
- This allows you to manage independent projects in one parent repository.

#### Adding a Submodule
- Syntax:
    ``` sh
    # <REPO_URL> is the URL of the repository to be added as a submodule.
    git submodule add <REPO_URL>    
    
    # Add a repo as a submodule named my-submodule
    # After navigating the the folder that will contain the submodule
    git submodule add https://github.com/user/repo.git my-submodule
    ```
- clones the submodule and creates a special file called `.gitmodules`. 
    - This file tracks the mapping between the project URL and the local subdirectory you've pulled it into.
- NOTE: After adding the submodule, need to commit this change to the main repository:
    ``` sh
    git commit -m "Added submodule"
    ```
- You can now use the contents from the submodule as if it were a part of the main repo


### Initializing and Updating a Submodule

- To initialize the submodule and **check out the latest commit** of the submodule.
    ``` sh
    git submodule update
    ```

Incorporating Changes in a Submodule
It’s crucial to incorporate changes in the submodule’s repository when there are any.


`git submodule update --remote`:  Update all submodules to their latest commit on their main branch into the parent repository.

Caveats
There are some caveats and things to keep in mind when using Git submodules:

Updating: Submodules do not automatically stay in sync with the remote repository. You need to go into each submodule and pull the changes manually.
Committing: If you make changes inside a submodule, you need to commit those changes within the submodule first, then go to the main repository and commit the change to the submodule. This is because the main repository tracks the submodule’s commit.
Cloning: When you clone a repository, the submodules will not be cloned along with it. You need to use git submodule update --init --recursive or git clone --recursive <repository> to clone the repository and its submodules.
Pulling: Similarly, when you pull your main repository, it will not pull the new commits of populated submodules. Use the --recurse-submodules option with the git pull to update all the submodules as well.
Cognitive Load: When working with submodules, it’s not always clear whether you’re working in the context of your project or one of its dependencies. This can increase the cognitive load and complexity of your project.