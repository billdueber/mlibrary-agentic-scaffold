---
id: critique:bin-scripts-critique
kind: critique
status: final
created_at: 2026-05-01T00:00:00Z
updated_at: 2026-05-01T00:00:00Z
scope:
  kind: repository
  repositories:
    - repo:root
review_target: bin/initialize_for_opencode + bin/update_mlibrary_scaffold
links:
  wiki:
    - wiki:new-project-setup
    - wiki:mlibrary-agentic-scaffold-overview
---

# Summary

Adversarial review of the two scaffold installer scripts:
`bin/initialize_for_opencode` and `bin/update_mlibrary_scaffold`.
Profiles applied: `code-change`, `operator-clarity`, `security`.

# Review Target

Both scripts were reviewed in full as of 2026-05-01. No prior Ralph packet;
this is a direct artifact critique triggered by the Loom checkpoint policy
(risky user-machine scripts warrant critique before acceptance).

# Verdict

`pass_with_findings`

Three high-severity bugs found (FIND-001, FIND-009, FIND-005). All three resolved
in the same session. Four low-severity observations: FIND-003 accepted risk,
FIND-004/006/008 deferred to future maintenance pass.

# Findings

## FIND-001: `_create_minimal_loom` called before it is defined

Severity: high
Confidence: high
Disposition: resolved (fixed in same session)

Observation:
`initialize_for_opencode` lines 630-679 handle Loom initialization. The
`if command -v loom` branch (line 630) contains a failure path at line 635:
```
log_warn "loom init failed — creating minimal structure manually"
_create_minimal_loom
```
However, `_create_minimal_loom` is only defined at line 639 inside the `else`
branch (when `loom` CLI is NOT found). If the `loom` CLI IS found and `loom
init` fails, bash calls the function before it exists — result: `bash:
_create_minimal_loom: command not found`, and `set -euo pipefail` kills the
script.

Why it matters:
The failure path for `loom init` silently fails with a misleading error rather
than creating the fallback structure. Users who have a `loom` binary installed
but get a non-zero exit from `loom init` are left with no `.loom/` directory
and a crashed script.

Follow-up:
Move the `_create_minimal_loom` function definition to before the `if/else`
block so it is always available. Applied.

Challenges: None - not claim-specific.

---

## FIND-009: MCP diff in `update_mlibrary_scaffold` is completely broken

Severity: high
Confidence: high
Disposition: resolved (fixed in same session)

Observation:
`update_mlibrary_scaffold` lines 155-201 attempt to extract recommended MCP
server names from `mcp/recommended-servers.md` using a regex:
```python
candidates = re.findall(r'^#{2,3}\s+([\w][\w\-\.@/]+)', content, re.MULTILINE)
```
The heading format in `recommended-servers.md` is:
```
### 1. `github` — GitHub API
```
The regex extracts the first word after `##`/`###`, which is `1.`, `2.`, ...
`7.`, `Installation`, `Recommended`, `What` — never the actual server names.
Verified by executing the regex against the actual file contents.

Because the extracted "recommended" set never intersects with actual opencode.json
MCP keys, the condition `if not missing` is always true, and the script always
prints "✓ All recommended MCP servers are installed" regardless of actual state.
The feature is completely non-functional.

Why it matters:
The MCP diff is the primary value proposition of the update script. It is
supposed to tell users when a new recommended server was added to the scaffold.
As-written it is silent regardless of state, giving false confidence.

Follow-up:
Rewrite the extraction to target backtick-quoted server names in headings:
```python
candidates = re.findall(r'^#{2,3}.*?`([\w][\w\-\.@/]*)`', content, re.MULTILINE)
```
Applied.

Challenges: None - not claim-specific.

---

## FIND-005: mem0 MCP config entry is stale — package archived, now cloud-only

Severity: high
Confidence: high
Disposition: resolved (fixed in same session, decision:0001 update needed)

Observation:
`initialize_for_opencode` line 560 (before fix):
```bash
MCP_CONFIGS["mem0"]='{"type":"local","command":["npx","-y","mem0-mcp"],"enabled":true}'
```
The `mem0-mcp` npm package (mem0ai/mem0-mcp) was **archived on March 24, 2026**
and is no longer maintained. The official mem0 MCP is now a cloud-hosted service
at `https://mcp.mem0.ai/mcp` requiring a `MEM0_API_KEY` — there is no longer a
local Docker self-hosted MCP option.

This means:
1. `npx -y mem0-mcp` downloads archived dead code
2. The scaffold's documented Tier 3 ("one Docker install, local, free") premise
   no longer exists
3. The `mcp/recommended-servers.md` mem0 entry, `instructions/agentic-memory.md`
   Tier 3 section, and `wiki-cross-session-memory` Tier 3 description all need
   updating to reflect the cloud-only reality

Why it matters:
Users who select mem0 get a non-functional MCP entry. The scaffold documentation
actively misleads about the infrastructure model for Tier 3 memory.

Follow-up (applied):
- `bin/initialize_for_opencode`: mem0 MCP config updated to cloud HTTP type with
  placeholder API key env var, and warning text added explaining cloud requirement.
- `mcp/recommended-servers.md`: mem0 entry updated to reflect cloud-only status.
- `instructions/agentic-memory.md`: Tier 3 updated to reflect cloud-only status.
- `wiki-cross-session-memory`: Tier 3 description updated.
- `decision:0001` update recommended (scope change in Tier 3 premise).

Challenges: None - not claim-specific.

---

## FIND-003: MCP config entry written even when binary install fails

Severity: low
Confidence: high
Disposition: accepted_risk

Observation:
`initialize_for_opencode` installs `github-mcp-server` via `install_github_mcp_server || true`
(line 511). If the install fails, the function returns 1 but execution continues.
The `MCP_APPROVED["github"]` sentinel is set before the install attempt, so the
`MCP_CONFIGS["github"]` entry is always written to `opencode.json` regardless of
whether the binary exists.

Why it matters:
OpenCode will attempt to spawn `github-mcp-server stdio` on startup and produce
an error if the binary is missing. The user sees a startup error rather than a
clear "install github-mcp-server first" message.

Follow-up:
After install failure, print a clear callout: "github-mcp-server was not
installed — OpenCode will show an MCP startup error until you install it."
Current warning message is adequate but could be made more specific.

Accepted as low risk: the user explicitly approved the server and was warned.
The opencode.json entry can be removed manually.

Challenges: None - not claim-specific.

---

## FIND-004: Unconditional `clear` before banner

Severity: low
Confidence: medium
Disposition: accepted_risk

Observation:
`initialize_for_opencode` line 223: `clear` is called unconditionally before
printing the banner. In a non-interactive context (subshell, CI, redirect to
log file) this emits escape sequences and clears terminal content the user may
want to see.

Why it matters:
Low impact in practice — the script is designed to be run interactively.
However, `clear` before prompts means any output prior to the script start
(e.g., the command invocation itself) is erased.

Follow-up:
Guard with `[[ -t 1 ]] && clear` to only clear when stdout is a terminal.

Challenges: None - not claim-specific.

---

## FIND-006: Fail-fast skill check uses first alphabetical skill only

Severity: low
Confidence: medium
Disposition: open

Observation:
`update_mlibrary_scaffold` lines 69-78 check whether initialization was run
by testing if the first scaffold skill (alphabetically by `ls`) is symlinked:
```bash
SCAFFOLD_FIRST_SKILL="$(ls -d "${SCAFFOLD_DIR}/skills/"*/ | head -1)"
FIRST_SKILL_NAME="$(basename "$SCAFFOLD_FIRST_SKILL")"
if [[ -L "${OPENCODE_SKILLS_DIR}/${FIRST_SKILL_NAME}" ]]; then
```
If `adr-create` (the alphabetically first skill) happens to exist as a
non-symlink in `~/.config/opencode/skills/` (e.g., a user's own version), the
check fails even if all other scaffold skills are correctly symlinked.

Why it matters:
False negative on the fail-fast check. The script exits with "initialization not
confirmed" when initialization was in fact performed correctly.

Follow-up:
Check for ANY scaffold skill symlink, not just the first:
```bash
for skill_dir in "${SCAFFOLD_DIR}/skills"/*/; do
  skill_name="$(basename "$skill_dir")"
  if [[ -L "${OPENCODE_SKILLS_DIR}/${skill_name}" ]]; then
    INIT_EVIDENCE += 1; break
  fi
done
```

Challenges: None - not claim-specific.

---

## FIND-008: `eval` on composed strings in server check loop

Severity: low
Confidence: high
Disposition: accepted_risk

Observation:
`initialize_for_opencode` line 475: `eval "$check_cmd"` runs the check command
for each MCP server. The `check_cmd` values are hardcoded strings defined in
the `MCP_SERVERS` array within the script — they are not user input.

Why it matters:
`eval` on any composed string is a code smell and creates a maintenance trap:
a future developer adding a server entry with shell metacharacters in the check
command could introduce unintended behavior. Not a current security issue.

Follow-up:
Replace `eval "$check_cmd"` with bash array-based command execution. This
requires changing the MCP_SERVERS array format to use a different delimiter
for the check command fields, or splitting them into a separate array.

Accepted as low risk given hardcoded values; tracked for future refactor.

Challenges: None - not claim-specific.

# Evidence Reviewed

- `bin/initialize_for_opencode` read in full (910 lines)
- `bin/update_mlibrary_scaffold` read in full (264 lines)
- `mcp/recommended-servers.md` heading structure verified
- MCP diff regex behavior verified via Python execution against actual file content
- `_create_minimal_loom` call graph traced manually through the bash if/else structure

# Residual Risks

1. `decision:0001` premise changed — mem0 is now cloud-only. Decision record
   update or amendment recommended to reflect that Tier 3 requires an API key
   and internet access (no local Docker self-hosted MCP option remains).
2. FIND-006 (first-skill check fragility) — minor, low probability. Deferred.
3. FIND-003 (config written before install confirmed) — accepted risk.

# Required Follow-up

- Amend `decision:0001` to note the mem0 self-hosted MCP deprecation and update
  Tier 3 description to cloud-with-API-key.
- Consider FIND-006 fix in a future maintenance pass.
- Consider FIND-004 (`clear` guard) in a future maintenance pass.

# Acceptance Recommendation

`pass_with_findings` — all three high-severity findings (FIND-001, FIND-009,
FIND-005) were resolved in this session. Residual risk is the stale premise in
`decision:0001` which should be amended but does not block acceptance. Low-severity
findings are accepted risk or deferred.
