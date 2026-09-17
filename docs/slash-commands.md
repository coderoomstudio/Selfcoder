# Slash Commands

Selfcoder supports slash commands in the sidepanel composer. Type a command at the start of a message to run a built-in action, inspect context, export a conversation, or hand a command to Agent mode.

Slash commands are recognized only when they are the first non-empty token in the message. For example, `/context explain this file` runs the `context` command, while `please run /context` is sent as a normal prompt.

## Available Commands

| Command | Modes | What it does |
| --- | --- | --- |
| `/help` | Chat, Plan, Agent | Shows the slash commands available in the current mode. |
| `/context [prompt]` | Chat, Plan, Agent | Shows the context Selfcoder would include for a request, including automatic context state, packed context blocks, token estimates, and attachments. If you add a prompt, Selfcoder uses it when deciding which context would be selected. |
| `/new` | Chat, Plan, Agent | Starts a fresh sidepanel conversation while keeping the current mode and selected model. |
| `/review [focus]` | Chat, Plan, Agent | Reviews uncommitted text changes in the current workspace with the selected model. Add a focus such as `/review security and tests` to steer the review. |
| `/models` | Chat, Plan, Agent | Lists visible models from the configured backend, including useful capability badges when metadata is available. |
| `/export` | Chat, Plan, Agent | Opens an untitled Markdown document containing the current conversation transcript. |
| `/compact` | Agent | Compact the current Agent session context. |
| `/summarize` | Agent | Alias for `/compact`. |
| `/init` | Agent | Create or update project agent instructions in `AGENTS.md`. |

Chat and Plan expose commands Selfcoder can handle safely. Plan does not forward OpenCode or project-defined commands because they may select a mutation-capable agent. Agent includes the full supported command set and custom project commands.

## Commands With Context Mentions

Select `@` context mentions before running `/context [prompt]` to preview their contribution to the request. The preview keeps your selections in the composer. `/review` consumes selected mentions only when it sends a review request to the model.

Commands forwarded to OpenCode, including custom project commands, cannot be combined with composer mentions. Selfcoder keeps those selections for a later normal prompt. See [Context and Attachments](context-and-attachments.md#choose-context-with--mentions) for selecting request context.

## Custom Project Commands

In Agent mode, Selfcoder discovers custom commands from Markdown files in each workspace folder's `.opencode/commands/` directory.

Each direct `.md` file becomes a slash command named after the file. For example:

```text
.opencode/commands/audit.md
```

creates:

```text
/audit
```

You can add a frontmatter description:

```md
---
description: Review current changes
---

Review the current code changes.

Look for:
- bugs
- risky behavior
- missing tests
- confusing code

Also pay attention to this request:

$ARGUMENTS
```

Running `/audit login flow` sends the command to Agent mode and replaces `$ARGUMENTS` with `login flow`.

Custom command names should start with a letter or number and then use only letters, numbers, `.`, `_`, or `-`.

## Autocomplete

The composer opens slash-command autocomplete while you are typing a command token at the start of the message.

- Chat mode shows Chat-compatible commands.
- Plan mode shows its read-only command set.
- Agent mode shows built-in Agent commands and discovered custom commands.
- Command aliases, such as `/summarize`, are included.
- Use Arrow Up or Arrow Down to move through suggestions.
- Use Tab, Enter, or click to insert a command.
- Use Escape to close the menu.
