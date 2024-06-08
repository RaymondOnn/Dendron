---
id: 723iopa0f3noovd3vkhy9hg
title: conventional_commits
desc: ''
updated: 1716819878731
created: 1716815133891
---
https://www.conventionalcommits.org/en/v1.0.0/

#### The Conventional Commits Specification
- a lightweight convention on top of commit messages. 
- provides an easy set of rules for creating an explicit commit history; which makes it easier to write automated tools on top of. 
- This convention dovetails with SemVer, by describing the features, fixes, and breaking changes made in commit messages.
- The commit message should be structured as follows:
    ``` cmdline
    <type>[optional scope]: <description>

    [optional body]

    [optional footer(s)]
    ```
##### Types: what did the commit do?
- The `<type>` field provides the context for the commit, communicating the intent of the change that was made. It tries to answer the question “what did the commit do?”
- The `<type>` field is an enumerated type that can be defined differently on each specific project. However there are some conventions, for example the `@commitlint/config-conventional` based on the Angular Convention, which defines the following types:
- Core
  - `feat`: The commit introduces a new feature or enhancement to the product or codebase.
  - `fix`: The commit addresses and resolves a bug, error, or issue in the codebase.
  - `style`: The commit deals with code style changes, such as formatting, indentation, or code comment adjustments, without affecting the code's functionality.
  - `refactor`: The commit involves code refactoring, which means restructuring or reorganizing the code without changing its external behavior
  - `build`: The commit introduces a change that affect the build system or external dependencies.
  - `perf`: The commit makes code changes aimed at improving performance or optimizing existing functionality.
- Supplemental (the app can still run locally without these)
  - `ci`: The commit involves changes to the continuous integration (CI) configuration or scripts used to automate build, testing, and deployment processes.
  - `docs`: The commit updates or adds documentation, such as README files, comments, or user guides, without affecting the code's functionality.
  - `test`: The commit includes changes related to testing, such as adding or modifying test cases, test suites, or testing infrastructure.
  - `chore`: The commit includes necessary technical tasks to take care of the product or repository, but it's not related to any specific feature or user story. These tasks are like routine maintenance, such as releasing the product or updating code for the repository.
  - `revert`: The commit undoes a previous commit, reverting the codebase to a previous state.


##### Scope: contextual identifier

- The scope provides a contextual identifier for the changes made in that particular commit. 
- It provides a way to categorize and organize commits, making it easier to track and manage changes in a project. 
- It can be thought of as a descriptor for the location, component, or module that is being modified or affected by the commit.
- For example, if a developer is working on a web application with multiple components such as the login system, user profile, and settings page, they can use scopes like login, profile or settings to indicate which part of the application the commit is related to. 
- This helps other team members and future contributors understand the purpose of the commit without having to inspect the actual changes right away.
- The standard says you should agree in your team what the scopes would be. Perhaps based on features, projects or directories.
    ``` shell
    Some possible examples.

    build(deps): upgrade packages
    style(deps): remove whitespace in requirements.txt
    fix(deps): correct typo in package.json package name
    ```

##### Description: a quick summary of what the commit intends to achieve
- The description provides a concise summary of what the commit accomplishes, making it easier for team members and contributors to understand the purpose of the commit at a glance.
- It should be written in the **present tense** and be clear and informative. 
- It should convey what the code changes do, rather than how they were implemented. 
- It is not necessary to provide every detail in the description; instead, focus on the high-level overview of the modifications.

##### Body: detailed description of what the commit does
- The body section is used to provide additional context, reasoning, or implementation details that might be helpful for other developers, reviewers, or future contributors to understand the commit thoroughly. 
- It is particularly useful when the changes introduced in the commit are complex or when there are specific design decisions that need to be explained.
- Optional, and not every commit requires it. Simple and straightforward changes might not need a detailed explanation beyond the description.


##### Footer: additional context or references
- Footers are used to include additional information related to the commit, such as references, links, or metadata. 
- It can include various types of content, but the most common use is to reference issues, pull requests, or other commits that are related to the current commit. 
- This referencing is done in a structured way, using the following syntax.: `<token>: <value>`
- One special footer that the conventional commit convention adds is the `BREAKING_CHANGE` footer. 
    - It is used to indicate that the commit introduces a change that is not backwards-compatible. 
    - The value of the footer is a description of the specific breaking change.

- GitHub also defines some keywords that can be used as footer tokens to link commits to issues or pull requests which will automate certain activities. Some examples are:

- `Fixes: <issue number>` or `Closes: <issue number>`: Links the commit to the specified issue and automatically closes it when the commit is merged into the default branch.
- `Refs: <pull request number>`: Links the commit to the specified pull request.
- `Co-authored-by: <name> [email]`: Credits other contributors who collaborated on the commit.


##### Examples: Commit in action
- All commit messages must include a type and a description, while the other components are optional. Here are some examples of how a commit can look like.

1. Simple commit message:
    ``` sh
    feat: add login functionality
    ```
2. Commit with a scope and a multiline body:
    ``` sh
    feat(profile): add user profile page

    This commit introduces a new user profile page that displays user 
    information, profile picture, and recent activities. 
    The profile information is fetched from the backend API.
    ```

1. Commit with a multiline body and a footer referencing an issue:

    ```
    fix: resolve login form validation 

    The login form validation was not properly handling special characters in the 
    password field, causing login failures for some users. 
    This commit addresses the issue by updating the validation regex pattern to 
    allow special characters. 
    
    Closes #123
    ```
2. Commit with a `BREAKING CHANGE` footer (without body):
    ``` sh
    feat!: upgrade authentication mechanism

    BREAKING CHANGE: Please update your code to use the new authentication flow.
    ```
3. Commit using the full specification:

    ``` sh
    refactor(user): improve code modularity and readability in users controller

    Extracted repeated code into separate functions for better maintainability 
    and readability. The code is now organized into smaller, reusable modules.

    Refs: #12
    Co-authored-by: JohnDoe
    ```