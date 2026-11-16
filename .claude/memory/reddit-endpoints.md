---
name: reddit-endpoints
description: "Read this file before you change how the client reads Reddit, or how it handles an HTTP 403 or an HTTP 429."
metadata:
  type: reference
---

- Reddit blocks the TLS fingerprint of the Python `requests` library. With `requests`, each read answers HTTP 403 and `inbox` answers "Not logged in", also with a valid cookie. Use `httpx`.
- Without the `reddit_session` cookie, each endpoint answers HTTP 403. Each read calls `_ensure_logged_in` first, which puts the cookie on the session.
- With the cookie, these endpoints answer HTTP 403 "Blocked" on old.reddit.com and on www.reddit.com:
    - each `.json` listing and search, for example `new.json` and `search.json`
    - `/api/morechildren.json`
    - `/api/info` and `/api/info.json`
    - the Atom feed of a search, `search.rss`, also with an `Accept` header for a feed
- With the cookie, these endpoints answer HTTP 200:
    - a thread `.json` and a comment permalink `.json`
    - `message/inbox.json` and `message/unread.json`
    - the Atom feeds `new.rss`, `<sort>.rss` and a thread `.rss`
    - the HTML pages of old.reddit.com, the search page `/r/<sub>/search` too
    - `/api/morechildren` without `.json`, with GET or POST
- After an HTTP 403, `search` reads the HTML search page of old.reddit.com, and then `search.rss`. A post of the HTML page has a `score` and a `num_comments`, but no `url` and no `is_self`. Each post is a `div` with the class `search-result-link` and the attribute `data-fullname="t3_..."`.
- After an HTTP 403, `read_listing` reads the `.rss` feed. An RSS post has no `score`, no `num_comments` and no `is_self`, so these fields are `None`.
- Each fallback answer has a `note` that names the source and the missing fields.
- The rate limit is about 100 requests in a window of about 10 minutes for the session. Each answer has the headers `x-ratelimit-used`, `x-ratelimit-remaining` and `x-ratelimit-reset`, which gives the seconds to the reset. When the limit is used, Reddit answers HTTP 429 with an empty body.
- When Reddit blocks one more endpoint, first find the endpoints that still answer HTTP 200 with a probe. Then change the client to the most reliable one. For the probe, read this file: [live-checks.md](live-checks.md).
