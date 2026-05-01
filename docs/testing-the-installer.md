# Testing `bin/initialize_for_opencode`

This document describes how to verify that `bin/initialize_for_opencode` works
correctly against a real project repository.

Run this test after:
- any non-trivial change to `bin/initialize_for_opencode`
- changes to instruction files that affect the `instructions` array written to `opencode.json`
- changes to `mcp/recommended-servers.md` that affect the MCP menu or configs
- changes to `skills/` that affect the symlinking step
- a major version of context-mode or github-mcp-server is released

---

## Two-target test matrix

| Target | Path | Preconditions | Purpose |
|--------|------|---------------|---------|
| A — existing setup | `~/devel/mlibrary/opensearch-sugar` | Has `AGENTS.md`, no `opencode.json`, has `adrs/` dir | Tests backup/preservation of existing AGENTS.md, no collision with existing structure |
| B — virgin repo | `~/devel/other/milemarker` | Has gemspec, no `AGENTS.md`, no `opencode.json` | Tests clean-slate install; AGENTS.md written from scratch |

Run both targets. A passing test requires both to pass.

---

## Before you start: capture the before-state

For each target, run this before touching anything:

```bash
TARGET=~/devel/mlibrary/opensearch-sugar  # change for Target B

# Save a manifest of files that exist before the run
find "$TARGET" -maxdepth 3 -not -path '*/.git/*' | sort > /tmp/before-manifest.txt

# Save current AGENTS.md (Target A only)
cp "$TARGET/AGENTS.md" /tmp/AGENTS.md.before 2>/dev/null || true

# Save current opencode.json (should not exist — verify)
ls "$TARGET/opencode.json" 2>/dev/null && echo "WARNING: opencode.json already exists"

# Record skill symlinks before
ls -la ~/.config/opencode/skills/ 2>/dev/null | sort > /tmp/skills-before.txt
```

---

## Running the installer

```bash
SCAFFOLD="$HOME/devel/ai/agentstuff/mlibrary-agentic-scaffold"
cd "$TARGET"
"$SCAFFOLD/bin/initialize_for_opencode"
```

### Recommended input sequence (automated simulation)

Pipe this heredoc to simulate a fully automated run. This picks the most common
production choice: context-mode (a), server-memory (a), install github+sequential-thinking+filesystem.

```bash
"$SCAFFOLD/bin/initialize_for_opencode" <<'EOF'
a
a
y
y
y
EOF
```

Where the lines mean:
1. Group 1 web fetching: `a` = context-mode
2. Group 2 memory: `a` = server-memory
3. github MCP: `y` = install
4. sequential-thinking MCP: `y` = install
5. filesystem MCP: `y` = install

For a **manual exploratory run**, answer the prompts yourself and note your
choices — the expected-state verification below must be adjusted to match.

---

## Expected end-state (automated sequence above)

### `opencode.json` created at project root

```bash
cat "$TARGET/opencode.json"
```

Must contain:
- `"mcp"` key (not `"mcpServers"`)
- `"context-mode"` entry: `{"type":"local","command":["context-mode"],...}`
- `"github"` entry: `{"type":"local","command":["github-mcp-server","stdio"],...}`
- `"sequential-thinking"` entry present
- `"filesystem"` entry present
- `"instructions"` array present, listing at minimum `core.md` from the scaffold's absolute path

Quick check:
```bash
python3 -c "
import json, sys
d = json.load(open('$TARGET/opencode.json'))
mcp = d.get('mcp', {})
assert 'context-mode' in mcp, 'context-mode missing'
assert mcp['context-mode'].get('type') == 'local', 'context-mode type must be local'
assert 'github' in mcp, 'github missing'
assert 'sequential-thinking' in mcp, 'sequential-thinking missing'
assert 'filesystem' in mcp, 'filesystem missing'
instr = d.get('instructions', [])
assert any('core.md' in i for i in instr), 'core.md missing from instructions'
print('opencode.json: PASS')
"
```

**Schema validation** — run this to verify all MCP entries are valid per the OpenCode config schema.
This catches type-field errors (e.g. `"type":"http"` is not a valid MCP type; only `"local"` and `"remote"` are):
```bash
pip install jsonschema --quiet 2>/dev/null || true
python3 - "$TARGET/opencode.json" <<'PYEOF'
import json, sys, urllib.request
path = sys.argv[1]
schema_url = "https://opencode.ai/config.json"
with urllib.request.urlopen(schema_url) as r:
    schema = json.load(r)
with open(path) as f:
    config = json.load(f)
try:
    import jsonschema
    jsonschema.validate(config, schema)
    print("Schema validation: PASS")
except jsonschema.ValidationError as e:
    print(f"Schema validation: FAIL — {e.message}")
    sys.exit(1)
except ImportError:
    print("jsonschema not installed — skipping schema validation (run: pip install jsonschema)")
PYEOF
```

### `AGENTS.md` at project root

**Target A (existing AGENTS.md)**:
- `AGENTS.md.bak` must exist (backup of the original)
- `AGENTS.md` must contain the scaffold header (look for `mlibrary-agentic-scaffold` reference)
- Original content must appear in a marked section (`## Original AGENTS.md content` or similar)

```bash
# Check backup preserved
diff /tmp/AGENTS.md.before "$TARGET/AGENTS.md.bak" && echo "Backup matches original: PASS"

# Check original content was kept
grep -c "opensearch-sugar" "$TARGET/AGENTS.md" | grep -qv '^0$' && echo "Original content preserved: PASS"
```

**Target B (no existing AGENTS.md)**:
- `AGENTS.md` must exist and contain scaffold content
- No `AGENTS.md.bak` should exist

```bash
ls "$TARGET/AGENTS.md.bak" 2>/dev/null && echo "FAIL: unexpected .bak file" || echo "No .bak: PASS"
grep -q "mlibrary-agentic-scaffold" "$TARGET/AGENTS.md" && echo "Scaffold content present: PASS"
```

### Skill symlinks installed

```bash
ls -la ~/.config/opencode/skills/
```

Must show symlinks to every directory in `$SCAFFOLD/skills/`:
- `adr-create` → `$SCAFFOLD/skills/adr-create`
- `ruby-gem-release` → `$SCAFFOLD/skills/ruby-gem-release`
- `integration-test` → `$SCAFFOLD/skills/integration-test`
- `lint-fix` → `$SCAFFOLD/skills/lint-fix`
- `docker-services` → `$SCAFFOLD/skills/docker-services`
- `worth-it` → `$SCAFFOLD/skills/worth-it`
- `ss` → `$SCAFFOLD/skills/ss`

Quick check:
```bash
for skill in adr-create ruby-gem-release integration-test lint-fix docker-services worth-it ss; do
  [ -L ~/.config/opencode/skills/$skill ] && echo "$skill symlink: PASS" || echo "$skill symlink: FAIL"
done
```

### `.loom/` initialized (if it didn't exist before)

**Target B only** (virgin repo):
```bash
[ -d "$TARGET/.loom" ] && echo ".loom exists: PASS" || echo ".loom missing: FAIL"
[ -f "$TARGET/.loom/constitution/constitution.md" ] && echo "constitution exists: PASS"
```

**Target A** (opensearch-sugar): Check whether it already had `.loom/` before the run. If not, same check applies. If it did, verify the installer did not overwrite it.

### No unintended deletions

```bash
find "$TARGET" -maxdepth 3 -not -path '*/.git/*' | sort > /tmp/after-manifest.txt

# Any files in before but NOT in after (deletions)
comm -23 /tmp/before-manifest.txt /tmp/after-manifest.txt
# This should output only: AGENTS.md (Target A only, if it was backed up)
# Everything else must be empty
```

Expected deletions:
- **Target A**: none (AGENTS.md is replaced but `.bak` is created; the original content is not lost)
- **Target B**: none

---

## Cleanup after testing

The installer mutates the target repo. To restore it for re-testing:

```bash
# Target A — restore
rm "$TARGET/opencode.json"
cp "$TARGET/AGENTS.md.bak" "$TARGET/AGENTS.md"
rm "$TARGET/AGENTS.md.bak"
# Remove .loom only if it didn't exist before
# rm -rf "$TARGET/.loom"

# Target B — restore
rm "$TARGET/opencode.json"
rm "$TARGET/AGENTS.md"
rm -f "$TARGET/AGENTS.md.bak"
# rm -rf "$TARGET/.loom"

# Remove skill symlinks (only if they weren't there before; check skills-before.txt)
# for skill in adr-create ruby-gem-release integration-test lint-fix docker-services worth-it ss; do
#   rm -f ~/.config/opencode/skills/$skill
# done
```

---

## Variant runs worth testing

Once the baseline (automated sequence above) passes, also test:

| Variant | Input change | What to verify |
|---------|-------------|----------------|
| Choose `fetch` instead of context-mode | `b` at Group 1 | `opencode.json` has `fetch` entry, no `context-mode` |
| Choose `neither` for memory | `c` at Group 2 | No `server-memory` or `mem0` in `opencode.json` |
| Skip github (`n`) | `n` at github prompt | No `github` key in `opencode.json` |
| mem0 cloud | `b` at Group 2 | `opencode.json` has `{"type":"http","url":"https://mcp.mem0.ai/mcp",...}` |
| Run installer twice on same target | run again after first run | `opencode.json` not clobbered; existing MCPs preserved; skills still point correctly |

The **idempotency variant** (run twice) is especially important. The script is supposed
to merge into `opencode.json` without losing prior entries.

---

## Known limitations / open issues

- The script has no dry-run mode. Every test run mutates the target repo (cleanup required).
- FIND-006 from critique `20260501-ewtdxi6z`: the first-skill fail-fast check in
  `update_mlibrary_scaffold` uses a fragile symlink probe that may give false positives.
  This is deferred.
- github-mcp-server install depends on `brew` (macOS) or `go install` (Linux). If
  neither is available, the binary won't be installed but the config entry will still
  be written. Verify manually that the binary resolves: `which github-mcp-server`.
