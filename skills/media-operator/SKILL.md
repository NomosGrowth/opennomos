---
name: media-operator
description: Use agent-browser to help users publish content to social media platforms. Activate this skill when the user needs to publish content, push articles, upload posts, or post to social platforms.
disable-model-invocation: false
allowed-tools: Bash(agent-browser:*), Bash(jq:*), Bash(osascript:*) ,Read
---

User input $ARGUMENTS

# Media Operator Skill

Use bash to run agent-browser, following the platform-specific workflows in `references/`, to help the user upload articles and images to the target social platform.

# Rules

1. Start the browser (see Chrome Startup Procedure below)
2. Use `agent-browser --auto-connect` to automatically connect to the user's browser
3. The final action must only be **saving a draft** — never auto-click the "Publish" button. The user must confirm publishing manually.
4. After each action, run `agent-browser snapshot -i` to get fresh element refs, since page state changes may reassign ref numbers
5. In multi-tab environments, always run `agent-browser tab list` before acting to confirm the active tab, then `agent-browser tab <n>` to switch to the target tab
6. After switching, verify the current page URL with `agent-browser eval "window.location.href"` to prevent operating on the wrong tab
7. When `agent-browser click` reports "blocked by another element", use JS fallback: `agent-browser eval "document.querySelector('...').click()"`

# Chrome Startup Procedure

The user's default Chrome profile often cannot bind the debug port (occupied by an existing instance). Follow this sequence:

1. Check the port first: `curl -s http://localhost:9222/json/version`
2. If the port is available, connect directly
3. If unavailable, kill all Chrome instances: `killall "Google Chrome" 2>/dev/null; sleep 3`
4. Launch with a separate profile: `nohup /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --remote-debugging-port=9222 --user-data-dir=/tmp/chrome-debug-profile > /tmp/chrome-debug.log 2>&1 &`
5. Wait and verify: `sleep 5 && curl -s http://localhost:9222/json/version`
6. Note: a separate profile has no login state — prompt the user to log in to the target platform first
7. If the user already has Chrome running with a debug port (launched by the user), connect directly

# Core Workflow

1. Confirm publishing details via AskUserQuestion tool: target platform (or **add new platform**), content type, content source (file path / direct input / AI-generated), title, hashtags
2. Connect to the browser following the Chrome Startup Procedure above
3. Read the matching platform and content type workflow from `references/`
4. Run `agent-browser tab list` to confirm the tab list, switch to the correct tab
5. Execute the workflow steps strictly in order
6. After each step, verify the page URL and element state — use JS fallback if blocked

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
3. Only after launching the browser and fully testing the new platform's interaction path step by step, confirming each action works correctly
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
