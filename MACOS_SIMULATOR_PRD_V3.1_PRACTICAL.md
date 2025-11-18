# macOS Simulator - Practical Implementation PRD
## Version 3.1 - Hands-On Testing & Implementation Guide

**Date:** 2024-11-18
**Current Status:** 142KB, 10 apps, Phase 2A complete
**Target:** World-class simulator with unlimited budget
**Approach:** Fix real bugs first, then add features systematically

---

## PART 1: REAL BUGS FOUND (Code Analysis)

### 🔴 CRITICAL BUG #1: Terminal cd Doesn't Update Input Prompt
**File:** macos-simulator.html
**Lines:** 2732-2738, 2670
**Status:** BROKEN - Confirmed by code inspection

**The Problem:**
```javascript
case 'cd':
    state.terminal.currentDir = args[0];  // Changes state
    return '';  // Returns empty - no visual feedback!
```

The input line at bottom shows: `user@macos ~ %` forever
After `cd Desktop`, should show: `user@macos Desktop %`

**Root Cause:** Input prompt line is only generated once on Terminal render (line 2212-2228). Never regenerated after cd.

**The Fix:**
After cd command, need to:
1. Update state.terminal.currentDir ✓ (already does this)
2. Regenerate the input line HTML with new prompt
3. Re-attach event listener
4. Focus input

---

### 🔴 CRITICAL BUG #2: Finder Global State Conflict
**File:** macos-simulator.html
**Lines:** 2347-2350
**Status:** BROKEN - Will cause bugs with multiple windows

**The Problem:**
```javascript
let finderCurrentPath = 'Desktop';  // GLOBAL!
let finderHistory = ['Desktop'];     // GLOBAL!
let finderHistoryIndex = 0;          // GLOBAL!
```

Opening 2 Finder windows = they share the same navigation state!
Navigate in Window 1 → Window 2 also changes!

**The Fix:**
Make Finder state per-window:
```javascript
// Store in window data when creating Finder
windowData.finderState = {
    currentPath: 'Desktop',
    history: ['Desktop'],
    historyIndex: 0
};
```

---

### 🔴 CRITICAL BUG #3: Window Drag No Bounds Check
**File:** macos-simulator.html
**Lines:** 1976-2011 (makeDraggable)
**Status:** BROKEN - Can lose windows

**The Problem:**
```javascript
currentX = e.clientX - initialX;
currentY = e.clientY - initialY;
window.style.left = currentX + 'px';  // No bounds check!
window.style.top = currentY + 'px';   // Can go off-screen!
```

Can drag window completely off-screen, title bar becomes inaccessible.

**The Fix:**
```javascript
// Keep at least 20px of title bar visible
const maxX = window.innerWidth - 20;
const maxY = window.innerHeight - 20;
currentX = Math.max(-windowWidth + 100, Math.min(currentX, maxX));
currentY = Math.max(0, Math.min(currentY, maxY));
```

---

### 🟡 MEDIUM BUG #4: Window Resize No Min Enforcement
**File:** macos-simulator.html
**Lines:** 2013-2074 (makeResizable)
**Status:** PARTIALLY BROKEN

**The Problem:**
CSS has `minWidth: 320px` and `minHeight: 200px` but resize drag doesn't enforce it.
Can drag resize handle to make 50px × 50px window!

**The Fix:**
```javascript
// In resize logic:
let newWidth = startWidth + dx;
let newHeight = startHeight + dy;
newWidth = Math.max(320, newWidth);
newHeight = Math.max(200, newHeight);
window.style.width = newWidth + 'px';
```

---

### 🟡 MEDIUM BUG #5: Finder Back/Forward Broken
**File:** macos-simulator.html
**Lines:** 2488-2502
**Status:** BROKEN - Logic error

**The Problem:**
```javascript
function finderNavigate(path) {
    finderCurrentPath = path;  // Just sets to new path
    finderHistory = finderHistory.slice(0, finderHistoryIndex + 1);
    finderHistory.push(path);  // Always pushes
    finderHistoryIndex = finderHistory.length - 1;
    // ...
}
```

This breaks back/forward! When you click Back, it calls finderNavigate() which ADDS to history instead of just moving index!

**The Fix:**
Need separate function for user navigation vs history navigation:
```javascript
function finderNavigateTo(path) {
    // User clicked folder - add to history
}

function finderGoBack() {
    // Just move index, don't modify history
}
```

---

### 🟡 MEDIUM BUG #6: Safari Back/Forward Don't Work
**File:** macos-simulator.html
**Lines:** 2644-2651
**Status:** NOT IMPLEMENTED

**The Problem:**
```javascript
function safariBack() {
    console.log('Back');  // Just logs!
}

function safariForward() {
    console.log('Forward');  // Just logs!
}
```

**The Fix:**
Need to track navigation history per tab:
```javascript
tab.history = [];
tab.historyIndex = 0;
// When navigating, push to history
// Back/forward just move index
```

---

### 🟢 MINOR BUG #7: Context Menu Can Go Off-Screen
**File:** macos-simulator.html
**Lines:** 3225-3277
**Status:** MINOR ISSUE

**The Problem:**
```javascript
menu.style.left = `${x}px`;  // No boundary check!
menu.style.top = `${y}px`;
```

Right-click near right edge = menu appears off-screen.

**The Fix:**
```javascript
const menuWidth = 200;
const menuHeight = menu.offsetHeight || 300;
let left = x;
let top = y;

if (left + menuWidth > window.innerWidth) {
    left = window.innerWidth - menuWidth - 10;
}
if (top + menuHeight > window.innerHeight) {
    top = window.innerHeight - menuHeight - 10;
}
```

---

### 🟢 MINOR BUG #8: Desktop Icons Don't Snap to Grid
**File:** macos-simulator.html
**Lines:** 3164-3205 (startDragIcon)
**Status:** MISSING FEATURE

**The Problem:**
Icons can be placed at any pixel position like x=137, y=243.
Should snap to 20px grid: x=140, y=240.

**The Fix:**
```javascript
// After drag ends:
const gridSize = 20;
iconData.x = Math.round(iconData.x / gridSize) * gridSize;
iconData.y = Math.round(iconData.y / gridSize) * gridSize;
```

---

### 🟢 MINOR BUG #9: Calculator Display Can Overflow
**File:** macos-simulator.html
**Lines:** 609-621 (calc-display)
**Status:** MINOR ISSUE

**The Problem:**
```css
.calc-display {
    font-size: 48px;  // Fixed size!
    // No overflow handling
}
```

Very long numbers like 999999999999 overflow the container.

**The Fix:**
```css
.calc-display {
    font-size: 48px;
    overflow-x: auto;
    word-break: break-all;
}
```
Or dynamically reduce font-size in JS.

---

### 🟢 MINOR BUG #10: Dock Event Listeners Not Attached Early
**File:** macos-simulator.html
**Lines:** 3417-3438
**Status:** POTENTIAL RACE CONDITION

**The Problem:**
```javascript
document.addEventListener('DOMContentLoaded', () => {
    document.querySelectorAll('.dock-item[data-app]').forEach(item => {
        item.removeAttribute('onclick');  // Removes inline onclick
        item.addEventListener('click', ...);  // Adds listener
    });
});
```

If user clicks dock before DOMContentLoaded fires, nothing happens!
Fixed by removing onclick from HTML ✓, but listeners need to attach during boot sequence.

**The Fix:**
Move dock initialization into `performLogin()` function after desktop shows, or ensure it runs before boot screen hides.

---

## PART 2: MISSING FEATURES (Priority Order)

### 🎵 TIER 1: MUST HAVE (User Explicitly Requested)

#### 1. Apple Music
**Priority:** P0 - CRITICAL
**User Request:** "apple music and all it should have everything"
**Size Est:** 30KB
**Time:** 2 hours

**Features:**
- ✅ Sidebar: Listen Now, Browse, Radio, Library, Playlists
- ✅ Album grid with cover art (gradient placeholders)
- ✅ Now Playing bar at top
- ✅ Player controls: Play, Pause, Next, Previous, Shuffle, Repeat
- ✅ Volume slider
- ✅ Progress bar with seek
- ✅ Song list view
- ✅ Search
- ✅ Sample library: 15 albums, 60 songs, 10 artists
- ✅ Queue/Up Next

---

### 📸 TIER 1: ESSENTIAL SYSTEM APPS

#### 2. Photos
**Priority:** P0
**Size Est:** 25KB
**Time:** 1.5 hours

**Features:**
- ✅ Photo grid (4 columns)
- ✅ Sample library: 24 photos (colored gradient placeholders)
- ✅ Sidebar: Recents, Favorites, Albums, People, Places
- ✅ Click photo → Full screen view
- ✅ Navigation arrows
- ✅ Favorite button (heart icon)
- ✅ Share button
- ✅ Delete photo
- ✅ Zoom in/out
- ✅ Slideshow mode

---

### 🔔 TIER 2: SYSTEM FEATURES

#### 3. Notification Center
**Priority:** P0
**Size Est:** 15KB
**Time:** 1 hour

**Features:**
- ✅ Slide in from right
- ✅ Click notification icon in menu bar
- ✅ Blur background overlay
- ✅ Widgets:
  - Calendar (today's events)
  - Weather (current temp, conditions)
  - Clock (world clocks)
- ✅ Sample notifications from apps
- ✅ Clear All button
- ✅ Do Not Disturb toggle
- ✅ Click notification → open app

---

#### 4. Launchpad
**Priority:** P0
**Size Est:** 12KB
**Time:** 1 hour

**Features:**
- ✅ Full-screen blur overlay
- ✅ App grid (7×5 = 35 apps per page)
- ✅ All installed apps shown
- ✅ Page indicators (dots)
- ✅ Search bar at top
- ✅ Click app → Launch
- ✅ ESC to close
- ✅ Activation: Click menu bar icon or F4 key
- ✅ Arrow keys or swipe to change pages

---

#### 5. Mission Control
**Priority:** P1
**Size Est:** 12KB
**Time:** 1 hour

**Features:**
- ✅ Show all windows scaled down
- ✅ Desktop preview at top
- ✅ Hover → Highlight window
- ✅ Click → Focus and exit
- ✅ Activation: F3 key or menu bar
- ✅ Smooth zoom animations

---

### 📱 TIER 3: ADDITIONAL APPS

#### 6. FaceTime
**Priority:** P1
**Size Est:** 10KB

- Call interface
- Contacts list
- Camera preview placeholder
- Mute/End call buttons

#### 7. App Store
**Priority:** P1
**Size Est:** 15KB

- Today, Games, Apps, Arcade tabs
- Featured app cards
- Top Charts
- App detail page
- Download button (simulated)

#### 8. Books
**Priority:** P1
**Size Est:** 12KB

- Library with book covers
- Reading interface
- Page turning
- Bookmarks

#### 9. Podcasts
**Priority:** P2
**Size Est:** 10KB

- Library view
- Episode list
- Player controls

#### 10. News
**Priority:** P2
**Size Est:** 10KB

- Today feed
- Article cards
- Reader view

#### 11. Voice Memos
**Priority:** P2
**Size Est:** 8KB

- Recording list
- Record button
- Playback

#### 12. Weather
**Priority:** P2
**Size Est:** 10KB

- Current conditions
- Hourly/daily forecast
- Multiple locations

#### 13. Maps
**Priority:** P2
**Size Est:** 12KB

- Map view (simulated)
- Search
- Directions

#### 14. Activity Monitor
**Priority:** P1
**Size Est:** 10KB

- CPU/Memory/Disk/Network tabs
- Process list
- Force Quit

---

## PART 3: ENHANCEMENTS TO EXISTING APPS

### Enhanced Finder
**Priority:** P0
**Time:** 2 hours

- ✅ File selection (click to select)
- ✅ Multi-select (Cmd+Click, Shift+Click)
- ✅ Right-click context menu on files
- ✅ Copy/Cut/Paste (Cmd+C/X/V)
- ✅ Delete files (Cmd+Delete)
- ✅ Rename (Enter key)
- ✅ New Folder (Cmd+Shift+N)
- ✅ Get Info panel (Cmd+I)
- ✅ Search bar
- ✅ List view
- ✅ Column view
- ✅ Navigation fixes (back/forward/parent)

### Enhanced Terminal
**Priority:** P1
**Time:** 1 hour

- ✅ Fix cd prompt update
- ✅ Add commands: cp, mv, rm, grep, find, ps, top
- ✅ Command piping: `ls | grep txt`
- ✅ Output redirection: `ls > file.txt`
- ✅ Colored output
- ✅ Tab completion

### Enhanced Safari
**Priority:** P1
**Time:** 1 hour

- ✅ Fix back/forward navigation
- ✅ History tracking
- ✅ Bookmarks bar
- ✅ Add/manage bookmarks
- ✅ More simulated pages (10+)
- ✅ Downloads panel
- ✅ Reading list

### Enhanced Calculator
**Priority:** P2
**Time:** 30 min

- ✅ Fix display overflow
- ✅ Basic/Scientific mode toggle
- ✅ Memory functions (MC, M+, M-, MR)
- ✅ Calculation history

### Enhanced Notes
**Priority:** P1
**Time:** 1 hour

- ✅ Formatting toolbar
- ✅ Bold/Italic/Underline (Cmd+B/I/U)
- ✅ Lists (bullet, numbered, checklist)
- ✅ Font picker
- ✅ Text alignment

### Enhanced Calendar
**Priority:** P1
**Time:** 1 hour

- ✅ Day view
- ✅ Week view
- ✅ Create events (double-click date)
- ✅ Event form
- ✅ Event list sidebar
- ✅ Multiple calendars with colors

### Enhanced Mail
**Priority:** P1
**Time:** 1 hour

- ✅ Compose new email
- ✅ Reply/Forward
- ✅ Search emails
- ✅ Attachments
- ✅ Rich text editor

### Enhanced Messages
**Priority:** P2
**Time:** 30 min

- ✅ Emoji picker
- ✅ Photo/file attachments
- ✅ Read receipts
- ✅ Typing indicator

### Enhanced Reminders
**Priority:** P2
**Time:** 30 min

- ✅ Due date picker
- ✅ Priority levels
- ✅ Notes field
- ✅ Subtasks

---

## PART 4: DYNAMIC MENU BAR

**Priority:** P0
**Time:** 1.5 hours

**Features:**
- ✅ Track active window
- ✅ Change app name in menu bar
- ✅ Show app-specific menus
- ✅ Working menu commands:
  - File: New, Open, Close, Save
  - Edit: Undo, Redo, Cut, Copy, Paste, Select All
  - Window: Minimize, Zoom, Bring All to Front
  - Help: Search
- ✅ Keyboard shortcuts shown (⌘K format)
- ✅ Execute menu actions

---

## PART 5: ENHANCED DOCK

**Priority:** P0
**Time:** 1 hour

**Features:**
- ✅ Right-click context menu
- ✅ Options: Keep in Dock, Remove, Show in Finder
- ✅ Quit / Force Quit
- ✅ Downloads folder with stack
- ✅ Trash with full/empty states
- ✅ Empty Trash confirmation
- ✅ Trash item count
- ✅ Recent applications section
- ✅ Dock preferences:
  - Position (Bottom/Left/Right)
  - Size slider
  - Magnification toggle
  - Auto-hide

---

## PART 6: IMPLEMENTATION PLAN

### 🚀 PHASE 1: Critical Bug Fixes (1 hour)
**Do First - Fixes existing functionality**

1. ✅ Terminal cd prompt update
2. ✅ Finder global state → per-window
3. ✅ Window drag bounds checking
4. ✅ Window resize minimum enforcement
5. ✅ Finder back/forward fix
6. ✅ Safari back/forward implementation
7. ✅ Context menu positioning
8. ✅ Desktop icon grid snapping
9. ✅ Calculator overflow fix

---

### 🎵 PHASE 2: Apple Music (2 hours)
**User's #1 Priority**

1. ✅ Apple Music app structure
2. ✅ Player interface
3. ✅ Sample library (15 albums, 60 songs)
4. ✅ Playback controls
5. ✅ Now playing bar
6. ✅ Search
7. ✅ Queue management

---

### 📸 PHASE 3: Photos + System Features (3 hours)
**Essential Apps**

1. ✅ Photos app (1.5 hours)
2. ✅ Notification Center (1 hour)
3. ✅ Launchpad (0.5 hours)

---

### 🔧 PHASE 4: Enhanced Finder + Menu Bar (3.5 hours)
**Core Functionality**

1. ✅ Enhanced Finder (2 hours)
2. ✅ Dynamic Menu Bar (1.5 hours)

---

### 🎨 PHASE 5: Enhanced Dock + Mission Control (2 hours)
**System Improvements**

1. ✅ Enhanced Dock (1 hour)
2. ✅ Mission Control (1 hour)

---

### 📱 PHASE 6: Additional Apps (4 hours)
**More Applications**

1. ✅ FaceTime (0.5 hours)
2. ✅ App Store (1 hour)
3. ✅ Books (0.5 hours)
4. ✅ Activity Monitor (0.5 hours)
5. ✅ Podcasts (0.5 hours)
6. ✅ News (0.5 hours)
7. ✅ Voice Memos (0.25 hours)
8. ✅ Weather (0.5 hours)
9. ✅ Maps (0.75 hours)

---

### ✨ PHASE 7: Enhanced Existing Apps (4 hours)
**Polish Everything**

1. ✅ Enhanced Safari (1 hour)
2. ✅ Enhanced Terminal (1 hour)
3. ✅ Enhanced Notes (0.5 hours)
4. ✅ Enhanced Calendar (0.5 hours)
5. ✅ Enhanced Mail (0.5 hours)
6. ✅ Enhanced Calculator (0.25 hours)
7. ✅ Enhanced Messages (0.25 hours)
8. ✅ Enhanced Reminders (0.25 hours)

---

### 🧪 PHASE 8: Testing & Polish (2 hours)
**Final Quality Pass**

1. ✅ Bug testing
2. ✅ Animation refinement
3. ✅ Performance optimization
4. ✅ Cross-browser testing
5. ✅ Visual polish

---

## PART 7: PROJECTED FINAL STATE

**Total Implementation Time:** 22-24 hours of focused coding
**Total Features:** 150+ features
**Total Applications:** 23 apps
**File Size:** 400-500KB
**Lines of Code:** 9,000-11,000

**Feature Breakdown:**
- ✅ Boot & Login
- ✅ 23 fully functional apps
- ✅ All system features (Launchpad, Notification Center, Mission Control)
- ✅ Dynamic menus
- ✅ Complete Finder
- ✅ Enhanced Dock
- ✅ Pixel-perfect design
- ✅ All keyboard shortcuts
- ✅ Smooth animations
- ✅ Zero critical bugs

---

## PART 8: IMMEDIATE ACTION PLAN

### Starting NOW:

**Response 1 (This one):**
- Fix all 10 critical bugs (1 hour of coding)
- Implement Apple Music (2 hours of coding)
- Total: ~3 hours = ~80KB added

**Response 2:**
- Photos app
- Notification Center
- Launchpad
- Total: ~3 hours = ~60KB added

**Response 3:**
- Enhanced Finder
- Dynamic Menu Bar
- Total: ~3.5 hours = ~70KB added

**Response 4:**
- Enhanced Dock
- Mission Control
- Additional apps (FaceTime, App Store, Books)
- Total: ~4 hours = ~80KB added

**Response 5:**
- Remaining apps (8 apps)
- Enhanced existing apps
- Testing & polish
- Total: ~6 hours = ~100KB added

**Final:** 500KB, 23 apps, world-class quality ✨

---

## CONCLUSION

This PRD is based on actual code inspection and identifies real, confirmed bugs plus a systematic plan to implement every requested feature. The simulator will be transformed from a good demo to a professional-grade, pixel-perfect macOS replica.

**Now beginning implementation of Phase 1 (Bug Fixes) + Phase 2 (Apple Music)...**

---

**Document Version:** 3.1 (Practical Implementation Guide)
**Last Updated:** 2024-11-18
**Status:** IMPLEMENTING NOW ⚡
