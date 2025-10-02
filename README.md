
# TimeMachine Backup on Raspberry Pi

This project configures a Raspberry Pi to function as a TimeMachine backup server for macOS devices. With Docker, the Raspberry Pi provides a backup destination for macOS, allowing automatic backups.

## Requirements

- Raspberry Pi (tested on Raspberry Pi 3/4)
- Docker installed on the Raspberry Pi
- macOS device for TimeMachine setup
- A large enough external storage (e.g., external hard drive) connected to the Raspberry Pi to store backups
- **Highly recommended:** Ensure that the external disk is configured in the `/etc/fstab` file to automatically mount on boot at the designated shared location for stability during backups.

## Installation Instructions

### Step 1: Clone the Repository

```bash
git clone https://github.com/mathospot/timemachine-raspberry.git
cd timemachine-raspberry
```

### Step 2: Build and Start the Docker Container

1. **Replace the path to your external disk** in the `docker-compose.yml` file under the `Samba - Volumes` section. Ensure it points to the location where your external disk is mounted.
2. **Optional:** Modify the `TZ` environment variable to reflect your local timezone for proper log timestamps (e.g., `TZ=Europe/Madrid`).
3. **Set up Samba credentials:** In the `docker-compose.yml` file under the `Samba - Command` section, create a username and password that will be used by macOS to connect to the Samba service for backups.
4. In the `Avahi - Volumes` section, verify that the path to your project repository is correctly specified.

Once the above modifications are done, you can build and start the Docker container with:

```bash
docker-compose up --build -d
```

### Step 3: Configure macOS TimeMachine

1. On your macOS device, open **System Preferences** > **Time Machine**.
2. Click **Select Backup Disk**, then choose the Raspberry Pi backup location.
3. Authenticate using the username and password you set up in the Samba service configuration.

### Step 4: Verify Backups

Once configured, macOS will automatically begin backing up to the Raspberry Pi at regular intervals. You can check the status or force a backup through the TimeMachine menu on your macOS device.

## Security and best-practices (important)

- Credentials: do NOT store real passwords in the repository. Use the provided `.env.example` to create a local `.env` file and set `SAMBA_USER` and `SAMBA_PASS`. Keep `.env` out of git and set secure permissions:

```bash
cp .env.example .env
chmod 600 .env
# edit .env and set SAMBA_PASS
```

- If you need persistent, more secure secrets and orchestration, consider Docker secrets or a system-level secret manager.

- Network: expose SMB ports only on your LAN. If your Raspberry Pi has a public IP, use firewall rules to block 139/445 from the internet.

- Permissions: ensure the host mount point is owned by the UID/GID that Samba inside the container will use, or configure Samba to `force user` appropriately. Example:

```bash
sudo chown -R 1000:1000 /mnt/storage/timemachine
```

- `smb.conf`: for best macOS compatibility (Time Machine over SMB) use a Samba configuration with `vfs objects = catia fruit streams_xattr` and `fruit:time machine = yes`. A template `smb.conf.example` is provided in this repo.

## Mounting external disk automatically (fstab example)

Add an entry to `/etc/fstab` so the external disk mounts at boot. Example (replace with the correct UUID and filesystem):

```text
UUID=YOUR-DRIVE-UUID /mnt/storage/timemachine ext4 defaults,noatime 0 2
```

You can get the UUID with `lsblk -f` or `blkid` on Linux.

## Files provided as templates

- `.env.example` — environment variables template (copy to `.env` and edit)
- `smb.conf.example` — Samba configuration tuned for Time Machine (use as reference or mount into the container)
- `docker-compose.yml.example` — template compose file which reads values from `.env`

If you want, I can add a Dockerfile that builds a custom Samba image with `vfs_fruit` enabled and copies the `smb.conf` into the image. Let me know and I will create it as an optional addition.

## Contact

For any questions or inquiries, feel free to reach out at mathospot@gmail.com.