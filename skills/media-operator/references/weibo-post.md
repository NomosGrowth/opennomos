## Post Weibo Workflow

1. Open Weibo homepage: `agent-browser --auto-connect open "https://weibo.com"`
2. Snapshot interactive elements: `agent-browser snapshot -i`
3. (Optional) Click the input field: `agent-browser click @e{input ref}`
4. Enter Weibo content: `agent-browser fill @e{input ref} "{content}"`
5. (Optional) Upload image: `agent-browser upload "input[type='file']" "{image path}"`
6. (Optional) Add topic: include `#topic#` format directly in the content (requires `#` on both sides)
7. Verify state: `agent-browser snapshot -i`
8. Prompt the user to manually click the "Send" button. Never auto-click.

## Element Reference

| Element | Function | Description |
|---------|----------|-------------|
| @e36 | Content input field | Placeholder: "What's new to share?" |
| @e37/e38 | Send button | Becomes active after entering content |
| input[type='file'] | File upload element | For uploading images and videos |

## Notes

- **Character limit**: 140 characters for regular users, 2000 for VIP users
- **Supported image formats**: JPG, PNG, GIF, HEIF, HEIC — up to 9 images
- **Supported video formats**: MP4, M4V, MKV, FLV
- **Topic tag format**: `#topic content#` (requires `#` on both sides)
- **Ref changes**: always re-run `agent-browser snapshot -i` after each action
- **No draft feature**: Weibo's simple compose box has no draft save — just leave the content without clicking Send

## Troubleshooting

1. **Ref changes**: re-run `agent-browser snapshot -i` after each action to get fresh refs
2. **Upload failure**: check image format and size — single image must be under 5MB
3. **Content restricted**: Weibo has sensitive word filtering — advise user to review before posting
4. **Choose File button appears after upload**: this is normal behavior indicating the file upload was triggered
