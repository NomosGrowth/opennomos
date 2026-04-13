---
name: media-operator
description: Use agent-browser to help users publish content to social media platforms after the user opens their own logged-in browser session, navigates to the target platform, and enables remote debugging. Activate this skill when the user needs to publish content, push articles, upload posts, or post to social platforms.
disable-model-invocation: false
allowed-tools: Bash(agent-browser:*), Bash(jq:*), Bash(osascript:*) ,Read
---

User input $ARGUMENTS

# Media Operator Skill

Use bash to run agent-browser, following the platform-specific workflows in `references/`, to help the user upload articles and images to the target social platform.

Default operating model:
- the user opens their own logged-in browser session
- the user opens the target platform or compose page
- the user enables remote debugging for that browser session
- the agent attaches to that existing browser and takes over from there

# Rules

1. Default to the user's existing browser session. Ask the user to open the target platform in their own logged-in Chrome window and enable remote debugging before any automation.
2. Verify the debug port first with `curl -s http://localhost:9222/json/version`; if that fails, also check `curl -s http://127.0.0.1:9222/json/version`.
3. Use `agent-browser --auto-connect` to connect to the user's browser once the debug port is available.
4. The final action must only be **saving a draft** — never auto-click the "Publish" button. The user must confirm publishing manually.
5. After each action, run `agent-browser snapshot -i` to get fresh element refs, since page state changes may reassign ref numbers.
6. In multi-tab environments, always run `agent-browser tab list` before acting to confirm the active tab, then `agent-browser tab <n>` to switch to the target tab.
7. After switching, verify the current page URL with `agent-browser eval "window.location.href"` to prevent operating on the wrong tab.
8. When `agent-browser click` reports "blocked by another element", use JS fallback: `agent-browser eval "document.querySelector('...').click()"`.
9. Do not launch a separate browser profile by default. Only use that fallback if the user explicitly agrees or no debuggable logged-in browser session is available.

# User Browser Hand-off Procedure

Prefer this sequence over launching a new browser:

1. Ask the user to open their own Chrome window and log in to the target platform first.
2. Ask the user to open the target compose/editor page in that same browser session.
3. Ask the user to enable remote debugging for that session:
   - Chrome 144+: open `chrome://inspect/#remote-debugging` and enable remote debugging there
   - If the user already launched Chrome with a debug port, that is also acceptable
4. Verify the port:
   - `curl -s http://localhost:9222/json/version`
   - if empty, retry `curl -s http://127.0.0.1:9222/json/version`
5. If the port is available, attach to the user's browser directly with `agent-browser --auto-connect`.
6. If the port is not available, stop and ask the user to re-check remote debugging instead of silently replacing their browser session.

Fallback only when the user explicitly approves it:

1. Launch a separate profile with remote debugging:
   - `nohup /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --remote-debugging-port=9222 --user-data-dir=/tmp/chrome-debug-profile > /tmp/chrome-debug.log 2>&1 &`
2. Wait and verify:
   - `sleep 5 && curl -s http://127.0.0.1:9222/json/version`
3. Warn clearly that a separate profile has no login state and may trigger platform warnings such as "insecure browser" or repeated login checks.

# Core Workflow

1. Confirm publishing details: target platform, content type, content source (file path / direct input / AI-generated), title, hashtags, and whether the user already has a logged-in browser session open.
2. Ask the user to open the target platform in their own browser and enable remote debugging using the User Browser Hand-off Procedure above.
3. Attach to that existing browser session.
4. Read the matching platform and content type workflow from `references/`.
5. Run `agent-browser tab list` to confirm the tab list, switch to the correct tab, and verify the current URL.
6. Execute the workflow steps strictly in order.
7. After each step, verify the page URL and element state — use JS fallback if blocked.
8. Stop at **draft saved**. Never auto-publish.

# Self-Evolution

## Fix and Verify Workflow

Web page interactions may change, causing workflows in `references/` to break. Fix them as follows:

1. Run `agent-browser snapshot` to inspect the current page elements
2. If lookup fails, run `agent-browser eval "js"` to inspect specific HTML elements
3. After verifying the correct interaction path, edit the corresponding workflow file in `references/`

## Adding a New Platform

When the user asks to add a new platform:

1. Use existing workflows in `references/` as a template
2. Run `agent-browser --help` to check available commands and the agent-browser skill
3. Prefer testing against the user's own browser session with remote debugging enabled; only fall back to a separate browser if the user explicitly approves it
4. Create the new platform's workflow file in `references/` and add a link in the References section below

# References

## Xiaohongshu
- `Image Post`: see [xiaohongshu-image-post](./references/xiaohongshu-image-post.md) — workflow for publishing short image posts
- `Long Article`: see [xiaohongshu-long-article](./references/xiaohongshu-long-article.md) — workflow for publishing long-form articles

## X (Twitter)
- `Tweet`: see [x-tweet](./references/x-tweet.md) — workflow for posting tweets

## Weibo
- `Weibo Post`: see [weibo-post](./references/weibo-post.md) — workflow for posting to Weibo

## WeChat Official Account
- `WeChat Article`: see [wechat-article](./references/wechat-article.md) — workflow for publishing WeChat Official Account articles

## Juejin
- `Juejin Article`: see [juejin-article](./references/juejin-article.md) — workflow for publishing Juejin articles and auto-saving drafts

## Zhihu
- `Zhihu Thought`: see [zhihu-thought](./references/zhihu-thought.md) — workflow for posting Zhihu thoughts

## Linux.do
- `Linux.do Post`: see [linuxdo-post](./references/linuxdo-post.md) — workflow for creating posts (with category and tag selection)
