# Context And Attachments

Selfcoder is built around local-model-friendly context. Instead of sending everything in your workspace, it builds a request-specific context package based on the active model, current editor state, conversation history, and your prompt.

## What Selfcoder Can Use As Context

Depending on the request and available context window, Selfcoder can include:

- explicit `@` context mentions for the current request
- pinned files
- selected text from the active editor
- the whole active file, when it fits
- diagnostics from the active file
- a recently focused file
- git diff summaries or focused hunks
- repository search snippets
- workspace instruction files
- manually attached text files
- manually attached images for vision-capable models
- previous conversation turns

Automatic context is selected at send time. This keeps requests focused and avoids wasting limited local model context.

## Choose Context With `@` Mentions

In the Selfcoder sidepanel, type `@` in Chat, Plan, or Agent to choose context for your next request.

| Mention | What it includes |
| --- | --- |
| `@file` | A selected workspace file, read when you send the request. |
| `@folder` | A bounded file tree and up to 8 text files selected for relevance to your prompt. |
| `@symbol` | A selected workspace symbol's definition and nearby source lines. |
| `@git` | Uncommitted text changes from the workspace repository. |
| `@terminal` | Recent commands and captured output from a selected open terminal. |
| `@codebase` | Repository search snippets selected using your prompt. |

To add a mention:

1. Type `@` and choose a category.
2. For files, folders, symbols, or terminals, search and select an item. Symbol searches need at least two characters and a workspace symbol provider from your language extension.
3. Use Arrow Up/Down to navigate and Tab, Enter, or a click to select. Escape closes the menu.
4. Check the removable chip above the input, then write your question and send it. `@git` and `@codebase` can be selected directly without choosing an item.

For example, select a folder with `@folder`, then ask: "Find where this module validates incoming requests." Select `@terminal` to ask about a captured build failure, or `@git` to review your current edits.

Mentions apply to that request and clear from the composer after it starts. Use pinned files when you want a file available across multiple turns. Mentions still work when `Selfcoder.contextMode` is `disabled`.

Mentions share the model's available context budget. Large files or folders may be truncated, and unavailable or over-budget selections produce a visible omission notice. `@folder` supplies a selection of files rather than the entire folder; `@codebase` supplies search results rather than the entire repository.

`@terminal` relies on VS Code shell integration. It keeps the three newest observed commands per open terminal, with bounded output, in memory. A terminal without captured command output cannot be selected. It does not read arbitrary terminal scrollback or execute a command.

Submitted messages show badges for their selected mentions, but resolved mention content is not saved in history. Select the context again when you need it for a new request after reopening a conversation. These composer mentions are a sidepanel feature; native `@Selfcoder` uses VS Code's own reference UI.

## Context Chips

The sidepanel shows context chips above the input.

These chips help you understand what Selfcoder is likely to use, such as:

- active file
- active selection
- diagnostics
- pinned files
- pending attachments
- selected `@` mentions

Some context sources, such as repository search and git diff packing, are finalized only after you send the prompt because they depend on the actual question and remaining token budget.

Chat, Plan, and Agent show activity while collecting context at the start of a request.

## Pinned Files

Pinned files are explicit context. They are useful when you know a file matters for the next few turns.

To pin a file:

1. Open the file in VS Code.
2. Use the editor title context menu.
3. Choose **Pin to Selfcoder Context**.

Current-request mentions are resolved first, followed by pinned files and then automatic context sources. Current behavior allows up to 3 pinned files.

Use pinned files for:

- implementation files related to a bug
- a test file and the source file it covers
- configuration files that affect the answer
- API contracts or schemas the model should respect

## Manual Text Attachments

The sidepanel can attach text files to a message.

Use attachments when:

- the file is not currently open
- you want to include a specific document or log
- the task depends on content outside the active editor

Current limits:

- up to 5 pending attachments
- text files up to 200 KB each
- unknown files are checked before being treated as text

## Image Attachments

Selfcoder supports image attachments when image support is enabled and the selected model supports vision.

Use images for:

- screenshots of UI bugs
- mockups
- error dialogs
- diagrams
- visual references

Current limits:

- images up to 2 MB each
- pasted clipboard images are supported
- images require `Selfcoder.enableVision` to be enabled
- images require a vision-capable model

If image upload is blocked, check the selected model and the vision setting.

## Workspace Instructions

Selfcoder can include workspace-specific instructions automatically.

It looks for the first matching instruction file in this priority order:

1. `local-instruction.md` (or `local-instructions.md`)
2. `.github/copilot-instructions.md`
3. `AGENTS.md` (or `AGENTS.MD`)
4. `CLAUDE.md` (or `CLAUDE.MD`)

Plan and Agent also use deeper discovery and include instructions from folders closer to the current context, while Chat uses only root-level discovery. This allows you to have different instructions for different subfolders in a repository.

Use workspace instructions for stable project rules, such as:

- coding style
- test commands
- architecture constraints
- preferred libraries
- security rules
- "do not modify" areas

Instruction changes apply on the next request.

## Token Budgeting

Local models vary a lot in context length. Selfcoder uses model metadata when available to estimate how much context can safely fit.

Explicit mentions, pins, and automatic sources share the remaining request budget. Selfcoder prioritizes your selections, and uses your prompt to choose relevant automatic sources.

When the conversation gets large, Selfcoder may omit older turns from the next request payload while keeping the visible history intact. The goal is to keep the model responsive without silently deleting your conversation.

The token indicator helps you see when a conversation is getting close to the model's context limit.

## Tips For Better Context

- Select the exact code before asking about a small section.
- Use `@` mentions to choose context for one request.
- Pin important files before asking a cross-file question.
- Attach the exact log or error text.
- Use larger-context models for repository-wide questions.
- Start a new chat when the topic changes.
- Ask for one task at a time.

Good prompt:

```text
Use the pinned test file and active implementation file. Find why this edge case fails and suggest the smallest fix.
```

Less effective prompt:

```text
Fix everything.
```
