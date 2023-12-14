---
id: 3ps0v6rr4v95teki39c0wo7
title: workflows
desc: ''
updated: 1702350500700
created: 1702350398677
---


### Workflows, Jobs and Steps

-   We start with a code repository
-   Workflows
    -   added to the code repository
    -   `.yaml` file stored in `.github/workflows`
    -   Triggered by Events (`on`)
        -   Events are changes in/to the repo e.g. PR created, contributor joined, issued created, PR merged
    -   Each workflow contains one or more jobs
-   Jobs (`jobs`)
    -   require a runner (`runs-on`)
        -   Runners are servers that execute the jobs
        -   There are pre-defined runner (installed with different OS)
    -   Can be conditional
    -   Run in parallel (default) or sequential (`needs`)
    -   Each job contains one or more steps which
-   Steps (`steps`)
    -   define the actual things that will be done
    -   execute a shell script (`run`) or an Action (`uses`)
    -   execute in order
    -   can be conditional
    -   can use custom or third-party actions

```yaml
name: Deploy Project
on: [push, workflow_dispatch]
jobs:
    test:
        runs-on: ubuntu-latest
        steps:
            - name: Get Code
                uses: actions/checkout@v3
            - name: Install NodeJS
                uses: actions/setup-node@v3
                with:
                    node-version: 18
            - name: Install dependencies
                run: npm ci
            - name: Run tests
                run: npm test
    deploy:
        needs: test
        runs-on: ubuntu-latest
        steps:
            - name: Get Code
                uses: actions/checkout@v3
            - name: Install NodeJS
                uses: actions/setup-node@v3
                with:
                    node-version: 18
            - name: Install dependencies
                run: npm ci
            - name: Build project
                run: npm run build
            - name: Build project
                run: echo 'Deploying...'
```

#### Cancelling Workflows

-   By default, if one or more steps fail, the job fail and the workflow gets cancelled
-   Cancelling workflow can also be done manually. There is a button available for this.

#### Skipping Workflows

-   Except for `push` and `pull_request`, all matching events will start a workflow by default
-   To skip a workflow, one of the special tag is needed in the commit message

```bash
  git commit -m 'some message [skip actions]'
```