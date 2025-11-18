# macOS Simulator - Complete World-Class PRD
## Version 3.0 - "World's Best Simulator - Exact Replica"

**Date:** 2024-11-18
**Status:** Comprehensive Analysis for Complete Implementation
**Goal:** Create the most accurate, feature-complete macOS simulator possible
**Size Budget:** UNLIMITED - Quality over everything

---

## Executive Summary

This document provides an exhaustive analysis of the current macOS simulator and defines requirements for making it a world-class, pixel-perfect replica of macOS Big Sur/Monterey. The goal is to implement EVERY major feature, application, and interaction that makes macOS unique.

**Current Status:**
- File Size: 142KB
- Applications: 10 (Finder, Safari, Terminal, Calculator, Notes, Calendar, Mail, Settings, Messages, Reminders)
- Core Features: Boot, Login, Desktop Icons, Window Management

**Target Status:**
- Complete macOS experience
- 25+ applications
- All system features
- Pixel-perfect design
- Professional-grade quality

---

## PART 1: CURRENT STATE ANALYSIS

### 1.1 What's Working Well ✅

1. **Boot Sequence** - Smooth Apple logo animation with progress bar
2. **Login Screen** - Beautiful blurred background, password input, power options
3. **Desktop Icons** - Draggable, selectable, context menus
4. **Window Management** - Drag, resize, minimize (genie effect), maximize
5. **Dock** - Hover magnification, active indicators, app launch
6. **Menu Bar** - Apple menu, system icons, live clock
7. **Messages** - Full conversation support, send/receive
8. **Reminders** - Complete task management with lists
9. **Safari** - Multi-tab browsing, simulated content
10. **Terminal** - Basic command execution
11. **Calculator** - Working scientific calculator
12. **Notes** - Full note editing
13. **Calendar** - Month view with navigation
14. **Mail** - Email reading interface
15. **System Settings** - Theme switching, preferences
16. **Spotlight** - Search overlay with keyboard shortcut
17. **Persistence** - localStorage saving state

### 1.2 Current Bugs 🐛

#### CRITICAL BUGS 🔴

1. **Terminal cd doesn't update prompt**
   - Location: executeTerminalCommand function
   - Prompt always shows "~" even after cd
   - Need to regenerate prompt line

2. **Finder can't navigate back to parent**
   - Location: finderNavigate function
   - No way to go up directory tree
   - Back button doesn't work properly

3. **Multiple Finder windows share state**
   - Global finderCurrentPath variable
   - Opening 2 Finders causes conflicts
   - Need per-window state

4. **Windows can be dragged off-screen**
   - No bounds checking in makeDraggable
   - Title bar can become inaccessible
   - Need to keep title bar visible

5. **Window resize doesn't enforce minimums**
   - CSS minWidth/minHeight not enforced during resize
   - Can drag to tiny sizes
   - Need Math.max() checks

6. **Context menu can appear off-screen**
   - No position adjustment
   - Right-click near edges causes issues
   - Need boundary detection

7. **Desktop icon grid not enforced**
   - Icons can be placed anywhere
   - Not snapped to grid
   - Should snap to 20px increments

8. **Calculator display can overflow**
   - Long numbers overflow container
   - No scrolling or font adjustment
   - Need dynamic sizing

9. **Safari back/forward don't work**
   - Just console.log()
   - No actual navigation
   - Need history implementation

10. **Dock event listener race condition**
    - DOMContentLoaded may fire after clicks
    - onclick removed but listener not yet attached
    - Need better initialization

---

## PART 2: MISSING CORE FEATURES

### 2.1 Missing Applications (HIGH PRIORITY)

#### 1. Apple Music 🎵 (CRITICAL - User Requested)
**Priority:** P0 - MUST HAVE
**Size Estimate:** ~25KB

**Required Features:**
- Sidebar navigation (Listen Now, Browse, Radio, Library, Playlists)
- Album grid display with cover art
- Now Playing bar at top
- Player controls (play, pause, skip, volume)
- Seek bar with time display
- Song queue/up next
- Search functionality
- Playlists view
- Artists view
- Albums view
- Songs list view
- Sample music library with data

**Layout:**
```
┌──────────┬─────────────────────────────────────┐
│          │ Now Playing: Song - Artist    [Controls] │
│ Sidebar  ├─────────────────────────────────────┤
│          │                                     │
│ Listen   │         Album Grid                  │
│ Browse   │         with Covers                 │
│ Radio    │                                     │
│ Library  │         [Album] [Album] [Album]     │
│ Playlist │         [Album] [Album] [Album]     │
│          │                                     │
└──────────┴─────────────────────────────────────┘
```

**Sample Library:**
- Albums: 10+ with cover art (gradient placeholders)
- Songs: 50+ with metadata
- Artists: 15+
- Playlists: 5+
- Genres: Pop, Rock, Electronic, Classical, Jazz

---

#### 2. Photos 📷 (HIGH PRIORITY)
**Priority:** P0
**Size Estimate:** ~20KB

**Required Features:**
- Years/Months/Days/All Photos views
- Sidebar with Recents, Favorites, Albums
- Photo grid with thumbnails
- Photo detail view (click to enlarge)
- Share button
- Favorite/unfavorite
- Delete photos
- Sample photo library (20-30 photos as colored placeholders)
- Zoom in/out
- Slideshow mode

**Layout:**
```
┌──────────┬─────────────────────────────────────┐
│          │  Photos            [Grid] [Search]  │
│ Recents  ├─────────────────────────────────────┤
│ Favorites│                                     │
│ Albums   │  [Photo] [Photo] [Photo] [Photo]    │
│ People   │  [Photo] [Photo] [Photo] [Photo]    │
│ Places   │  [Photo] [Photo] [Photo] [Photo]    │
│          │  [Photo] [Photo] [Photo] [Photo]    │
└──────────┴─────────────────────────────────────┘
```

---

#### 3. FaceTime 📞 (MEDIUM PRIORITY)
**Priority:** P1
**Size Estimate:** ~15KB

**Required Features:**
- Recent calls list
- Contact list
- Call interface (simulated video)
- Camera preview (placeholder)
- Mute/unmute
- End call button
- Screen share option
- Add person to call

---

#### 4. App Store 🏪 (MEDIUM PRIORITY)
**Priority:** P1
**Size Estimate:** ~20KB

**Required Features:**
- Today, Games, Apps, Arcade, Search tabs
- Featured app cards
- Top Charts
- Categories
- App detail page
- Screenshots
- Ratings and reviews
- Download/Install button (simulated)
- Updates tab

---

#### 5. Books/iBooks 📚 (MEDIUM PRIORITY)
**Priority:** P1
**Size Estimate:** ~15KB

**Required Features:**
- Library view with book covers
- Reading List
- Want to Read
- Finished
- Book reader interface
- Page turning animation
- Bookmark
- Adjust font size
- Night mode

---

#### 6. Podcasts 🎙️ (LOW PRIORITY)
**Priority:** P2
**Size Estimate:** ~15KB

**Required Features:**
- Library view
- Browse/Search
- Episode list
- Player controls
- Playback speed
- Sleep timer
- Downloaded episodes

---

#### 7. News 📰 (LOW PRIORITY)
**Priority:** P2
**Size Estimate:** ~15KB

**Required Features:**
- Today feed
- Following channels
- Article cards
- Categories (Politics, Tech, Sports, etc.)
- Article reader
- Save for later
- Share article

---

#### 8. Voice Memos 🎤 (LOW PRIORITY)
**Priority:** P2
**Size Estimate:** ~10KB

**Required Features:**
- Recording list
- Record button
- Playback controls
- Waveform display
- Trim/edit
- Share
- Delete

---

#### 9. Weather ☀️ (LOW PRIORITY)
**Priority:** P2
**Size Estimate:** ~12KB

**Required Features:**
- Current conditions
- Hourly forecast
- 10-day forecast
- Temperature graph
- Wind, humidity, precipitation
- Multiple locations
- Beautiful backgrounds

---

#### 10. Maps 🗺️ (LOW PRIORITY)
**Priority:** P2
**Size Estimate:** ~18KB

**Required Features:**
- Map view (simulated with placeholder)
- Search location
- Directions
- Satellite view toggle
- Traffic layer
- Points of interest
- 3D buildings

---

### 2.2 Missing System Features

#### 1. Notification Center (CRITICAL)
**Priority:** P0
**Size Estimate:** ~12KB

**Features:**
- Slide-in from right side
- Notifications grouped by app
- Clear all button
- Clear per-app
- Do Not Disturb toggle
- Widgets:
  - Calendar (today's events)
  - Weather (current conditions)
  - Stocks
  - Screen Time
  - Battery
  - World Clock

**Interactions:**
- Click notification to open app
- Swipe to dismiss
- Long press for options
- Widget customization

---

#### 2. Launchpad (CRITICAL)
**Priority:** P0
**Size Estimate:** ~10KB

**Features:**
- Full-screen overlay with blur
- App grid (7 columns × 5 rows)
- Multiple pages
- Page indicators (dots)
- Search bar at top
- Folders support
- Drag to rearrange
- Smooth page transitions
- F4 or pinch gesture to activate

**Interactions:**
- Click app to launch
- Drag apps to reorder
- Drag app onto another to create folder
- Escape to close

---

#### 3. Mission Control / Exposé (HIGH PRIORITY)
**Priority:** P1
**Size Estimate:** ~12KB

**Features:**
- Show all open windows scaled down
- Desktop preview at top
- Virtual desktops (spaces)
- Hover to highlight window
- Click to focus window
- Drag windows between spaces
- F3 or three-finger swipe up

**Layout:**
```
┌─────────────────────────────────────────┐
│ [Desktop 1] [Desktop 2] [+]             │
├─────────────────────────────────────────┤
│                                         │
│   [Window 1]    [Window 2]              │
│                                         │
│   [Window 3]    [Window 4]  [Window 5]  │
│                                         │
└─────────────────────────────────────────┘
```

---

#### 4. Quick Look (HIGH PRIORITY)
**Priority:** P1
**Size Estimate:** ~8KB

**Features:**
- Press Space on selected file
- Preview window with content
- Support for:
  - Images
  - PDFs
  - Text files
  - Videos (placeholder)
- Navigation (previous/next)
- Open in app button
- Share button

---

#### 5. AirDrop Interface (MEDIUM PRIORITY)
**Priority:** P1
**Size Estimate:** ~8KB

**Features:**
- Available devices list
- Send/receive files
- Acceptance dialog
- Progress bar
- Success/failure notifications

---

#### 6. Time Machine (LOW PRIORITY)
**Priority:** P2
**Size Estimate:** ~15KB

**Features:**
- Starfield background
- File history browser
- Timeline on right
- Restore file button
- Navigate through backups

---

#### 7. Activity Monitor (MEDIUM PRIORITY)
**Priority:** P1
**Size Estimate:** ~12KB

**Features:**
- CPU tab with graph
- Memory tab
- Energy tab
- Disk tab
- Network tab
- Process list with fake data
- Search processes
- Force quit

---

#### 8. Disk Utility (LOW PRIORITY)
**Priority:** P2
**Size Estimate:** ~10KB

**Features:**
- Volume list
- First Aid
- Partition
- Erase
- Restore
- Disk information

---

#### 9. Screen Saver Preview (LOW PRIORITY)
**Priority:** P2
**Size Estimate:** ~8KB

**Features:**
- Screen saver selection
- Preview window
- Options button
- Hot corners configuration

---

### 2.3 Enhanced Menu Bar Features

#### Current Issues:
- ❌ App name doesn't change based on focused window
- ❌ No app-specific menus (File, Edit, View, etc.)
- ❌ No keyboard shortcuts shown in menus
- ❌ No working menu commands

#### Required Implementation:

**1. Dynamic App Menus:**

**Finder Menus:**
```
Finder    File         Edit        View        Go          Window      Help
├─About   ├─New Folder ├─Undo      ├─as Icons  ├─Back      ├─Minimize  ├─Search
├─Prefs   ├─New Tab    ├─Redo      ├─as List   ├─Forward   ├─Zoom      └─Finder Help
├─Empty   ├─Open       ├─Cut       ├─as Column ├─Enclosing └─Bring All
└─Hide    ├─Close      ├─Copy      └─as Gallery├─Recent
          ├─Get Info   ├─Paste                 ├─Go to Folder
          └─Move to    ├─Select All            └─Connect
            Trash      └─Invert Sel
```

**Safari Menus:**
```
Safari    File        Edit        View        History     Bookmarks   Window      Help
├─About   ├─New Tab   ├─Undo      ├─Show      ├─Back      ├─Show All  ├─Minimize  ├─Search
├─Prefs   ├─New Pvt   ├─Redo      ├─Reload    ├─Forward   ├─Add       ├─Zoom      └─Safari
└─Quit    ├─Open      ├─Cut       ├─Enter     ├─Home      └─Edit      └─Bring All   Help
          ├─Close Tab ├─Copy      ├─Exit      └─Show All
          └─Save Page ├─Paste     └─Developer
                      └─Find
```

**Notes Menus:**
```
Notes     File        Edit        Format      Window      Help
├─About   ├─New Note  ├─Undo      ├─Font      ├─Minimize  ├─Search
├─Prefs   ├─New Folder├─Redo      ├─Text      ├─Zoom      └─Notes
└─Quit    ├─Delete    ├─Cut       ├─Align     └─Bring All   Help
          └─Export    ├─Copy      ├─List
                      ├─Paste     └─Table
                      ├─Select All
                      └─Find
```

**Implementation Requirements:**
- Track active window
- Switch menu structure on focus
- Render appropriate menus
- Execute menu commands
- Show keyboard shortcuts (⌘K format)
- Highlight on hover
- Working dropdown menus

---

### 2.4 Enhanced Dock Features

#### Missing Features:
- ❌ Right-click context menus on apps
- ❌ Drag to reorder apps
- ❌ Recent applications section
- ❌ Downloads folder with stack animation
- ❌ Trash full/empty states with count
- ❌ Empty Trash functionality
- ❌ Keep in Dock / Remove from Dock
- ❌ Show in Finder option
- ❌ Force Quit option
- ❌ Dock preferences

#### Required Implementation:

**1. Right-Click Context Menu:**
```
Options >
  ├─ Open at Login
  └─ Show in Finder
─────────────
Keep in Dock
─────────────
Quit
Force Quit
```

**2. Dock Preferences:**
- Position: Bottom, Left, Right
- Size slider (30px - 128px)
- Magnification toggle + slider
- Minimize effect: Genie / Scale
- Auto-hide dock
- Show recent applications (3 most recent)

**3. Downloads Stack:**
- Click to fan out recent downloads
- Grid or fan layout
- Click file to open
- Clear button

**4. Trash:**
- Empty state: Gray icon
- Full state: Papers visible
- Show item count on hover
- Right-click "Empty Trash"
- Confirmation dialog
- Animation on empty

---

### 2.5 Enhanced Finder

#### Current Issues:
- ❌ Can't select files (no highlighting)
- ❌ No multi-select (Cmd+Click, Shift+Click)
- ❌ No file operations (copy, cut, paste, delete)
- ❌ No drag and drop files
- ❌ No inline rename
- ❌ No Get Info panel
- ❌ No search functionality
- ❌ Only icon view (no list/column/gallery)
- ❌ No file preview
- ❌ No tags
- ❌ Can't create new folders in Finder
- ❌ Navigation broken

#### Required Implementation:

**1. File Selection:**
- Click file to select (blue highlight)
- Cmd+Click for multi-select
- Shift+Click for range select
- Cmd+A to select all
- Selection rectangle drag
- Click empty space to deselect

**2. File Operations:**
- Copy (Cmd+C)
- Cut (Cmd+X)
- Paste (Cmd+V)
- Duplicate (Cmd+D)
- Delete (Cmd+Delete)
- Rename (Enter key or click name)
- Get Info (Cmd+I) - Show panel
- Compress (right-click → Compress)
- New Folder (Cmd+Shift+N)

**3. Navigation:**
- Back/Forward buttons working
- Breadcrumb path in toolbar
- Click path to navigate
- Go menu with shortcuts
- Recent locations
- Favorites in sidebar
- Path bar at bottom (option)

**4. View Modes:**
- Icon view (current)
- List view with sortable columns:
  - Name, Date Modified, Size, Kind, Tags
- Column view (Miller columns)
- Gallery view with preview
- View options panel

**5. Search:**
- Search field in toolbar
- Live filtering as you type
- Search scope (This Mac / Current Folder)
- Clear search button
- Smart Folders

**6. Context Menu (Enhanced):**
```
Open
Open With >
  ├─ App 1
  ├─ App 2
  └─ Other...
─────────────
Get Info                ⌘I
Rename
Compress "Filename"
Duplicate               ⌘D
Make Alias              ⌘L
Quick Look              Space
─────────────
Copy "Filename"         ⌘C
Share >
─────────────
Copy
Paste
─────────────
Move to Trash           ⌘⌫
─────────────
Tags >
  ├─ Red
  ├─ Orange
  ├─ Yellow
  ├─ Green
  └─ Blue
```

**7. Get Info Panel:**
- File icon and name
- Kind (folder, document, etc.)
- Size
- Created date
- Modified date
- Where (path)
- Tags
- Sharing & Permissions
- Preview (for images)
- Name & Extension
- Open with application

---

### 2.6 Enhanced Terminal

#### Current Issues:
- ❌ cd doesn't update prompt
- ❌ Only 12 basic commands
- ❌ No command piping (|)
- ❌ No output redirection (>)
- ❌ No tab completion
- ❌ No colored output
- ❌ No multi-line commands

#### Required Commands:

**File Operations:**
- `cp source dest` - Copy files
- `mv source dest` - Move/rename
- `rm file` - Remove file
- `rm -r dir` - Remove directory
- `rmdir dir` - Remove empty directory
- `ln -s` - Create symlink
- `chmod` - Change permissions (simulated)
- `chown` - Change owner (simulated)

**Search & Filter:**
- `grep pattern file` - Search in files
- `find . -name pattern` - Find files
- `locate filename` - Locate files
- `which command` - Show command path

**Text Processing:**
- `head file` - Show first lines
- `tail file` - Show last lines
- `wc file` - Word count
- `sort` - Sort lines
- `uniq` - Remove duplicates
- `cut` - Cut columns
- `sed` - Stream editor (basic)
- `awk` - Text processing (basic)

**System:**
- `ps` - Process list
- `top` - Process monitor
- `kill pid` - Kill process
- `df` - Disk space
- `du` - Directory usage
- `free` - Memory info
- `uptime` - System uptime
- `hostname` - Show hostname

**Network:**
- `ping host` - Ping server (simulated)
- `curl url` - Fetch URL (simulated)
- `wget url` - Download file (simulated)
- `ssh` - SSH connection (simulated)
- `ifconfig` - Network interfaces

**Other:**
- `history` - Command history
- `alias` - Create aliases
- `export` - Set environment variable
- `env` - Show environment
- `man command` - Manual pages (simulated)
- `sudo command` - Run as root (simulated)

**Advanced Features:**
- Tab completion for files/commands
- Command piping: `ls | grep txt`
- Output redirection: `ls > file.txt`
- Append: `echo text >> file.txt`
- Command substitution: `echo $(pwd)`
- Colored output (errors in red, etc.)
- Multi-line commands with \
- Command history search (Ctrl+R)

**Prompt Update:**
```javascript
// After cd command:
const prompt = `user@macos ${state.terminal.currentDir} % `;
// Regenerate input line with new prompt
```

---

### 2.7 Enhanced Safari

#### Missing Features:
- ❌ Bookmarks bar not showing
- ❌ Can't save bookmarks
- ❌ No bookmarks manager
- ❌ No history tracking
- ❌ Back/forward just console.log()
- ❌ No reading list
- ❌ No sidebar (bookmarks/reading list)
- ❌ No downloads indicator
- ❌ No private browsing mode
- ❌ No developer tools
- ❌ Only 2 simulated pages

#### Required Implementation:

**1. Working Navigation:**
- Proper history stack
- Back button (disabled when no history)
- Forward button (disabled when no forward)
- Reload button actually reloads
- Stop button while loading
- Home button

**2. Bookmarks:**
- Bookmarks bar below toolbar
- Add bookmark (Cmd+D)
- Bookmarks folder structure
- Bookmarks sidebar
- Edit bookmarks
- Folders support
- Bookmark icons

**3. More Simulated Pages:**
- Apple.com ✓ (already have)
- Google.com ✓ (already have)
- Wikipedia.org
- YouTube.com
- GitHub.com
- Twitter/X.com
- Reddit.com
- Amazon.com
- Netflix.com
- Gmail.com
- Blank page (about:blank)
- Error page (404)

**4. Reading List:**
- Add to reading list
- Reading list sidebar
- Mark as read
- Remove from list

**5. Downloads:**
- Downloads icon in toolbar
- Downloads panel
- Progress bars
- Open file
- Show in Finder
- Clear downloads

**6. Private Browsing:**
- Dark theme when active
- No history saved
- No cookies
- Indicator in window

**7. Developer Tools:**
- Console panel
- Elements inspector (simulated)
- Network tab
- Toggle with Cmd+Option+I

---

### 2.8 Enhanced Calculator

#### Current Issues:
- ✅ Basic functionality works
- ❌ Display can overflow
- ❌ Only scientific mode
- ❌ No basic mode toggle
- ❌ No memory functions working
- ❌ No history/tape

#### Required Enhancement:

**1. Display Fix:**
- Auto-reduce font size for long numbers
- Or use scrolling
- Max display length

**2. Basic Mode:**
- Toggle between Basic and Scientific
- Basic: Simple 4-function calc
- Scientific: Advanced functions

**3. Memory Functions:**
- MC (Memory Clear)
- M+ (Memory Add)
- M- (Memory Subtract)
- MR (Memory Recall)
- Visual indicator when memory has value

**4. History/Tape:**
- Show recent calculations
- Click to reuse value
- Clear history

---

### 2.9 Enhanced Notes

#### Current Status:
- ✅ Basic functionality works
- ❌ No formatting toolbar
- ❌ Can't bold/italic/underline
- ❌ No lists support
- ❌ No checklist support
- ❌ No tables
- ❌ No attachments

#### Required Enhancement:

**1. Formatting Toolbar:**
- Bold (Cmd+B)
- Italic (Cmd+I)
- Underline (Cmd+U)
- Strikethrough
- Heading styles (H1, H2, H3)
- Font picker
- Color picker
- Alignment (left, center, right)

**2. Lists:**
- Bullet list
- Numbered list
- Checklist (with checkboxes)
- Indent/outdent

**3. Tables:**
- Insert table
- Add/remove rows
- Add/remove columns
- Resize columns

**4. Attachments:**
- Add images (placeholders)
- Add files
- Attachments list

**5. Organization:**
- Folders (already have lists)
- Tags
- Pin notes
- Lock notes

---

### 2.10 Enhanced Calendar

#### Current Status:
- ✅ Month view works
- ❌ No event creation
- ❌ No event list
- ❌ No week view
- ❌ No day view
- ❌ Can't click dates to create events

#### Required Enhancement:

**1. Views:**
- Day view (hourly slots)
- Week view (7 columns)
- Month view ✓ (already have)
- Year view

**2. Events:**
- Double-click date to create
- Event form (title, time, location, notes)
- All-day events
- Recurring events
- Color-coded calendars
- Event list in sidebar

**3. Multiple Calendars:**
- Work, Personal, Birthdays, Holidays
- Toggle visibility
- Color per calendar
- Import calendar

---

### 2.11 Enhanced Mail

#### Current Status:
- ✅ Basic reading works
- ❌ Can't compose new mail
- ❌ No reply/forward
- ❌ No search
- ❌ No attachments
- ❌ No folders management

#### Required Enhancement:

**1. Compose Window:**
- New window for composing
- To, Cc, Bcc fields
- Subject line
- Rich text editor
- Send button
- Save draft
- Attachments

**2. Actions:**
- Reply
- Reply All
- Forward
- Delete
- Mark as read/unread
- Flag/unflag
- Move to folder

**3. Search:**
- Search field
- Search in: From, Subject, Content
- Date range filter

**4. Folders:**
- Create new folders
- Drag emails to folders
- Smart mailboxes

---

### 2.12 Enhanced Messages

#### Current Status:
- ✅ Conversations work
- ✅ Send messages works
- ❌ No emoji picker
- ❌ No attachments
- ❌ No read receipts
- ❌ No typing indicator

#### Required Enhancement:

**1. Emoji Picker:**
- Emoji button
- Emoji categories
- Search emojis
- Recently used

**2. Attachments:**
- Photo/video attachments
- File attachments
- Show preview in bubble

**3. Enhanced Features:**
- Read receipts (Read 10:45 AM)
- Typing indicator (3 dots animation)
- Message reactions
- Edit message
- Delete message
- Message effects (simulated)

---

### 2.13 Enhanced Reminders

#### Current Status:
- ✅ Basic functionality works
- ❌ No due dates shown
- ❌ No priorities
- ❌ No notes on reminders
- ❌ No subtasks

#### Required Enhancement:

**1. Due Dates:**
- Date picker
- Time picker
- Today shortcut
- Tomorrow shortcut
- "Today" list shows only today's items

**2. Priority:**
- None, Low, Medium, High
- Visual indicator (! symbols)
- Sort by priority

**3. Notes:**
- Click reminder to see details
- Add notes field
- URL support

**4. Subtasks:**
- Add subtasks to reminders
- Indent subtasks
- Complete parent completes all

---

### 2.14 System Settings Enhancements

#### Current Status:
- ✅ Basic settings work
- ✅ Theme toggle works
- ❌ Limited settings categories
- ❌ Settings don't fully apply

#### Required Enhancement:

**More Categories:**
- General (already have)
- Desktop & Screen Saver (already have)
- Dock & Menu Bar (new)
- Mission Control (new)
- Language & Region
- Security & Privacy
- Notifications & Focus
- Internet Accounts
- Users & Groups
- Accessibility
- Screen Time
- Extensions
- Battery
- Date & Time

**Dock & Menu Bar Settings:**
- Size slider (affects actual dock)
- Magnification toggle
- Position (Bottom, Left, Right)
- Minimize effect
- Auto-hide
- Show recent apps
- Menu bar items toggles

**Notifications Settings:**
- Per-app notification settings
- Alert style
- Badge app icon
- Sound
- Show in Notification Center

---

## PART 3: PIXEL-PERFECT DESIGN REQUIREMENTS

### 3.1 Color Accuracy

**macOS Big Sur Colors:**
```css
/* System Colors */
--system-blue: #007AFF;
--system-green: #34C759;
--system-indigo: #5856D6;
--system-orange: #FF9500;
--system-pink: #FF2D55;
--system-purple: #AF52DE;
--system-red: #FF3B30;
--system-teal: #5AC8FA;
--system-yellow: #FFCC00;

/* Gray Scale */
--gray-1: #8E8E93;
--gray-2: #AEAEB2;
--gray-3: #C7C7CC;
--gray-4: #D1D1D6;
--gray-5: #E5E5EA;
--gray-6: #F2F2F7;

/* Light Mode */
--label-primary: #000000;
--label-secondary: #3C3C43 (60% opacity);
--label-tertiary: #3C3C43 (30% opacity);
--fill-primary: #78788033 (20% opacity);
--fill-secondary: #78788028 (16% opacity);
--separator: #3C3C4349 (29% opacity);

/* Dark Mode */
--label-primary-dark: #FFFFFF;
--label-secondary-dark: #EBEBF5 (60% opacity);
--label-tertiary-dark: #EBEBF5 (30% opacity);
--fill-primary-dark: #7878805C (36% opacity);
--fill-secondary-dark: #78788051 (32% opacity);
--separator-dark: #54545899 (60% opacity);
```

### 3.2 Typography

**SF Pro Font Stack:**
```css
font-family: -apple-system, BlinkMacSystemFont, "SF Pro Text", "SF Pro Display", system-ui, sans-serif;

/* Sizes */
--large-title: 26px;
--title-1: 22px;
--title-2: 17px;
--title-3: 15px;
--headline: 13px (semibold);
--body: 13px;
--callout: 12px;
--subheadline: 11px;
--footnote: 10px;
--caption-1: 10px;
--caption-2: 10px;

/* Weights */
--ultralight: 100;
--thin: 200;
--light: 300;
--regular: 400;
--medium: 500;
--semibold: 600;
--bold: 700;
--heavy: 800;
--black: 900;
```

### 3.3 Spacing System

**8-Point Grid:**
```css
--space-0: 0px;
--space-1: 4px;
--space-2: 8px;
--space-3: 12px;
--space-4: 16px;
--space-5: 20px;
--space-6: 24px;
--space-7: 28px;
--space-8: 32px;
--space-10: 40px;
--space-12: 48px;
--space-16: 64px;
```

### 3.4 Border Radius

```css
--radius-small: 4px;
--radius-medium: 6px;
--radius-large: 8px;
--radius-xlarge: 10px;
--radius-button: 6px;
--radius-window: 10px;
--radius-dock: 16px;
```

### 3.5 Shadows

```css
/* Window Shadow */
--shadow-window: 0 0 0 1px rgba(0,0,0,0.04),
                 0 2px 8px rgba(0,0,0,0.08),
                 0 16px 32px rgba(0,0,0,0.12);

/* Dock Shadow */
--shadow-dock: 0 5px 20px rgba(0,0,0,0.3);

/* Menu Shadow */
--shadow-menu: 0 2px 8px rgba(0,0,0,0.15);

/* Button Shadow */
--shadow-button: 0 1px 2px rgba(0,0,0,0.1);
```

### 3.6 Animations

**Timing Functions:**
```css
--ease-in: cubic-bezier(0.42, 0, 1, 1);
--ease-out: cubic-bezier(0, 0, 0.58, 1);
--ease-in-out: cubic-bezier(0.42, 0, 0.58, 1);
--ease-standard: cubic-bezier(0.4, 0, 0.2, 1);
--ease-spring: cubic-bezier(0.175, 0.885, 0.32, 1.275);
```

**Durations:**
```css
--duration-instant: 100ms;
--duration-fast: 150ms;
--duration-normal: 250ms;
--duration-slow: 350ms;
--duration-slower: 500ms;
```

---

## PART 4: IMPLEMENTATION PRIORITIES

### Phase 3A: Critical Fixes (2-3 hours)
**Priority: P0**

1. ✅ Fix Terminal cd prompt
2. ✅ Fix Finder navigation
3. ✅ Fix window drag bounds
4. ✅ Fix window resize minimums
5. ✅ Fix context menu positioning
6. ✅ Fix desktop icon grid snapping
7. ✅ Fix calculator overflow

---

### Phase 3B: Apple Music + Photos (4-5 hours)
**Priority: P0**

1. ✅ Apple Music app
   - Full player interface
   - Library with sample data
   - Playlists
   - Now playing bar
   - Search functionality

2. ✅ Photos app
   - Photo grid
   - Sample photo library
   - Detail view
   - Albums/favorites

---

### Phase 3C: System Features (4-5 hours)
**Priority: P0**

1. ✅ Notification Center
2. ✅ Launchpad
3. ✅ Mission Control
4. ✅ Quick Look
5. ✅ Activity Monitor

---

### Phase 3D: Additional Apps (5-6 hours)
**Priority: P1**

1. ✅ FaceTime
2. ✅ App Store
3. ✅ Books
4. ✅ Podcasts
5. ✅ News
6. ✅ Voice Memos
7. ✅ Weather
8. ✅ Maps

---

### Phase 3E: Enhanced Menus & Dock (3-4 hours)
**Priority: P0**

1. ✅ Dynamic menu bar
2. ✅ App-specific menus
3. ✅ Working menu commands
4. ✅ Dock context menus
5. ✅ Dock preferences
6. ✅ Downloads stack
7. ✅ Trash enhancements

---

### Phase 3F: Enhanced Finder (4-5 hours)
**Priority: P0**

1. ✅ File selection (single/multi)
2. ✅ File operations (copy/paste/delete)
3. ✅ View modes (list/column/gallery)
4. ✅ Search functionality
5. ✅ Get Info panel
6. ✅ Navigation fixes

---

### Phase 3G: Enhanced Apps (5-6 hours)
**Priority: P1**

1. ✅ Safari improvements
2. ✅ Terminal enhancements
3. ✅ Notes formatting
4. ✅ Calendar events
5. ✅ Mail compose
6. ✅ Messages enhancements
7. ✅ Reminders enhancements
8. ✅ Calculator improvements

---

### Phase 3H: Polish & Testing (3-4 hours)
**Priority: P1**

1. ✅ Pixel-perfect design adjustments
2. ✅ Animation refinements
3. ✅ Performance optimization
4. ✅ Cross-browser testing
5. ✅ Bug fixes
6. ✅ Documentation

---

## PART 5: TOTAL IMPLEMENTATION ESTIMATE

**Total Features to Implement:** 100+
**Total Development Time:** 35-45 hours
**Expected Final File Size:** 400-500KB (acceptable with no limit)

**Breakdown:**
- Critical Fixes: 2-3 hours
- Apple Music + Photos: 4-5 hours
- System Features: 4-5 hours
- Additional Apps (8): 5-6 hours
- Enhanced Menus & Dock: 3-4 hours
- Enhanced Finder: 4-5 hours
- Enhanced Existing Apps: 5-6 hours
- Polish & Testing: 3-4 hours

**Total Lines of Code (Projected):** 8,000-10,000 lines

---

## PART 6: SUCCESS CRITERIA

### 6.1 Feature Completeness
- ✅ 25+ fully functional applications
- ✅ All major system features working
- ✅ Complete menu bar implementation
- ✅ Full dock functionality
- ✅ Comprehensive Finder
- ✅ All keyboard shortcuts
- ✅ Pixel-perfect design

### 6.2 Quality Metrics
- ✅ Looks identical to real macOS
- ✅ All interactions smooth (60fps)
- ✅ No critical bugs
- ✅ Professional-grade polish
- ✅ Works in all major browsers

### 6.3 User Experience
- ✅ Feels exactly like macOS
- ✅ Intuitive and natural
- ✅ Fast and responsive
- ✅ Delightful animations
- ✅ Complete feature set

---

## CONCLUSION

This PRD defines the requirements for creating the world's most comprehensive macOS simulator. The implementation will transform the current basic simulator into a pixel-perfect, feature-complete replica of macOS Big Sur/Monterey.

**Key Goals:**
1. **Complete Feature Set** - Every major macOS feature
2. **Pixel-Perfect Design** - Identical to real macOS
3. **Professional Quality** - Production-ready code
4. **No Compromises** - Size budget unlimited

**Next Steps:**
1. Implement critical bug fixes
2. Add Apple Music (user's #1 request)
3. Add all remaining applications
4. Enhance all existing features
5. Polish to perfection

---

**Document Version:** 3.0
**Last Updated:** 2024-11-18
**Status:** Ready for Complete Implementation
**Target:** World's Best macOS Simulator
