# macOS Simulator PRD v5.0 - Final Implementation Plan
## "World's Best Simulator - Exact Replica" - Complete Roadmap

**Date:** 2025-11-18
**Current Status:** Phase 3 Complete - 11 apps, 175KB
**Final Goal:** 21+ apps, all features, pixel-perfect replica
**Budget:** UNLIMITED SIZE

---

## EXECUTIVE SUMMARY

This PRD outlines the complete implementation plan to transform the current simulator into the **definitive macOS simulator**.

**Current State:** 11 functional apps, excellent core functionality
**Target State:** 21+ apps, complete macOS ecosystem, all system features

**Total Work Remaining:**
- **Phase 4:** Fix 7 bugs in existing apps (~470 lines)
- **Phase 5:** Add 5 essential apps (~800 lines)
- **Phase 6:** Add system features (~350 lines)
- **Phase 7:** UI/UX polish (~200 lines)
- **Phase 8:** Add 5 more apps + final polish (~600 lines)

**Total Estimate:** ~2,420 lines, final size ~250KB

---

## PHASE 4: COMPLETE EXISTING APPS (470 LINES)

### PHASE 4A: HIGH PRIORITY FIXES (4 issues, ~270 lines)

#### 4A.1: Finder - View Mode Switching (Icon/List/Column)
**Current:** Toolbar buttons ⊞ ⊟ ≣ do nothing
**Location:** macos-simulator.html:2519-2521
**Priority:** HIGH

**Implementation:**
```javascript
// Add to state
state.finder = {
    viewMode: 'icon' // 'icon', 'list', 'column'
};

// Update toolbar buttons
function setFinderViewMode(mode) {
    state.finder.viewMode = mode;
    renderFinderItems();
}

// Modify finderNavigate to use view mode
function renderFinderItems() {
    if (state.finder.viewMode === 'list') {
        // List view with name, size, date columns
    } else if (state.finder.viewMode === 'column') {
        // Miller columns view
    } else {
        // Icon view (current)
    }
}
```

**CSS Needed:**
- `.finder-item.list-view` - row layout
- `.finder-item.column-view` - column layout
- `.finder-column-header` - sortable headers

**Lines:** ~100 lines (CSS + JS)

---

#### 4A.2: Finder - Search Functionality
**Current:** No search capability
**Location:** Add to finder toolbar
**Priority:** HIGH

**Implementation:**
```html
<input type="text" class="finder-search"
       placeholder="Search"
       oninput="finderSearch(this.value)">
```

```javascript
function finderSearch(query) {
    const windowEl = getActiveFinderWindow();
    const finderState = getFinderState(windowEl);
    const currentPath = finderState.currentPath;
    const allItems = state.fileSystem['/'][currentPath]?.contents || {};

    if (!query.trim()) {
        // Show all items
        renderFinderItems(allItems);
        return;
    }

    // Filter items by name
    const filtered = Object.entries(allItems).filter(([name]) =>
        name.toLowerCase().includes(query.toLowerCase())
    );

    renderFinderItems(Object.fromEntries(filtered));
}
```

**Lines:** ~80 lines

---

#### 4A.3: Safari - Address Bar Navigation
**Current:** Typing in address bar does nothing
**Location:** macos-simulator.html:2557
**Priority:** HIGH

**Implementation:**
```javascript
// In Safari render function
document.getElementById('address-bar').addEventListener('keydown', (e) => {
    if (e.key === 'Enter') {
        const url = e.target.value.trim();
        if (url) {
            safariNavigate(url, true);
            e.target.blur();
        }
    }
});
```

**Lines:** ~10 lines

---

#### 4A.4: Calculator - Keyboard Support
**Current:** Keyboard input doesn't work
**Location:** Calculator app
**Priority:** HIGH

**Implementation:**
```javascript
// Add to calculator render function
container.addEventListener('keydown', (e) => {
    if (e.key >= '0' && e.key <= '9') {
        calcNumber(e.key);
    } else if (e.key === '.') {
        calcDecimal();
    } else if (e.key === '+' || e.key === '-' ||
               e.key === '*' || e.key === '/') {
        calcOperation(e.key);
    } else if (e.key === 'Enter' || e.key === '=') {
        calcEquals();
    } else if (e.key === 'Escape' || e.key === 'c') {
        calcClear();
    }
});

// Focus calculator on window activation
container.setAttribute('tabindex', '0');
container.focus();
```

**Lines:** ~30 lines

---

### PHASE 4B: MEDIUM PRIORITY FIXES (3 issues, ~170 lines)

#### 4B.1: Safari - New Tab Button
**Current:** No way to add tabs from UI
**Location:** Safari tabs container
**Priority:** MEDIUM

**Implementation:**
```html
<div class="safari-tabs" id="safari-tabs">
    <!-- tabs rendered here -->
    <div class="safari-new-tab" onclick="safariNewTab()">+</div>
</div>
```

```javascript
function safariNewTab() {
    const newId = state.safari.nextTabId++;
    state.safari.tabs.forEach(t => t.active = false);
    state.safari.tabs.push({
        id: newId,
        title: 'New Tab',
        url: '',
        active: true,
        history: [''],
        historyIndex: 0
    });
    renderSafariTabs();
    renderSafariContent();
}
```

**CSS:**
```css
.safari-new-tab {
    width: 30px;
    height: 30px;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    font-size: 20px;
    color: var(--text-secondary);
}
.safari-new-tab:hover {
    background: var(--bg-secondary);
    border-radius: 4px;
}
```

**Lines:** ~30 lines

---

#### 4B.2: Safari - Better Content Display
**Current:** Shows placeholder text only
**Location:** renderSafariContent function
**Priority:** MEDIUM

**Implementation:**
```javascript
function renderSafariContent() {
    const activeTab = state.safari.tabs.find(t => t.active);
    if (!activeTab) return;

    const content = document.getElementById('safari-content');
    const url = activeTab.url;

    // Show different fake content based on URL
    if (url.includes('apple.com')) {
        content.innerHTML = `
            <div class="safari-page">
                <div class="safari-page-header">
                    <h1>🍎 Apple</h1>
                    <p>Think Different</p>
                </div>
                <div class="safari-page-content">
                    <div class="product-card">
                        <h2>iPhone 15 Pro</h2>
                        <p>Titanium. So strong. So light. So Pro.</p>
                    </div>
                    <div class="product-card">
                        <h2>MacBook Pro</h2>
                        <p>Mind-blowing. Head-turning.</p>
                    </div>
                </div>
            </div>
        `;
    } else if (url.includes('google')) {
        content.innerHTML = `
            <div class="safari-page">
                <div class="google-search">
                    <div class="google-logo">Google</div>
                    <input type="text" class="google-search-box" placeholder="Search Google">
                </div>
            </div>
        `;
    } else if (url) {
        content.innerHTML = `
            <div class="safari-page">
                <h1>Website Preview</h1>
                <p>Simulated content for: ${url}</p>
                <p>In a real implementation, this would use an iframe or fetch the actual page.</p>
            </div>
        `;
    } else {
        content.innerHTML = `
            <div class="safari-page">
                <h1>Safari Start Page</h1>
                <div class="favorites-grid">
                    <div class="favorite-item" onclick="safariNavigate('https://apple.com')">
                        <div class="fav-icon">🍎</div>
                        <div class="fav-name">Apple</div>
                    </div>
                    <div class="favorite-item" onclick="safariNavigate('https://google.com')">
                        <div class="fav-icon">G</div>
                        <div class="fav-name">Google</div>
                    </div>
                </div>
            </div>
        `;
    }
}
```

**Lines:** ~100 lines

---

#### 4B.3: Mail - Compose Functionality
**Current:** Compose button doesn't work
**Location:** Mail app
**Priority:** MEDIUM

**Implementation:**
```javascript
function composeMail() {
    const modal = document.createElement('div');
    modal.className = 'mail-compose-modal';
    modal.innerHTML = `
        <div class="mail-compose">
            <div class="compose-header">
                <span>New Message</span>
                <button onclick="closeCompose()">✕</button>
            </div>
            <div class="compose-fields">
                <input type="text" placeholder="To:" id="compose-to">
                <input type="text" placeholder="Subject:" id="compose-subject">
            </div>
            <textarea class="compose-body" id="compose-body"
                      placeholder="Message"></textarea>
            <div class="compose-footer">
                <button class="btn btn-primary" onclick="sendComposedMail()">Send</button>
            </div>
        </div>
    `;
    document.body.appendChild(modal);
}

function sendComposedMail() {
    const to = document.getElementById('compose-to').value;
    const subject = document.getElementById('compose-subject').value;
    const body = document.getElementById('compose-body').value;

    // Add to sent folder
    state.mail.folders.sent.push({
        id: Date.now(),
        sender: 'Me',
        subject: subject || '(No Subject)',
        preview: body.substring(0, 50),
        content: body
    });

    closeCompose();
    renderMailList();
}

function closeCompose() {
    document.querySelector('.mail-compose-modal')?.remove();
}
```

**Lines:** ~40 lines

---

### PHASE 4C: MENU BAR ENHANCEMENTS (30 lines)

#### 4C.1: Show Active App Name in Menu Bar
**Current:** Always shows "Finder"
**Location:** macos-simulator.html:1759
**Priority:** HIGH

**Implementation:**
```javascript
// In WindowManager.setActiveWindow
static setActiveWindow(windowId) {
    const window = document.getElementById(windowId);
    if (!window) return;

    // ... existing code ...

    // Update menu bar app name
    const windowData = state.windows.find(w => w.id === windowId);
    if (windowData) {
        const app = apps[windowData.appId];
        if (app) {
            document.getElementById('app-menu-name').textContent = app.name;
        }
    }
}
```

**Lines:** ~10 lines

---

#### 4C.2: Live Clock in Menu Bar
**Current:** Static time display
**Location:** Menu bar
**Priority:** MEDIUM

**Implementation:**
```javascript
// Add to DOMContentLoaded
function updateMenuBarClock() {
    const now = new Date();
    const timeStr = now.toLocaleTimeString('en-US', {
        hour: 'numeric',
        minute: '2-digit',
        hour12: true
    });
    const dateStr = now.toLocaleDateString('en-US', {
        weekday: 'short',
        month: 'short',
        day: 'numeric'
    });

    const clockEl = document.getElementById('menu-bar-time');
    if (clockEl) {
        clockEl.textContent = `${dateStr} ${timeStr}`;
    }
}

// Update every minute
setInterval(updateMenuBarClock, 60000);
updateMenuBarClock(); // Initial update
```

**HTML Update:**
```html
<div class="menu-right">
    <div class="menu-item" id="menu-bar-time">Mon Nov 18 2:30 PM</div>
    <div class="menu-item" onclick="toggleSpotlight()">🔍</div>
</div>
```

**Lines:** ~20 lines

---

## PHASE 5: ESSENTIAL NEW APPS (800 LINES)

### PHASE 5A: CRITICAL APPS (3 apps, ~480 lines)

#### 5A.1: Photos App (150 lines)
**Priority:** HIGH
**Features:**
- Grid view of sample photos (emoji-based)
- Albums sidebar
- Photo viewer on click
- Delete functionality

**Implementation:**
```javascript
photos: {
    id: 'photos',
    name: 'Photos',
    width: '1100px',
    height: '700px',
    render: (container) => {
        container.innerHTML = `
            <div class="photos-container">
                <div class="photos-sidebar">
                    <div class="photos-sidebar-item active">📷 All Photos</div>
                    <div class="photos-sidebar-item">⭐ Favorites</div>
                    <div class="photos-sidebar-item">📁 Albums</div>
                    <div class="photos-sidebar-item">🗑️ Recently Deleted</div>
                </div>
                <div class="photos-main">
                    <div class="photos-grid" id="photos-grid"></div>
                </div>
            </div>
        `;
        renderPhotosGrid();
    }
}
```

**Sample Photos:**
```javascript
state.photos = {
    items: [
        { id: 1, emoji: '🌅', title: 'Sunset', date: '2025-01-15', favorite: false },
        { id: 2, emoji: '🏔️', title: 'Mountains', date: '2025-01-14', favorite: true },
        { id: 3, emoji: '🌊', title: 'Beach', date: '2025-01-13', favorite: false },
        { id: 4, emoji: '🌲', title: 'Forest', date: '2025-01-12', favorite: false },
        { id: 5, emoji: '🏙️', title: 'City', date: '2025-01-11', favorite: true },
        { id: 6, emoji: '🌸', title: 'Flowers', date: '2025-01-10', favorite: false },
        { id: 7, emoji: '🌈', title: 'Rainbow', date: '2025-01-09', favorite: false },
        { id: 8, emoji: '🌙', title: 'Night', date: '2025-01-08', favorite: true },
        { id: 9, emoji: '☀️', title: 'Sunny Day', date: '2025-01-07', favorite: false },
        { id: 10, emoji: '⛰️', title: 'Peak', date: '2025-01-06', favorite: false },
    ]
};
```

---

#### 5A.2: FaceTime App (160 lines)
**Priority:** HIGH
**Features:**
- Contact list
- Fake video call interface
- Call controls
- Picture-in-picture simulation

**Implementation:**
```javascript
facetime: {
    id: 'facetime',
    name: 'FaceTime',
    width: '800px',
    height: '600px',
    render: (container) => {
        if (!state.facetime.inCall) {
            container.innerHTML = `
                <div class="facetime-container">
                    <div class="facetime-contacts">
                        ${state.facetime.contacts.map(c => `
                            <div class="facetime-contact" onclick="startFaceTimeCall('${c.name}')">
                                <div class="contact-avatar">${c.avatar}</div>
                                <div class="contact-name">${c.name}</div>
                                <div class="contact-call-btn">📹</div>
                            </div>
                        `).join('')}
                    </div>
                </div>
            `;
        } else {
            container.innerHTML = `
                <div class="facetime-call">
                    <div class="call-video">
                        <div class="remote-video">
                            <div class="video-placeholder">${state.facetime.currentCall.avatar}</div>
                            <div class="caller-name">${state.facetime.currentCall.name}</div>
                        </div>
                        <div class="local-video">
                            <div class="video-placeholder-small">👤</div>
                        </div>
                    </div>
                    <div class="call-controls">
                        <button class="call-btn" onclick="toggleFaceTimeMute()">
                            ${state.facetime.muted ? '🔇' : '🔊'}
                        </button>
                        <button class="call-btn end-call" onclick="endFaceTimeCall()">📞</button>
                        <button class="call-btn" onclick="toggleFaceTimeVideo()">
                            ${state.facetime.videoOff ? '📹' : '🎥'}
                        </button>
                    </div>
                </div>
            `;
        }
    }
}
```

**State:**
```javascript
state.facetime = {
    inCall: false,
    currentCall: null,
    muted: false,
    videoOff: false,
    contacts: [
        { name: 'Mom', avatar: '👩' },
        { name: 'Dad', avatar: '👨' },
        { name: 'Sarah', avatar: '👧' },
        { name: 'John', avatar: '👦' },
        { name: 'Emily', avatar: '👱‍♀️' }
    ]
};
```

---

#### 5A.3: App Store (170 lines)
**Priority:** HIGH
**Features:**
- Featured apps grid
- Categories
- App detail pages
- Install simulation

**Implementation:**
```javascript
appstore: {
    id: 'appstore',
    name: 'App Store',
    width: '1000px',
    height: '700px',
    render: (container) => {
        container.innerHTML = `
            <div class="appstore-container">
                <div class="appstore-sidebar">
                    <div class="appstore-nav-item active">🏠 Today</div>
                    <div class="appstore-nav-item">🎮 Games</div>
                    <div class="appstore-nav-item">📱 Apps</div>
                    <div class="appstore-nav-item">🎵 Music</div>
                    <div class="appstore-nav-item">⬇️ Updates</div>
                </div>
                <div class="appstore-main">
                    <div class="appstore-header">
                        <h1>Featured Apps</h1>
                    </div>
                    <div class="appstore-grid" id="appstore-grid"></div>
                </div>
            </div>
        `;
        renderAppStoreApps();
    }
}
```

**Sample Apps:**
```javascript
state.appstore = {
    apps: [
        { id: 1, name: 'Productivity Pro', icon: '✅', category: 'Productivity',
          price: 'Free', rating: 4.8, description: 'Get things done faster' },
        { id: 2, name: 'Photo Editor', icon: '🎨', category: 'Photo & Video',
          price: '$9.99', rating: 4.5, description: 'Professional editing' },
        { id: 3, name: 'Music Maker', icon: '🎵', category: 'Music',
          price: '$4.99', rating: 4.7, description: 'Create amazing tracks' },
        { id: 4, name: 'Fitness Tracker', icon: '💪', category: 'Health',
          price: 'Free', rating: 4.6, description: 'Track your workouts' },
        { id: 5, name: 'Budget Manager', icon: '💰', category: 'Finance',
          price: '$2.99', rating: 4.9, description: 'Manage your money' },
        { id: 6, name: 'Code Editor', icon: '💻', category: 'Developer Tools',
          price: 'Free', rating: 4.8, description: 'Write code anywhere' },
    ]
};
```

---

### PHASE 5B: USEFUL APPS (2 apps, ~320 lines)

#### 5B.1: Maps App (180 lines)
**Priority:** MEDIUM
**Features:**
- Fake map display (CSS gradient + markers)
- Search bar
- Directions panel
- Satellite/Map toggle

#### 5B.2: Activity Monitor (140 lines)
**Priority:** MEDIUM
**Features:**
- Fake CPU/Memory graphs
- Process list
- Network activity
- System stats

---

## PHASE 6: SYSTEM FEATURES (350 LINES)

### 6.1: Notification Center (200 lines)
**Implementation:**
- Slide-in panel from right edge
- Notification cards
- Widgets (Calendar, Weather)
- Do Not Disturb toggle

### 6.2: Mission Control (100 lines)
**Implementation:**
- Show all windows in grid
- Desktop preview at top
- Click to activate window
- ESC to exit

### 6.3: Launchpad (50 lines)
**Implementation:**
- Full-screen app grid
- Search bar
- Page dots
- Fade animation

---

## PHASE 7: UI/UX POLISH (200 LINES)

### 7.1: Custom Scrollbars (30 lines)
### 7.2: Window Shadow Variations (20 lines)
### 7.3: Menu Bar Icons (40 lines)
### 7.4: Window Titlebar Subtitles (30 lines)
### 7.5: Inactive Window Dimming (20 lines)
### 7.6: Dock Magnification Effect (60 lines)

---

## PHASE 8: FINAL APPS + POLISH (600 LINES)

### 8.1: Podcasts App (120 lines)
### 8.2: News App (120 lines)
### 8.3: Books App (120 lines)
### 8.4: Weather App (100 lines)
### 8.5: Voice Memos App (80 lines)
### 8.6: Final Testing & Bug Fixes (60 lines)

---

## IMPLEMENTATION ORDER

**Session 1 - Phase 4 (470 lines):**
1. Finder view modes + search
2. Safari address bar + new tab
3. Calculator keyboard
4. Mail compose
5. Menu bar enhancements

**Session 2 - Phase 5 (800 lines):**
1. Photos app
2. FaceTime app
3. App Store
4. Maps app
5. Activity Monitor

**Session 3 - Phases 6-8 (1,150 lines):**
1. Notification Center
2. Mission Control + Launchpad
3. UI/UX polish
4. Final 5 apps
5. Testing & refinement

---

## FINAL STATISTICS

**Starting Point:** 175KB, 4,730 lines, 11 apps
**Final Target:** ~260KB, ~7,150 lines, 21 apps

**Apps (21 total):**
1. ✅ Finder
2. ✅ Safari
3. ✅ Terminal
4. ✅ Calculator
5. ✅ Notes
6. ✅ Calendar
7. ✅ Mail
8. ✅ Settings
9. ✅ Messages
10. ✅ Reminders
11. ✅ Music
12. 📷 Photos (NEW)
13. 📹 FaceTime (NEW)
14. 🛍️ App Store (NEW)
15. 🗺️ Maps (NEW)
16. 📊 Activity Monitor (NEW)
17. 🎙️ Podcasts (NEW)
18. 📰 News (NEW)
19. 📚 Books (NEW)
20. ⛅ Weather (NEW)
21. 🎤 Voice Memos (NEW)

**System Features:**
- ✅ Boot & Login
- ✅ Desktop & Icons
- ✅ Windows & Dock
- ✅ Menu Bar & Spotlight
- 🆕 Notification Center
- 🆕 Mission Control
- 🆕 Launchpad

---

**END OF PRD v5.0**

Let's build the world's best macOS simulator! 🚀
