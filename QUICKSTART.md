# Quick Start Guide - SliTaz Browser VM

Get up and running with SliTaz in your browser in 5 minutes!

## 🚀 Fastest Way to Try It

### Option 1: GitHub Pages (Recommended)

1. **Fork this repository** on GitHub
2. Go to **Settings** → **Pages**
3. Select **main** branch as source
4. Wait 2-3 minutes for deployment
5. Visit: `https://yourusername.github.io/SilTaz-Emulator/`
6. Click **"Start VM"** and wait for boot!

**First boot:** ~1-2 minutes (downloads ISO)  
**Subsequent boots:** ~30 seconds (cached)

---

### Option 2: Local Testing (2 Minutes)

```bash
# Clone the repository
git clone https://github.com/sriail/SilTaz-Emulator.git
cd SilTaz-Emulator

# Start a web server (choose one):

# Python 3
python3 -m http.server 8000

# OR Node.js
npx serve

# OR PHP
php -S localhost:8000
```

Open browser: `http://localhost:8000`

---

## 📋 First Steps After Boot

### 1. Login

When you see the login prompt:

```
SliTaz login: tux
Password: [just press Enter - no password]
```

### 2. Basic Commands

Try these commands in the terminal:

```bash
# Check current directory
pwd

# List files
ls

# See system info
uname -a

# Check available programs
ls /usr/bin

# Create a test file
echo "Hello from SliTaz!" > test.txt
cat test.txt
```

### 3. Start Graphical Mode

For the desktop environment:

```bash
startx
```

**In X11 Desktop:**
- Click the menu icon (bottom left)
- Launch applications
- Use mouse and keyboard normally

### 4. Try the Text Browser

```bash
# Text-mode browser
links2 http://example.com

# Navigation:
# Arrow keys: move
# Enter: follow link
# Q: quit
```

### 5. Try the Graphical Browser

```bash
# GUI browser (after startx)
links2 -g http://example.com
```

---

## 💾 Saving Your Work

### Automatic Saving

By default, the VM **auto-saves every 30 seconds**. Your files persist automatically!

### Manual Saving

Click the **"💾 Save State"** button before closing the browser.

### Verify Persistence

1. Create a file: `echo "test" > myfile.txt`
2. Click "Save State"
3. Click "Stop VM"
4. Click "Start VM" again
5. Login and check: `cat myfile.txt`

The file should still be there! 🎉

---

## ⚙️ Adjusting Settings

### Before Starting VM

**Memory Allocation:**
- Drag slider: 128MB - 512MB
- More memory = better performance for graphical apps
- Recommended: 256MB for general use

**Network:**
- Enable for web browsing within VM
- Disable if not needed (saves resources)

**Auto-Save:**
- Keep enabled (recommended)
- Disabling requires manual saves

---

## 🔧 Essential Tips

### Performance Tips

1. **First boot is slow** - downloading ~40MB ISO
2. **Second boot is fast** - loading from cache
3. **Lower memory** if slow - try 128MB or 256MB
4. **Close other tabs** - frees up browser resources
5. **Use Chrome/Firefox** - best compatibility

### Keyboard Tips

1. **Grab Keyboard** button - captures all keyboard input
2. **ESC key** - releases keyboard
3. **Click in screen** - focus the VM
4. **Tab key** - navigate menus in X11

### Troubleshooting

**VM won't start:**
- Check browser console (F12)
- Try incognito/private mode
- Disable ad blockers
- Use modern browser

**Slow performance:**
- Reduce memory to 128MB
- Stay in text mode (don't use startx)
- Disable auto-save temporarily

**No keyboard:**
- Click "Grab Keyboard" button
- Click inside the black screen
- Ensure browser tab has focus

---

## 📚 Learning Resources

### Basic Linux Commands

```bash
# File operations
ls          # list files
cd          # change directory
pwd         # print working directory
cat         # view file contents
nano        # edit files (easy)
vi          # edit files (advanced)
cp          # copy files
mv          # move/rename files
rm          # delete files
mkdir       # create directory

# System info
whoami      # current user
date        # show date/time
free        # memory usage
df          # disk usage
top         # running processes (q to quit)

# Network (if enabled)
ping        # test connectivity
wget        # download files
```

### SliTaz-Specific

```bash
# Package management
tazpkg list               # list installed packages
tazpkg search <name>      # search for package
tazpkg get-install <pkg>  # install package

# System tools
startx      # start graphical mode
poweroff    # shutdown (stops VM)
reboot      # restart
```

---

## 🎯 Common Tasks

### Create and Edit a Text File

```bash
# Using nano (easier)
nano myfile.txt
# Type your text
# Ctrl+O to save
# Ctrl+X to exit

# Using vi (traditional)
vi myfile.txt
# Press 'i' to insert
# Type your text
# Press ESC, then ':wq' to save and quit
```

### Browse the Web

```bash
# Text mode
links2 http://example.com

# Graphical mode (after startx)
links2 -g http://example.com
```

### Install Software

```bash
# Update package list
tazpkg recharge

# Search for package
tazpkg search python

# Install package
tazpkg get-install python
```

### Manage Files Graphically

```bash
# Start X11 if not already
startx

# Open file manager
pcmanfm
```

---

## 🎓 Next Steps

Once comfortable with basics:

1. **Learn Linux**: Practice commands, explore filesystem
2. **Install packages**: Try different software
3. **Customize**: Edit config files, change settings
4. **Develop**: Write scripts, programs
5. **Experiment**: It's safe - nothing affects your real computer!

---

## 🆘 Need Help?

1. **Check Console**: Click on terminal at bottom of page
2. **Read Logs**: Messages show what's happening
3. **TROUBLESHOOTING.md**: Solutions for common issues
4. **GitHub Issues**: Ask questions, report bugs
5. **Start Fresh**: Clear cache and reload if stuck

---

## 🎉 You're Ready!

You now know enough to:
- ✅ Start and stop the VM
- ✅ Login and use basic commands
- ✅ Save your work
- ✅ Start graphical mode
- ✅ Browse the web
- ✅ Manage files
- ✅ Troubleshoot issues

**Have fun exploring Linux in your browser! 🐧**

---

## 📖 More Documentation

- **README.md** - Full user guide
- **DEPLOYMENT.md** - Hosting and deployment
- **TESTING.md** - Testing procedures
- **TROUBLESHOOTING.md** - Detailed problem solving
