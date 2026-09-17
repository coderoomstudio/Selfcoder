# Troubleshooting

This guide covers common Selfcoder setup and usage issues.

## Selfcoder Cannot Connect To The Backend

Check:

1. The selected backend is running.
2. `Selfcoder.backend` matches your backend: `LMStudio`, `Ollama`, or `OpenAICompatible`.
3. `Selfcoder.backendAddress` points to the correct server root or compatible `/v1` base URL.
4. A chat model is loaded or available.
5. Your firewall or security software is not blocking local HTTP connections.
6. `Selfcoder.backendAuthorizationToken` contains the correct bearer token if the server requires one.

Default endpoints:

| Backend | Endpoint |
| --- | --- |
| LM Studio | `http://localhost:1234` |
| Ollama | `http://localhost:11434` |
| OpenAI compatible / vLLM | `http://localhost:8000` |
| OpenAI compatible / llama.cpp | commonly `http://localhost:8080` |

After fixing the backend, use the Selfcoder reconnect action or reload VS Code.

## No Models Appear In Selfcoder

Possible causes:

- No chat model is installed in LM Studio.
- Ollama has no local models installed.
- The backend address is wrong.
- The backend is running, but the selected backend setting is wrong.
- The compatible server's `/v1/models` response does not contain a top-level `data` array with non-empty model IDs.
- The server rejected model discovery because the bearer token is missing or incorrect.
- The model is an embedding model, not a chat model.
- The model was hidden from Selfcoder pickers. Plan and Agent do not support models that explicitly report no tool capability.

For Ollama, check:

```powershell
ollama list
```

For LM Studio, check that the local server is running and a model is available.

For an OpenAI-compatible server, verify `GET <base-url>/models` directly. Enter only the server root or `/v1` base in `Selfcoder.backendAddress`; do not enter `/v1/models`, because Selfcoder appends the route.

## OpenAI-Compatible Chat Works But Other Features Fail

Plan, Agent, and VS Code native tool flows require the served model and chat template to support OpenAI-style tool calls. The standard `/v1/models` response has no portable tool-capability field, so Selfcoder may make a compatible model selectable even when its actual template cannot call tools.

If ordinary Chat works but a tool workflow fails:

- use a model that supports tool calling
- enable or select the model's tool-aware chat template on the server
- check the server logs for malformed or unsupported tool calls
- verify that the same model handles a streaming `/v1/chat/completions` request with tools

It is normal for a generic compatible backend to show no loaded-model list, pre-warm step, or model-load progress. Those states are not portable in the OpenAI-compatible API.

If the server returns `401` or `403`, set the required bearer token in `Selfcoder.backendAuthorizationToken`. Custom headers such as `x-api-key` are not supported.

## Model Does Not Appear In The VS Code Model Picker

The Selfcoder sidepanel and VS Code native model picker have different requirements.

A model may be available in the sidepanel but missing from the native picker if:

- it does not report tool-use support
- it is hidden in Selfcoder
- it is an embedding model
- `Selfcoder.privacyProtection` is set to `strict`
- VS Code needs to be reloaded after backend/model changes

Try a tool-capable model and reload VS Code.

## `@Selfcoder` Does Not Work In Native Chat

Check:

- VS Code is `1.100.0` or newer.
- `Selfcoder.privacyProtection` is set to `standard`.
- Selfcoder is installed and enabled.
- Your backend is connected.
- A chat-capable model is available.

If the participant still does not appear, reload VS Code.

## Plan Or Agent Mode Issues

Plan and Agent run through an OpenCode runtime that Selfcoder downloads and manages automatically on first use.

If Plan or Agent does not start:

- Open a folder or workspace first. Both modes need a workspace and are disabled when no folder is open.
- Select a tool-capable model and chat template. If the selected model explicitly reports no tool support, switch to one that supports it.
- Make sure your backend is connected and the model is available.
- Make sure the first Plan or Agent use can reach the internet so Selfcoder can download its managed OpenCode runtime. After the runtime is cached, later sessions can start offline.
- If permission prompts feel too frequent or too broad, check `Selfcoder.permissionMode`. The default is `balanced`; `elevated` is more permissive, while `restricted` asks more often.

## Image Attachments Are Rejected

Image support requires all of the following:

- `Selfcoder.enableVision` is set to `enabled`
- the selected model supports vision
- the image is within the current size limit
- the backend accepts image input for that model

Current image size limit is 2 MB per image.

## Responses Are Too Short, Confused, Or Missing Context

Try:

- selecting the relevant code before asking
- pinning important files
- selecting explicit context with `@file`, `@folder`, `@symbol`, `@git`, `@terminal`, or `@codebase`
- attaching the exact log or error text
- using a model with a larger context window
- starting a new chat for a new topic
- asking for one task at a time
- enabling automatic context with `Selfcoder.contextMode`

Better prompt:

```text
Use the selected function and current diagnostics. Explain the error and suggest the smallest safe fix.
```

Less useful prompt:

```text
Fix this project.
```

## A Context Mention Is Missing Or Unavailable

Check the omission notice shown for the selected mention. Mentions resolve when you send the request, so a deleted file, stale symbol, or closed terminal may no longer be available.

- For `@symbol`, enter at least two characters and ensure your language extension provides workspace symbols.
- For `@terminal`, run the command in a VS Code terminal with shell integration enabled while Selfcoder is active. Only observed command output is available; older scrollback is not captured. You can also attach the log as a text file.
- For `@git`, make sure the repository has uncommitted text changes.
- If a selection exceeds the available model budget, choose a smaller file or symbol, reduce the number of selections, or start a new conversation.

Mentions apply to one request. Select them again for a later request, or pin a file for ongoing context. See [Context and Attachments](context-and-attachments.md#choose-context-with--mentions).

## Undo Last Edit Is Missing Or Reports A Conflict

Undo appears in the Agent change summary only when there is a safe snapshot for the latest completed edit. It has one level and does not survive a reload or restore from chat history.

A conflict means a file no longer matches the state left by the Agent edit. Review that file's diff and reconcile the changes manually. See [Undo The Last Agent Edit](core-workflows.md#undo-the-last-agent-edit).

## Reasoning Is Not Showing

Reasoning output appears only when the selected model and backend support it.

Check:

- the model reports reasoning support
- `Selfcoder.reasoning` is not set to `disabled`
- the backend supports reasoning events for that model

If unsupported, Selfcoder still works as a normal streaming chat.

## Reporting An Issue

Open a GitHub issue from the [Issues page](https://github.com/coderoomstudio/self-coder/issues).
