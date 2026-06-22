<!-- markdownlint-disable -->

# Hardening Report: shmokmt--actions-setup-tfcmt/v2.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **shmokmt--actions-setup-tfcmt/v2.1.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

action.yml line 11: The run: block directly interpolates ${{ github.action_path }} into the shell command string (sub-rule a violation). Additionally, the env var ${TFCMT_VERSION} — sourced from ${{ inputs.version }} (user-controlled) — is used unquoted in the shell command (sub-rule b violation). Offending line: `sudo bash ${{ github.action_path }}/install.sh -b /usr/local/bin -d ${TFCMT_VERSION}`

Locations:

- `action.yml:11`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed two script injection sub-rule violations in action.yml line 11:
1. Moved `${{ github.action_path }}` out of the run: shell string into the env: block as `ACTION_PATH`, then referenced it as `"$ACTION_PATH"` (double-quoted) in the shell command.
2. Changed unquoted `${TFCMT_VERSION}` to properly double-quoted `"$TFCMT_VERSION"` to prevent word splitting and glob expansion.
The run: line is now: `sudo bash "$ACTION_PATH/install.sh" -b /usr/local/bin -d "$TFCMT_VERSION"`

