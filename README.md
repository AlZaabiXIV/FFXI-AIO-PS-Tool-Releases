# FFXI AIO PS Tool

Version: 2.2.0

Final Fantasy XI All-In-One Private Server Tool is a WPF desktop utility for managing a local LandSandBoat private server.

## Version 2.2.0 Notes

- Fixed the true Install DB freeze by removing blocking process-output `EndOfStream` checks from database, Git, build, and tool-detection process runners.
- SQL import now stays responsive while dbtool/mysql are quiet before the next output line.
- Install DB tracks actual SQL import progress from 60% to 90% and shows the current imported file.
- Saved the administrator startup choice and added `Configurations > Server Settings > PC Admin Settings`.
- Added administrator startup recovery so a failed elevated relaunch cannot loop forever.
- Renamed directory labels for server, Git Bash, SQL files, Ashita, and Server Settings paths.
- Added the prepared `FFXI Maps` main tab after Characters.
- Restyled Game Client Memory Control to match the dark fantasy theme.
- Increased the default window size to make tabs and Server Update panels easier to see.

See `VERSIONS.md` for the full update history.

## Main Tabs

- Server
- Accounts
- Characters
- FFXI Maps
- FFXI PS Commands
- Logs
- Configurations
- Future Features
- About

## Configurations Sub-Tabs

- MariaDB
- AI Stuff
- Ashita Stuff
- Server Update
- Server Settings
- Game Client Memory Control

## Server Settings Nested Tabs

`Configurations > Server Settings` contains the auto-loaded LandSandBoat settings/default files:

- PC Admin Settings
- Logging
- Login
- Main
- Map
- Network
- Search
- Test

These nested tabs keep the existing feature panels, value editing, setting descriptions, tooltips, save behavior, refresh behavior, backup behavior, and validation behavior.

The Server Settings Folder panel appears above the nested tabs and contains:

- Server Settings' Directory:
- Browse
- Refresh
- Save Changes
- Reset Selected File

## Outputs Panel

Outputs is the global top-right action output panel beside the tool title. It stays visible on every tab and keeps rich colored output, bold success/error formatting, auto-scroll, and persistent history.

The persistent file remains `Server Outputs.log` for compatibility and is available from the Logs tab as `Outputs`.

## Server Tab

Server focuses on process management:

- Server Folder
- Location path field
- Detect
- Save
- Server Status
- START ALL
- RESTART ALL
- STOP ALL
- REFRESH STATUS
- Server Window Mode
- Per-process Start, Restart, and Stop actions

Action button colors:

- Start: green
- Restart: yellow
- Stop: red

The managed executable names are:

- `xi_connect.exe`
- `xi_search.exe`
- `xi_world.exe`
- `xi_map.exe`
- `xi_test.exe`

Process status behavior:

- On startup and folder changes, the tool checks the Windows process table.
- Running executables are shown as `Running` even if they were started outside this tool.
- START ALL is disabled if any main server executable is already running: `xi_connect.exe`, `xi_search.exe`, `xi_world.exe`, or `xi_map.exe`.
- The row Start button is disabled for an already-running main executable.
- `xi_test.exe` remains manually startable because it can be used differently from the main server processes.
- Exact executable path matches are preferred.
- If a process path cannot be read because of permissions, the tool can fall back to the process name and reports that in Outputs.
- Stop and Restart can stop matching externally-started processes.
- If the server folder is missing, rows show `MissingFolder`.

## Logs

The Logs tab shows selected server logs in a CMD-style terminal view. The actual log files are never modified by formatting.

Color priority:

- Red bold: errors, warnings, failed actions, exceptions, and fatal lines.
- Green bold: success, completed, done, connected, detected successfully, and build succeeded lines.
- Yellow bold: active work such as connecting, updating, stopping, starting, building, downloading, cloning, checking, importing, running, processing, loading, refreshing, and pulling lines.
- Normal terminal text: neutral lines.

Log dropdown entries:

- `connect-server.log`
- `search-server.log`
- `world-server.log`
- `map-server.log`
- `xi_test-tool-output.log`
- `Outputs`

Connect/Search/World/Map Tool Output entries are intentionally hidden from the dropdown. Test Tool Output remains visible, and Outputs remains available as the persistent action log.

## MariaDB

`Configurations > MariaDB` contains the MariaDB connection settings used by account search, character search, database update checks, and GM/account updates.

Available actions:

- Save
- Test Connection

Database passwords are not hardcoded. UI input is never interpolated into SQL, and character/account lookups use parameterized SQL.

## Server Update

`Configurations > Server Update` contains three panels.

Git Bash Update:

- Location:
- Git Bash
- Browse
- Detect
- Download
- Update
- View Local Changes
- Progress bar

Download clones the latest LandSandBoat server repository from:

`https://github.com/LandSandBoat/server.git`

Download rules:

- The target server folder must be empty or missing.
- Existing folders with files are never overwritten.
- If no usable server folder is selected, choose a parent folder and the tool creates `[selected parent]\server`.
- The tool runs `git clone --recursive --progress https://github.com/LandSandBoat/server.git "[target folder]"`.
- After clone, the tool also verifies submodules with `git submodule update --init --recursive --progress`.
- After success, the downloaded folder is saved as the active Server Folder and executable detection refreshes.
- Live clone output appears in Outputs, including receiving objects, resolving deltas, updating files, and submodule progress.

Update rules:

- The tool checks local changes before updating.
- User-selected local-change handling is applied before pull.
- After pull, the tool runs `git submodule update --init --recursive --progress`.
- If stash handling was selected, the tool runs `git stash pop` after pull and submodule update.

Server Building Steps:

- Manual Visual Studio 2022 build instructions.
- Visual Studio Professional 2022 download link: [Click Here](https://my.visualstudio.com/Downloads?q=Visual%20Studio%202022)
- Open Visual Studio 2022
- Open Server Folder
- Verify Server Executables
- Open Build Guide
- Progress bar

Recommended LandSandBoat Windows build workflow:

- Download and install Visual Studio Professional 2022.
- Open Visual Studio 2022.
- Choose Open a local folder.
- Select the LandSandBoat server root folder.
- Wait until the Visual Studio CMake output says generation is finished.
- Wait for `1> CMake generation finished.`.
- Choose `x64-Debug`.
- Use Build > Build All.
- Confirm that Visual Studio reports `Build All succeeded.`.
- Confirm `xi_connect.exe`, `xi_search.exe`, `xi_world.exe`, `xi_map.exe`, and `xi_test.exe` exist in the main server folder.

Reference: [LandSandBoat Quick Start Guide](https://github.com/LandSandBoat/lsb-wiki/blob/main/Quick-Start-Guide.md)

Database Update:

- SQL files Directory:
- Install DB
- Update DB
- Check Migrations
- Backup DB
- Restore / Import DB
- Reset DB
- Maintenance Tasks
- Launch Server
- Settings
- Progress bar
- Cancel DB Task, visible only while a database operation is active

Install DB uses LandSandBoat `server\tools\dbtool.py` behavior where possible. It validates the server folder, `dbtool.py`, Python 3, the MySQL/MariaDB bin folder, `mysql.exe`, username, password, and database name.

Install DB popup fields:

- MySQL Bin Directory
- MySQL Username
- MySQL Password
- MySQL Database

MySQL bin detection searches PATH, `C:\Program Files\MariaDB*\bin`, `C:\Program Files\MySQL*\bin`, and matching `Program Files (x86)` folders. A valid folder must contain `mysql.exe`.

Database credentials are prefilled from `Configurations > MariaDB` and saved back to the app settings. The install flow also updates `settings/default/network.lua` values for SQL host, port, login, password, and database, while never logging the password.

If a saved MySQL bin path exists and is valid, Install DB asks whether to Continue, Change Path, or Cancel. Continue uses the saved path. Change Path opens the full install form again.

Database install/update behavior:

- Runs `dbtool.py` through redirected, unbuffered process output so Outputs updates live.
- Uses async process waiting and cancellation instead of blocking the WPF UI thread.
- Updates progress from 60% to 90% as `Importing ...sql` lines are received.
- Shows `No database output received for a while. The process may still be working.` if the process is quiet during a long SQL import.
- If installation fails halfway, Outputs shows the last SQL file, how many files were started, and a safe error message.
- Clicking Install DB again after a failed install asks whether to retry from the beginning, continue/retry the failed step through the official dbtool workflow, or cancel.
- If duplicate or partial-data errors appear after a halfway failure, use Reset DB or manually reset the database before retrying.
- MySQL passwords are never printed to Outputs or the persistent Outputs log.

Current dbtool-style behavior:

- Install DB creates the database if missing after confirmation, updates `tools\config.yaml`, updates `settings/default/network.lua`, then runs `py -3 -u dbtool.py update full`.
- Update DB runs `py -3 -u dbtool.py update`.
- Check Migrations runs `py -3 -u dbtool.py migrate`.
- Backup DB uses the existing safe app backup service.
- Restore / Import DB, Reset DB, Maintenance Tasks, Launch Server, and Settings remain enabled and show friendly Outputs messages until their guarded service flows are expanded.

The app preserves existing dbtool `config.yaml` metadata, including `db_ver`, when updating the MySQL bin path.

Renamed Server Update labels:

- `Git Bash Update > Server's Directory:`
- `Git Bash Update > Git Bash file:`
- `Database Update > SQL files Directory:`
- `Ashita Stuff > Ashita's Directory:`
- `Server Settings Folder > Server Settings' Directory:`

## Administrator Startup

On startup, the tool checks whether it is running as administrator. The first choice is saved, so future launches either request administrator permission automatically or continue normally without asking again. The setting can be changed at `Configurations > Server Settings > PC Admin Settings`.

PC Admin Settings includes:

- Run the tool as administrator toggle.
- Current admin status indicator.
- Save Admin Setting.
- Restart as Administrator.
- Restart Normally.

If an elevated startup attempt fails or the tool exits before loading, the pending startup flag is cleared and the saved administrator request is disabled so the app does not get stuck in an admin startup loop.

## FFXI Maps

`FFXI Maps` is a prepared future-feature tab after Characters. It contains disabled placeholders for Load Map, Show Online Characters, Show Character Location, Zone Viewer, Teleport From Map, and Refresh Map Data. Each disabled button shows `Coming in a future version.`

## Troubleshooting Partial Database Installs

- If Install DB stops during SQL import, read the last red error in Outputs and note the last SQL file listed.
- Click Install DB again to choose a retry option.
- Retry From Beginning reruns the official LandSandBoat `dbtool.py update full` workflow.
- Continue / Retry Failed Step reruns the safest available official dbtool workflow because dbtool.py does not expose a single-file resume command.
- If the database contains partial tables or duplicate data, reset/recreate the database before retrying.

Executable verification checks the main server folder for:

- `xi_connect.exe`
- `xi_search.exe`
- `xi_world.exe`
- `xi_map.exe`
- `xi_test.exe`

## Theme And Header

- The header uses `Assets/Header/ffxi_aio_header_display.png`, a display-optimized version of `Assets/Header/chatgpt_created_image_of_ffxi_aio_header.png`, behind the Outputs panel.
- The side window margins use `Assets/Borders/chain_border_vertical_tile.png`, generated from the uploaded chain image, as a repeated vertical fantasy border.
- The left side of the image shows the title, subtitle, and fantasy MMORPG artwork.
- The right side is darkened for the Outputs panel.
- A semi-transparent overlay and dark Outputs background keep action output readable.
- The UI theme uses dark fantasy panels, rounded group boxes, gold borders, green success/action colors, yellow progress colors, and red warning/stop colors.
- Selected tabs use a dark fantasy green background with readable light text instead of a white selected state.
- `VERSIONS.md` lists update history from latest to oldest in app-store style notes.

Troubleshooting:

- If Download appears slow, watch Outputs; clone progress can take time because the repository and submodules are large.
- If a Git GUI/progress window appears, verify the app is updated to 2.0.0 or newer; Download should use hidden `git.exe` with redirected output.
- If Start All is disabled, stop or restart the already-running main server executables.
- If Verify Server Executables reports missing files, open the server root folder in Visual Studio 2022 and wait for `1> CMake generation finished.`, then use Build > Build All.
- If Install DB cannot find `mysql.exe`, use Detect in the install popup or browse to your MariaDB/MySQL `bin` folder.

Progress bar colors:

- 0% to 65%: red
- 66% to 99%: yellow
- 100%: green

## AI Command Generation

Command Creator (By A.I.) uses settings from `Configurations > AI Stuff`. API keys are protected with Windows DPAPI and are never hardcoded, displayed, or logged.

When a protected API key is saved, the AI API Key field shows a masked saved-key placeholder instead of looking empty. The real key is never shown in plain text. Selecting `Other / Custom` shows Custom AI Provider and hides Model Name; selecting `OpenAI / ChatGPT` shows Model Name.

For OpenAI / ChatGPT, generation uses the OpenAI Responses API. The prompt requests only raw Lua source code and validates that the generated result contains `cmdprops`, `permission`, `parameters`, and `function onTrigger(player, ...)`.

If the first response is not valid command Lua, the service sends one stricter repair request before failing.

## Command In-Game Assist

Command In-Game Assist appears under the Custom Commands table. Selecting a command automatically fills the Command textbox, and users can also type manually.

Send to Game can be clicked at any time. If no bridge is configured, the tool reports:

`Ashita command bridge is not configured. Configure it in Configurations > Ashita Stuff.`

## Ashita Stuff

`Configurations > Ashita Stuff` includes:

- Ashita's Directory:
- Browse
- Detect
- Bridge Method
- Bridge Status
- Test Bridge

Supported bridge methods:

- Thirdparty Plugin
- Ashita Addon Bridge
- Text Script File
- Not Configured

## Game Client Memory Control

`Configurations > Game Client Memory Control` hosts the existing game-client control UI:

- Running game clients list
- Refresh Clients
- Attach Selected
- Test Memory Read
- Command Action
- DLL Action
- Status display

## Status Indicator Meanings

Green badge:

- `Ready`: The tool found the needed executable or settings and the item is ready to use.
- `DatabaseConnected`: MariaDB connection test completed successfully.
- `GitDetected`: Git Bash or git.exe was found.
- `UpdateSuccess`: Git pull, AI test, bridge validation, or build action completed successfully.

Yellow badge:

- `NotConfigured`: A required setting has not been selected or saved yet.
- `Running`: The server process is currently active.
- `Starting`: A start action has been requested and the tool is launching the process.
- `Stopping`: A stop action has been requested and the tool is closing the process.
- `Updating`: A Git update, AI API test, bridge validation, database update, or build action is running.

Red badge:

- `Stopped`: The process or service is not currently running.
- `Error`: The last action failed. Check Outputs.
- `MissingExecutable`: The selected server folder does not contain the expected executable.
- `MissingFolder`: The configured folder does not exist.
- `DatabaseDisconnected`: MariaDB connection has not been tested or the last test failed.
- `GitNotDetected`: Git Bash or git.exe was not found.
- `UpdateFailed`: Git pull, AI test, bridge validation, database update, or build action failed. Check Outputs.

## Notes For Future Work

The disabled buttons are intentional placeholders. They should stay greyed out with the tooltip `Coming in a future version.` until each feature has real service-layer logic and confirmation safeguards.

TODO-CODEX: Keep future navigation changes documented here when new tabs graduate from placeholders to working tools.
