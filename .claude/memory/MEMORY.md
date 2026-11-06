# Project memory index: reddit-mcp

Read this index from the top. A line with no link is the full rule.

- Never call `reddit_comment`, `reddit_submit`, `reddit_vote` or `reddit_delete`, or a write method of `RedditClient`, also not as a test. Never print the cookie or `session.json`.
- `main` of `ALERTua/reddit-mcp` is the maintained branch. The MCP setup of its owner runs the HEAD of `main`. Each push to `main` reaches that setup at the next restart of the server.
- [Reddit endpoints](reddit-endpoints.md) — an HTTP 403 or 429 from Reddit, the choice of an endpoint, the RSS fallback, the rate limit
- [Thread reading](thread-reading.md) — `read_post`, `thread_search`, "more" stubs, `/api/morechildren`, `comments_info`, a missing comment
- [Dependencies and Python versions](dependencies.md) — `pyproject.toml`, a dependency, the extra `browser`, a new Python version, the `mcp` API in `server.py`
- [Live checks](live-checks.md) — a probe or a measurement against Reddit, a test of the MCP server without a client
