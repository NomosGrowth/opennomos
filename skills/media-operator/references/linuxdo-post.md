## Create Linux.do Post Workflow

1. Open Linux.do homepage: `agent-browser --auto-connect open "https://linux.do/"`
2. Snapshot interactive elements: `agent-browser --auto-connect snapshot -i`
3. Click "New Topic" button to open the editor (ref based on snapshot)
4. Re-snapshot interactive elements: `agent-browser --auto-connect snapshot -i`
5. Enter title: `agent-browser --auto-connect fill @title-input "{title}"`
6. Select category (required):
   `agent-browser --auto-connect click @category-dropdown`
   `agent-browser --auto-connect snapshot -i`
   `agent-browser --auto-connect click @target-category-option`
7. Add tags (at least 1 recommended):
   `agent-browser --auto-connect click @tag-dropdown`
   `agent-browser --auto-connect snapshot -i`
   `agent-browser --auto-connect fill @tag-search "{tag1}"`
   `agent-browser --auto-connect press Enter`
8. For multiple tags, repeat step 7 (press Enter after each tag)
9. Enter body content: `agent-browser --auto-connect fill @body-input "{content}"`
10. Verify state: `agent-browser --auto-connect snapshot -i`
11. Save draft only — never auto-click "Create Topic":
    - Option: click "Save and Close" (saves as draft)
    - Or leave the page open and prompt the user to manually click "Create Topic"

## Element Reference (examples — always verify with snapshot)

| Element | Function | Description |
|---------|----------|-------------|
| `textbox "Type title, or paste a link here"` | Title input | Post title |
| `listbox "Filter by: xxx"` | Category dropdown | Must select a valid category |
| `menuitemradio "Dev/Resources/..."` | Category option | Click the target category |
| `listbox "Choose at least 1 tag…"` | Tag entry | Opens tag selection |
| `searchbox "Search…"` | Tag search field | Type tag name then press Enter |
| `textbox "Type here..."` | Body input | Markdown editor area |
| `button "Save and Close"` | Draft button | Safe exit, avoids accidental publish |
| `button "Create Topic"` | Publish button | Never auto-click |

## Notes

- Linux.do usually requires selecting a category first; some categories require at least 1 tag
- Categories and tags are dynamic lists — always re-run `snapshot -i` after expanding them to get fresh refs
- Recommended tag workflow: type keyword → press Enter; if suggestions appear, click the desired option directly
- If a Cloudflare "Just a moment..." page appears, wait or navigate from the homepage first then click "New Topic"
- Strictly follow the rule: only reach draft or ready-to-publish state — never auto-click "Create Topic"
