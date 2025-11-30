# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a containerized Claude Code automation system designed to run Claude Code in continuous loops. The system uses Docker to create an isolated environment where Claude Code can autonomously execute tasks defined in `workspace/prompt.md`, with results logged to `logs/loop.log`.

## Architecture

The system consists of three main components:

1. **Docker Container** (`Dockerfile`): Ubuntu 22.04 base with Node.js 20 and Claude Code CLI installed globally
2. **Entry Point** (`start.sh`): Handles initial setup and launches the continuous loop
3. **Loop Logic** (`loop.sh`): Alternate loop implementation with enhanced formatting and JSON output processing

### Key Design Patterns

- **Stateless Iterations**: Each loop iteration is independent, but state can be persisted via `/workspace/loop_data.json` or similar files
- **OAuth Authentication**: Uses long-lived OAuth tokens stored in environment variables (`CLAUDE_CODE_OAUTH_TOKEN`)
- **Volume Mounts**: Three critical mount points:
  - `/workspace` - Working directory for Claude Code operations and prompt files
  - `/logs` - Output logs from continuous execution
  - `/workspace/plans` (optional) - Read-only mount for implementation plans

## Building and Running

### Build the Docker Image
```bash
docker build -t claude-loop:v1 .
```

### Prepare Host Directories
```bash
mkdir -p ./workspace ./logs
# Place your prompt.md into ./workspace
```

### Authentication Setup

Create a long-lived OAuth token (one-time setup):
```bash
claude setup-token
export CLAUDE_CODE_OAUTH_TOKEN=<token>
```

Extract existing token from macOS Keychain:
```bash
export ANTHROPIC_AUTH_TOKEN=$(security find-generic-password -s "Claude Code-credentials" -a "$(whoami)" -w | python3 -c "import sys, json; print(json.load(sys.stdin)['claudeAiOauth']['accessToken'])")
```

### Run the Container

Interactive mode (first-time authentication):
```bash
docker run -it \
  --user $(id -u):$(id -g) \
  -e CLAUDE_CODE_OAUTH_TOKEN="$CLAUDE_CODE_OAUTH_TOKEN" \
  -v "$(pwd)/logs:/logs" \
  -v "$(pwd)/workspace:/workspace" \
  claude-loop:v1
```

After interactive login, exit Claude with `/quit` to start the non-interactive loop.

### Monitor Logs
```bash
tail -f ./logs/loop.log
```

## Loop Behavior

The prompt in `workspace/prompt.md` instructs Claude to:
- Implement software described in `/workspace/plans`
- Stick to implementation plans
- Pass information between iterations using `/workspace/loop_data.json` or `CLAUDE.md`
- Create a `CLAUDE.md` file if one doesn't exist

### Command Flags Used in Loop

- `--verbose`: Extended output
- `--dangerously-skip-permissions`: Bypass permission checks (used in loop.sh)
- `--include-partial-messages`: Include incomplete responses (used in loop.sh)
- `--output-format=stream-json`: JSON streaming format for parsing

## File Structure

```
.
├── Dockerfile              # Container definition
├── start.sh               # Main entry point script
├── loop.sh                # Alternative loop implementation with enhanced formatting
├── workspace/             # Mounted working directory
│   ├── prompt.md         # Main instruction prompt for Claude
│   ├── plans/            # Optional: Implementation plans directory
│   └── loop_data.json    # Persistent state between iterations
└── logs/
    └── loop.log          # Continuous execution logs
```

## Important Notes

- The loop runs indefinitely - there's no built-in graceful exit or iteration cap
- Authentication is handled once at container startup via OAuth flow
- The container may not have a browser, so OAuth URL may need to be opened manually on the host
- For non-interactive auth, supply `ANTHROPIC_API_KEY` or `ANTHROPIC_AUTH_TOKEN` environment variables
- The system uses user ID mapping (`--user $(id -u):$(id -g)`) to match host file permissions
