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


