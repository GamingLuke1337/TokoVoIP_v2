# TokoVOIP Master Server

## API Endpoints

### Health Check
- `GET /health` - Returns server status and uptime information

### Client Management
- `POST /register` - Register a client for handshake coordination
- `GET /handshake` - Retrieve handshake information for the requesting IP

### Server Management
- `POST /heartbeat` - Send heartbeat from WebSocket servers to maintain registration
- `GET /verify` - Verify if a TeamSpeak server is registered and active

### Version Control
- `GET /version` - Get current plugin version information and update messages

## Installation

1. Install dependencies:
```bash
npm install
```

2. Start the server:
```bash
npm start
```

## Automatic Cleanup

The server runs automatic cleanup jobs every 5 minutes to remove:
- Handshakes that haven't been updated in 2 minutes
- WebSocket servers that haven't sent heartbeats in 10 minutes
- Empty TeamSpeak server entries


### **(Optional) Run in the background and start automatically**

---

#### On Windows

##### Start on User-Login

1. Open **Notepad**, and paste the following:

   ```cmd
   @echo off
   cd /d "C:\Path\To\master_server"
   npm start
   ```

   Replace `C:\Path\To\ws-server` with the actual path.
2. Save the file as `start-master-server.bat`.
3. Press `Win + R`, type `shell:startup`, and press Enter.
4. Copy the `.bat` file into this folder.

##### Start on Boot

1. Press `Win + R`, type `taskschd.msc`, and press Enter.
2. In the right pane, click **Create Task**.
3. Under the **General** tab:

   * Name the task (e.g., `master-server`).
   * Check "Run whether user is logged on or not".
   * Check "Run with highest privileges".
4. Under the **Triggers** tab:

   * Click **New**, set "Begin the task" to `At startup`, click OK.
5. Under the **Actions** tab:

   * Click **New**.
   * Set "Action" to `Start a program`.
   * In the **Program/script** box, enter:

     ```powershell
     C:\Windows\System32\cmd.exe
     ```
   * In the **Add arguments** box, enter:

     ```cmd
     /c cd /d "C:\Path\To\master-server" && npm start
     ```

     Replace `C:\Path\To\master-server` with the actual path to your `master-server` directory.
6. Click OK and enter your password if prompted.

---

#### On Linux

## Option A: Using `screen` (simple background method)

### Install `screen` (if not already):

```bash
sudo apt install screen -y
```

### Start the server in a screen session:

```bash
screen -S tokovoip-master
cd /path/to/master_server
npm start
```

> To **detach** from the screen: press `Ctrl + A`, then `D`
> To **resume** later: `screen -r tokovoip-master`
> To **list** sessions: `screen -ls`
> To **kill** a session: `screen -XS tokovoip-master quit`

---

## Using `systemd` (recommended)

### 1. Create a systemd service file

```bash
sudo nano /etc/systemd/system/master-toko.service
```

### 2. Paste the following (edit paths and user as needed):

```ini
[Unit]
Description=TokoVoIP master server
After=network.target

[Service]
User=yourusername
WorkingDirectory=/home/yourusername/path/to/master_server
ExecStart=/usr/bin/npm start
Restart=always
Environment=NODE_ENV=production

[Install]
WantedBy=multi-user.target
```

> Replace:
>
> * `yourusername` with your Linux username.
> * The `WorkingDirectory` path to your actual `master_server` folder.
> * Check `which npm` if it's installed in a different path.

### 3. Reload systemd, enable and start the service

```bash
sudo systemctl daemon-reload
sudo systemctl enable master-toko
sudo systemctl start master-toko
```

### 4. Check status / logs

```bash
sudo systemctl status master-toko
sudo journalctl -u master-toko -f
```

---

### To stop or disable:

```bash
sudo systemctl stop master-toko
sudo systemctl disable master-toko
```