---
id: 33mic50d4fjfnhzb81x6d6w
title: Branching_strategies
desc: ''
updated: 1693388831171
created: 1693388757023
---

## Resources
- [Intermediate GIT](https://www.youtube.com/watch?v=Uszj_k0DGsg&t=9s&ab_channel=freeCodeCamp.org)

## Branching Strategies

### A Written Convention

- Agree on a Branching Workflow in your team
  - Git allows you to create branches but it doesn't tell you how to use them!
  - You need a written best practice of how work is ideally structured in your team to avoid mistakes and collisions
  - It highly depends on your team / team size, on your project, and how you handle releases
  - It helps to onboard new team members i.e. how the team works here

### Integrating Changes & Structuring Releases (Two Extreme Examples)

1. Mainline Development i.e. Always Integrating
   - Here, Code is quickly integrating into your production code
   - Few Branches
   - Relatively small commits
   - High-Quality Testing & QA Standards
2. State, Release and Feature Branches
   - Here, Branches Enhance Structure and Workflows
   - Different types of branches
   - Fulfils different types of jobs

### Main Types of Branches

1. Long Running
   - for e.g. main/master branch
   - exist through the complete lifetime of the project
   - There are also integration branches (for staging, development) will represents stages in the dev life cycle
   - Common convention: No direct comments, but rather through integratiion via `merge` or `rebase`
     - To avoid untested and un-reviewed code to production. Instead, code should passed through tests/reviews at different stages before arriving at production
     - Another reason: Release bundling and scheduling. Direct commits makes tracking what's released difficult
1. Short Lived
   - created for certain purposes e.g. new features, bug fixes, refactorings, experiments
   - generally will be deleted after integration (merge/rebase)
   - typically short lived branches will be based on a long running branch

### Two Examples of Branching Strategies

1. GitHub Flow
   - very simple, very lean: only one long-running branch (for e.g. `main`) + feature branches
   - Work is done in a separate branch 
   - main -> features, releases
2. GitFlow
   - long-running: `main` + `develop`
   - short-lived: features, releases, hot fixes
   - main -> develop -> features, releases