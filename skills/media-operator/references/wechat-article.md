## WeChat Official Account Article Workflow

1. Open the WeChat MP backend: `agent-browser --auto-connect open "https://mp.weixin.qq.com/"`
2. Wait for page load: `agent-browser --auto-connect wait --load networkidle`
3. Click "Article" to create new: `agent-browser --auto-connect eval "document.querySelector('.new-creation__menu-title').click()"`
4. Wait for editor to load: `agent-browser --auto-connect wait --load networkidle`
5. Enter title: `agent-browser --auto-connect fill @e4 "{title}"`
6. Click author field and select default author: `agent-browser --auto-connect click @e5`, then `agent-browser --auto-connect find text "{author name}" click`
7. Click the body editor: `agent-browser --auto-connect click "#ueditor_0"`
8. Enter body content: `agent-browser --auto-connect keyboard type "{body content}"` (use `agent-browser --auto-connect press "Enter"` for line breaks between paragraphs)
9. Snapshot state: `agent-browser --auto-connect snapshot`
10. Add cover image (hover to trigger menu): `agent-browser --auto-connect find text "拖拽或选择封面" hover`
11. Click "Select from image library": `agent-browser --auto-connect eval "document.querySelector('#js_cover_null .js_imagedialog').click()"`
12. Upload cover image: `agent-browser --auto-connect upload "input[type=file][accept*='image/bmp']" "{image path}"`
13. Wait for upload: `agent-browser --auto-connect wait 2000`
14. Click "Next": `agent-browser --auto-connect click @e26`
15. Click "Confirm" to finish cropping: `agent-browser --auto-connect click @e21`
16. Declare original content: `agent-browser --auto-connect find text "未声明" click`, if agreement is already checked then directly `agent-browser --auto-connect click @e24`
17. Save draft: `agent-browser --auto-connect click @e14` ("Save as Draft" button)
18. Prompt the user to confirm and manually publish in the WeChat MP backend. Never auto-click "Publish".

## Element Reference

| Element | Function |
|---------|----------|
| `.new-creation__menu-title` | New article entry point |
| `@e4` | Title input field |
| `@e5` | Author input field |
| `#ueditor_0` | Body editor area |
| `#js_cover_null .js_imagedialog` | Cover "Select from image library" button |
| `input[type=file][accept*='image/bmp']` | Image library upload file input |
| `@e12` / "Publish" | Publish button (never auto-click) |
| `@e14` / "Save as Draft" | Save draft button |

## Notes

- The body editor is UEditor, not ProseMirror — use `keyboard type` for input, not `fill`
- Cover upload requires hovering over the cover area to trigger the menu, then selecting "Select from image library"
- The image library dialog has two `input[type=file]` elements — use the one with `accept*='image/bmp'` for cover upload
- The original content declaration dialog has the agreement pre-checked — just click confirm
- After clicking the author field, a "Recently used" dropdown appears — click the desired option directly
- Only save as draft. User must manually confirm publishing.
