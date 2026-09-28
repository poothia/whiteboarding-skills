---
name: miro-import
description: >-
  Import whiteboard designs to Miro boards via the official Miro MCP server.
  Takes SVG output from the Whiteboarding Champion skill (or any compatible SVG)
  and creates it on a Miro board using canvas tools. Handles board creation,
  SVG upload, verification, and incremental updates. Also guides first-time
  setup of the Miro MCP connection in Cursor. Understands Red Hat adoption
  board structure (Their World -> Pain -> Solution -> Easy Path). Use when
  the user wants to push a whiteboard to Miro, import a design to Miro,
  create a Miro board from SVG, or set up the Miro MCP connection.
---

# Miro Import

Import whiteboard designs to Miro boards using the Miro MCP server.

## Prerequisites: Miro MCP Setup

Before importing, the Miro MCP server must be connected to Cursor. Check if it's available by looking for Miro tools in the dynamic tool namespace. If tools like `board_create` or `canvas_create_from_svg` are not available, guide the user through setup.

### First-Time Setup

1. **Install the Miro MCP Plugin**
   - Open Cursor Settings (Cmd+, on Mac, Ctrl+, on Windows/Linux)
   - Navigate to the MCP section
   - Search for "Miro" in the marketplace and click Install
   - Alternatively, add the configuration manually by adding this to the MCP config:
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

2. **Authenticate with Miro**
   - Click "Connect" on the Miro MCP entry
   - A browser window opens with Miro's OAuth flow
   - Log in to your Miro account if not already logged in
   - **Select the correct team** -- Miro MCP is team-specific. Choose the team where you want boards created
   - Click "Allow" to grant permissions
   - You'll see "Authentication successful" and be redirected back to Cursor

3. **Verify the Connection**
   - Ask the agent: "Search my Miro boards" -- this should call `board_search_boards` and return results
   - If it works, the connection is ready
   - If it fails, see [reference.md](reference.md) troubleshooting section

### Supported Miro Plans
Miro MCP works on all plans including free teams. Note that free plans have a limit of 3 team boards.

---

## Import Workflow

### Step 1: Locate the SVG

Check for SVG files produced by the Whiteboarding Champion skill:
- Look in `whiteboard-output/` directory in the workspace (typical naming: `{customer}-{topic}.svg`)
- The user may also provide a direct file path
- If no SVG exists, suggest the user run the Whiteboarding Champion skill first

Read the SVG file and validate it has content (not empty, has valid SVG structure).

**Red Hat board structure awareness**: Boards from the Whiteboarding Champion follow a 4-act story flow: Their World (current state architecture) -> The Pain (pain stickies on architecture) -> The Solution (Red Hat product architecture) -> The Easy Path (adoption timeline + before/after). They also have a Discussion Zone at the bottom. When verifying, check that all 4 acts and the discussion zone are present.

### Step 2: Get the SVG Spec

Call the Miro MCP tool `canvas_get_canvas_composer_skill` to retrieve the current SVG format specification. This must happen once per session before any board creation.

Read the returned spec carefully. It defines:
- Supported element types and their SVG representation
- Required attributes for each element type
- Positioning and sizing conventions
- Color and styling options

If the SVG from the Whiteboarding Champion doesn't perfectly match the spec, adapt it:
- Adjust element types to match supported Miro elements
- Convert any unsupported elements to the closest supported alternative
- Ensure all required attributes are present

### Step 3: Create or Select Board

Ask the user using AskQuestion:
- **Create a new board** -- suggest a name based on the SVG filename: `{Customer} - {Topic} Adoption Board` (e.g., "Acme Bank - GitOps Compliance Adoption Board")
- **Add to an existing board** -- search for it by name using `board_search_boards`

If creating a new board:
1. Call `board_create` with the board name and optional description
2. Record the returned board ID and URL
3. Tell the user the board URL

If using an existing board:
1. Call `board_search_boards` with the user's search term
2. Present matching boards and let the user choose
3. Optionally call `canvas_read_as_svg` to see what's already on the board
4. Confirm with the user before adding content (to avoid cluttering an existing board)

### Step 4: Push SVG to Miro

Call `canvas_create_from_svg` with:
- The board ID (from Step 3)
- The SVG content (from Step 1, adapted per Step 2 if needed)

If the SVG is very large (many elements), consider:
- Splitting into logical sections and creating in order (backgrounds first, then content, then connectors)
- This preserves correct z-ordering

### Step 5: Verify

Call `canvas_read_as_svg` on the board to verify creation:
1. Count the created elements
2. Spot-check that key sections/frames exist
3. Report to the user:
   - Total elements created
   - Board URL (clickable link)
   - Any elements that failed or were skipped
   - Screenshot or summary of what's on the board

### Step 6: Report

Give the user a summary:
```
Board created successfully!
- Board: [Board Name](https://miro.com/app/board/<boardId>/)
- Elements: X frames, Y sticky notes, Z shapes, W connectors
- Status: All elements created successfully

You can open the board in Miro to review and customize further.
```

---

## Incremental Updates

If the user wants to modify the board after initial creation:

1. **Read current state**: Call `canvas_read_as_svg` to get the current board as SVG
2. **Identify changes**: Compare with the desired state
3. **Apply diff**: Call `canvas_update_from_svg` with only the changes
   - Include `data-miro-id` attributes on items to update (from the read step)
   - New items without `data-miro-id` will be created
   - Items in the diff with `data-miro-id` will be updated
4. **Verify**: Read again and confirm changes applied

Common update scenarios:
- **Add a section**: Create new SVG with just the new frame and its contents
- **Change text**: Read the item, modify text, send update with `data-miro-id`
- **Rearrange**: Read items, change positions, send update
- **Delete**: Not directly supported via SVG diff -- inform user to delete in Miro UI

---

## Error Recovery

If `canvas_create_from_svg` fails:
1. Check the error message for specific issues
2. Try a smaller subset of the SVG (e.g., just the frames first)
3. Verify the SVG matches the spec from `canvas_get_canvas_composer_skill`
4. If persistent, fall back to creating elements in stages:
   - First: frames and shapes (structural elements)
   - Second: text and sticky notes (content)
   - Third: connectors and arrows (relationships)

If the Miro MCP is completely unavailable:
1. Tell the user the SVG file is ready at the workspace path
2. Suggest they can:
   - Set up the Miro MCP connection (see Prerequisites above)
   - Manually import using Miro's interface
   - Use the Miro REST API with a separate script

---

## Additional Resources

- For Miro MCP tool details and troubleshooting, see [reference.md](reference.md)
- For creating whiteboards, use the Whiteboarding Champion skill
- Miro MCP documentation: https://developers.miro.com/docs/miro-mcp.md
- Miro MCP tools list: https://developers.miro.com/docs/miro-mcp-tools.md
