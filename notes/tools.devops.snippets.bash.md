---
id: s0g91gbjnvcq45yd6xokxh2
title: bash
desc: ''
updated: 1749980493840
created: 1749978462337
---

https://medium.com/@obaff/13-bash-only-tricks-you-can-drop-into-any-ci-cd-pipeline-e70ed0caca4c

### Fail Fast Everywhere

- Make your pipeline fail immediately if any command exits with a non-zero status.
- Use in: Build, test, and deploy steps to avoid unexpected continuation after failures.

    ```bash
    #!/usr/bin/env bash
    set -euo pipefail   # -e exit on error | -u undefined var | pipefail any pipe error
    - -e: Exit on any command failure
    - -u: Exit if using an undefined variable
    - -o pipefail: Exit if any command in a pipeline fails
    ```

### Zero-Downtime Deploy with rsync + Symlink Swap

- Use rsync and a symlink swap to deploy with zero downtime.
- Use in: Production deploy steps where zero downtime and rollback support are required.

    ``` bash
    #!/usr/bin/env bash
    set -euo pipefail

    BUILD_DIR="/var/www/releases/$(date +%Y%m%d%H%M%S)"
    LIVE_LINK="/var/www/live"

    rsync -a "./build/" "$BUILD_DIR"          # copy new files
    if [[ -d "$BUILD_DIR" ]]; then            # safety check
    ln -sfn "$BUILD_DIR" "$LIVE_LINK"       # atomic swap (f = force, n = no-deref)
    fi
    # Instant rollback, just point the symlink back to the previous folder.
    ```

### Retry with Real Exponential Back-off

- Retry flaky commands (e.g., network calls) safely.
- Example: retry curl --fail -sSL <https://example.com/health>

    ``` bash
    #!/usr/bin/env bash
    set -euo pipefail

    retry() {
    local retries=5 delay=1
    for ((i=1; i<=retries; i++)); do
        if "$@"; then return 0; fi
        echo "Attempt $i failed. Waiting $delay s…" >&2
        sleep "$delay"
        delay=$((delay * 2))
    done
    return 1   # bubble up failure to pipeline
    }
    ```

### Package Artifacts with Git + Date Labels

- Create a build artifact with versioning and Git info.
- Use in: Build steps for versioned, traceable artifacts.

    ``` bash
    # !/usr/bin/env bash
    set -euo pipefail

    VERSION="$(git rev-parse --short HEAD || echo unknown)"
    STAMP="$(date +%Y%m%d%H%M%S)"
    ARTIFACT="app-${VERSION}-${STAMP}.tar.gz"

    tar -czf "$ARTIFACT" ./build/
    echo "Created $ARTIFACT"
    ```

### Mask Secrets in Log Output

- Redact sensitive variables in CI logs.
- Avoids leaking credentials into shared logs, even if the secret has “/” or “&”.
- Use in: Deploy logs or third-party integrations.

    ``` bash
    # !/usr/bin/env bash
    set -euo pipefail

    SECRET="${API_KEY:-changeme}"
    ESCAPED_SECRET=$(printf '%s\n' "$SECRET" | sed 's/[\/&]/\\&/g')

    printf 'Deploying with API_KEY=%s\n' "$SECRET" \
    | sed "s/$ESCAPED_SECRET/[REDACTED]/g"
    ```

### Auto-Find and Run Every Test Script

- Drop a new*.sh into ./tests and it starts running next build, no YAML edits.
- Use in: Test steps that scale as your codebase grows.

    ``` bash
    # !/usr/bin/env bash
    set -euo pipefail
    shopt -s nullglob   # pattern expands to nothing if empty

    for test in ./tests/*.sh; do
    echo "Running $test"
    bash "$test"
    done
    ```

### Change Detection for Selective Jobs

- Only run steps if certain files have changed.
- Speeds up big monorepos by skipping untouched areas.

    ``` bash
    # !/usr/bin/env bash
    set -euo pipefail

    git fetch origin main --quiet || true     # shallow clones safety

    if git diff --name-only origin/main...HEAD | grep -q '^infra/'; then
    echo "CI tasks for infra code…"

    # run-infra-tasks.sh

    else
    echo "No infra changes, skipping."
    fi
    ```

### Dump Logs Automatically on Failure

- Gives you context in the CI output without extra clicks.
- Use in: Any critical job where you need context after a failure.

    ``` bash
    # !/usr/bin/env bash

    set -euo pipefail
    LOG_FILE="/var/log/myapp.log"

    trap '{
            echo "Job failed — printing last 200 lines of log:"
            [[ -f "$LOG_FILE" ]] && tail -n 200 "$LOG_FILE"
        }' ERR

    ./run-deploy.sh        # your risky command
    ```

### Pipeline Metrics Logging

- Track execution time per step.
- Use in: Performance tuning and benchmarking CI steps.

    ``` bash
    # !/usr/bin/env bash

    set -euo pipefail
    start=$(date +%s.%N)

    # …your command here…

    sleep 2

    end=$(date +%s.%N)
    printf '⏱  Step took %.2f seconds\n' "$(echo "$end - $start" | bc)"
    ```

### Environment-Aware Deploy Logic

- Differentiate behavior between dev, staging, and prod.
- One script covers dev, staging, and prod with clear guards.
- Use in: Deploy or config provisioning stages.

    ``` bash
    # !/usr/bin/env bash

    set -euo pipefail

    case "${ENVIRONMENT:-}" in
    prod)  deploy_prod  ;;
    stage) deploy_stage ;;
    dev)   deploy_dev   ;;
    *)     echo "Unknown ENVIRONMENT=$ENVIRONMENT" >&2; exit 1 ;;
    esac
    ```

### Auto-Cleanup for Temporary Resources

- Create and clean up temp files or resources.
- Stops leftover junk from filling your CI runner’s disk.
- Use in: Any CI step that uses temp dirs, like test runners or build tools.

    ``` bash
    # !/usr/bin/env bash

    set -euo pipefail

    TMPDIR=$(mktemp -d)
    echo "Using temp dir: $TMPDIR"
    trap 'rm -rf "$TMPDIR"' EXIT

    # work with "$TMPDIR" here

    ```

### Push Errors into GitHub Actions UI

- Make Bash scripts talk to GitHub Actions for better feedback.
- Red text shows up right in the Actions view, no scrolling through raw logs.

    ``` bash
    # !/usr/bin/env bash

    set -euo pipefail

    if ! ./run-tests.sh; then
    echo "::error file=run-tests.sh,line=1::Tests failed"
    exit 1
    fi
    ```

### Timeout Guard to Kill Hanging Commands

- Prevents stuck processes from eating up CI minutes (and your budget).

    ``` bash
    # !/usr/bin/env bash

    set -euo pipefail

    timeout_guard() {       # usage: timeout_guard 300 my_long_task
    local seconds="$1"; shift
    ( sleep "$seconds"; echo " Timeout after ${seconds}s"; kill -TERM $$ ) & watcher=$!
    "$@"                   # run the real command
    kill -9 "$watcher" 2>/dev/null || true
    }

    # Example: stop integration tests if they run > 5 min
    timeout_guard 300 ./integration-tests.sh
    ```
