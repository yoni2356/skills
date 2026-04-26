---
name: to-prd
description: Turn the current conversation context into a PRD and store it according to the project's configured storage backend. Use when user wants to create a PRD from the current context.
---

# To PRD

Takes the current conversation context and codebase understanding and produces a PRD. Do NOT interview the user — just synthesize what you already know.

## Storage Backend

Before doing anything, read `.claude/issues.json` from the project root. Use the `backend` field to determine where to store output:

- **`local`**: write the PRD to `{prds_path}/{kebab-title}.md`
- **`obsidian`**: write the PRD to `{vault}/{prds_folder}/{kebab-title}.md`
- **`github`**: write the PRD to `temp/prds/{kebab-title}.md` (GitHub has no native PRD concept; store locally)

If `.claude/issues.json` does not exist, fall back to writing to `temp/prds/`.

Create the target directory if it does not exist.

## Process

1. Explore the repo to understand the current state of the codebase, if you haven't already.

2. Sketch out the major modules you will need to build or modify to complete the implementation. Actively look for opportunities to extract deep modules that can be tested in isolation.

A deep module (as opposed to a shallow module) is one which encapsulates a lot of functionality in a simple, testable interface which rarely changes.

Check with the user that these modules match their expectations. Check with the user which modules they want tests written for.

3. Write the PRD using the template below.

<prd-template>

## Problem Statement

The problem that the user is facing, from the user's perspective.

## Solution

The solution to the problem, from the user's perspective.

## User Stories

A LONG, numbered list of user stories. Each user story should be in the format of:

1. As an <actor>, I want a <feature>, so that <benefit>

<user-story-example>
1. As a mobile bank customer, I want to see balance on my accounts, so that I can make better informed decisions about my spending
</user-story-example>

This list of user stories should be extremely extensive and cover all aspects of the feature.

## Implementation Decisions

A list of implementation decisions that were made. This can include:

- The modules that will be built/modified
- The interfaces of those modules that will be modified
- Technical clarifications from the developer
- Architectural decisions
- Schema changes
- API contracts
- Specific interactions

Do NOT include specific file paths or code snippets. They may end up being outdated very quickly.

## Testing Decisions

A list of testing decisions that were made. Include:

- A description of what makes a good test (only test external behavior, not implementation details)
- Which modules will be tested
- Prior art for the tests (i.e. similar types of tests in the codebase)

## Out of Scope

A description of the things that are out of scope for this PRD.

## Further Notes

Any further notes about the feature.

</prd-template>

4. Write the PRD to the configured backend path and print the full path of the file created.
