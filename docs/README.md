# Selfcoder User Documentation

Welcome to the public documentation for Selfcoder, a local and self-hosted AI coding assistant for VS Code. Selfcoder supports LM Studio, Ollama, vLLM, llama.cpp, and other OpenAI Chat Completions-compatible servers.

Selfcoder is designed for developers who want practical AI assistance inside VS Code while keeping model choice, backend configuration, and workspace context under their own control.

## Documentation Map

| Guide | What it covers |
| --- | --- |
| [Getting Started](getting-started.md) | Install Selfcoder, connect a supported model server, and send your first message. |
| [Core Workflows](core-workflows.md) | Choose between Chat, Plan, and Agent, organize and branch conversations, undo an Agent edit, and use native VS Code integrations. |
| [Plan Mode](plan-mode.md) | Investigate a repository and prepare an implementation plan under an enforced read-only boundary. |
| [Slash Commands](slash-commands.md) | Use built-in sidepanel commands and custom project commands. |
| [Context and Attachments](context-and-attachments.md) | Choose explicit `@` context mentions and understand pins, automatic context, attachments, workspace instructions, and token budgeting. |
| [Models and Backends](models-and-backends.md) | Configure LM Studio, Ollama, vLLM, llama.cpp, or another OpenAI-compatible endpoint and choose useful models. |
| [Settings and Privacy](settings-and-privacy.md) | Configure Selfcoder behavior, privacy mode, history, reasoning, vision, and workspace context. |
| [Troubleshooting](troubleshooting.md) | Fix common connection, model, native chat, attachment, and response quality issues. |

## What You Can Do With Selfcoder

- Chat with models from a dedicated VS Code sidepanel.
- Use Plan mode to read and search a repository, ask clarifying questions, and design an implementation without changing the workspace.
- Switch the sidepanel into Agent mode to let a model read files, make edits, and run commands across your workspace, with change tracking and one-click revert.
- Move from Plan to Agent without losing the session context; implementation begins only after you switch modes and send an Agent request.
- Use models through LM Studio, Ollama, vLLM, llama.cpp, or another compatible endpoint.
- Ask questions about the current file, selected code, diagnostics, recent work, or repository changes.
- Pin files into the conversation context when a task needs specific source files.
- Select files, folders, symbols, Git changes, terminal output, or codebase search with `@` mentions for the next request.
- Attach text files and images, when supported by the selected model.
- Run sidepanel slash commands such as `/context`, `/review`, `/models`, `/export`, `/compact`, and `/init`.
- Add custom Agent mode slash commands with Markdown files in each workspace folder's `.opencode/commands/`.
- Use `@Selfcoder` inside VS Code native chat.
- Expose eligible local models to VS Code's model picker for native chat and agent-style workflows.
- Search, rename, and favorite conversations globally or within the current repository, or branch from an earlier assistant response.
- Revert all edits from the current session or undo only the latest Agent edit.

## Recommended First Setup

1. Install Selfcoder from the [latest release](https://marketplace.visualstudio.com/items?itemName=coderoom.Selfcoder).
2. Install and start [LM Studio](https://lmstudio.ai/download), [Ollama](https://ollama.com/download), or an OpenAI-compatible server such as [vLLM](https://vllm.ai/) or [llama.cpp](https://llama.app/).
3. Make a chat-capable model available through the server.
4. Open the Selfcoder sidepanel in VS Code.
5. Select your model.
6. Ask a small, focused question about the current file.

For the full walkthrough, start with [Getting Started](getting-started.md).
