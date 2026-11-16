<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-setup/v1.5.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-setup/v1.5.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

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

## Iteration Notes

### Iteration 1

**Fixes applied:** prior-fix

**Notes:**

I re-applied the previous patch to action.yml. Neither of its visible changes is a security fix. The emoji in the description is now written as the YAML escape "\U0001F436", which shows the same emoji, and I removed a blank line before the branding comment.

The patch's header says it added 17 lines, but those lines are not shown in the diff, so I could not re-apply them. I checked the current file for anything they might have fixed and found nothing that needs changing:
- Both inputs (reviewdog_version and runner.temp) already reach the scripts through env: and are not written into the shell commands.
- action.yml has no `uses:` references to pin and no fixed passwords or keys.
- install.sh does pipe a script into sh, but it is the action's own bundled install-reviewdog.sh (`cat ... | sh`), not something downloaded at run time, so I left it as is.

