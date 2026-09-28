# Miro MCP Tool Reference

Quick reference for the Miro MCP server tools used by the Miro Import skill.

---

## Core Canvas Tools

These are the primary tools for creating and managing board content via SVG.

### canvas_get_canvas_composer_skill
- **Purpose**: Returns the current SVG format specification. Must be called once per session before writing.
- **When**: First action before any board creation/update
- **Parameters**: None
- **Returns**: SVG spec document describing all supported element types, attributes, and patterns

### canvas_create_from_svg
- **Purpose**: Create multiple board items from an SVG document
- **When**: Pushing a new whiteboard design to a board
- **Parameters**: Board ID + SVG content string
- **Returns**: Created item IDs and status
- **Notes**: This is the main tool for importing Whiteboarding Champion output

### canvas_read_as_svg
- **Purpose**: Read existing board items and return them as SVG
- **When**: Verifying what was created, reading existing boards for modification
- **Parameters**: Board ID + optional area/frame filter
- **Returns**: SVG document with `data-miro-id` attributes on every item
- **Notes**: Use for verification after creation

### canvas_update_from_svg
- **Purpose**: Apply an SVG document as a diff (creates, updates, and deletes)
- **When**: Modifying an existing board -- updating content, adding sections, fixing layout
- **Parameters**: Board ID + SVG diff content
- **Returns**: Change summary (created/updated/deleted counts)
- **Notes**: Preserves items not mentioned in the diff. Include `data-miro-id` on items to update

### canvas_search
- **Purpose**: Find what is on a board before reading it
- **When**: Exploring existing board content, finding specific items
- **Parameters**: Board ID + search query (optional: overview mode for high-level pass)

### canvas_load_format_skill
- **Purpose**: Load format-specific guidance on top of the base SVG spec
- **When**: Creating diagrams (Mermaid notation), specific widget types
- **Parameters**: Format name (e.g., "diagram")

## Board Management Tools

### board_create
- **Purpose**: Create a new Miro board
- **Parameters**: Board name, description (optional), team ID (optional)
- **Returns**: Board ID and URL
- **Notes**: Board URL format: `https://miro.com/app/board/<boardId>/`

### board_search_boards
- **Purpose**: Search and list boards accessible to the current user
- **Parameters**: Search query string
- **Returns**: List of boards with IDs, names, and URLs

## Image Tools

### image_create
- **Purpose**: Create an image item on a board
- **Parameters**: Board ID + image URL or upload token
- **Notes**: Use for logos, photos, or visual assets on the board

### image_get_upload_url
- **Purpose**: Get a temporary presigned POST URL for image upload
- **Returns**: Upload URL (single-use)

### image_get_url
- **Purpose**: Get download URL for an existing image on a board

## Comment Tools

### comment_list_comments
- **Purpose**: List comments from a board or specific item

### comment_reply
- **Purpose**: Add a reply to an existing comment thread

### comment_resolve
- **Purpose**: Resolve or unresolve a comment thread

---

## SVG Format Tips

### Element Types Available
The SVG spec from `canvas_get_canvas_composer_skill` defines these, but common patterns include:

- **Frames**: Grouping containers with titles. Use for zones/sections
- **Sticky notes**: Colored rectangles with text, standard Miro palette colors
- **Shapes**: Rectangle, circle, diamond, triangle, cloud, arrow shapes with fill/stroke
- **Text**: Free text with font size, weight, alignment
- **Connectors**: Lines/arrows between elements with start/end points and labels
- **Cards**: Structured items with title, description, due date, assignee
- **Mermaid diagrams**: Native diagram widgets authored in Mermaid syntax inside SVG
- **Tables**: Structured data with typed columns and view options
- **Documents**: Rich text content blocks
- **Code widgets**: Syntax-highlighted code blocks
- **Workshop widgets**: Polls, voting, timers, flip cards, etc.

### Positioning
- Miro uses a coordinate system with (0,0) at center of board
- X increases to the right, Y increases downward
- All positions are in logical units (roughly 1 unit = 1 pixel at 100% zoom)
- Frames define relative coordinate spaces for their children

### Z-Ordering
- Creation order determines z-index
- Draw background elements first, then foreground
- Connectors drawn before nodes will sit behind them

### Sticky Note Colors (Miro palette)
Standard Miro sticky note colors that agents should use:
- Yellow: default, general purpose
- Blue: information, data, inputs
- Green: positive, success, opportunities
- Pink/Red: problems, risks, hot spots
- Orange: events, actions, warnings
- Purple: policies, decisions, rules
- Gray: neutral, completed, archived

---

## Board Organization Best Practices

1. **Use frames for major sections**: Every logical zone should be a named frame
2. **Consistent spacing**: Keep 40-80 units between frames, 10-20 between items within frames
3. **Left-to-right flow**: Primary reading direction should be left-to-right for Western audiences
4. **Header frame**: Always include a title frame at top-left with board name, date, and context
5. **Legend**: If using color coding, include a legend frame explaining what each color means
6. **Instructions**: For workshop boards, include facilitator instructions in each zone
7. **Breathing room**: Don't pack items too tightly; leave space for participants to add content

---

## Troubleshooting

### MCP Not Connected
**Symptom**: Tools return "MCP server not found" or similar
**Fix**: 
1. Open Cursor Settings > MCP
2. Find Miro MCP or add it manually:
   ```json
   {
     "mcpServers": {
       "miro-mcp": {
         "url": "https://mcp.miro.com/",
         "disabled": false,
         "autoApprove": []
       }
     }
   }
   ```
3. Click Connect and complete the OAuth flow
4. Select the correct Miro team

### Authentication Errors
**Symptom**: "Unauthorized" or "Token expired" errors
**Fix**:
1. Re-authenticate: disconnect and reconnect the Miro MCP in Cursor settings
2. Ensure you selected the correct Miro team (MCP is team-specific)
3. Check that your Miro account has board creation permissions

### Board Not Found
**Symptom**: `board_search_boards` returns empty or board ID not found
**Fix**:
1. Verify the board exists in the team you authenticated with
2. MCP is team-specific -- if the board is in a different team, re-auth with that team
3. Check board sharing permissions

### SVG Creation Fails
**Symptom**: `canvas_create_from_svg` returns errors
**Fix**:
1. Always call `canvas_get_canvas_composer_skill` first to get the current spec
2. Validate SVG structure against the spec
3. Check for unsupported element types or attributes
4. Try creating a smaller subset of elements first to isolate the issue
5. Ensure board ID is correct and you have write permissions

### Rate Limiting
**Symptom**: "Rate limit exceeded" or 429 errors
**Fix**:
1. Miro MCP has daily usage limits depending on your plan
2. Space out requests -- don't rapid-fire multiple create calls
3. Batch content into fewer, larger SVG payloads rather than many small ones
4. Check https://developers.miro.com/docs/mcp-usage-and-daily-limits.md for current limits
