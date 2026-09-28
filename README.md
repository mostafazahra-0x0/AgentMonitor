# Agent Monitor

A Linux-native system for discovering, monitoring, and collecting information about AI agents running on a system.

Agent Monitor provides a unified data layer for AI agents, independent of any specific interface, desktop environment, or AI provider.

## Overview

AI coding agents and autonomous tools increasingly run across terminals, editors, and background processes. Agent Monitor aims to make their state and activity observable through a common interface.

```text
AI Agents
    │
    ▼
Agent Monitor
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
* Activity and system events
* Token usage and provider limits, where available

## Architecture

Agent Monitor is designed as a backend and data collection layer rather than a user interface.

Applications can consume its data and implement their own interface or workflow.

Potential consumers include:

* Command-line applications
* TUIs
* Desktop environments and shells
* Status bars
* Web applications
* Developer tools

For example, a desktop shell can use Agent Monitor as a data source without requiring Agent Monitor to depend on that shell.

## Linux First

The project is built around the Linux environment and its process, filesystem, terminal, and desktop capabilities.

The goal is to provide system-level visibility into AI agents while remaining independent of individual AI providers and applications.

## Development Status

Agent Monitor is currently in early development.

The initial implementation focuses on reliable agent discovery and process monitoring. Additional data sources and agent integrations will be introduced incrementally.

## Goals

* Provide a unified representation of AI agents on Linux
* Support multiple agent implementations
* Remain independent of any specific UI
* Expose collected information through well-defined interfaces
* Provide a foundation for agent monitoring and management tools

## License

TBD

