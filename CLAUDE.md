# Project Context

## Overview
This is an Open Stage Control (OSC) project for controlling REAPER via iPad.
Developer: Alden Hammersmith

## Tech Stack
- **Platform**: Open Stage Control v1.27.3
- **Target Device**: iPad (touch interface)
- **DAW Integration**: REAPER via OSC protocol

## File Locations
- **Git repo**: `/Users/jahammersmith/projects/ipad-osc-surface`
- **Desktop working files**: `/Users/jahammersmith/Desktop/` (for testing)
- **Open Stage Control app**: `/Applications/open-stage-control.app`

## Session Structure

### Current Session: IPAD Surface 5Pages.json
5-page navigation system with:
- **Page_1** (Main Page) - Primary controls
- **Page_2** through **Page_5** - Additional pages for future buttons

### Navigation System
- All 5 tab buttons appear at bottom of every page (same position)
- Current page's button is **magenta** (#fd6dd9)
- Clicking a tab shows that page's panel and hides others

### Panel Visibility Toggle (JavaScript)
```javascript
if (value === 1) {
  send("self", "/EDIT/MERGE", "Page_1", "{visible:false}");
  send("self", "/EDIT/MERGE", "Page_2", "{visible:true, left:0, top:0}");
  // ... hide other pages
}
```

## Working with Sessions

### Opening a Session
```bash
/Applications/open-stage-control.app/Contents/MacOS/open-stage-control --load "/path/to/session.json" &
open "http://127.0.0.1:8080"
```

### Editing Sessions
1. Open in Open Stage Control
2. Use Editor mode (left sidebar)
3. Save changes (Cmd+S)
4. Commit to git

### Creating a New Button
Buttons need these key properties:
```json
{
  "type": "button",
  "id": "unique_button_id",
  "label": "Button Label",
  "top": "50%",
  "left": "10%",
  "width": "15%",
  "height": "8%",
  "mode": "tap",
  "address": "/reaper/action/12345",
  "colorWidget": "auto"
}
```

## Integration with REAPER Scripts

### Planned Workflow
When a Lua script is completed and moved to a permanent folder:
1. Script is added to REAPER Action List
2. A corresponding OSC button can be created
3. Button sends OSC message to trigger the REAPER action
4. Update the tracking spreadsheet with osc_page, button_id, button_name

### OSC Address Format for REAPER Actions
```
/reaper/action/ACTION_ID
```
or for custom scripts:
```
/reaper/action/_SCRIPT_COMMAND_ID
```

## Git Workflow

### After Making Changes
```bash
cd /Users/jahammersmith/projects/ipad-osc-surface
git add .
git commit -m "Description of changes"
git push
```

### Backup Before Major Changes
```bash
cp "session.json" "session_backup_YYYY-MM-DD.json"
```

## Color Scheme
- **Magenta** (#fd6dd9): Current page indicator, highlights
- **Default**: Standard buttons
- **Consider**: Color-coding by function (transport=blue, MIDI=green, etc.)

## Session Notes
*Add notes here as we work together.*

- 2026-01-14: Created 5-page navigation system with working tab switching
