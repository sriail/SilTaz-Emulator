# Testing Guide for SliTaz Browser VM

This guide helps you test the SliTaz Browser VM emulator at various stages of development and deployment.

## Pre-Deployment Tests (Local)

### 1. Static File Serving Test

**Verify the HTML file is accessible:**

```bash
# Start local server
cd SilTaz-Emulator
python3 -m http.server 8000

# In another terminal, test
curl -I http://localhost:8000/index.html
```

**Expected result:**
```
HTTP/1.0 200 OK
Content-type: text/html
```

### 2. Browser Console Test

**Steps:**
1. Open `http://localhost:8000` in browser
2. Open DevTools (F12)
3. Go to Console tab
4. Check for errors

**Expected:**
- No critical errors
- Console shows: "SliTaz VM ready to start"
- v86 library loads from CDN

### 3. IndexedDB Test

**Steps:**
1. Open DevTools → Application tab
2. Navigate to IndexedDB
3. Click "Start VM" button (may fail due to network restrictions)
4. Check if `SliTazVM` database is created

**Expected:**
- Database "SliTazVM" exists
- Object stores "osFiles" and "diskState" are present

### 4. UI Element Test

**Checklist:**
- [ ] Page loads with gradient background
- [ ] Header shows "SliTaz Browser VM"
- [ ] All 6 control buttons visible
- [ ] VM display area shows black screen
- [ ] File operations section visible
- [ ] Settings panel with 4 options
- [ ] Console terminal at bottom
- [ ] Status indicator shows "Stopped" (red)

### 5. Settings Interaction Test

**Test each setting:**

**Memory Slider:**
- Move slider: 128MB, 256MB, 384MB, 512MB
- Display should update in real-time

**Network Toggle:**
- Click checkbox on/off
- Should toggle smoothly

**Cache Buttons:**
- Click "Cache Info" → Should show alert (may be empty initially)
- Click "Clear Cache" → Should ask for confirmation

**Auto-Save Toggle:**
- Click to enable/disable
- State should persist

### 6. Button State Test

**Initial state (VM stopped):**
- [x] Start VM: Enabled
- [x] Stop VM: Disabled
- [x] Restart VM: Disabled
- [x] Save State: Disabled
- [x] Fullscreen: Enabled
- [x] Grab Keyboard: Enabled

**After clicking Start (even if VM fails to boot):**
- Status changes to "Starting" or "Error"
- Console shows initialization messages

---

## Deployment Environment Tests

### Test 1: GitHub Pages

**Prerequisites:**
- Repository deployed to GitHub Pages
- HTTPS enabled

**Tests:**

1. **Page Load Test:**
   ```
   Visit: https://username.github.io/SilTaz-Emulator/
   ```
   - [ ] Page loads without SSL errors
   - [ ] All CSS styles applied
   - [ ] JavaScript loads from CDN

2. **Cross-Origin Test:**
   - [ ] v86 library loads from jsdelivr CDN
   - [ ] Check browser console for CORS errors
   - [ ] Check Network tab for blocked requests

3. **ISO Download Test:**
   - [ ] Click "Start VM"
   - [ ] Progress bar appears
   - [ ] ISO download begins (or loads from cache)
   - [ ] Check Network tab for download progress

### Test 2: Netlify

**Deploy test:**
```bash
# Install Netlify CLI
npm install -g netlify-cli

# Deploy
cd SilTaz-Emulator
netlify deploy --prod
```

**Verification:**
- [ ] Deployment succeeds
- [ ] Given a .netlify.app URL
- [ ] Page loads on generated URL
- [ ] All functionality works

### Test 3: Vercel

**Deploy test:**
```bash
# Install Vercel CLI
npm i -g vercel

# Deploy
cd SilTaz-Emulator
vercel --prod
```

**Verification:**
- [ ] Deployment succeeds
- [ ] Given a .vercel.app URL
- [ ] Page accessible
- [ ] Headers properly set

---

## Functional Tests

### Test 4: First Boot (Full Test)

**This test requires actual ISO download to work:**

**Prerequisites:**
- Deployed on platform with proper CORS headers
- Network access to mirror.slitaz.org
- Modern browser (Chrome 90+, Firefox 88+)

**Steps:**
1. Clear browser cache and IndexedDB
2. Open application
3. Open DevTools → Network tab
4. Click "Start VM"

**Expected behavior:**
- [ ] Console logs: "Checking for cached ISO..."
- [ ] Console logs: "Downloading SliTaz ISO..."
- [ ] Progress bar appears and shows 0-100%
- [ ] Network tab shows ~40MB download from mirror.slitaz.org
- [ ] After download: "ISO cached for future use"
- [ ] "Loading ISO, starting emulator..."
- [ ] Status changes to "Running" (green indicator)
- [ ] Black screen shows BIOS messages
- [ ] SliTaz boot sequence begins
- [ ] Login prompt appears (after ~30-60 seconds)

**Troubleshooting if download fails:**
- Check browser console for specific error
- Verify mirror.slitaz.org is accessible
- Try different browser
- Check if hosting platform blocks outbound requests

### Test 5: Cached Boot

**Prerequisites:**
- Completed Test 4 successfully
- ISO cached in IndexedDB

**Steps:**
1. Close browser tab
2. Reopen application
3. Click "Start VM"

**Expected behavior:**
- [ ] Console logs: "Loading ISO from cache (fast boot!)"
- [ ] No download progress bar
- [ ] Boot starts almost immediately
- [ ] Boots in <30 seconds
- [ ] Login prompt appears

**Verify cache:**
- DevTools → Application → IndexedDB → SliTazVM → osFiles
- Should see entry for "slitaz.iso" (~40MB)

### Test 6: VM Functionality

**After successfully booting to login:**

**Login:**
```
Username: tux
Password: (press Enter - no password)
```

**Test terminal:**
- [ ] Type commands: `ls`, `pwd`, `whoami`
- [ ] Commands execute properly
- [ ] Output displays correctly

**Test file creation:**
```bash
echo "Hello from SliTaz" > test.txt
cat test.txt
```
- [ ] File created successfully
- [ ] Content readable

**Test graphical mode:**
```bash
startx
```
- [ ] X11 starts
- [ ] Desktop appears
- [ ] Mouse works
- [ ] Can click menu

**Test Links2 browser (text mode):**
```bash
links2 http://example.com
```
- [ ] Browser starts
- [ ] Can navigate with arrow keys
- [ ] Press 'q' to quit

**Test Links2 browser (graphical mode):**
```bash
links2 -g http://example.com
```
- [ ] GUI browser window opens
- [ ] Page renders
- [ ] Can interact with mouse

### Test 7: State Persistence

**Steps:**
1. Boot VM and login
2. Create file: `echo "persistence test" > /home/tux/persist.txt`
3. Click "Save State" button
4. Wait for console: "State saved successfully"
5. Click "Stop VM"
6. Click "Start VM" again
7. Login
8. Check file: `cat /home/tux/persist.txt`

**Expected:**
- [ ] File still exists after restart
- [ ] Content is preserved

### Test 8: Auto-Save

**Steps:**
1. Boot VM and login
2. Create file: `echo "autosave test" > /home/tux/auto.txt`
3. Wait 30+ seconds (don't click Save State)
4. Console should show: "Disk state saved to IndexedDB"
5. Close browser tab completely
6. Reopen and start VM
7. Login and check: `cat /home/tux/auto.txt`

**Expected:**
- [ ] Auto-save triggers automatically
- [ ] File persists without manual save

---

## Performance Tests

### Test 9: Load Time Measurement

**First load (uncached):**
```javascript
// Run in browser console
performance.mark('start-download');
// Click Start VM
// After ISO downloads, run:
performance.mark('end-download');
performance.measure('download-time', 'start-download', 'end-download');
console.log(performance.getEntriesByName('download-time'));
```

**Target times:**
- ISO download: <60s on decent connection
- Total first boot: <120s

**Cached load:**
- ISO load: <1s
- Total boot: <30s

### Test 10: Memory Usage

**Monitor browser task manager:**
1. Open browser task manager (Shift+Esc in Chrome)
2. Boot VM
3. Note memory usage

**Expected:**
- Before boot: ~50-100MB
- After boot (256MB VM): ~350-450MB total
- After boot (512MB VM): ~600-700MB total

### Test 11: Storage Usage

**Check IndexedDB size:**
1. DevTools → Application → Storage
2. View quota usage

**Expected storage:**
- ISO: ~40MB
- Disk state: ~50-100MB (varies with usage)
- Total: ~100-150MB

---

## Browser Compatibility Tests

### Test 12: Cross-Browser Testing

**Test on each browser:**

**Chrome/Chromium:**
- [ ] Page loads
- [ ] VM boots
- [ ] Graphics work
- [ ] Keyboard input works

**Firefox:**
- [ ] Page loads
- [ ] VM boots
- [ ] Graphics work
- [ ] Keyboard input works

**Safari:**
- [ ] Page loads
- [ ] VM boots
- [ ] Graphics work
- [ ] Keyboard input works
- [ ] Note: May have IndexedDB quota differences

**Edge:**
- [ ] Page loads
- [ ] VM boots
- [ ] Graphics work
- [ ] Keyboard input works

### Test 13: Mobile Testing

**Test on mobile devices:**

**Android Chrome:**
- [ ] Page loads and is responsive
- [ ] Can click Start VM
- [ ] Touch controls work
- [ ] Screen scales properly

**iOS Safari:**
- [ ] Page loads and is responsive
- [ ] Can click Start VM
- [ ] Touch controls work
- [ ] Screen scales properly
- [ ] Note: Performance may be limited

---

## UI/UX Tests

### Test 14: Responsive Design

**Test at different viewport sizes:**

**Desktop (1920x1080):**
- [ ] Layout looks good
- [ ] All elements visible
- [ ] Buttons properly sized

**Tablet (768x1024):**
- [ ] Layout adapts
- [ ] Controls wrap properly
- [ ] Console readable

**Mobile (375x667):**
- [ ] Stacked layout
- [ ] Touch-friendly buttons
- [ ] Scrollable content

### Test 15: Fullscreen Mode

**Steps:**
1. Boot VM
2. Click "Fullscreen" button

**Expected:**
- [ ] Fullscreen overlay appears
- [ ] Controls visible at top
- [ ] VM display centered
- [ ] Exit button works
- [ ] ESC key exits fullscreen

### Test 16: Keyboard Grab

**Steps:**
1. Boot VM to login
2. Click "Grab Keyboard"
3. Try typing

**Expected:**
- [ ] Button text changes to "Release Keyboard"
- [ ] All keyboard input goes to VM
- [ ] Shortcuts like Ctrl+W don't close tab
- [ ] ESC releases keyboard

---

## Error Handling Tests

### Test 17: Network Failure

**Simulate:**
1. Disconnect internet
2. Clear IndexedDB
3. Click "Start VM"

**Expected:**
- [ ] Error message in console
- [ ] Status shows "Error"
- [ ] User-friendly error displayed
- [ ] App doesn't crash

### Test 18: Quota Exceeded

**Simulate:**
1. Artificially fill IndexedDB (developer tools)
2. Try to start VM

**Expected:**
- [ ] Graceful error handling
- [ ] Clear error message
- [ ] Suggestion to clear cache

### Test 19: WebAssembly Unavailable

**Test in older browser (or disable WASM):**

**Expected:**
- [ ] Clear error message
- [ ] Suggestion to update browser
- [ ] No JavaScript errors

---

## Security Tests

### Test 20: XSS Prevention

**Check console input sanitization:**
- No user input is directly rendered as HTML
- Console logs are escaped

### Test 21: CSP Headers

**Check in DevTools → Network → Response Headers:**
```
Content-Security-Policy: (if set)
Cross-Origin-Embedder-Policy: require-corp
Cross-Origin-Opener-Policy: same-origin
```

---

## Automated Testing (Optional)

### Playwright Test Script

```javascript
// test.spec.js
const { test, expect } = require('@playwright/test');

test('SliTaz VM loads', async ({ page }) => {
  await page.goto('http://localhost:8000');
  
  // Check title
  await expect(page).toHaveTitle(/SliTaz Browser VM/);
  
  // Check elements exist
  await expect(page.locator('text=Start VM')).toBeVisible();
  await expect(page.locator('text=Settings')).toBeVisible();
  
  // Check status
  await expect(page.locator('text=Stopped')).toBeVisible();
});
```

Run:
```bash
npm install @playwright/test
npx playwright test
```

---

## Test Results Template

```markdown
## Test Results - [Date]

**Environment:**
- Browser: Chrome 120
- OS: Windows 11
- Hosting: GitHub Pages
- URL: https://username.github.io/SilTaz-Emulator/

**Tests Passed:** 18/21

### Passed Tests
- ✅ Page load
- ✅ UI elements
- ✅ First boot
- ✅ Cached boot
- ✅ State persistence
- ✅ Auto-save
- ✅ Fullscreen mode
- ✅ Keyboard grab
- ✅ Responsive design
...

### Failed Tests
- ❌ Mobile Safari - Performance issues
- ❌ Network test - CORS error on specific mirror
- ❌ Older Firefox - WASM compatibility

### Notes
- ISO download took 45 seconds
- Cached boot in 28 seconds
- Memory usage: 380MB with 256MB VM
- No critical errors in console
```

---

## Continuous Testing

**Recommended schedule:**
- Before each deployment: Run functional tests
- Weekly: Full browser compatibility check
- Monthly: Performance benchmarks
- After v86 updates: Full regression test

---

**Happy Testing! 🧪**
