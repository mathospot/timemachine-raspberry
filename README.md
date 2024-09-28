
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

## Contact

For any questions or inquiries, feel free to reach out at mathospot@gmail.com.