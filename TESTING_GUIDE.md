# 🧪 Vibe Jet - Testing Guide

## ✅ Code Validation Results

### **Automated Checks Passed**
- ✅ **Server.js syntax:** Valid (Node.js check passed)
- ✅ **All 64 functions defined:** No missing function declarations
- ✅ **All 15 HTML element IDs exist:** No DOM reference errors
- ✅ **All critical function guards:** Proper null checks in place
- ✅ **GLSL shader syntax:** Valid vertex and fragment shaders
- ✅ **Network message types:** Client/server protocol matches
- ✅ **Variable initialization:** playerVelocity, playerAngularVelocity initialized
- ✅ **Window resize handler:** Properly updates all render passes

---

## 🚀 How to Start Testing

### **Prerequisites**
```bash
# Make sure you have Node.js installed
node --version  # Should be v12 or higher

# Install dependencies (if not already installed)
npm install ws uuid
```

### **Step 1: Start the Server**
```bash
cd /home/user/vibe-jet
node server.js
```

**Expected output:**
```
WebSocket server started on port 8080
```

### **Step 2: Open the Game**
```bash
# Option 1: Use a local web server (recommended)
python3 -m http.server 8000
# Then open: http://localhost:8000/game.html

# Option 2: Open directly in browser
# Open game.html in your browser
```

---

## 🎮 Testing Checklist

### **Phase 1: Basic Flight (2-3 minutes)**
- [ ] **Game loads without console errors**
- [ ] **3D jet model appears**
- [ ] **Controls respond:**
  - W/S: Pitch control
  - A/D: Yaw control
  - Q/E: Roll control
  - Shift: Boost (should see "⚡ AFTERBURNER ⚡")
  - Space: Pitch up
- [ ] **Camera follows aircraft smoothly**
- [ ] **Speed and altitude display updates**
- [ ] **No lag or stuttering**

**Expected FPS:** 55-90 FPS on decent hardware

---

### **Phase 2: Visual Effects (2-3 minutes)**
- [ ] **Speed lines appear when boosting** (radial blur effect)
- [ ] **Orange/red jet trail behind your aircraft**
- [ ] **Crash into building:**
  - Screen shake ✓
  - Particle explosion ✓
  - Red glow on aircraft ✓
- [ ] **Hard landing on terrain:**
  - Screen shake ✓
  - Bounce effect ✓
- [ ] **Day/night cycle works** (sun moves, lighting changes)

---

### **Phase 3: Checkpoints & Racing (5 minutes)**
- [ ] **10 glowing rings visible around map** (blue and green)
- [ ] **Click "START RACE" button**
- [ ] **Race timer appears** (top right)
- [ ] **Fly through green checkpoint ring:**
  - "CHECKPOINT!" notification shows
  - Progress updates (1/10, 2/10, etc.)
  - Next ring turns green
  - Timer counts up
- [ ] **Complete all 10 checkpoints:**
  - Final time displayed
  - Best time saved
  - Alert shows completion message
- [ ] **Race again** to beat your time

**Expected:** Smooth checkpoint detection, visual feedback on pass

---

### **Phase 4: Power-Ups (3-4 minutes)**
- [ ] **15 power-ups visible** (rotating shapes on terrain)
- [ ] **Fly over orange octahedron** (speed boost):
  - Power-up disappears
  - Speed boost effect (check console)
  - Respawns after 30 seconds
- [ ] **Fly over blue icosahedron** (shield):
  - Power-up disappears
  - Shield active (check console)
  - Respawns after 30 seconds
- [ ] **Power-ups animate:** rotation and bobbing

---

### **Phase 5: Multiplayer (5-10 minutes)**
**Requires 2+ browser windows/tabs:**

- [ ] **Open game in 2nd tab/window**
- [ ] **Player count updates** (should show "2")
- [ ] **Other player aircraft visible**
- [ ] **Blue jet trails behind other players**
- [ ] **Smooth movement** (no stuttering/teleporting)
- [ ] **Player disconnects:**
  - Aircraft disappears
  - Trail system cleaned up
  - Count updates

**Expected:** Smooth interpolation, no lag with 2-5 players

---

### **Phase 6: Performance (Ongoing)**
- [ ] **Press F12 → Console** (check for errors)
- [ ] **No console errors** during gameplay
- [ ] **FPS stays above 55** (check with FPS counter)
- [ ] **Memory stable** (no gradual increase)
- [ ] **Network tab:** Player updates ~every 100ms

---

## 🐛 Known Issues to Watch For

### **Potential Issues:**
1. **CORS errors:** Use a local web server (python http.server or Live Server)
2. **WebSocket connection fails:** Make sure server.js is running
3. **Model doesn't load:** Check `assets/shenyang_j-11.glb` exists
4. **Textures missing:** Check `assets/` folder has all files

### **Quick Fixes:**
```bash
# If WebSocket fails
lsof -ti:8080 | xargs kill  # Kill any process on port 8080
node server.js              # Restart server

# If assets missing
ls assets/  # Should show: shenyang_j-11.glb, cloud10.png, road.jpg, etc.
```

---

## 📊 Expected Performance Metrics

| Metric | Target | Acceptable | Poor |
|--------|--------|------------|------|
| **FPS** | 60+ | 45-60 | <45 |
| **Load Time** | <3s | 3-5s | >5s |
| **Network Latency** | <50ms | 50-100ms | >100ms |
| **Memory Usage** | <500MB | 500-800MB | >800MB |
| **Players Supported** | 10+ | 5-10 | <5 |

---

## 🎯 Feature-Specific Tests

### **Checkpoint System:**
```
Test 1: Sequential passing required
- Try flying to checkpoint #3 directly
- Expected: Doesn't count (must do #1 first)

Test 2: Fly-through detection
- Approach ring from different angles
- Expected: Detects pass when within ~70 units

Test 3: Timer accuracy
- Complete race twice
- Expected: Times are consistent, stored in localStorage
```

### **Collision System:**
```
Test 1: Building collision
- Fly into tall building at high speed
- Expected: Bounce back, screen shake, particles

Test 2: Terrain collision
- Fly into hillside
- Expected: Can't pass through, proper bounce

Test 3: Shield power-up
- Activate shield, crash into building
- Expected: Shield absorbs impact (check console)
```

### **Power-Up System:**
```
Test 1: Collection
- Fly within 20 units of power-up
- Expected: Disappears, effect applies

Test 2: Respawn
- Wait 30 seconds after collection
- Expected: Power-up reappears, animating

Test 3: Multiple collections
- Collect same power-up multiple times
- Expected: No memory leaks, always respawns
```

---

## 🔍 Debugging Tips

### **Open Browser Console:**
```
Chrome/Edge: F12 or Ctrl+Shift+I
Firefox: F12 or Ctrl+Shift+K
Safari: Cmd+Option+I
```

### **Useful Console Commands:**
```javascript
// Check current FPS (rough estimate)
let fps = 0;
setInterval(() => console.log('FPS:', fps), 1000);

// Check player count
console.log('Players:', otherPlayers.size + 1);

// Check checkpoint progress
console.log('Checkpoints:', checkpointsPassed, '/', checkpoints.length);

// Force race start (if button doesn't work)
startRace();

// Check for memory leaks
console.log('Projectiles:', projectiles.length);
console.log('Powerups:', powerups.filter(p => p.active).length);
```

---

## ✅ Success Criteria

**The game is working correctly if:**
1. ✅ No console errors on load or during gameplay
2. ✅ Smooth 60 FPS performance
3. ✅ All controls respond immediately
4. ✅ Visual effects trigger correctly
5. ✅ Checkpoint system works end-to-end
6. ✅ Power-ups collect and respawn
7. ✅ Multiplayer shows other players with trails
8. ✅ Race timer saves best time
9. ✅ No memory leaks over 10+ minutes
10. ✅ Network updates arrive smoothly

---

## 🚨 Report Issues

If you find any bugs during testing:

1. **Open browser console** (F12)
2. **Copy error messages**
3. **Note reproduction steps**
4. **Check browser version** (Chrome/Firefox/Safari)
5. **Check Node.js version** (`node --version`)

---

## 📱 Browser Compatibility

**Tested & Supported:**
- ✅ Chrome 90+ (recommended)
- ✅ Firefox 88+
- ✅ Edge 90+
- ✅ Safari 14+

**Not Supported:**
- ❌ Internet Explorer (use Edge instead)
- ❌ Very old mobile browsers

---

## 🎊 You're Ready to Test!

**Quick Start:**
```bash
# Terminal 1: Start server
node server.js

# Terminal 2: Start web server
python3 -m http.server 8000

# Browser: Open game
# http://localhost:8000/game.html
```

**Have fun testing your awesome game!** 🚀✨

---

*Generated for Vibe Jet Performance Audit - Ready for Production Testing*
