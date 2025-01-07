# Ubuntu Backup and Restore Guide

## Steps to Take a Backup

### 1. Mount the External Drive
1. Identify the external drive:
   ```bash
   lsblk
   ```
2. Create a mount point and mount the drive:
   ```bash
   sudo mkdir -p /media/moamen/sda3
   sudo mount /dev/sda3 /media/moamen/sda3
   ```

### 2. Create the Backup
- Use `tar` to create a compressed backup of your system:
  ```bash
  sudo tar -cvpzf /media/moamen/sda3/system-backup.tar.gz \
    --exclude={"/dev/*","/proc/*","/sys/*","/tmp/*","/run/*","/mnt/*","/media/*","/lost+found","/swapfile"} /
  ```

### 3. Verify the Backup
- Ensure the backup file exists and check its size:
  ```bash
  ls -lh /media/moamen/sda3/system-backup.tar.gz
  ```

### 4. Unmount the Drive
- Safely unmount the external drive:
  ```bash
  sudo umount /media/moamen/sda3
  ```

---

## Steps to Restore the Backup

### 1. Boot Into Live Ubuntu
1. Use a bootable Ubuntu USB or installation media.
2. Select "Try Ubuntu" to enter the live session.

### 2. Mount the External Drive
1. Identify the external drive:
   ```bash
   lsblk
   ```
2. Mount the drive containing the backup:
   ```bash
   sudo mkdir -p /mnt/backup
   sudo mount /dev/sda3 /mnt/backup
   ```
3. Verify the backup file:
   ```bash
   ls /mnt/backup/system-backup.tar.gz
   ```

### 3. Mount the Root Partition
1. Identify the root partition of the new Ubuntu installation:
   ```bash
   lsblk
   ```
2. Mount the root partition:
   ```bash
   sudo mkdir -p /mnt/ubuntu
   sudo mount /dev/nvme0n1p2 /mnt/ubuntu
   ```

### 4. Extract the Backup
- Restore the backup using `tar`:
  ```bash
  sudo tar -xvpzf /mnt/backup/system-backup.tar.gz -C /mnt/ubuntu
  ```

### 5. Fix the Bootloader (If Necessary)
1. Chroot into the restored system:
   ```bash
   sudo mount --bind /dev /mnt/ubuntu/dev
   sudo mount --bind /proc /mnt/ubuntu/proc
   sudo mount --bind /sys /mnt/ubuntu/sys
   sudo chroot /mnt/ubuntu
   ```
2. Reinstall GRUB:
   ```bash
   grub-install /dev/nvme0n1
   update-grub
   ```
3. Exit the chroot environment:
   ```bash
   exit
   ```
4. Unmount all partitions:
   ```bash
   sudo umount /mnt/ubuntu/dev
   sudo umount /mnt/ubuntu/proc
   sudo umount /mnt/ubuntu/sys
   sudo umount /mnt/ubuntu
   ```

### 6. Reboot and Test
- Reboot your system:
  ```bash
  sudo reboot
  ```
- Your system should boot into the restored environment.

---

## Notes
- Ensure your external drive has sufficient free space for the backup.
- Use high-speed USB ports for faster backups.
- If restoring to new hardware, adjust configurations or reinstall drivers as necessary.

#%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
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

