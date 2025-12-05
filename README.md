# SliTaz Browser VM Emulator 🖥️

A full-featured **SliTaz Linux** virtual machine running directly in your web browser, powered by the [v86 emulator](https://github.com/copy/v86). No installation required - just open the page and boot Linux!

## ✨ Features

### Core Functionality
- **Full Linux Environment**: Run complete SliTaz Linux distribution in your browser
- **Persistent Storage**: Your files and changes are saved between sessions using IndexedDB
- **Smart Caching**: Downloads the ~40MB ISO once, then loads instantly from cache
- **Local ISO Upload**: Upload your own ISO file if automatic download fails (recommended for reliability)
- **Network Support**: Optional network connectivity via WebSocket relay
- **File Operations**: Import and export files between your computer and the VM

### Built-in Linux Applications
SliTaz includes many useful applications out of the box:
- **Links2 Browser**: Text-mode and graphical web browser
- **Terminal**: Full bash terminal with standard Unix utilities
- **File Manager**: PCManFM for managing files and folders
- **Text Editors**: Nano, Vi, and Leafpad
- **System Tools**: Complete Linux toolchain

### UI Features
- **Modern Interface**: Clean, unified color scheme with consistent design
- **SVG Icons**: Professional icons throughout the interface
- **Live Console**: Real-time status updates and system logs
- **Fullscreen Mode**: Maximize your workspace
- **Keyboard Grab**: Full keyboard control for the VM
- **Progress Indicators**: Visual feedback for all operations
- **Auto-Save**: Automatically saves VM state every 30 seconds

### Performance Features
- **Hybrid Caching System**: Uses IndexedDB for optimal performance
- **Configurable Memory**: Allocate 128MB to 512MB RAM
- **Fast Boot**: Cached boots typically complete in seconds
- **State Persistence**: Virtual hard disk state survives browser restarts

## 🚀 Quick Start

> **New to this?** Check out [QUICKSTART.md](QUICKSTART.md) for a beginner-friendly guide!

### Option 1: Local Testing (Recommended for Development)

1. **Clone the repository**:
   ```bash
   git clone https://github.com/sriail/SilTaz-Emulator.git
   cd SilTaz-Emulator
   ```

2. **Start a local web server**:
   
   Using Python 3:
   ```bash
   python3 -m http.server 8000
   ```
   
   Or using Node.js:
   ```bash
   npx serve
   ```

3. **Open in your browser**:
   ```
   http://localhost:8000
   ```

4. **Click "Start VM"** and wait for SliTaz to boot!

### Option 2: GitHub Pages Deployment

1. Fork this repository
2. Go to repository Settings → Pages
3. Set source to "main" branch
4. Access your VM at: `https://yourusername.github.io/SilTaz-Emulator/`

### Option 3: Other Hosting Platforms

**Netlify**:
- Drag and drop the repository folder to [Netlify](https://app.netlify.com/)
- Or connect your GitHub repository for auto-deployment

**Vercel**:
```bash
npx vercel
```

## 📖 Usage Guide

### First Boot

**Option 1: Automatic Download (Recommended for deployed sites)**
1. Click the **"Start VM"** button
2. Wait for the ~40MB SliTaz ISO to download (one-time only)
3. The ISO is cached automatically for future use
4. Watch the boot process in the screen area
5. SliTaz will boot to a login prompt

**Option 2: Local ISO File (Recommended if download fails)**
1. Download SliTaz ISO manually from [slitaz.org](http://mirror.slitaz.org/iso/rolling/)
2. Click **"Load Local ISO"** button in Settings
3. Select the downloaded ISO file
4. Click **"Start VM"** button
5. SliTaz will boot from your local file

### Login Credentials
- **Username**: `tux` (or `root` for admin)
- **Password**: (typically blank - just press Enter)

### Using the VM

**Starting a Graphical Session**:
```bash
startx
```

**Launching Applications**:
- From desktop: Click the menu icon
- From terminal: Type application name (e.g., `links2`, `leafpad`, `pcmanfm`)

**Text-mode Web Browser**:
```bash
links2 https://www.example.com
```

**Graphical Web Browser**:
```bash
links2 -g https://www.example.com
```

### Controls

- **Start VM**: Boot the virtual machine
- **Stop VM**: Safely shutdown and save state
- **Restart VM**: Reboot the virtual machine
- **Save State**: Manually save current VM state to IndexedDB
- **Fullscreen**: Toggle fullscreen mode for the display
- **Grab Keyboard**: Capture all keyboard input for the VM (press ESC to release)

### Settings

**Memory Allocation**:
- Adjust RAM from 128MB to 512MB
- Changes take effect on next boot
- More memory = better performance for graphical applications

**Network**:
- Toggle network connectivity on/off
- Uses WebSocket relay for networking
- Required for web browsing from within the VM

**Auto-Save**:
- Enabled by default (saves every 30 seconds)
- Ensures your work is not lost
- Can be disabled if needed

**Cache Management**:
- **Cache Info**: View size of cached ISO and disk state
- **Clear Cache**: Remove all cached data (requires re-download)

### File Operations

**Import Files** (Experimental):
1. Click "Import File to VM"
2. Select file(s) from your computer
3. Files are read but require additional VM setup to transfer

**Export Files** (Placeholder):
- Requires 9P filesystem configuration
- Coming in future updates

## 🏗️ Technical Architecture

### Components

1. **Frontend (index.html)**:
   - Single-page application
   - No build process required
   - Pure HTML, CSS, and JavaScript

2. **v86 Emulator**:
   - Loaded from CDN (jsdelivr)
   - WebAssembly-based x86 emulation
   - Supports full PC hardware emulation

3. **IndexedDB Storage**:
   - `osFiles` store: Cached ISO image
   - `diskState` store: Virtual hard disk state
   - Automatic management and persistence

4. **Network Relay**:
   - Uses `wss://relay.widgetry.org/`
   - WebSocket-based network proxy
   - Optional but recommended for full functionality

### Storage Structure

```
IndexedDB: SliTazVM
├── osFiles
│   └── slitaz.iso (40MB, cached)
└── diskState
    └── hda.img (virtual HDD state)
```

### Boot Process

1. Check IndexedDB for cached ISO
2. If not cached: Download from mirror.slitaz.org
3. Cache ISO in IndexedDB for future use
4. Load any saved disk state
5. Initialize v86 emulator with configuration
6. Boot SliTaz from ISO
7. Auto-save state periodically

## 🔧 Configuration

### ISO Source
Default: `http://mirror.slitaz.org/iso/rolling/slitaz-rolling.iso`

To use a different ISO, edit the `ISO_URL` constant in index.html:
```javascript
const ISO_URL = 'your-iso-url-here';
```

### Memory Settings
Default: 256MB RAM, 8MB Video RAM

Modify in the v86 configuration:
```javascript
memory_size: memory * 1024 * 1024,
vga_memory_size: 8 * 1024 * 1024,
```

### Virtual Disk Size
Default: 512MB

Change the HDD size:
```javascript
hda: {
    size: 512 * 1024 * 1024, // Change this value
    async: true
}
```

## 🧪 Testing Checklist

### First Boot Test
- [ ] Page loads without errors
- [ ] Click "Start VM" button
- [ ] ISO download begins with progress indicator
- [ ] Download completes and caching message appears
- [ ] VM boots and displays SliTaz boot screen
- [ ] Login prompt appears
- [ ] Can log in with credentials

### Cached Boot Test
- [ ] Close browser tab
- [ ] Reopen page
- [ ] Click "Start VM"
- [ ] "Loading from cache" message appears
- [ ] Boot is significantly faster (no download)
- [ ] VM starts successfully

### Persistence Test
- [ ] Boot VM and log in
- [ ] Create a test file: `echo "test" > /home/tux/testfile.txt`
- [ ] Verify file exists: `cat /home/tux/testfile.txt`
- [ ] Click "Save State" button
- [ ] Click "Stop VM"
- [ ] Click "Start VM" again
- [ ] Log in and verify file still exists

### Built-in Features Test
- [ ] **Terminal**: Open bash and run commands
- [ ] **Text Browser**: Run `links2 https://example.com`
- [ ] **Graphical Mode**: Run `startx` to start X11
- [ ] **File Manager**: Launch PCManFM from desktop
- [ ] **Text Editor**: Open Leafpad or nano
- [ ] **System Tools**: Try various utilities

### UI/UX Test
- [ ] All buttons work correctly
- [ ] Status indicator updates properly
- [ ] Console logs show activity
- [ ] Memory slider adjusts settings
- [ ] Fullscreen mode works
- [ ] Keyboard grab functions
- [ ] Settings save correctly
- [ ] Auto-save runs periodically

### Network Test (if enabled)
- [ ] Network toggle is enabled
- [ ] Boot VM
- [ ] Try network commands: `ping example.com`
- [ ] Use Links2 to browse a website

## 🐛 Troubleshooting

### VM Won't Start
- Check browser console for errors (F12)
- Ensure browser supports WebAssembly
- Try clearing cache and reloading
- Disable browser extensions that might interfere

### ISO Download Fails
- **NEW: Use Local ISO Feature** - Click "Load Local ISO" in Settings to upload your own ISO file
- Check internet connection
- Verify ISO URL is accessible
- Try a different SliTaz mirror
- Check browser's download restrictions
- Disable ad blockers or content blockers
- Try deploying to GitHub Pages, Netlify, or Vercel for better CORS handling

### Performance Issues
- Reduce memory allocation in settings
- Close other browser tabs
- Disable auto-save temporarily
- Use a modern browser (Chrome, Firefox, Edge)

### Display Issues
- Try toggling fullscreen mode
- Restart the VM
- Check screen container CSS
- Ensure canvas element is rendering

### Keyboard Not Working
- Click "Grab Keyboard" button
- Ensure VM window has focus
- Press ESC to release if stuck
- Check browser keyboard permissions

## 🌐 Browser Compatibility

**Fully Supported**:
- ✅ Chrome 90+
- ✅ Firefox 88+
- ✅ Edge 90+
- ✅ Safari 14+

**Required Features**:
- WebAssembly support
- IndexedDB support
- ES6+ JavaScript
- Canvas API
- XMLHttpRequest / Fetch API

## 📊 Performance Notes

### First Boot
- ISO Download: ~40MB (varies by connection)
- Download Time: 10-60 seconds (depends on speed)
- Boot Time: 30-60 seconds
- Total First Load: 1-2 minutes

### Cached Boots
- ISO Load: Instant (from IndexedDB)
- Boot Time: 20-40 seconds
- Total Load: <1 minute

### Resource Usage
- RAM: 256MB default (configurable)
- Disk: ~40MB ISO + ~50-100MB state
- CPU: Medium (depends on VM activity)

## 🔐 Security Considerations

- VM is sandboxed in browser
- No access to host filesystem (by design)
- Network goes through relay proxy
- IndexedDB data is local to browser
- Clear cache to remove all data
- No data transmitted to third parties (except relay for networking)

## 🛠️ Development

### Project Structure
```
SilTaz-Emulator/
├── index.html          # Main application (all-in-one)
└── README.md          # Documentation
```

### Making Changes
1. Edit `index.html` directly
2. Test locally with a web server
3. Deploy updated version

### Adding Features
- All code is in a single HTML file
- CSS is in `<style>` tag
- JavaScript is in `<script>` tag
- Modify and test incrementally

## 📝 License

This project uses:
- **v86**: BSD-2-Clause License
- **SliTaz**: GNU General Public License
- This wrapper code: Use freely

## 🤝 Contributing

Contributions welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## 🙏 Credits

- **v86**: [copy/v86](https://github.com/copy/v86) - Amazing x86 emulator
- **SliTaz**: [slitaz.org](http://www.slitaz.org/) - Lightweight Linux distro
- **WebSocket Relay**: widgetry.org - Network proxy service

## 📞 Support

Issues? Suggestions? 
- Open a GitHub issue
- Check existing issues for solutions
- Review troubleshooting section above

## 🎯 Roadmap

Future improvements:
- [ ] Enhanced file import/export with 9P filesystem
- [ ] Service Worker for offline functionality
- [ ] Multiple disk snapshots
- [ ] VM templates for different configurations
- [ ] Better serial console integration
- [ ] Drag-and-drop file upload
- [ ] Direct filesystem browsing
- [ ] CD-ROM disk swapping
- [ ] Save/load state to files

---

**Enjoy Linux in your browser! 🐧**
