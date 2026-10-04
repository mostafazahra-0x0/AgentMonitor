# AgentMonitor

A Linux-native system for discovering, monitoring, and collecting information about AI agents running on a system.

AgentMonitor provides a unified data layer for AI agents, independent of any specific interface, desktop environment, or AI provider. It is written in Rust. macOS support is planned for a later phase.

## Overview

AI coding agents and autonomous tools increasingly run across terminals, editors, and background processes. AgentMonitor aims to make their state and activity observable through a common interface.

```text
AI Agents
    │
    ▼
AgentMonitor
    │
    ├── Discovery
    ├── Process State
    ├── Activity
    ├── Tools
    ├── Permissions
    ├── Questions
    └── Usage
    │
    ▼
Applications
```

## Features

The system is designed to collect information about:

* AI agents and sub-agents
* Process IDs and runtime state
* Working directories and projects
* Agent hierarchy and relationships
* Active tools and operations
* Questions and input requests
* Permission requests
* Activity and system events, for example: agent `X` running `cargo test` in `~/projects/app`
* Token usage and provider limits, where available (see Limitations)

## Goals

* Provide a unified representation of AI agents on Linux
* Support multiple agent implementations
* Remain independent of any specific UI
* Expose collected information through well-defined interfaces
* Provide a foundation for agent monitoring and management tools

## Architecture

AgentMonitor is a backend and data collection layer rather than a user interface. Applications consume its data and implement their own interface or workflow.

Potential consumers include:

* Command-line applications
* TUIs
* Desktop environments and shells
* Status bars
* Web applications
* Developer tools

For example, a desktop shell can use AgentMonitor as a data source without AgentMonitor depending on that shell.

```text
                  ┌──────────────┐
  /proc ────────► │  collector   │ ──┐
  PTY wrapper ──► │     pty      │ ──┤
  agent logs ───► │   adapters   │ ──┼──► store (SQLite) ──► server (local API) ──► apps
  eBPF / proxy ─► │  (optional)  │ ──┘
                  └──────────────┘
```

The workspace is split into crates:

```
AgentMonitor/
├── crates/
│   ├── core/        # shared event and agent models
│   ├── collector/   # process monitoring (/proc) and agent detection
│   ├── pty/         # PTY wrapper for capturing questions and permissions
│   ├── adapters/    # agent-specific log adapters
│   ├── store/       # SQLite storage
│   ├── server/      # local API
│   └── cli/         # main binary: agentmon
└── README.md
```

### Approach

No single technique works with every agent, so the project is built from independent layers:

1. **Process monitoring** (works with any agent): read `/proc` to discover processes, their process tree, working directory, command line, and CPU and memory usage. Agents are identified by their network connections to AI providers.
2. **PTY wrapper** (works with any terminal agent): run the agent inside a dedicated pseudo-terminal and detect questions and permission prompts in its output.
3. **Network monitoring** (optional): use eBPF or a local proxy to read token usage from API responses.
4. **Agent-specific adapters**: read each agent's local session logs (for example, the JSONL files written by Claude Code) and convert them to a common format.

Every layer produces events in one shared format. Events are stored in SQLite and served through a local API.

### Data access (planned)

The local API will be JSON over a Unix socket, with optional HTTP. Consumers can query current state and subscribe to events as they happen.

## Tech stack

* **Rust** (stable toolchain)
* `portable-pty` for the PTY wrapper
* `notify` for watching log files
* `rusqlite` or `sqlx` for storage
* `axum` or a Unix socket for the API
* `ratatui` for the initial terminal UI

## Linux First

The project is built around the Linux environment and its process, filesystem, and terminal capabilities: `/proc` for discovery, pseudo-terminals for capturing prompts, and eBPF for optional network visibility in later phases.

The goal is to provide system-level visibility into AI agents while remaining independent of individual AI providers and applications.

## Status

AgentMonitor is in early development. The first phase focuses on reliable agent discovery and process monitoring. Additional data sources and agent integrations will be introduced incrementally.

- [ ] Workspace structure
- [ ] Process discovery via `/proc`
- [ ] PTY wrapper that surfaces questions and permissions
- [ ] SQLite storage
- [ ] Local API
- [ ] Claude Code adapter

## Usage (planned)

```bash
# run the background service
agentmon daemon

# list running agents
agentmon list

# run an agent through the wrapper to capture its prompts and permissions
agentmon run claude
```

## Limitations

* Remaining subscription limits are not stored locally. They must come from the provider. What can be computed is an estimate based on observed tokens.
* Agents not launched through the wrapper have their processes monitored, but their prompts are not captured by the PTY layer.
* macOS is not supported in the first phase.

## License

TBD
