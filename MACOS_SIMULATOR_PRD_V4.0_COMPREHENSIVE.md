# macOS Simulator PRD v4.0 - Comprehensive Analysis
## "World's Best Simulator - Exact Replica"

**Date:** 2025-11-18
**Current Status:** 11 apps, 169KB, 4,685 lines
**Goal:** Create pixel-perfect macOS replica with unlimited size budget

---

## EXECUTIVE SUMMARY

After deep testing of the current simulator (Phase 1+2 complete), I've identified **63 issues** across **5 categories**:
- 🐛 **18 Critical Bugs** - Features broken or not working
- ⚠️ **22 Missing Features** - Expected macOS functionality not implemented
- 🎨 **8 UI/UX Issues** - Visual/interaction problems
- 📱 **10 Missing Apps** - Essential macOS applications
- ✨ **5 Enhancement Opportunities** - Polish and refinement

**Current Apps:** Finder, Safari, Terminal, Calculator, Notes, Calendar, Mail, Settings, Messages, Reminders, Music (11 total)

---

## PART 1: CRITICAL BUGS (18 Issues)

### BUG #1: Finder - Multiple Windows Share State ⚠️ CRITICAL
**File:** macos-simulator.html:2815-2840
**Issue:** Opening multiple Finder windows all share the same path/history
**Expected:** Each Finder window should have independent navigation state
**Impact:** HIGH - Core functionality broken
**Root Cause:** Global variables `finderCurrentPath`, `finderHistory`, `finderHistoryIndex`
**Fix Required:** Per-window state storage in window data object

### BUG #2: Finder - No File/Folder Icons in Item View
**File:** macos-simulator.html:2895-2900
**Issue:** Folder items show basic emoji icons (📁📄) instead of detailed icons
**Expected:** Proper folder colors, file type icons
**Impact:** MEDIUM - Visual clarity
**Fix Required:** Icon mapping system for file types

### BUG #3: Finder - View Options Not Implemented
**File:** macos-simulator.html:2491-2495 (toolbar buttons)
**Issue:** Buttons ⊞ ⊟ ≣ (Icon/List/Column view) do nothing
**Expected:** Switch between different view modes
**Impact:** MEDIUM - Core Finder feature missing
**Fix Required:** Implement view mode switching logic

### BUG #4: Finder - No Search Functionality
**File:** Finder app definition
**Issue:** No search bar or search capability
**Expected:** Search files/folders in current location
**Impact:** MEDIUM - Important feature missing
**Fix Required:** Add search input and filter logic

### BUG #5: Finder - Sidebar Items Not Updating Active State
**File:** macos-simulator.html:2921-2928
**Issue:** `textContent.includes(path)` logic is fragile and often fails
**Expected:** Active sidebar item always highlights correctly
**Impact:** LOW - Visual feedback issue
**Fix Required:** Better active state tracking

### BUG #6: Safari - No Actual Web Content Rendering
**File:** macos-simulator.html:2993-3022
**Issue:** Shows placeholder text, no real iframe or content loading
**Expected:** Actually display websites (even if fake/iframe)
**Impact:** MEDIUM - App feels incomplete
**Fix Required:** Add iframe loading or better fake content

### BUG #7: Safari - Address Bar Doesn't Navigate
**File:** macos-simulator.html:2561 (address bar input)
**Issue:** Typing in address bar has no effect
**Expected:** Press Enter to navigate to URL
**Impact:** MEDIUM - Core Safari feature missing
**Fix Required:** Add keydown handler for Enter key

### BUG #8: Safari - New Tab Button Missing
**File:** Safari app definition
**Issue:** No "+" button to open new tab
**Expected:** Button next to tabs to create new tab
**Impact:** LOW - Expected feature
**Fix Required:** Add new tab button and handler

### BUG #9: Terminal - No ls Output Based on Actual File System
**File:** macos-simulator.html:3088
**Issue:** `ls` always shows same hardcoded string
**Expected:** Show actual files from `state.fileSystem` based on `currentDir`
**Impact:** MEDIUM - Terminal not connected to file system
**Fix Required:** Read from state.fileSystem and display actual contents

### BUG #10: Terminal - pwd Output Doesn't Match cd
**File:** macos-simulator.html:3091
**Issue:** `pwd` logic may not match actual `currentDir` state
**Expected:** Perfect synchronization between cd and pwd
**Impact:** LOW - Consistency issue
**Fix Required:** Use exact same state.terminal.currentDir

### BUG #11: Terminal - No Command History (Up Arrow)
**File:** Terminal input handler
**Issue:** Pressing up arrow doesn't recall previous commands
**Expected:** Arrow keys navigate command history
**Impact:** HIGH - Essential terminal feature
**Fix Required:** Store command history array and handle arrow keys

### BUG #12: Terminal - No Tab Completion
**File:** Terminal input handler
**Issue:** Tab key doesn't autocomplete commands or paths
**Expected:** Tab autocompletes filenames, commands
**Impact:** MEDIUM - Nice-to-have feature
**Fix Required:** Implement tab completion logic

### BUG #13: Calculator - No Keyboard Support
**File:** Calculator app
**Issue:** Number keys and operators don't work
**Expected:** Keyboard input works same as clicking
**Impact:** MEDIUM - Expected feature
**Fix Required:** Add keydown event listener for calculator window

### BUG #14: Notes - No Note Persistence in State
**File:** macos-simulator.html:2637-2660
**Issue:** Notes are stored in DOM only, not in state object
**Expected:** Notes should be in state.notes array, persist on reload
**Impact:** HIGH - Data loss on page refresh
**Fix Required:** Add notes array to state, save/load logic

### BUG #15: Calendar - Hardcoded to January 2025
**File:** macos-simulator.html:3245-3268
**Issue:** Always shows January 2025, no month navigation
**Expected:** Show current month, navigate with arrows
**Impact:** HIGH - Calendar completely static
**Fix Required:** Dynamic calendar rendering with month/year navigation

### BUG #16: Mail - No Send Functionality
**File:** macos-simulator.html:2714 (compose button)
**Issue:** `composeMail()` function not implemented
**Expected:** Open compose window, add to Sent folder
**Impact:** MEDIUM - Core feature missing
**Fix Required:** Implement mail composition and sending

### BUG #17: Settings - Completely Non-Functional
**File:** macos-simulator.html:2665-2682
**Issue:** Just shows placeholder text, no actual settings
**Expected:** Control theme, wallpaper, system preferences
**Impact:** HIGH - App is a stub
**Fix Required:** Implement actual settings panels

### BUG #18: Music - No Actual Audio Playback
**File:** Music player controls
**Issue:** Play button doesn't play actual audio
**Expected:** At minimum, simulate playback with progress bar animation
**Impact:** MEDIUM - Player feels incomplete
**Fix Required:** Add timer to update progress bar when playing

---

## PART 2: MISSING FEATURES (22 Issues)

### FEATURE #1: Window - No Double-Click Titlebar to Minimize
**Expected:** Double-clicking window titlebar minimizes to dock
**Current:** Only maximize is implemented
**Priority:** MEDIUM
**macOS Behavior:** Double-click titlebar = minimize OR maximize (based on system setting)

### FEATURE #2: Window - No CMD+W to Close Window
**Expected:** Keyboard shortcut to close active window
**Current:** Only red button closes window
**Priority:** HIGH
**macOS Behavior:** ⌘W closes active window

### FEATURE #3: Window - No Full Screen Mode
**Expected:** Green button can enter full screen
**Current:** Green button only maximizes
**Priority:** MEDIUM
**macOS Behavior:** Hold Option on green button for old-style maximize

### FEATURE #4: Window - No Window Shake to Minimize Others
**Expected:** Shake window to minimize all other windows
**Current:** Not implemented
**Priority:** LOW
**macOS Behavior:** Rapid drag motion minimizes other windows

### FEATURE #5: Dock - No App Bounce on Notification
**Expected:** Apps bounce in dock when they need attention
**Current:** Only bounce on launch
**Priority:** LOW
**macOS Behavior:** Continuous bounce until user clicks

### FEATURE #6: Dock - No Right-Click Context Menu
**Expected:** Right-click dock item shows menu (Options, Quit, etc.)
**Current:** No context menu on dock items
**Priority:** MEDIUM
**macOS Behavior:** Rich context menus with app-specific options

### FEATURE #7: Dock - No Magnification Effect
**Expected:** Hovering over dock items magnifies them and neighbors
**Current:** Simple hover scale
**Priority:** LOW - NICE TO HAVE
**macOS Behavior:** Smooth magnification with physics

### FEATURE #8: Dock - No Recently Opened Apps Section
**Expected:** Divider separating pinned apps from recent apps
**Current:** Only pinned apps shown
**Priority:** LOW
**macOS Behavior:** Recent apps appear after divider

### FEATURE #9: Menu Bar - Static App Name
**Expected:** Menu bar shows active app name and menus
**Current:** Always shows "Finder" in app menu
**Priority:** HIGH
**macOS Behavior:** Menu bar changes based on active app

### FEATURE #10: Menu Bar - No Working Menus
**Expected:** Clicking menu items opens dropdowns
**Current:** No dropdown menus implemented
**Priority:** HIGH - CRITICAL
**macOS Behavior:** File, Edit, View, Window, Help menus

### FEATURE #11: Menu Bar - No Control Center
**Expected:** Icons in top-right for WiFi, Bluetooth, Battery, etc.
**Current:** Only shows clock and search
**Priority:** MEDIUM
**macOS Behavior:** Control Center with system toggles

### FEATURE #12: Menu Bar - Clock Not Updating
**Expected:** Clock shows current time and updates every minute
**Current:** Static hardcoded time
**Priority:** MEDIUM
**macOS Behavior:** Live clock with date on click

### FEATURE #13: Desktop - No Wallpaper Selection
**Expected:** Right-click desktop → Change Wallpaper
**Current:** Fixed gradient background
**Priority:** LOW
**macOS Behavior:** System wallpapers, custom images

### FEATURE #14: Desktop - No Icon Auto-Arrange
**Expected:** View → Arrange By (Name, Date, Size, Kind)
**Current:** Icons stay where placed
**Priority:** LOW
**macOS Behavior:** Multiple sort options

### FEATURE #15: Desktop - Stacks Not Implemented
**Expected:** Group files by type in stacks
**Current:** Context menu has "Use Stacks" but doesn't work
**Priority:** LOW
**macOS Behavior:** Stacks organize desktop items

### FEATURE #16: Spotlight - No Real Search Results
**Expected:** Search apps, files, calculations, definitions
**Current:** Shows only hardcoded app results
**Priority:** MEDIUM
**macOS Behavior:** Universal search with live results

### FEATURE #17: Spotlight - No Quick Actions
**Expected:** Search results include actions (Open, Get Info, etc.)
**Current:** Only shows item names
**Priority:** LOW
**macOS Behavior:** Right side shows preview and actions

### FEATURE #18: Finder - No Quick Look
**Expected:** Space bar on file shows preview
**Current:** Not implemented
**Priority:** MEDIUM
**macOS Behavior:** Full-screen file preview with Space bar

### FEATURE #19: Finder - No File Operations
**Expected:** Drag files to move, copy with Option key
**Current:** Can't manipulate files
**Priority:** HIGH
**macOS Behavior:** Full drag-and-drop file management

### FEATURE #20: Safari - No Bookmarks
**Expected:** Bookmarks bar with favorites
**Current:** Not implemented
**Priority:** LOW
**macOS Behavior:** Bookmarks bar under address bar

### FEATURE #21: Safari - No Private Browsing
**Expected:** File → New Private Window
**Current:** Not implemented
**Priority:** LOW
**macOS Behavior:** Purple private browsing windows

### FEATURE #22: Terminal - No Multi-Tab Support
**Expected:** CMD+T opens new terminal tab
**Current:** Single session only
**Priority:** LOW
**macOS Behavior:** Multiple tabs in Terminal

---

## PART 3: UI/UX ISSUES (8 Issues)

### UI #1: Window Titlebar Lacks Subtitle
**Issue:** No subtitle or path shown in titlebar
**Expected:** Finder shows current path, apps show document name
**Priority:** LOW
**Example:** "Desktop — 3 items" in Finder

### UI #2: Window Toolbar Too Minimalist
**Issue:** App toolbars are very simple with few controls
**Expected:** Rich toolbars with icons and actions
**Priority:** MEDIUM
**Example:** Finder should have New Folder, Delete, Share, etc.

### UI #3: Scrollbars Not macOS Style
**Issue:** Using default browser scrollbars
**Expected:** Auto-hiding overlay scrollbars like macOS
**Priority:** LOW - POLISH
**CSS Fix:** Custom scrollbar styling

### UI #4: No Window Drop Shadow Variety
**Issue:** All windows have same shadow
**Expected:** Active window has darker shadow
**Priority:** LOW
**CSS Fix:** Different shadow for .window.active

### UI #5: Menu Bar Icons Too Simplistic
**Issue:** Time shows as text only
**Expected:** Icons for battery, WiFi, user, etc.
**Priority:** LOW
**Design:** Add icon set for system tray

### UI #6: Dock Reflection Missing
**Issue:** Dock has no reflection effect
**Expected:** Subtle reflection below dock items
**Priority:** LOW - POLISH
**CSS Fix:** :after pseudo-element with reflection

### UI #7: Animations Not Following macOS Timing
**Issue:** Some animations feel off compared to real macOS
**Expected:** Exact cubic-bezier curves from macOS
**Priority:** LOW - POLISH
**Research:** Profile real macOS animation timings

### UI #8: No Blur Effect on Inactive Windows
**Issue:** All windows equally sharp
**Expected:** Background windows slightly dimmed/blurred
**Priority:** LOW
**CSS Fix:** Opacity or filter on inactive windows

---

## PART 4: MISSING APPLICATIONS (10 Apps)

### APP #1: Photos 📸
**Priority:** HIGH
**Features Needed:**
- Grid view of sample photos
- Albums sidebar (Recents, Favorites, etc.)
- Photo viewer with zoom
- Basic info panel

### APP #2: FaceTime 📹
**Priority:** MEDIUM
**Features Needed:**
- Contact list
- Fake video call interface
- Call controls (mute, video on/off, end)
- Picture-in-picture

### APP #3: App Store 🛍️
**Priority:** MEDIUM
**Features Needed:**
- Featured apps grid
- Categories sidebar
- Fake app detail pages
- Install buttons (simulated)

### APP #4: Podcasts 🎙️
**Priority:** LOW
**Features Needed:**
- Podcast library
- Episode list
- Player controls similar to Music
- Sample podcast episodes

### APP #5: News 📰
**Priority:** LOW
**Features Needed:**
- Article cards grid
- Categories (Top Stories, Technology, etc.)
- Fake article reader
- Following section

### APP #6: Books 📚
**Priority:** LOW
**Features Needed:**
- Book shelf view
- Sample book covers
- Reader interface
- Library and Store tabs

### APP #7: Maps 🗺️
**Priority:** MEDIUM
**Features Needed:**
- Fake map view (static image or canvas)
- Search bar
- Directions panel
- Satellite/Map toggle

### APP #8: Weather ⛅
**Priority:** LOW
**Features Needed:**
- Current weather card
- 7-day forecast
- Location picker
- Fake weather data

### APP #9: Voice Memos 🎤
**Priority:** LOW
**Features Needed:**
- Recording list
- Fake waveform
- Play/Pause controls
- Delete functionality

### APP #10: Activity Monitor 📊
**Priority:** MEDIUM
**Features Needed:**
- CPU usage graph
- Memory usage
- Process list
- Network activity

---

## PART 5: ENHANCEMENT OPPORTUNITIES (5 Items)

### ENHANCEMENT #1: Notification Center
**Description:** Right sidebar for notifications and widgets
**Features:**
- Notification cards with app icons
- Widgets (Calendar, Weather, Stocks)
- Do Not Disturb toggle
- Slide in from right edge

**Priority:** MEDIUM
**Effort:** MEDIUM (200 lines CSS, 150 lines JS)

### ENHANCEMENT #2: Mission Control
**Description:** Show all windows in grid, switch spaces
**Features:**
- All open windows arranged in grid
- Desktop preview at top
- Hover to highlight window
- Click to activate window

**Priority:** MEDIUM
**Effort:** HIGH (300 lines JS for layout calculation)

### ENHANCEMENT #3: Launchpad
**Description:** Full-screen app grid launcher
**Features:**
- Paginated grid of all apps
- Search bar
- Page dots at bottom
- Smooth fade-in animation

**Priority:** LOW
**Effort:** MEDIUM (150 lines CSS, 100 lines JS)

### ENHANCEMENT #4: System Sounds
**Description:** Subtle audio feedback for actions
**Features:**
- Window minimize sound
- Trash sound
- Alert sound
- Use HTML5 Audio API

**Priority:** LOW - POLISH
**Effort:** LOW (50 lines JS, need audio files)

### ENHANCEMENT #5: Touch Bar Simulation
**Description:** Bottom bar simulating MacBook Pro Touch Bar
**Features:**
- Context-sensitive controls
- Brightness/volume sliders
- App-specific shortcuts
- Slide up from bottom

**Priority:** LOW - NICE TO HAVE
**Effort:** MEDIUM (200 lines)

---

## PART 6: IMPLEMENTATION PHASES

### Phase 3: Critical Fixes (Priority 1)
**Time Estimate:** 2-3 hours
**Size Impact:** +30KB

**Tasks:**
1. Fix Finder multiple windows state (Bug #1)
2. Fix Notes persistence (Bug #14)
3. Fix Calendar to be dynamic (Bug #15)
4. Fix Settings app to be functional (Bug #17)
5. Fix Terminal ls/pwd to use file system (Bug #9, #10)
6. Add Terminal command history (Bug #11)
7. Implement menu bar app switching (Feature #9)
8. Implement working menu dropdowns (Feature #10)
9. Fix Music playback simulation (Bug #18)
10. Add window keyboard shortcuts (Feature #2)

**Deliverables:**
- All critical bugs fixed
- Menu bar fully functional
- Core apps working properly

### Phase 4: Missing Features (Priority 1)
**Time Estimate:** 3-4 hours
**Size Impact:** +40KB

**Tasks:**
1. Finder view modes (Icon/List/Column) (Bug #3)
2. Finder search functionality (Bug #4)
3. Safari address bar navigation (Bug #7)
4. Safari new tab button (Bug #8)
5. Mail compose functionality (Bug #16)
6. Menu bar control center (Feature #11)
7. Live clock in menu bar (Feature #12)
8. Spotlight real search results (Feature #16)
9. Calculator keyboard support (Bug #13)
10. Dock context menus (Feature #6)

**Deliverables:**
- All high-priority features implemented
- Apps feel complete and functional

### Phase 5: New Applications (Priority 1)
**Time Estimate:** 4-5 hours
**Size Impact:** +50KB

**Tasks:**
1. Photos app with sample gallery
2. FaceTime app with call UI
3. App Store with featured apps
4. Maps app with fake map
5. Activity Monitor with stats

**Deliverables:**
- 5 new essential apps
- Total apps: 16

### Phase 6: UI/UX Polish (Priority 2)
**Time Estimate:** 2 hours
**Size Impact:** +10KB

**Tasks:**
1. Custom macOS scrollbars (UI #3)
2. Active window shadow enhancement (UI #4)
3. Menu bar icon improvements (UI #5)
4. Window titlebar subtitles (UI #1)
5. Inactive window dimming (UI #8)

**Deliverables:**
- Professional polish
- Authentic macOS feel

### Phase 7: System Features (Priority 2)
**Time Estimate:** 3 hours
**Size Impact:** +25KB

**Tasks:**
1. Notification Center (Enhancement #1)
2. Mission Control (Enhancement #2)
3. Launchpad (Enhancement #3)
4. Desktop wallpaper selector (Feature #13)

**Deliverables:**
- Full macOS system integration
- All major system features present

### Phase 8: Remaining Apps & Polish (Priority 3)
**Time Estimate:** 2-3 hours
**Size Impact:** +20KB

**Tasks:**
1. Add Podcasts, News, Books, Weather, Voice Memos apps
2. Dock magnification effect (Feature #7)
3. Dock reflection effect (UI #6)
4. System sounds (Enhancement #4)
5. Final testing and bug fixes

**Deliverables:**
- 21 total applications
- World-class simulator complete

---

## TOTAL SCOPE

**Total Bugs to Fix:** 18
**Total Features to Add:** 22
**Total New Apps:** 10
**Total UI Improvements:** 8
**Total Enhancements:** 5

**Grand Total:** 63 improvements

**Estimated Time:** 16-20 hours of implementation
**Estimated Final Size:** ~340KB (2x current size)
**Estimated Final Lines:** ~9,500 lines

---

## IMMEDIATE NEXT STEPS - PHASE 3

I will now implement Phase 3 (Critical Fixes) which includes:

✅ **10 Critical Fixes:**
1. Finder multi-window state isolation
2. Notes persistence in state
3. Dynamic calendar with navigation
4. Functional Settings app
5. Terminal connected to file system
6. Terminal command history
7. Menu bar app switching
8. Working menu dropdowns
9. Music playback simulation
10. Window keyboard shortcuts

**Target:** Fix all critical bugs, make simulator feel solid and professional.

---

**END OF PRD v4.0**
