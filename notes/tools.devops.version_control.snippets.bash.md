---
id: 5slfybr35n1irhetab925ff
title: bash
desc: ''
updated: 1749985929818
created: 1749984953182
---

### Automating Branch Creation with a Bash Script

- Creating a new branch from the command line is simple, but you can automate this further with a script that checks out the branch and pushes it to the remote.

- This automates the common task of creating and pushing new branches.
  - The script prompts the user for a branch name.
  - It would then create the branch locally (git checkout -b).
  - Finally, it pushes the new branch to the remote repository (git push -u origin).

``` bash
#!/bin/bash

# Script: create_branch.sh
echo "Enter the new branch name:"
read branch_name
git checkout -b $branch_name 
git push -u origin $branch_name
echo "Branch $branch_name created and pushed to remote."
```

### Automating Pre-Commit Checks

- The pre-commit hook ensures code quality by running automated checks before a commit is made.
- This script checks staged Python files for formatting issues using Black.
  - If unformatted code is detected, it blocks the commit and displays an error message.

``` bash
#!/bin/bash

# Check for unformatted Python code using Black
files=$(git diff --cached --name-only --diff-filter=ACM | grep -E '\.py$')
if [ -n "$files" ]; then
  unformatted=$(black --check $files 2>&1)
  if [ $? -ne 0 ]; then
    echo "ERROR: Unformatted Python code detected!"
    echo "$unformatted"
    exit 1
  fi
fi

exit 0
```

- To use it:
  1. Save the script in .git/hooks/pre-commit.
  2. Make it executable: chmod +x .git/hooks/pre-commit.

### Running Tests Automatically Before Pushing

- The pre-push hook can be used to run tests before allowing a push to the remote repository.

``` bash
#!/bin/bash

echo "Running tests before pushing..."
npm test
if [ $? -ne 0 ]; then
  echo "ERROR: Tests failed! Push aborted."
  exit 1
fi

exit 0
```

- This hook runs npm test before pushing changes.
- If tests fail, the push is aborted, ensuring only stable code is pushed.

### Preventing Secrets from Being Committed
- Detect and block sensitive data like API keys or passwords before committing.

``` bash
#!/bin/bash

# Regex to detect potential secrets (e.g., API keys)
secret_regex="(AWS_SECRET|API_KEY|PASSWORD)="

files=$(git diff --cached --name-only)
for file in $files; do
  if grep -qE "$secret_regex" "$file"; then
    echo "ERROR: Potential secret detected in $file!"
    exit 1
  fi
done

exit 0
```
- This hook scans staged files for sensitive information using regular expressions.
- Blocks the commit if secrets are detected.

### Automatically Updating Dependencies
Use a post-checkout hook to automatically install dependencies when switching branches.
``` bash
#!/bin/bash

if [ -f "package.json" ]; then
  echo "Installing dependencies..."
  npm install
fi

exit 0
- Checks for package.json in the checked-out branch and installs dependencies automatically.

6. Generating Changelogs Automatically
Automate changelog generation with a script and post-merge hook.
``` bash
#!/bin/bash

echo "Generating changelog..."
git log --pretty=format:"%h %s" --no-merges > CHANGELOG.md
echo "Changelog updated!"

exit 0
- Updates the CHANGELOG.md file with commit messages after merging.

7. Automating Code Deployment
The post-receive hook can automate deployments to a server when pushing to a specific branch.
``` bash
#!/bin/bash

read branch
if [ "$branch" == "refs/heads/staging" ]; then
  echo "Deploying to staging server..."
  ssh user@staging-server "cd /path/to/project && git pull && ./deploy.sh"
fi

exit 0
- Monitors pushes to the staging branch and triggers a deployment script.

8. Automating Pull Requests with Git Aliases
Simplify pull request creation with a Git alias.
Git Alias for PR Creation example — add the following to your .gitconfig file:

[alias]
  pr = "!f() { branch=$(git rev-parse --abbrev-ref HEAD); open https://github.com/your-repo/pulls?q=is%3Aopen+is%3Apr+head%3A$branch; }; f"
- This alias opens the pull request page in your browser for the current branch.
- Replace ‘your-repo’ with your actual repository URL.

9. Automating Merge Conflict Resolution
Automating merge conflicts involves identifying files with conflicts and notifying the developer.
``` bash
#!/bin/bash

# Script: auto_merge.sh
git fetch origin
git merge origin/main
if git diff --name-only --diff-filter=U; then
  echo "Merge conflicts detected. Resolve them manually."
else
  echo "Merge successful!"
fi
- The script fetches the latest updates from the remote main branch and attempts to merge it into your current branch.
- If there are any merge conflicts, it will notify the user to resolve them manually.

10. Git Commit Message Validator Hook
A commit message should follow a specific format to maintain a clean history. You can automate this with a Git hook.

Create a file .git/hooks/commit-msg with the following content:
``` bash
#!/bin/bash

# Commit Message Validator
COMMIT_MSG=$(cat "$1")
if ! [[ "$COMMIT_MSG" =~ ^(feat|fix|docs|style|refactor|test|chore)\: ]]; then
  echo "Commit message must start with 'feat:', 'fix:', etc."
  exit 1
fi
- This pre-commit hook script checks whether the commit message follows a specific pattern (e.g., feat:, fix:, etc.).
- If the message doesn’t follow the format, the commit will be rejected with an error message.

Or you can use regex for something like a Jira ticket. A working example script would be:
``` bash
#!/bin/bash

# Require commit messages to start with a Jira ticket ID (e.g., ABC-123)
regex="^ABC-[0-9]+: .+"

commit_msg=$(cat $1)
if [[ ! $commit_msg =~ $regex ]]; then
  echo "ERROR: Commit message must start with a Jira ticket ID (e.g., 'ABC-123: Fix bug')."
  exit 1
fi

exit 0
This script enforces a specific commit message pattern using a regular expression. Improper messages result in an error, blocking the commit.

11. Git Pre-Commit Hook to Check for Staged Files
This hook prevents commits if there are any unstaged files.
Create a file .git/hooks/pre-commit:
``` bash
#!/bin/bash

# Pre-Commit Hook to Check for Staged Files
if ! git diff --cached --exit-code; then
  echo "There are staged changes. Please commit them before proceeding."
  exit 1
fi
- This script checks if there are any staged files. If there are, it prompts the user to commit them before proceeding with the action.
- This helps avoid accidentally committing changes that haven’t been staged yet.

12. Git Tagging Automation Script
Creating tags for releases is a common task in a DevOps workflow. This script automates the process of creating and pushing a new tag.
``` bash
#!/bin/bash

# Script: git_tag.sh
echo "Enter the tag name:"
read tag_name
git tag $tag_name
git push origin $tag_name
- The script prompts the user for a tag name, tags the current commit, and pushes the tag to the remote repository.

13. Git Log Parser to View Specific Commits
You can automate parsing git log to filter commits based on specific criteria.
``` bash
#!/bin/bash

# Script: git_log_parser.sh
echo "Enter the author name:"
read author
git log --author="$author" --oneline
- The script filters the commit log to show commits made by a specific author.
- It’s useful for quickly reviewing contributions by specific team members.

14. Automating Pull Requests with GitHub CLI
If you use GitHub, you can automate the creation of a pull request using the GitHub CLI.
``` bash
#!/bin/bash

# Script: create_pull_request.sh
gh pr create --base main --head $(git branch --show-current) --title "Automated PR" --body "This is an automated pull request."
- The script uses the GitHub CLI (gh) to create a pull request from the current branch to the main branch.
- This automates the creation of a pull request with a default title and body.
