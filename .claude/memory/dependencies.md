---
name: dependencies
description: "Read this file before you change pyproject.toml or a dependency, add a Python version, or change the use of the mcp API in server.py."
metadata:
  type: reference
---

## The extra `browser`

- `browser_cookie3` is in the optional extra `browser`, not in the main dependencies. It needs `lz4`, and `lz4` 4.4.5 has wheels only up to CPython 3.14. On a newer Python, uv builds `lz4` from source, and the build fails with "Microsoft Visual C++ 14.0 or greater is required" when no compiler is found.
- The server must not import `browser_cookie3`. Only `reddit auth` and the cookie refresh in `RedditClient.login` use it, through `auth_from_browser`, which reports a missing package.
- To make sure that the server installs only wheels on the newest Python, start it with an empty uv cache. Set `UV_CACHE_DIR` to an empty folder, run `uvx --from . reddit-mcp`, and make sure that uv builds only `reddit-mcp-noapi`. A warm cache can hold a wheel that uv built before, and then it hides a source build.

## mcp 1.x and 2.x

- The server supports `mcp` 1.x and 2.x, so the range is `mcp>=1.0.0,<3`.
- mcp 2.x has no `Server.list_tools` and no `Server.call_tool` decorator. For 2.x, `server.py` gives `on_list_tools` and `on_call_tool` to `Server(...)`. On 2.x, `serverInfo.version` of the `initialize` answer is empty.
- Do not tell users to pin `mcp<2` in a launcher command. On Windows, a launcher that runs the command through `cmd /c`, for example mcpproxy, breaks an argument with `<`.
- After a change of the `mcp` API use, start the server on 1.x and on 2.x. `uvx --with "mcp>=1,<2" --from . reddit-mcp` gives 1.x, and `uvx --from . reddit-mcp` gives the newest 2.x.

## Python versions

- `requires-python` is `>=3.10`. The server starts on CPython 3.12, 3.13, 3.14 and 3.15 with mcp 1.30 and with mcp 2.3. Before you use a syntax newer than 3.10, raise `requires-python`.
