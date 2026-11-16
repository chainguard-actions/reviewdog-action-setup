<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-setup/v1.3.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-setup/v1.3.2** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unsafe-shell (severity: high)

install.sh downloads the reviewdog installer with curl/wget and pipes it straight into a shell: `( curl -sfL "${INSTALL_SCRIPT}" ... wget -O - "${INSTALL_SCRIPT}" ) | sh -s -- -b "${TEMP}/reviewdog/bin" "${VERSION}"`. The URL is pinned to a commit, but the downloaded content still runs without first being saved to a file and checked (for example against a checksum). To fix this, download the script to a file, verify it, then run it as a separate step.

Locations:

- `install.sh:34`

### github-env-injection (severity: high)

install.sh writes a path built from environment variables to $GITHUB_PATH without stripping newlines first: `echo "${TEMP}/reviewdog/bin" >>"${GITHUB_PATH}"`. TEMP comes from REVIEWDOG_TEMPDIR (set from runner.temp in the step's env) or falls back to the inherited RUNNER_TEMP. Neither is set inside this script. Before the write, sanitize the value, e.g. `safe=$(printf '%s' "${TEMP}/reviewdog/bin" | tr -d '\n\r')`.

Locations:

- `install.sh:37`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index f687a43..6cc259e 100644
--- a/action.yml
+++ b/action.yml
@@ -1,5 +1,5 @@
 name: "Setup reviewdog"
-description: "🐶 This action sets up reviewdog"
+description: "\U0001F436 This action sets up reviewdog"
 inputs:
   reviewdog_version:
     description: "reviewdog version. [latest,nightly,vX.Y.Z]"
@@ -7,6 +7,23 @@ inputs:
 runs:
   using: "composite"
   steps:
     - name: install reviewdog
       run: |
         set -eu
@@ -20,7 +37,6 @@ runs:
         set -eu
         "$GITHUB_ACTION_PATH/check-installed.sh"
       shell: sh
-
 # Ref: https://haya14busa.github.io/github-action-brandings/
 branding:
   icon: "terminal"

```

Locations:

- `action.yml`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed install.sh without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/install.sh b/install.sh
index 6738103..4828f56 100755
--- a/install.sh
+++ b/install.sh
@@ -21,17 +21,18 @@ fi
 
 mkdir -p "${TEMP}/reviewdog/bin"
 
+INSTALL_SCRIPT_FILE="${TEMP}/reviewdog_install.sh"
+
 echo '::group::🐶 Installing reviewdog ... https://github.com/reviewdog/reviewdog'
-(
-  if command -v curl 2>&1 >/dev/null; then
-    curl -sfL "${INSTALL_SCRIPT}"
-  elif command -v wget 2>&1 >/dev/null; then
-    wget -O - "${INSTALL_SCRIPT}"
-  else
-    echo "curl or wget is required" >&2
-    exit 1
-  fi
-) | sh -s -- -b "${TEMP}/reviewdog/bin" "${VERSION}" 2>&1
+if command -v curl 2>&1 >/dev/null; then
+  curl -sfL "${INSTALL_SCRIPT}" -o "${INSTALL_SCRIPT_FILE}"
+elif command -v wget 2>&1 >/dev/null; then
+  wget -O "${INSTALL_SCRIPT_FILE}" "${INSTALL_SCRIPT}"
+else
+  echo "curl or wget is required" >&2
+  exit 1
+fi
+sh "${INSTALL_SCRIPT_FILE}" -b "${TEMP}/reviewdog/bin" "${VERSION}" 2>&1
 echo '::endgroup::'
 
 echo "${TEMP}/reviewdog/bin" >>"${GITHUB_PATH}"

```

Locations:

- `install.sh`

## Iteration Notes

### Iteration 1

**Fixes applied:** unsafe-shell, github-env-injection, prior-fix

**Notes:**

Fixed both install.sh findings and re-applied the earlier action.yml change.

install.sh: the reviewdog installer is now saved to a file first instead of being piped straight into sh. Before running it, the script checks that the file isn't empty and starts with '#!'. It then runs as `sh "$file" -b "${TEMP}/reviewdog/bin" "${VERSION}"`. I dropped the '-s --' from the piped form so the installer still gets '-b' as its first argument. The file is deleted afterwards.

This check is not the checksum the finding asks for. I didn't add one because I had no verified hash for the installer files and wouldn't make one up. The download URLs are still pinned to upstream commits.

The path written to $GITHUB_PATH now has newlines and carriage returns removed with `tr -d '\n\r'` first.

action.yml: re-applied the earlier patch's only visible change, writing the description's dog emoji as an escape code (\U0001F436) instead of the raw character. The earlier diff says it also added about 17 lines to the steps, but those lines weren't shown, so I couldn't recreate them. The steps already put inputs and runner.temp in env: rather than inline in run:, so I didn't find any other security problem in that file. Nothing was run to test these changes.

