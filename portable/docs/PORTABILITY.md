# Portable Workflow Notes

This folder contains generalized variants of the Feature Dev workflow intended as a cross-runtime starting point for VS Code, CLI, and other agent hosts.

## Generalization Rules

### 1. Do Not Remove Human Gates

`vscode/askQuestions` should not be removed conceptually.

What should change is the implementation detail:

- In VS Code, the workflow can use `vscode/askQuestions`.
- In CLI or other hosts, the workflow should ask the user directly in normal conversation and wait for an explicit reply.

So the behavior is preserved, but the VS Code-specific tool dependency is removed.

### 2. Replace Tool-Specific Language with Capability Language

Instead of hard-coding tool IDs in the instructions, use generic capability wording such as:

- "use the available search tools"
- "use the available file editing tools"
- "run diagnostics or tests with the available execution tools"
- "ask the user directly and wait for an explicit response"

### 3. Remove Provider-Specific Tool Lists from Portable Templates

The files under `portable/agents/` intentionally omit `tools` and `model` from frontmatter.

Reason:

- tool IDs differ across hosts
- model names differ across hosts
- a template is more reusable if the runtime supplies its own available tools and models

When targeting a concrete platform, reintroduce the matching tool names in a provider-specific folder such as:

- `.github/agents/` for GitHub Copilot / VS Code
- `.claude/agents/` for Claude Code
- `.codex/agents/` for Codex-compatible setups

### 4. Keep Orchestration Semantics Stable

The following should stay the same across platforms:

- prompt-builder remains optional
- feature classification remains internal routing, not a standalone logged phase
- backend-only work skips designer
- explicit user approval is required before moving between gated phases

## Mapping Guidance

| VS Code-specific form                        | Portable form                                  |
| -------------------------------------------- | ---------------------------------------------- |
| `vscode/askQuestions`                        | Ask the user directly and wait                 |
| `#askQuestions`                              | approval prompt / direct user question         |
| `agent/runSubagent`                          | runtime subagent invocation capability         |
| `web/fetch`                                  | available fetch/web retrieval capability       |
| `edit/createFile`, `edit/editFiles`          | available file editing tools                   |
| `vscode/runCommand`, `execute/runInTerminal` | available shell/command execution capability   |
| `#search`, `#usages`                         | available search / reference lookup capability |
| `#problems`                                  | available diagnostics / lint / compile checks  |

## Recommended Usage

Use the files in `portable/agents/` as the neutral baseline.

Then create provider-specific runtime folders from that baseline when needed.

This avoids overfitting the main workflow instructions to one host while still preserving the same agent roles and approval model.
