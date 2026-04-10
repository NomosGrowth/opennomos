## Xiaohongshu Long Article Workflow

1. Open the article publish page: `agent-browser --auto-connect open "https://creator.xiaohongshu.com/publish/publish?source=official&from=tab_switch&target=article"`
2. Snapshot interactive elements: `agent-browser snapshot -i`
3. Switch to long article mode: `agent-browser click @e1`
4. Enter title: `agent-browser fill @e11 "{title}"`
5. Copy file content to clipboard: `cat "{file path}" | pbcopy`
6. Click the body editor: `agent-browser click ".ProseMirror"`
7. Paste clipboard content: `agent-browser press "Meta+v"`
8. Replace Markdown image references (see Image Upload section below)

---

8. Auto-format: `agent-browser click @e13`
9. Snapshot interactive elements: `agent-browser snapshot -i`
10. Next step: `agent-browser click @e15`
11. Snapshot interactive elements: `agent-browser snapshot -i`
12. Enter description: `agent-browser type ".ProseMirror" "{description}"`
13. Add topic: `agent-browser type ".ProseMirror" " #{topic}"`, then `agent-browser press "Enter"` to confirm
14. Save draft and leave: `agent-browser click @e13`

## Element Reference

### Editor Page (after clicking "New Creation")

| Element | Selector | Function |
|---------|----------|----------|
| Title field | `@e11` | Enter title |
| Body field | `.ProseMirror` | Enter body content |
| Upload image | `@e9` | Toolbar image button |
| Auto-format | `@e13` | Format the article |
| Save draft | `@e14` | Save and leave |

### Publish Settings Page (after clicking "Next")

| Element | Function |
|---------|----------|
| `.ProseMirror` | Description/summary input |
| `.ProseMirror` | Topic input |
| @e13 | Save draft and leave |
| @e14 | Publish button (never auto-click) |

## Image Upload

### Method 1: Paste Image Directly

```bash
# 1. Copy image to clipboard (macOS)
osascript -e 'set the clipboard to (read (POSIX file "{absolute image path}") as «class PNGf»)'

# 2. Click the body editor
agent-browser click ".ProseMirror"

# 3. Paste the image
agent-browser press "Meta+v"
```

### Method 2: Replace Markdown Image References

After pasting Markdown text, replace `![alt](path)` with actual images:

```bash
# 1. Find and select the paragraph containing the image reference
agent-browser eval "
const ps = document.querySelectorAll('.ProseMirror p');
for (let i = 0; i < ps.length; i++) {
  if (ps[i].innerText.startsWith('![')) {
    const range = document.createRange();
    range.selectNodeContents(ps[i]);
    const sel = window.getSelection();
    sel.removeAllRanges();
    sel.addRange(range);
    break;
  }
}
"

# 2. Delete the selected image reference text
agent-browser press "Backspace"

# 3. Copy the actual image to clipboard and paste
osascript -e 'set the clipboard to (read (POSIX file "{image path}") as «class PNGf»)'
agent-browser press "Meta+v"
```

Repeat the above steps for all image references.
