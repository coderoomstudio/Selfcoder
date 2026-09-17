# Settings And Privacy

Selfcoder is local-first: requests are sent to the backend address you configure, usually LM Studio, Ollama, vLLM, or llama.cpp running on your own machine.

This page explains the main settings and how they affect behavior.

## Privacy Model

By default, Selfcoder sends model requests to your configured backend address.

For a typical local setup, that means:

- LM Studio at `http://localhost:1234`, or
- Ollama at `http://localhost:11434`, or
- an OpenAI-compatible server at `http://localhost:8000`

If you configure `Selfcoder.backendAddress` to point to a remote server, requests go to that server. Treat the backend address as the boundary for where prompts, attached files, selected workspace context, and tool-call results are sent.

## Privacy Protection Mode

`Selfcoder.privacyProtection` controls native integration behavior.

| Value | Behavior |
| --- | --- |
| `standard` | Enables native VS Code chat integrations and normal Selfcoder model listings. |
| `strict` | Disables native chat integrations and hides cloud-tagged models from Selfcoder model lists when that metadata is available. |

Use `strict` when you want to avoid potential data sharing with VS Code's native chat features or models tagged as cloud-hosted. This setting cannot infer whether an arbitrary OpenAI-compatible address is local or remote; verify the address yourself.

## Main Settings

All settings are under the `Selfcoder` namespace.

| Setting | Default | What it does |
| --- | --- | --- |
| `Selfcoder.backend` | `Ollama` | Selects `LMStudio`, `Ollama`, `VLLM`, or `OpenAICompatible`. |
| `Selfcoder.backendAddress` | backend default | Sets the backend server address; leaving it empty uses the selected backend's default. Compatible addresses may be a server root or end in `/v1`. |
| `Selfcoder.backendAuthorizationToken` | empty | Sends an optional bearer token to the selected backend. Machine-scoped, but stored as an ordinary unmasked setting. |
| `Selfcoder.privacyProtection` | `standard` | Controls native chat integration and cloud-tagged model visibility. |
| `Selfcoder.systemPrompt` | helpful coding assistant prompt | Sets the base instruction sent with requests. |
| `Selfcoder.enableVision` | `enabled` | Enables image attachment support for vision-capable models. |
| `Selfcoder.reasoning` | `medium` | Sets reasoning effort for models that support it. |
| `Selfcoder.historyScope` | `global` | Shows all saved conversations or only conversations for the current repository. |
| `Selfcoder.historyRetention` | `100` | Sets the maximum number of conversations kept in history. |
| `Selfcoder.contextMode` | `enabled` | Enables or disables automatic editor and workspace context. |
| `Selfcoder.responseTimeout` | `30` | Maximum initial wait in minutes for streamed Chat data or a Plan/Agent session update. Clears once streaming begins. |
| `Selfcoder.permissionMode` | `balanced` | Permission tier for Plan and Agent: `autonomous`, `elevated`, `balanced`, or `restricted`. |

## Backend Address And Authentication

Use `Selfcoder.backendAddress` when your backend is not running on the default address.

Leaving the address empty uses the selected backend's default: `http://localhost:11434` for Ollama, `http://localhost:1234` for LM Studio, and `http://localhost:8000` for vLLM or OpenAI compatible. An explicitly blank setting also uses this fallback.

Examples:

```text
http://localhost:1234
```

```text
http://localhost:11434
```

```text
http://localhost:8000
```

```text
http://localhost:8080/v1
```

Make sure the address matches the selected backend. For `OpenAICompatible`, Selfcoder accepts either the server root or its `/v1` base and then calls `/models` and `/chat/completions` itself. Do not enter a complete route such as `/v1/models`.

If you previously set a custom `Selfcoder.backendURL`, copy that value to `Selfcoder.backendAddress` after upgrading. The old key is no longer used.

Set `Selfcoder.backendAuthorizationToken` only when the selected server requires bearer authentication. Selfcoder sends it as `Authorization: Bearer <token>` to LM Studio, Ollama, or an OpenAI-compatible backend. Blank values omit the header.

The setting has machine scope to reduce accidental Settings Sync or repository sharing, but VS Code does not encrypt or password-mask it. Keep real tokens out of workspace settings, screenshots, and checked-in examples. Selfcoder keeps the raw token out of the generated OpenCode configuration and does not pass it to terminal commands started by the model. Selfcoder does not currently support custom authentication headers.

## System Prompt

The system prompt is the base instruction Selfcoder sends with each request.

Use it for stable preferences, such as:

- concise answers
- preferred language
- testing expectations
- code style
- security posture
- "ask before making broad changes" rules

Keep the system prompt short enough that it does not waste too much model context.

## Context Mode

`Selfcoder.contextMode` controls automatic context collection.

| Value | Behavior |
| --- | --- |
| `enabled` | Selfcoder may include active editor content, diagnostics, recent files, git diffs, or repository snippets when useful. |
| `disabled` | Selfcoder skips automatic context providers. Explicit context still works. |

Even when automatic context is disabled, Selfcoder can still use explicit `@` mentions, pinned files, manual attachments, workspace instructions, system prompt, and chat history. See [Context and Attachments](context-and-attachments.md#choose-context-with--mentions) for the mention picker and its limits.

## Response Timeout

`Selfcoder.responseTimeout` sets how long Selfcoder waits for the first streamed Chat data or Plan/Agent session update. If nothing arrives before the timeout, Selfcoder stops the request, the same as pressing Stop.

The value is measured in minutes and is clamped from 1 to 1440. The default is 30 minutes.

## Plan And Agent Permissions

`Selfcoder.permissionMode` controls which Agent actions run immediately and which Plan or Agent actions require approval.

| Value | Behavior |
| --- | --- |
| `autonomous` | Runs without permission prompts. |
| `elevated` | Allows almost everything, but asks before destructive or irreversible commands, `.env` access, external directories, and runaway-loop protection. |
| `balanced` | Recommended default. Lets the agent handle reads, edits, and basic shell commands, while requiring approval for destructive commands, network access, and sensitive file edits. |
| `restricted` | Asks before most non-allowlisted shell commands, denies reading secrets, and blocks external-directory access. |

In Agent mode, file edits are allowed by default in every tier, change tracking remains active, and the current session supports one-click revert.

Plan starts with the selected tier's read rules, but hard-denies file changes, terminal commands, delegated work, and model-controlled exit into Agent in every tier. For example, `balanced` Plan can ask before a web request or external-directory read, but approving that request cannot unlock editing or terminal access. See [Plan Mode](plan-mode.md) for the full safety model.

## Plan And Agent Runtime

Plan and Agent use a managed OpenCode runtime. On first use, Selfcoder downloads the platform-matched runtime into extension storage, verifies it, and reuses the cached copy later.


## History

Selfcoder can save completed sidepanel conversations so you can resume them later.

`Selfcoder.historyScope` controls what appears in history:

- `global`: show conversations across workspaces
- `repo`: show conversations created for the current repository

`Selfcoder.historyRetention` controls how many conversations are kept, with a default of 100 and a supported range of 1 to 999. Favorites are retained first, then the newest remaining conversations. 

History supports title and message search, manual renaming, up to three favorites, and branching from an assistant response. New conversations can receive a title generated by the selected model after their first completed response; manually set names are preserved. See [Find And Organize Conversations](core-workflows.md#find-and-organize-conversations) for the controls.

Image binary content is not kept in conversation history, which helps avoid storing large image payloads in VS Code state.

## Workspace Instructions

Workspace instruction files let you define project-specific behavior without repeating it in every prompt.

Selfcoder checks for:

1. `local-instruction.md` (or `local-instructions.md`)
2. `.github/copilot-instructions.md`
3. `AGENTS.md` (or `AGENTS.MD`)
4. `CLAUDE.md` (or `CLAUDE.MD`)

Agent mode also uses deeper discovery and includes instructions from the folders which are closer to the current context, while chat mode uses only the root-level discovery. This allows you to have different instructions for different subfolders in a repository.

Use these files for project rules and stable context that should apply across requests.

## Sensible Defaults

For most users:

- use `standard` privacy mode
- keep `contextMode` enabled
- keep `enableVision` enabled
- use `medium` reasoning
- use `balanced` Agent permissions
- keep history scope as `global` unless you work across many repositories

Switch to stricter settings when you need a narrower, more controlled workflow.
