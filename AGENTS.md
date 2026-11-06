# AGENTS.md

Guidance for AI coding agents in this repository. Make small changes, make sure that each claim agrees with the code or with a live probe, and obey the boundaries at the end.

## Project memory

The traps and the decisions with their reasons are in the project memory. Before you start a task, read this file: [.claude/memory/MEMORY.md](.claude/memory/MEMORY.md).

## Project overview

reddit-mcp is an MCP server and a CLI that read and write Reddit with the `reddit_session` cookie of a browser, with no API key. The package name is `reddit-mcp-noapi`. `README.md` gives the user view.

## Repository layout

| Path | What it holds |
|---|---|
| `reddit_mcp/reddit.py` | `RedditClient`: the session, the reads (`read_post`, `thread_search`, `read_listing`, `search`, `inbox`), the writes, `auth_from_browser`, and the `reddit` CLI |
| `reddit_mcp/server.py` | the MCP tools, their dispatch, and the layer for `mcp` 1.x and 2.x |
| `pyproject.toml`, `uv.lock` | the package, its dependencies and the extra `browser` |

## Commands

- Start the MCP server from the clone: `uvx --from . reddit-mcp`.
- Read a thread with the CLI: `uvx --from . reddit read <url> --json`.
- Update the lock file after a change of `pyproject.toml`: `uv lock`.

## Boundaries

- Never call a write tool or a write method of `RedditClient`, also not as a test: `reddit_comment`, `reddit_submit`, `reddit_vote`, `reddit_delete`.
- Never print, log or commit the cookie or `session.json`.
