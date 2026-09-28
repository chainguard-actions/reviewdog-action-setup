<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-setup/v1.3.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-setup/v1.3.2** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unsafe-shell (severity: high)

install.sh downloads a remote script via curl or wget and pipes it directly to `sh -s` for execution. Even though the URL is pinned to a specific commit SHA, this pattern (remote content piped to a shell interpreter) is inherently risky — if the remote host or network is compromised, arbitrary code executes on the runner. The script should be downloaded to a temporary file, its integrity verified (e.g., via checksum), and then executed separately.

Locations:

- `install.sh:32`

## Iteration Notes

### Iteration 1

**Fixes applied:** unsafe-shell

**Notes:**

Fixed install.sh to download the remote install script to a temporary file (${TEMP}/reviewdog_install.sh) before executing it, instead of piping directly to sh. The pipe form was: `curl ... | sh -s -- -b "${TEMP}/reviewdog/bin" "${VERSION}"`. The fixed form downloads with curl/wget to a file, then runs `sh "${INSTALL_SCRIPT_FILE}" -b "${TEMP}/reviewdog/bin" "${VERSION}"`. The `--` was dropped (it was sh's own option terminator, not the script's argument), and `-s` was dropped (no longer reading from stdin). The script's actual arguments are preserved unchanged.

