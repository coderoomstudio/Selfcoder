# Selfcoder

**A self-hosted AI coding assistant for VS Code that works with LM Studio, Ollama, vLLM, llama.cpp, and more.**

Selfcoder brings chat, planning, and autonomous coding workflows into Visual Studio Code. Connect LM Studio, Ollama, vLLM, llama.cpp, or another OpenAI compatible backend and choose the model that fits your hardware.

This repository is the public home for Selfcoder documentation, Marketplace installation details, and issue tracking.

<p align="center">
  <a href="https://marketplace.visualstudio.com/items?itemName=coderoom.Selfcoder">
    <img alt="VS Code Marketplace" src="https://img.shields.io/badge/marketplace-Selfcoder-007ACC">
  </a>
  <a href="https://selfcoder.dev">
    <img alt="Selfcoder Website" src="https://img.shields.io/badge/website-selfcoder.dev-2f6f9f">
  </a>
  <a href="https://github.com/coderoomstudio/self-coder/issues"><img alt="GitHub Issues" src="https://img.shields.io/github/issues/coderoomstudio/self-coder"></a>
  <a href="#supported-backends"><img alt="Runtime: LM Studio, Ollama, or OpenAI compatible" src="https://img.shields.io/badge/runtime-LM%20Studio%20%7C%20Ollama%20%7C%20OpenAI%20compatible-2f6f9f"></a>
</p>

## What Selfcoder Does

- Adds a dedicated chat panel for self-hosted AI coding sessions.
- Adds **Plan mode** for investigating a codebase and designing a solution without editing files or running terminal commands.
- Includes **Agent mode** for implementing and verifying larger workspace tasks, with permission controls, change review, and revert.
- Connects to models served by **LM Studio**, **Ollama**, **vLLM**, **llama.cpp**, and other OpenAI-compatible servers.
- Supports model-aware workspace context, pinned files, attachments, and explicit `@` mentions for files, folders, symbols, Git changes, terminal output, and codebase search.
- Keeps searchable chat history with editable names and favorites, and lets you branch any assistant response into a new conversation.
- Includes sidepanel slash commands for help, context preview, review, model listing, export, compaction, and project initialization.
- Integrates with VS Code native chat through the `@Selfcoder` participant.
- Exposes eligible configured models to the VS Code model picker.
- Supports image attachments for vision-capable models.


<p align="center">
  <img alt="Selfcoder sidepanel demo" src="https://raw.githubusercontent.com/coderoomstudio/self-coder/main/resources/img/general.gif">
</p>

## Installation

Install Selfcoder from the [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=coderoom.Selfcoder).

To install it from VS Code:

1. Open VS Code.
2. Go to **Extensions**.
3. Search for "Selfcoder".
4. Install the extension published by "Coderoom".
5. Reload VS Code if prompted.

After installing, start your model backend, pick a model, and open Selfcoder from the VS Code activity bar.

## Chat, Plan, And Agent

Use the mode selector in the Selfcoder composer to choose how much action the model can take:

| Mode | Best for | Workspace access |
| --- | --- | --- |
| **Chat** | Questions, explanations, and code suggestions | Uses the context Selfcoder selects; does not modify the workspace. |
| **Plan** | Repository investigation and implementation planning | Can actively read and search, but cannot edit files, run terminal commands, delegate work, or switch itself into Agent. |
| **Agent** | Implementing and verifying a solution | Can read, edit, and run commands according to your permission setting. |

Plan and Agent require a tool-capable model. They run through Selfcoder's managed OpenCode runtime and share one session, so you can investigate in Plan and switch to Agent without losing the reasoning and repository context. Switching modes never starts implementation by itself.

See [Plan Mode](docs/plan-mode.md) for the safety model and workflow, or [Core Workflows](docs/core-workflows.md) for all three modes.

## Supported Backends

| Backend | Default endpoint | Use case |
| --- | --- | --- |
| [LM Studio](https://lmstudio.ai/download) | `http://localhost:1234` | Easy setup, local model management, GUI friendly |
| [Ollama](https://ollama.com/download) | `http://localhost:11434` | CLI-driven model management, simple local runtime |
| OpenAI compatible | `http://localhost:8000` | Connect [vLLM](https://vllm.ai/), [llama.cpp](https://llama.app/), or another backend with `/v1/models` and streaming `/v1/chat/completions` |


See [Models and Backends](docs/models-and-backends.md#openai-compatible-servers) for setup details and compatibility requirements.

## Documentation

Project documentation is available in the [`docs`](docs/) directory.

Useful starting points:

- [Getting Started](docs/getting-started.md)
- [Core Workflows](docs/core-workflows.md)
- [Plan Mode](docs/plan-mode.md)
- [Slash Commands](docs/slash-commands.md)
- [Context and Attachments](docs/context-and-attachments.md)
- [Models and Backends](docs/models-and-backends.md)
- [Settings and Privacy](docs/settings-and-privacy.md)
- [Troubleshooting](docs/troubleshooting.md)

## Reporting Issues

Use [GitHub Issues](https://github.com/coderoomstudio/self-coder/issues) to report bugs, request improvements, or share compatibility notes for specific models and backends.

## Marketplace

Use the [Visual Studio Marketplace page](https://marketplace.visualstudio.com/items?itemName=coderoom.Selfcoder) for the current version, installation, and changelog details.

## Website

Visit the [Selfcoder website](https://selfcoder.dev) for the product overview, installation links, and updates.
