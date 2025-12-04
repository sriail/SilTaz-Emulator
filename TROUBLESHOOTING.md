# Troubleshooting Guide for SliTaz Browser VM

Common issues and their solutions when running the SliTaz Browser VM emulator.

## 🔴 Critical Issues

### VM Won't Start - "Network Error While Downloading ISO"

**Symptoms:**
- Click "Start VM" button
- Console shows: "Downloading SliTaz ISO..."
- Error: "Network error while downloading ISO"

**Causes:**
1. **CORS restrictions** - Browser blocking cross-origin request
2. **Network connectivity** - Can't reach mirror.slitaz.org
3. **Ad blocker** - Extension blocking download
4. **Firewall** - Corporate/school firewall blocking

**Solutions:**

**Solution 1: Use a proper hosting platform**
```
GitHub Pages, Netlify, or Vercel handle CORS automatically.
Don't use file:// protocol - must use HTTP/HTTPS server.
```

**Solution 2: Disable browser extensions**
```
Temporarily disable ad blockers and privacy extensions
Test in incognito/private mode
```

**Solution 3: Host ISO locally**
1. Download ISO manually:
   ```bash
   wget http://mirror.slitaz.org/iso/rolling/slitaz-rolling.iso
   ```
2. Place in same directory as index.html
3. Edit index.html line 453:
   ```javascript
   const ISO_URL = './slitaz-rolling.iso';
   ```

**Solution 4: Try alternative mirror**
Edit index.html:
```javascript
const ISO_URL = 'http://mirror.switch.ch/ftp/mirror/slitaz/iso/rolling/slitaz-rolling.iso';
```

---

### WebAssembly Fails to Load

**Symptoms:**
- Console error: "WebAssembly is not defined"
- Or: "Failed to load WASM module"

**Causes:**
- Outdated browser
- Browser doesn't support WASM
- SharedArrayBuffer disabled

**Solutions:**

**Solution 1: Update browser**
```
Chrome: Version 90+
Firefox: Version 88+
Safari: Version 14+
Edge: Version 90+
```

**Solution 2: Check browser support**
Open console and run:
```javascript
typeof WebAssembly !== 'undefined'
// Should return: true
```

**Solution 3: Enable required features**
Your server needs these headers:
```http
Cross-Origin-Embedder-Policy: require-corp
Cross-Origin-Opener-Policy: same-origin
```

---

### IndexedDB Quota Exceeded

**Symptoms:**
- Error: "QuotaExceededError"
- Can't save state
- ISO won't cache

**Solutions:**

**Solution 1: Clear existing data**
1. Click "Cache Info" button to see usage
2. Click "Clear Cache" button
3. Restart browser

**Solution 2: Increase browser storage**
- Chrome: Settings → Privacy → Site Settings → Storage
- Firefox: about:preferences → Privacy → Manage Data
- Clear data for the site and try again

**Solution 3: Use smaller disk**
Edit index.html (line ~450):
```javascript
hda: {
    size: 256 * 1024 * 1024,  // Changed from 512MB to 256MB
    async: true
}
```

---

## ⚠️ Common Issues

### ISO Downloads Every Time (Not Caching)

**Symptoms:**
- Every restart downloads ISO again
- "Loading from cache" never appears

**Solutions:**

**Solution 1: Check IndexedDB**
1. Open DevTools (F12)
2. Application tab → IndexedDB → SliTazVM
3. Check if "osFiles" contains "slitaz.iso"

**Solution 2: Verify completion**
- Let full download complete before closing
- Wait for "ISO cached for future use" message

**Solution 3: Browser storage settings**
- Ensure browser isn't clearing cache on exit
- Chrome: Settings → Privacy → Cookies → "Keep local data"
- Firefox: Preferences → Privacy → History → "Remember history"

---

### VM Boots but Keyboard Doesn't Work

**Symptoms:**
- Can see boot messages
- Can't type at login prompt
- Keyboard unresponsive

**Solutions:**

**Solution 1: Click "Grab Keyboard"**
1. Click the "⌨️ Grab Keyboard" button
2. Click inside the VM screen area
3. Try typing again

**Solution 2: Focus the canvas**
- Click directly on the black screen area
- Ensure browser tab has focus

**Solution 3: Release and re-grab**
- Press ESC to release keyboard
- Click "Grab Keyboard" again

---

### Display Issues - Black Screen or Garbled Graphics

**Symptoms:**
- Screen stays black after boot
- Corrupted/garbled graphics
- No BIOS messages

**Solutions:**

**Solution 1: Wait longer**
- First boot can take 60-90 seconds
- Watch console for progress messages

**Solution 2: Restart VM**
1. Click "Stop VM"
2. Wait 5 seconds
3. Click "Start VM" again

**Solution 3: Reduce memory**
1. Set memory slider to 128MB or 256MB
2. Restart VM
3. Lower settings can improve compatibility

**Solution 4: Try different browser**
- Test in Chrome first (best compatibility)
- Try Firefox as alternative
- Safari may have issues

---

### Mouse Not Working in Graphical Mode

**Symptoms:**
- After running `startx`, mouse doesn't move
- Can't click on desktop

**Solutions:**

**Solution 1: Click screen to capture**
- Click inside the VM screen area
- Some browsers require explicit click to capture mouse

**Solution 2: Use keyboard navigation**
- Press Alt+F1 for menu
- Use arrow keys and Enter
- Tab to move between elements

**Solution 3: Grab keyboard first**
- Click "Grab Keyboard" button
- Then click in screen
- Then try mouse

---

### Slow Performance

**Symptoms:**
- VM runs very slowly
- Laggy graphics
- Delayed keyboard input

**Solutions:**

**Solution 1: Reduce memory allocation**
- Settings → Memory Allocation → 128MB or 256MB
- Higher memory = more CPU needed

**Solution 2: Close other tabs**
- Browser tabs compete for resources
- Close unnecessary tabs and windows

**Solution 3: Disable auto-save temporarily**
- Uncheck "Auto-Save (30s)"
- Reduces periodic disk operations

**Solution 4: Use lighter apps**
- Avoid graphical mode if slow
- Use text-mode applications
- Links2 text mode instead of GUI

**Solution 5: Hardware acceleration**
- Ensure browser hardware acceleration enabled
- Chrome: Settings → System → Hardware acceleration
- Firefox: Preferences → Performance

---

### Network Not Working in VM

**Symptoms:**
- Can't ping from within VM
- Links2 can't connect to sites
- Network appears down

**Solutions:**

**Solution 1: Enable network toggle**
- Settings → "Enable Network" checkbox
- Must be enabled BEFORE starting VM
- Restart VM after enabling

**Solution 2: Wait for DHCP**
```bash
# Inside VM, run:
udhcpc -i eth0
```

**Solution 3: Check relay**
- Default relay: wss://relay.widgetry.org/
- Relay may be temporarily down
- Network is optional for most features

---

### Auto-Save Not Working

**Symptoms:**
- Files don't persist between sessions
- No "Disk state saved" messages
- Changes lost on restart

**Solutions:**

**Solution 1: Enable auto-save**
- Settings → Check "Auto-Save (30s)"
- Look for console messages every 30 seconds

**Solution 2: Manual save before closing**
- Click "💾 Save State" button before stopping
- Wait for confirmation message

**Solution 3: Don't force-close browser**
- Use "Stop VM" button properly
- Let save complete before closing tab

**Solution 4: Check storage quota**
- May be hitting storage limits
- Clear cache and try again

---

### File Import Doesn't Work

**Symptoms:**
- Click "Import File to VM"
- Select file
- Nothing happens in VM

**Current Limitation:**
File import is partially implemented and requires additional setup within SliTaz to receive files.

**Workarounds:**

**Option 1: Use network transfer**
1. Enable network
2. Use `wget` inside VM to fetch files
3. Or set up simple HTTP server on host

**Option 2: Paste text content**
1. For text files, copy content
2. Use nano/vi in VM
3. Paste content manually

**Option 3: Future enhancement**
- Full 9P filesystem support coming soon
- Will enable drag-and-drop file transfer

---

### Fullscreen Mode Issues

**Symptoms:**
- Fullscreen mode doesn't work
- Exit button doesn't respond
- Stuck in fullscreen

**Solutions:**

**Solution 1: Use ESC key**
- Press ESC to exit fullscreen
- Works in all browsers

**Solution 2: Browser fullscreen**
- Press F11 to exit browser fullscreen
- Different from app fullscreen

**Solution 3: Refresh page**
- If stuck, refresh page (F5)
- Will reset to normal view

---

## 🔧 Browser-Specific Issues

### Chrome/Chromium Issues

**Issue: "Aw, Snap!" Crash**
- Reduce memory allocation
- Disable browser extensions
- Update Chrome to latest version

**Issue: High CPU usage**
- Normal for emulation
- Close other tabs
- Reduce VM memory

---

### Firefox Issues

**Issue: Slower than Chrome**
- Firefox WASM performance varies
- Try Chrome for comparison
- Update Firefox to latest

**Issue: Canvas rendering issues**
- Clear browser cache
- Disable hardware acceleration
- Reset Firefox (about:support)

---

### Safari Issues

**Issue: SharedArrayBuffer not available**
- Safari has stricter security
- Requires proper headers
- May need macOS Big Sur or later

**Issue: Storage quota differences**
- Safari has lower default quota
- May need to allow more storage
- Preferences → Websites → Storage

---

### Edge Issues

**Issue: Similar to Chrome**
- Edge is Chromium-based
- Same solutions as Chrome apply

---

## 📱 Mobile Issues

### iOS Safari

**Performance:**
- Limited by iOS constraints
- Reduce memory to 128MB
- May be very slow

**Touch controls:**
- Tap screen to grab focus
- Use keyboard grab carefully
- Some features may not work

---

### Android Chrome

**Generally works better than iOS:**
- Reduce memory still recommended
- Touch keyboard may be awkward
- External keyboard recommended

---

## 🔍 Debugging Tools

### Browser Console

**Open console (F12) and check for:**
```javascript
// Any red errors
// Network tab for failed requests
// Application tab for IndexedDB contents
// Performance tab for slowness
```

### Useful Console Commands

**Check v86 version:**
```javascript
console.log(typeof V86Starter);
// Should output: "function"
```

**Check IndexedDB:**
```javascript
indexedDB.databases().then(console.log);
// Should show SliTazVM database
```

**Check storage usage:**
```javascript
navigator.storage.estimate().then(console.log);
// Shows quota and usage
```

---

## 💡 Tips for Best Experience

### Recommended Configuration

```
Browser: Chrome 120+ or Firefox 120+
Memory: 256MB for general use, 512MB for graphical mode
Network: Disabled unless needed
Auto-save: Enabled
Connection: Broadband for first download
```

### First-Time Setup

1. Open in Chrome/Firefox on desktop
2. Good internet connection
3. Let ISO download fully (watch progress bar)
4. Wait for "ISO cached" message
5. Boot to login
6. Login as 'tux'
7. Test with simple commands
8. Once working, try graphical mode

### For Development/Testing

1. Always test in incognito first (fresh state)
2. Check browser console regularly
3. Monitor IndexedDB size
4. Test on localhost before deploying
5. Clear cache between major changes

---

## 📞 Getting Help

### Before Asking for Help

**Gather this information:**

1. **Browser & Version:**
   ```
   Help → About Google Chrome
   ```

2. **Console Errors:**
   - F12 → Console tab
   - Copy any red error messages

3. **Network Tab:**
   - F12 → Network tab
   - Look for failed requests (red)
   - Note status codes

4. **What you tried:**
   - Steps to reproduce
   - Expected vs actual behavior
   - Any error messages

5. **Environment:**
   - Where hosted (GitHub Pages, local, etc.)
   - Operating system
   - Any special network setup

### Where to Get Help

- **GitHub Issues**: Report bugs and ask questions
- **Browser DevTools**: Self-diagnose issues
- **v86 Documentation**: For emulator-specific issues
- **SliTaz Forums**: For Linux-specific help

---

## 🆘 Emergency Fixes

### Nuclear Option: Complete Reset

If nothing works:

1. **Clear everything:**
   ```javascript
   // In browser console
   indexedDB.deleteDatabase('SliTazVM');
   ```

2. **Clear browser cache:**
   - Chrome: Ctrl+Shift+Del → Clear browsing data
   - Select "All time"
   - Check "Cached images and files" and "Cookies"

3. **Restart browser completely**

4. **Reload page and start fresh**

5. **Let ISO download again**

---

**Still having issues? Open a GitHub issue with your console logs and we'll help! 🚀**
