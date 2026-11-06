---
name: thread-reading
description: "Read this file before you change read_post, thread_search or the expansion of the \"more\" stubs, or before you explain why a comment is missing."
metadata:
  type: reference
---

## How a full thread loads

- A thread `.json` with `limit=500` gives up to about 500 comments in one request. Reddit hides the rest behind `kind: "more"` stubs.
- A stub with `children` ids is expanded with `POST /api/morechildren`, with up to 100 ids in one request. A stub with no `children` is a "continue this thread" link. The client reads it from the permalink `.json` of its parent comment.
- Keep a deleted comment in the tree. Its replies are real comments, and its subtree is lost when the node is dropped.

## The answer of /api/morechildren

- With cookie auth, `/api/morechildren` answers only with rendered HTML, and its `.json` variant answers HTTP 403. `_html_thing_to_json` converts each thing to the shape of the thread `.json`.
- Each thing has `data.id`, `data.parent`, `data.contentText` and `data.content`. It has no `name`, no `author` and no `body`.
- `contentText` is the markdown source of the body. It differs from the `.json` body only for an embedded image: `contentText` has `![img](<media id>)`, and the `.json` body has the full URL. The client takes that URL from an `href` in `content`.
- `content` is escaped HTML. After `html.unescape`, it has `data-author`, `class="score unvoted" title="N"` and `<time datetime="...">`. A deleted comment has no `data-author` and no score.
- A "more" thing has its ids only in `onclick="return morechildren(this, '<link id>', '<sort>', '<id,id,...>', ...)"`.

## Lost and hidden comments

- One answer holds about 100 things at most, with the descendants. Reddit lists some of the cut ids in new "more" things, but not all of them. So the client asks once more for each asked id that did not come back. An id that does not come back twice goes to `hidden_by_reddit`.
- `num_comments` also counts comments that Reddit does not show. On a thread with 1284 comments, about 90 ids never came back. The permalink of 25 of 30 of them was an empty listing, and 5 of them were visible comments. Thus the reader loses about 1 % of the visible comments of a large thread. To get them, read the permalink of each lost id, at the cost of one request for each.

## Limits

- A read of a thread with 1284 comments costs about 20 requests and 35 seconds. Keep `MAX_THREAD_REQUESTS` and `RATE_RESERVE`, so that one read cannot use the whole rate limit window of the session.
- Wait `REQUEST_DELAY` only right before a real request, not for each stub. A large thread has more than 100 stubs, and most of them go into a batch with no request of their own.
- MCP clients can cut a long tool answer. A full tree of 110 comments is about 40 KB of compact JSON, and a tree of 1200 comments is about 400 KB. Keep the tool answers compact JSON.
- `read_post` and `thread_search` run in `asyncio.to_thread`, because the waits of one read take many seconds and must not stop the event loop of the server.
