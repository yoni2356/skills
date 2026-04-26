---
name: manage-issues
description: Day-to-day issue management — list open issues, open a new blank issue, or close an issue. Routes to the configured backend (local, obsidian, github) via .claude/issues.json. Use when user wants to see, create, or close issues without generating them from a plan.
---

# Manage Issues

Day-to-day interface for the issue tracker configured in `.claude/issues.json`.

## Reading the config

Always start by reading `.claude/issues.json` from the project root. Extract:

- `backend` — active backend (`local`, `obsidian`, `github`)
- Path for the active backend's `issues_folder` / `issues_path`
- For `obsidian`: expand `~` in `vault` to the actual home directory

If the file does not exist, fall back to `local` with `issues_path = "temp/issues"`.

---

## Commands

The user invokes this skill with an optional subcommand argument. If no argument is given, default to `list`.

### `list`

Show all issues in the configured backend.

**`local` / `obsidian`**: list all `.md` files in the issues folder. For each file, parse and display:
- Issue number (from filename prefix `NNN-`)
- Title (from filename or `# Title` heading)
- Status (from `## Metadata` → `Status` field, default `open`)

Display as a table: `NNN | Title | Status | Blocked by`.

**`github`**: run `gh issue list --repo {repo}` and display output.

---

### `new <title>`

Create a new blank issue with the given title.

**`local` / `obsidian`**:
1. Scan the issues folder for the highest existing `NNN` prefix. New issue number = highest + 1 (start at `001` if none exist).
2. Convert the title to kebab-case for the filename: `NNN-kebab-title.md`.
3. Write the file using the blank template below.
4. Print the full path.

**`github`**: run `gh issue create --repo {repo} --title "{title}" --body ""` and print the URL.

<blank-issue-template>
# {Title}

## What to build

<!-- describe the goal -->

## Acceptance criteria

- [ ] 

## Blocked by

None - can start immediately

## Metadata

- **Status**: open
</blank-issue-template>

---

### `close <NNN or title fragment>`

Mark an issue as closed.

**`local` / `obsidian`**:
1. Find the matching issue file by number or title substring.
2. Update the `Status` field in `## Metadata` from `open` to `closed`.
3. Print confirmation with the file path.

**`github`**: run `gh issue close <number> --repo {repo}`.

---

### `switch <backend>`

Change the active backend for this project.

1. Read `.claude/issues.json`.
2. Update the `backend` field to the new value (`local`, `obsidian`, or `github`).
3. Write the file back.
4. Print: "Backend switched to {backend}. Issues will now be read/written at {resolved path}."

Do NOT migrate existing issues — just update the pointer.

---

## Notes

- When writing files, create the target directory if it does not exist.
- For `obsidian`, expand `~` to the actual home directory path before using it in Bash commands.
- Issue numbers are always zero-padded to 3 digits (`001`, `042`, `100`).
