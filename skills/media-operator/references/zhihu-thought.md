## Post Zhihu Thought Workflow

1. Open Zhihu homepage: `agent-browser --auto-connect open "https://www.zhihu.com/"`
2. Snapshot interactive elements: `agent-browser snapshot -i`
3. Click "Post Thought" button: `agent-browser click @e25` (ref may change — verify from snapshot)
4. Snapshot the compose UI: `agent-browser snapshot -i`
5. Enter title: `agent-browser fill @e20 "{title}"`
6. Enter thought content: `agent-browser type ".public-DraftEditor-content" "{content}"`
7. (Optional) Upload image: click the image button in the toolbar or paste from clipboard
   ```bash
   # Copy image to clipboard (macOS) then paste
   osascript -e 'set the clipboard to (read (POSIX file "{image path}") as «class PNGf»)'
   agent-browser click ".public-DraftEditor-content"
   agent-browser press "Meta+v"
   ```
8. Verify state: `agent-browser snapshot -i`
9. Close the compose UI to auto-save draft: `agent-browser press "Escape"`
10. Prompt the user to open their drafts and manually publish. Never auto-click "Publish".

## Element Reference

| Element | Function | Description |
|---------|----------|-------------|
| @e25 | "Post Thought" button | Homepage button, ref may change |
| @e20 | Title input field | textarea, placeholder="Title" |
| .public-DraftEditor-content | Content editor | contenteditable div, uses Draft.js editor |
| @e27 | Publish button | Changes from disabled to enabled after entering content |

## Notes

- Title is required — must be filled before publishing
- The content editor uses Draft.js — it's a contenteditable div
- Pressing Escape closes the compose UI and auto-saves content to drafts
- Drafts can be viewed and edited from the "Drafts" link
- Images can be uploaded by pasting from clipboard
- Ref numbers change with page state — always use `agent-browser snapshot -i` after each action

## Login Issues

If automated login to Zhihu fails, use this manual approach:

```bash
# 1. Launch Chrome in debug mode
open "/Applications/Google Chrome for Testing.app" --args --remote-debugging-port=9222

# 2. Get WebSocket address
sleep 2 && curl -s http://localhost:9222/json/version

# 3. Connect to browser
agent-browser connect "ws://localhost:9222/devtools/browser/xxx"

# 4. Manually log in to Zhihu, then save state
agent-browser state save ~/my-state.json
```

## Draft Management

- Pressing Escape to close the compose UI auto-saves content as a draft
- All drafts can be accessed via the "Drafts" link on the homepage sidebar
- Drafts can be continued or published directly
