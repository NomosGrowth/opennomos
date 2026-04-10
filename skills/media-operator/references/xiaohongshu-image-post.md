## Xiaohongshu Image Post Workflow

1. Open the image post page: `agent-browser --auto-connect open "https://creator.xiaohongshu.com/publish/publish?source=official&from=tab_switch&target=image"`
2. Snapshot interactive elements: `agent-browser snapshot -i`
3. Upload file: `agent-browser upload @e1 "{image path}"`
4. Enter title: `agent-browser fill @e1 "{title}"`
5. Enter body text: `agent-browser fill ".ProseMirror" "{body content}"`
6. Add topic: `agent-browser type ".ProseMirror" "#{topic}"`, then `agent-browser press "Enter"` to confirm
7. Save draft and leave: `agent-browser click @e13`

## Notes

- Title must not exceed 20 characters
