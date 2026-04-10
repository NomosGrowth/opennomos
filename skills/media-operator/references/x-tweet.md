## Post Tweet Workflow

### Prerequisites
1. List all tabs: `agent-browser tab list`
2. Open X compose page: `agent-browser --auto-connect open "https://x.com/compose/post"`
3. Find the X tab index with `agent-browser tab list`, switch with `agent-browser tab <n>`
4. Confirm current page: `agent-browser eval "window.location.href"` — should be `https://x.com/compose/post`

### Fill Content
5. Snapshot interactive elements: `agent-browser snapshot -i`
6. Enter tweet text: `agent-browser fill @e6 "{tweet content}"` (ref number based on actual snapshot)
7. (Optional) Upload image: `agent-browser upload @e10 "{image path}"` (ref based on snapshot)
8. Wait for upload: `agent-browser wait 2000`
9. Verify state: `agent-browser snapshot -i`

### Save Draft
10. Click the Drafts button (ref based on snapshot, usually labeled "Drafts")
11. If `agent-browser click` reports "blocked by another element", use JS fallback:
    ```
    agent-browser eval "Array.from(document.querySelectorAll('button')).find(b => b.textContent.includes('Drafts')).click()"
    ```
12. A Save/Discard confirmation dialog appears — click Save via JS:
    ```
    agent-browser eval "Array.from(document.querySelectorAll('button')).find(b => b.textContent.trim() === 'Save').click()"
    ```
13. Prompt the user to manually confirm publishing. Never auto-click the "Post" button.

## Element Reference

> Note: ref numbers change with page state. Always use `snapshot -i` for the latest refs.

| Element | Function |
|---------|----------|
| Drafts button | Save to drafts |
| Post text input | Tweet text input field |
| Add photos or video | Media upload |
| Choose File | File upload |
| Add a GIF | GIF picker |
| Post button | Publish button (never auto-click) |

## Common Issues

### Element blocked by another element
X pages often have full-screen overlay layers (fixed position divs) that cause `agent-browser click` to fail. Solution:
1. Use `agent-browser eval` to inspect the blocking element
2. Use JS `document.querySelector('...').click()` to trigger the click directly

### Wrong tab after switching
After `agent-browser tab <n>`, subsequent commands may still execute on the old tab. Solution:
- Immediately verify with `agent-browser eval "window.location.href"`
- If the URL is wrong, run `agent-browser tab list` again and re-switch

### Promotional banner blocking
X may show banners like "Try out the new article composer" that block action buttons. Solution:
- Close via JS: `agent-browser eval "Array.from(document.querySelectorAll('button')).filter(b => b.textContent.includes('Close'))[1].click()"`

## Notes

- Tweet character limit: **280 characters** for regular users, more for Premium users
- Supported image formats: JPG, PNG, GIF — up to 4 images
- Hashtags: include `#hashtag` directly in the tweet text
