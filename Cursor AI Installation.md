# Cursor AI Installation and Setup

This guide provides step-by-step instructions to download, set up, and resolve issues with Cursor AI on Linux.

---

## Step 1: Download the Cursor AppImage

1. Download the AppImage:
   ```bash
   wget -O ~/Downloads/cursor.AppImage https://getcursor.app/releases/latest/linux-x86_64.AppImage
   ```

2. Move the AppImage to the `~/Applications` folder:
   ```bash
   mkdir -p ~/Applications
   mv ~/Downloads/cursor.AppImage ~/Applications/
   ```

3. Make it executable:
   ```bash
   chmod +x ~/Applications/cursor.AppImage
   ```

4. Verify it’s executable:
   ```bash
   ls -l ~/Applications/cursor.AppImage
   ```

---

## Step 2: Run the AppImage

1. Run the AppImage:
   ```bash
   ~/Applications/cursor.AppImage
   ```

2. If it fails with a `chrome-sandbox` error, try:
   ```bash
   ~/Applications/cursor.AppImage --no-sandbox
   ```

---

## Step 3: Extract the AppImage

If the AppImage still doesn’t work:

1. Extract the AppImage contents:
   ```bash
   ~/Applications/cursor.AppImage --appimage-extract
   ```

2. Navigate to the extracted directory:
   ```bash
   cd squashfs-root
   ```

3. Run the extracted application:
   ```bash
   ./AppRun
   ```

4. If the `chrome-sandbox` error persists, fix its permissions.

---

## Step 4: Fix `chrome-sandbox` Permissions

1. Change ownership to root:
   ```bash
   sudo chown root:root chrome-sandbox
   ```

2. Set the correct permissions:
   ```bash
   sudo chmod 4755 chrome-sandbox
   ```

3. Retry running the application:
   ```bash
   ./AppRun
   ```

4. If it still fails, try:
   ```bash
   ./AppRun --no-sandbox
   ```

---

## Step 5: Repack the AppImage (Optional)

If the extracted app works and you want to create a fixed AppImage:

1. Go back to the parent directory:
   ```bash
   cd ..
   ```

2. Install `appimagetool`:
   ```bash
   sudo apt install appimagetool
   ```

3. Repack the AppImage:
   ```bash
   appimagetool squashfs-root cursor-fixed.AppImage
   ```

4. Run the fixed AppImage:
   ```bash
   ./cursor-fixed.AppImage
   ```

---

## Step 6: Install Dependencies

Ensure all required libraries are installed:
```bash
sudo apt update
sudo apt install libnss3 libatk1.0-0 libatk-bridge2.0-0 libx11-xcb1 libxcomposite1 libxrandr2 libgbm1 libasound2
```

---

## Step 7: Enable User Namespaces (If Needed)

If sandboxing issues persist, enable unprivileged user namespaces:

1. Temporarily enable:
   ```bash
   sudo sysctl -w kernel.unprivileged_userns_clone=1
   ```

2. To make it permanent, edit `/etc/sysctl.conf`:
   ```bash
   sudo nano /etc/sysctl.conf
   ```
   Add this line:
   ```plaintext
   kernel.unprivileged_userns_clone=1
   ```

3. Save and reboot:
   ```bash
   sudo reboot
   ```

---

## Step 8: Debugging with `strace` (Optional)

If the issue persists, debug the application using `strace`:
```bash
strace ./AppRun
```

---

Feel free to report any errors or issues encountered during setup. Provide error messages, and we’ll guide you further!

