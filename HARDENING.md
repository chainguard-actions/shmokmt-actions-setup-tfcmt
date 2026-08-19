<!-- markdownlint-disable -->

# Hardening Report: shmokmt--actions-setup-tfcmt/v2.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **shmokmt--actions-setup-tfcmt/v2.1.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The run: block in action.yml directly interpolates `${{ github.action_path }}` into the shell command string. Any `${{ ... }}` expression inside a run: block is a script-injection risk because YAML template substitution occurs before the shell ever sees the value. The offending line is: `run: sudo bash ${{ github.action_path }}/install.sh -b /usr/local/bin -d ${TFCMT_VERSION}`

Sub-rule (b): `${TFCMT_VERSION}` (which holds the value of `${{ inputs.version }}`, an attacker-controllable input) is expanded unquoted in the same run: command. An unquoted expansion allows the shell to parse metacharacters out of the value, enabling command injection.

Locations:

- `action.yml:11`

### unpinned-uses (severity: high)

The workflow file uses `actions/checkout@v4` — a mutable tag reference rather than a pinned 40-character commit SHA. If the tag is moved (e.g. by a supply-chain compromise), the action will silently execute different code. All `uses:` references should be pinned to a full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `.github/workflows/test.yml:12`

### missing-permissions (severity: medium)

The workflow file `.github/workflows/test.yml` has no top-level `permissions:` key and neither of its jobs (`test-linux`, `test-darwin`) defines a job-level `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (e.g. `write` on `contents`). A minimal `permissions:` block (e.g. `permissions: read-all` or specific scopes) should be added.

Locations:

- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

1. action.yml script-injection: Moved `${{ github.action_path }}` into env var ACTION_PATH and quoted both $ACTION_PATH and $TFCMT_VERSION in the run command (`sudo bash "$ACTION_PATH/install.sh" -b /usr/local/bin -d "$TFCMT_VERSION"`). 2. test.yml unpinned-uses: Pinned both `actions/checkout@v4` references to full SHA `34e114876b0b11c390a56381ad16ebd13914f8d5 # v4`. 3. test.yml missing-permissions: Added `permissions: {}` at the workflow top level.

