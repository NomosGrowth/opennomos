## Publish Juejin Article Workflow

1. Open Juejin new draft page: `agent-browser --auto-connect open "https://juejin.cn/editor/drafts/new?v=2"`
2. Wait for page load: `agent-browser --auto-connect wait --load networkidle`
3. Snapshot interactive elements: `agent-browser --auto-connect snapshot -i`
4. Enter title: `agent-browser --auto-connect fill @e1 "{title}"`
5. Re-snapshot interactive elements: `agent-browser --auto-connect snapshot -i`
6. Enter body (CodeMirror editor): `agent-browser --auto-connect fill ".CodeMirror textarea" "{body content}"`
7. Press Enter to trigger editor state sync: `agent-browser --auto-connect press Enter`
8. Wait for auto-save: `agent-browser --auto-connect wait 2000`
9. Verify state: `agent-browser --auto-connect snapshot`
10. Confirm the page shows "Saved successfully", then notify the user that the draft has been saved
11. Never auto-click the "Publish" button. User must manually confirm publishing.

## Element Reference

| Element | Function | Description |
|---------|----------|-------------|
| `@e1` | Title input field | Placeholder: "Enter article title..." |
| `.CodeMirror textarea` | Body editor input | Juejin editor's underlying textarea, suitable for `fill` with long text |
| Publish button | Final publish | User must click manually |

## Notes

- The body area may show duplicate `textbox` refs in `snapshot -i` — prefer using the `.CodeMirror textarea` selector
- Always re-run `agent-browser --auto-connect snapshot -i` after any page state change to avoid stale refs
- Default behavior is auto-save draft only — never trigger the publish action
