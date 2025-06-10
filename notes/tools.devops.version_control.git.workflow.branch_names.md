---
id: czllw005qd89yfci8ohz2fe
title: branch_names
desc: ''
updated: 1748788455057
created: 1748787393569
---

## [Git Branches Are More Than Just Names](https://medium.com/data-science-collective/the-day-i-broke-production-and-how-git-branch-naming-saved-my-team-after-that-33fa57f87871)

- Most developers see branch names as an afterthought.
- But in real teams, real deadlines, and real chaos, a good branch name can:
  - Tell you exactly what you’re working on
  - Tell teammates why you’re working on it
  - Let reviewers trust the intent of a PR
  - Help ops triage problems faster

### Our Branch Naming Scheme (And Why It Works)

- We adopted a simple rule: Every branch starts with a <type>/ prefix.
- Here’s our cheat sheet:

    | Prefix| Used For... |
    |--- |--- |
    | `feature/`| New features or enhancements    |
    | `bugfix/` | Fixes for bugs not yet in production   |
    | `hotfix/` | Emergency fixes to production   |
    | `release/`| Prepping for a version release   |
    | `test/`   | Experimental or throwaway code   |
    | `docs/`   | Documentation updates   |
    | `ci/`     | CI/CD pipeline changes   |
    | `chore/`  | Maintenance work (configs, refactors, etc)   |

- Some examples:
  - `feature/add-user-auth`
  - `bugfix/login-redirect`
  - `hotfix/null-check-payment`
  - `docs/update-api-readme`
- NOTE: If we had a Jira or GitHub issue ID, we added it too: `feature/2134-payment-gateway`

### Why This Simple Change Worked

- Faster Code Reviews
  - When I opened a PR from feature/add-payment-summary, reviewers instantly understood the scope.

- Quicker Triage in Emergencies
  - When production misbehaved, it was easy to see whether it came from a `hotfix/`, a `bugfix/`, or an unchecked `test/` branch that got merged by accident.

- Smoother DevOps & CI/CD
  - Our deployment pipeline started recognizing patterns:
    - Automatically pushing release/ branches to staging
    - Blocking test/ branches from production
- Better Git Hygiene
  - After a while, stale or unmerged branches were easy to archive or delete.
  - No more `test-new-final-3-please-work`.

#### Git Branch Naming: Best Practices That Stick

- Always use a prefix to signal intent (feature/, bugfix/, etc.)
- Use lowercase and dashes: ci/update-workflow, not CI_UpdateWorkflow
- Keep names short but clear: bugfix/404-error > fixstuff
- Use issue numbers if you track work: feature/912-dashboard-export
- Never merge test/ or chore/ into production by mistake
- Document your rules in the contribution guide
