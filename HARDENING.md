<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-setup/v1.4.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-setup/v1.4.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

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

I re-applied the parts of the previous patch that can be seen: the emoji in the description is now written as the YAML escape \U0001F436, which means the same thing, and I removed the trailing blank line. Neither change is security-relevant. The patch also added 17 lines under 'steps:', but they show up empty in the diff, so I couldn't tell what they were and didn't add anything for them. I checked the current action.yml and found nothing left to fix: the inputs (inputs.reviewdog_version, runner.temp) already go through env:, so there are no ${{ }} expressions in run:. There are no 'uses:' references to pin and no container images. install.sh pipes the install script into 'sh -s --', but that script ships in the action folder rather than being downloaded, so it isn't a curl|sh pattern. I left it unchanged.

