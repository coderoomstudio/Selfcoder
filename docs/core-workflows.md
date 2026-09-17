# Core Workflows

Selfcoder gives you several ways to work with local or self-hosted models inside VS Code. The best surface depends on how much action you want the model to take and whether you prefer Selfcoder's dedicated UI or VS Code's native chat tools.

## Sidepanel Chat

The Selfcoder sidepanel is the main workspace for local coding conversations.

Use it when you want:

- a dedicated chat panel for one project or task
- model selection inside the extension UI
- streaming markdown responses
- code blocks with copy actions
- context chips showing what Selfcoder may include
- pinned files and manual attachments
- explicit `@` context mentions for the next request
- image attachments for vision-capable models
- searchable chat history, editable names, favorites, and conversation branching
- token usage visibility for local model context windows
- a Chat, Plan, and Agent mode selector

Typical flow:

1. Open the Selfcoder activity bar view.
2. Select model.
3. Open the file you want to discuss.
4. Select code if the question is about a specific block.
5. Ask your question in the Selfcoder sidepanel.

Good sidepanel prompts:

```text
Explain the selected code and suggest a safer implementation.
```

```text
Use the active file and diagnostics to help me fix this error.
```

```text
Review my current changes and call out likely regressions.
```


## Slash Commands

The sidepanel composer supports slash commands when the first non-empty token starts with `/`.

Useful slash commands:

- `/help` shows the commands available in the current mode.
- `/context [prompt]` previews the context Selfcoder would include for a request.
- `/review [focus]` reviews uncommitted workspace changes with the selected model.
- `/models` lists visible backend models and capabilities.
- `/export` opens the current conversation as Markdown.
- `/new` starts a fresh conversation.

Plan supports the safe local set shown above. Agent mode also supports `/compact`, `/summarize`, `/init`, and custom commands from `.opencode/commands/*.md` in each workspace folder.

See [Slash Commands](slash-commands.md) for the full command list and custom command format.

## `@` Mentions

Use `@` mentions in the sidepanel composer when you want to choose explicit context for a single request.

Typical uses:

- include one file or a specific symbol instead of relying on automatic context
- pass focused repository context such as codebase search or Git changes
- include terminal output or diagnostics that matter for the current task

Typical flow:

1. In Chat, Plan, or Agent mode, type `@` in the composer.
2. Choose the context items you want to include.
3. Send your request.

Mentions are request-scoped and clear from the composer after send. Use pinned files when you want context to persist across turns.

See [Context and Attachments](context-and-attachments.md#choose-context-with--mentions) for mention categories, limits, and budgeting behavior.

## Model Selection

Selfcoder lists chat-capable models reported by the active backend.

Depending on backend metadata, the model list can show details such as:

- context length
- parameter size
- model size
- vision support
- reasoning support
- tool-use support

You can hide models you do not want to see in pickers and set aliases for easier recognition.

## `@Selfcoder` In VS Code Native Chat

Selfcoder also registers a native VS Code chat participant named `@Selfcoder`.

Use this when you want answers from your configured model inside the built-in VS Code Chat panel.

Typical flow:

1. Open VS Code Chat.
2. Type `@Selfcoder` followed by your request.
3. Selfcoder answers using the active backend and the model selected in the Selfcoder sidepanel.
4. The answer streams inside native VS Code Chat.

Example:

```text
@Selfcoder Explain the active file and suggest the next refactor.
```

The native participant uses Selfcoder's active LM Studio, Ollama, or OpenAI-compatible backend along with the system prompt, workspace instructions, and request-scoped context pipeline.

## Model Used By `@Selfcoder`

The native participant shares the sidepanel's model selection. Whatever model you select in the Selfcoder sidepanel is the model `@Selfcoder` uses.

If you have not selected a sidepanel model yet, or the selected model is hidden, filtered, or unavailable for the active backend, the participant falls back to the first available chat model from the backend.


## Local Models In The VS Code Model Picker

Selfcoder can expose eligible models from the active backend to VS Code's native model picker.

This is useful for native chat and agent-style workflows where VS Code expects a language model provider. Models usually appear only when they report the capabilities VS Code needs, especially tool support.

Use this workflow when you want:

- local models in VS Code's model dropdown
- VS Code native Ask or Agent mode
- tool-aware workflows with eligible local models
- a more native VS Code experience

If a model does not appear in the native model picker, see [Troubleshooting](troubleshooting.md#model-does-not-appear-in-the-vs-code-model-picker).

## Plan And Agent Modes

The Selfcoder sidepanel has three modes, selected in the composer header:

- **Chat** — the local-model chat described above.
- **Plan** — a read-only agent that actively investigates the workspace and designs a solution.
- **Agent** — an autonomous coding agent that can read files, make edits, and run commands in your workspace.

### Plan Mode

Use Plan when the task is broad enough to need repository investigation, but you are not ready to let the model change anything. Plan can read files, search the codebase, ask clarifying questions, and return a structured implementation plan.

Plan enforces a read-only boundary. It cannot:

- edit files
- run terminal commands, including apparently read-only commands
- delegate to another agent
- run forwarded or project-defined slash commands that could select a mutation-capable agent
- switch itself into Agent

The selected permission tier still controls reads, external directories, and web access. It never enables edits or terminal commands in Plan. Selfcoder also requires OpenCode's native Plan mode and fails the request if that mode cannot be selected.

Plan and Agent share the same OpenCode session while their workspace, backend, model, credentials, runtime, and permission tier stay unchanged. You can therefore investigate in Plan, switch to Agent, and preserve the context. Switching modes does not start implementation; send a new Agent request when you are ready.

See [Plan Mode](plan-mode.md) for the complete workflow and safety model.

### Agent Mode

Use Agent mode when you want the model to work through a larger task end to end instead of answering a single question.

Agent mode adds:

- a live activity timeline showing the agent's reads, searches, edits, and commands
- change tracking with per-file `+N/-M` counts
- in-editor diff visualization of the agent's edits
- one-click revert for a single file or the whole session
- single-level Undo last edit for the latest completed Agent edit
- interactive permission prompts before sensitive actions

Agent mode runs on the model you select in the sidepanel and the active backend, so agent work stays on your machine. The selected model must support tool use; if it does not, pick a tool-capable model before running the agent.

On first Plan or Agent use, Selfcoder downloads and manages a pinned OpenCode runtime. The download is SHA-256 verified and cached for later offline use.

Agent permissions are controlled by `Selfcoder.permissionMode`:

| Mode | Behavior |
| --- | --- |
| `autonomous` | Runs without permission prompts. |
| `elevated` | Allows most actions, but asks before destructive commands, `.env` access, external directories, and runaway-loop protection. |
| `balanced` | Recommended default. Allows normal reads, edits, and basic shell commands while asking before destructive actions, network access, and sensitive file edits. |
| `restricted` | Asks before most shell commands and blocks more sensitive reads or external-directory access. |

Typical flow:

1. Open the Selfcoder sidepanel.
2. Select a tool-capable model.
3. Switch the composer mode to **Agent**.
4. Describe the task, for example "add input validation to the signup form and update the tests".
5. Watch the activity timeline and approve any permission prompts.
6. Review the changed files and revert anything you do not want to keep.

## Choosing The Right Workflow

| Goal | Recommended workflow |
| --- | --- |
| Ask about the current file | Sidepanel chat |
| Use selected text as context | Sidepanel chat |
| Explore how a feature or bug spans the repository | Plan mode |
| Prepare and refine an implementation plan without workspace changes | Plan mode |
| Ask from VS Code's built-in chat UI | `@Selfcoder` |
| Use local models in VS Code's own Agent mode | VS Code model picker |
| Attach files or images manually | Sidepanel chat |
| Resume older conversations | Sidepanel chat history |
| Let the model edit files and run commands | Agent mode |
| Work through a larger automated coding task | Agent mode |

### Modes At A Glance

| Behavior | Chat | Plan | Agent |
| --- | --- | --- | --- |
| Actively reads and searches the repository | No; uses selected context | Yes | Yes |
| Modifies files | No | Never | Yes |
| Runs terminal commands | No | Never | According to permissions |
| Requires a tool-capable model | No | Yes | Yes |
| Typical result | Answer or suggestion | Implementation plan | Working changes and verification |
