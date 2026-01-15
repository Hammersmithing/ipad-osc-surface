# Project Context

## Overview
This is an Open Stage Control (OSC) project for controlling REAPER via iPad.
Developer: Alden Hammersmith

## Tech Stack
- **Platform**: Open Stage Control v1.27.3
- **Target Device**: iPad (touch interface)
- **DAW Integration**: REAPER via OSC protocol

## File Locations
- **Git repo (development)**: `/Users/jahammersmith/projects/ipad-osc-surface`
- **REAPER OSC folder (deployment)**: `/Users/jahammersmith/Library/Application Support/REAPER/Alden Hammersmith open-stage-control/`
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

**IMPORTANT:** Always open .json session files in Open Stage Control, NOT in Xcode or other text editors. Use the command below.

### Opening a Session
```bash
pkill -f "open-stage-control" 2>/dev/null; sleep 1
/Applications/open-stage-control.app/Contents/MacOS/open-stage-control \
  --send "127.0.0.1:8000" \
  --osc-port 9000 \
  --port 8080 \
  --load "/Users/jahammersmith/Library/Application Support/REAPER/Alden Hammersmith open-stage-control/IPAD Surface 5Pages.json" &
sleep 2 && open "http://127.0.0.1:8080"
```

**OSC Settings (required for REAPER communication):**
- `--send "127.0.0.1:8000"` - Send OSC to REAPER's listen port
- `--osc-port 9000` - Receive OSC from REAPER on this port
- `--port 8080` - Web interface port

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

## Google Sheets Tracker

### Spreadsheet
- **Name:** Json OSC Tracker
- **URL:** https://docs.google.com/spreadsheets/d/1D6jcJvcr5rz_RCZT8A0skEriLGfi5IhIGrYsnidb5Uw
- **Tab:** Buttons

### Columns
| Column | Description |
|--------|-------------|
| button_id | Unique ID from JSON |
| button_label | Display text |
| page | Page 1-5 |
| osc_address | REAPER action address |
| linked_script | Related Lua script name |
| date_added | When created |
| notes | Any notes |

### API Endpoint
```
https://script.google.com/macros/s/AKfycbx2SdVEimQWwDVQGhPluBlb-sXpVT4b2WH5klLsdxirs9THIXcfQ5PiCfJiDI1hMaEKkQ/exec
```

### Add a Button to Tracker
```bash
curl -L -H "Content-Type: application/json" \
  -d '{
    "tab": "Buttons",
    "button_id": "BUTTON_ID",
    "button_label": "Button Label",
    "page": "1",
    "osc_address": "/reaper/action/ACTION_ID",
    "linked_script": "",
    "date_added": "YYYY-MM-DD",
    "notes": ""
  }' \
  "https://script.google.com/macros/s/AKfycbx2SdVEimQWwDVQGhPluBlb-sXpVT4b2WH5klLsdxirs9THIXcfQ5PiCfJiDI1hMaEKkQ/exec"
```

### Delete a Button from Tracker
```bash
curl -L -H "Content-Type: application/json" \
  -d '{"action": "delete", "tab": "Buttons", "button_id": "BUTTON_ID"}' \
  "https://script.google.com/macros/s/AKfycbx2SdVEimQWwDVQGhPluBlb-sXpVT4b2WH5klLsdxirs9THIXcfQ5PiCfJiDI1hMaEKkQ/exec"
```

## Git Workflow

### After Making Changes (commit, push, and deploy)
```bash
cd /Users/jahammersmith/projects/ipad-osc-surface
git add .
git commit -m "Description of changes"
git push
```

### Deploy to REAPER folder
```bash
cp "/Users/jahammersmith/projects/ipad-osc-surface/IPAD Surface 5Pages.json" "/Users/jahammersmith/Library/Application Support/REAPER/Alden Hammersmith open-stage-control/"
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
- 2026-01-14: Added MainPanel widgets (38 buttons) to Page_1
- 2026-01-14: Reorganized layout: transport centered at top, uniform button grid below
- 2026-01-14: Page_1 has 43 widgets total (38 buttons + 5 nav tabs)
- 2026-01-14: Pages 2-5 are empty (ready for future content)
- 2026-01-14: Set up Google Sheets tracker (Json OSC Tracker) with API integration
- 2026-01-15: Added p1_view_midieditor button (MIDI Editor toggle)
- 2026-01-15: Fixed OSC launch command to include --send and --osc-port flags
- 2026-01-15: Added p1_view_unusedtracks button (Show/Hide Unused Tracks)
- 2026-01-15: Created Page 2 with MIDI velocity tools (11 buttons total)
- 2026-01-15: Row 1: 5 Velocity Compressor buttons (5%, 20%, 30%, 50%, Slider)
- 2026-01-15: Row 2: 6 Velocity Max buttons (20, 40, 60, 80, 100, 120)
- 2026-01-15: All Page 2 buttons bound to MIDI Editor section scripts

## Bound Buttons Registry
*Buttons that have been bound to REAPER actions via Action List*

**IMPORTANT:** Always use **lowercase** button IDs (e.g., `p1_transport_back1` NOT `P1_Transport_Back1Measure`). The OSC addresses are case-sensitive and must match the JSON button IDs exactly.

**Cleanup completed 2026-01-15:** Deleted old capitalized entries from spreadsheet. Only lowercase entries remain.

| button_id | label | REAPER Action | Action ID |
|-----------|-------|---------------|-----------|
| p1_view_midieditor | MIDI Editor | View: Toggle show MIDI editor windows | 40716 |
| p1_transport_fwd1 | FWD 1 Measure | ALDENHammersmith_Transport Playhead Forward 1 Measure.lua | script |
| p1_transport_back1 | Back 1 Measure | ALDENHammersmith_Transport Playhead Backward 1 Measure.lua | script |
| p1_view_unusedtracks | Show/Hide Unused Tracks | ALDENHammersmith_Toggle Hide View All Unused Tracks.lua | script |
| p2_midi_velcomp5 | Vel Comp 5% | ALDENHammersmith_Midi Velocity Compressor 5%.lua | script (MIDI Editor) |
| p2_midi_velcomp20 | Vel Comp 20% | ALDENHammersmith_Midi Velocity Compressor 20%.lua | script (MIDI Editor) |
| p2_midi_velcomp30 | Vel Comp 30% | ALDENHammersmith_Midi Velocity Compressor 30%.lua | script (MIDI Editor) |
| p2_midi_velcomp50 | Vel Comp 50% | ALDENHammersmith_Midi Velocity Compressor 50%.lua | script (MIDI Editor) |
| p2_midi_velcompslider | Vel Comp Slider | ALDENHammersmith_Midi Velocity Linear Compressor Slider.lua | script (MIDI Editor) |
| p2_midi_velmax20 | Vel Max 20 | ALDENHammersmith_Quantize Midi Velocity Max 20.lua | script (MIDI Editor) |
| p2_midi_velmax40 | Vel Max 40 | ALDENHammersmith_Quantize Midi Velocity Max 40.lua | script (MIDI Editor) |
| p2_midi_velmax60 | Vel Max 60 | ALDENHammersmith_Quantize Midi Velocity Max 60.lua | script (MIDI Editor) |
| p2_midi_velmax80 | Vel Max 80 | ALDENHammersmith_Quantize Midi Velocity Max 80.lua | script (MIDI Editor) |
| p2_midi_velmax100 | Vel Max 100 | ALDENHammersmith_Quantize Midi Velocity Max 100.lua | script (MIDI Editor) |
| p2_midi_velmax120 | Vel Max 120 | ALDENHammersmith_Quantize Midi Velocity Max 120.lua | script (MIDI Editor) |

### How to Bind a Button
1. Open REAPER → Actions → Show Action List
2. Search for the action you want
3. Select it → click **Add...**
4. Choose OSC binding option
5. Tap the button on your iPad surface
6. The OSC address (e.g., `/p1_view_midieditor`) appears in shortcuts
7. Update this registry and the Google Sheets tracker

## Current State
- **Working session:** `IPAD Surface 5Pages.json`
- **Page_1:** Transport at top, 32 other buttons in grid, 5 nav tabs at bottom
- **Page_2:** 5 Midi Velocity Compressor buttons (row 1), 6 Quantize Velocity Max buttons (row 2) + nav tabs
- **Pages 3-5:** Only nav tabs (empty, ready for content)
- **Original backup:** `ALDENHammersmith_IPAD-Surface-original.json` (user's original 2-page version)

## Next Steps (when resuming)
- Continue adding MIDI tools to Page 2 (more rows available)
- Consider adding transport buttons to all pages
- Pages 3-5 available for other categories (Tracks, Rendering, etc.)
- Continue script cleanup project (256 scripts remaining)
