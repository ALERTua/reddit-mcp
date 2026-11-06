---
name: live-checks
description: "Read this file before you run a probe or a measurement against Reddit, or before you test the MCP server without a client."
metadata:
  type: reference
---

- The repository has no automatic tests. Make sure of a change with a live probe against Reddit.
- Write each probe to a script file. The script imports `reddit_mcp.reddit` and uses `RedditClient`, so that it sends the same headers and the same cookie as the server. Run it from the clone with `PYTHONPATH` set to the clone: `uv run --no-project --with httpx python <script>`.
- A probe prints only status codes, sizes and counts. It never prints the cookie, `session.json` or a request header.
- Each probe uses the rate limit window of the session, which the MCP server uses too. Before a measurement of more than about 40 requests, read `x-ratelimit-remaining` and `x-ratelimit-reset` from a GET of `message/unread.json?limit=1`. When few requests are left, wait for the reset in the script.
- Measure the thread reader on three threads: one with fewer than 20 comments, one with about 100, and one with more than 500. For each thread, give `num_comments`, `loaded`, `requests`, the seconds, the count of wrong parent links and the count of comments with a missing field. A parent link is correct when `parent_id` is the id of the comment above, or the post id at depth 1.
- To find test threads, read `top.rss?t=year` and `new.rss` of a large subreddit, then read `num_comments` from the thread `.json` with `limit=1`.
- To test the MCP server without a client, start `uvx --from . reddit-mcp` and send JSON-RPC lines on stdin: `initialize`, `notifications/initialized`, then `tools/list`. Send the stderr of the server to a file, because a pipe that nobody reads can stop the server.
