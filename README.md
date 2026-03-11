# WSL2 Ubuntu Full Desktop Environment (XFCE4 + XRDP)

This repository provides a comprehensive guide to setting up a full Graphical User Interface (GUI) on Ubuntu WSL2 using XRDP. This configuration bypasses the "Black Screen" bug and permission issues with WSLg.

---

## 1. Windows Host Configuration
To prevent WSLg (Wayland) from conflicting with XRDP (X11) and causing "Read-only file system" errors in `/tmp`, you must disable native GUI support.

1. Open **File Explorer** and go to `%USERPROFILE%` (e.g., `C:\Users\YourName`).
2. Create or edit a file named `.wslconfig`.
3. Paste the following:

    ```
    [wsl2]
    guiApplications=false
    ```

4. Open PowerShell and restart WSL:

    ```
    wsl --shutdown
    ```

## 2. Desktop Environment Installation
Open your Ubuntu terminal and install the lightweight XFCE4 desktop and XRDP server:

```
sudo apt update && sudo apt upgrade -y
sudo apt install xfce4 xfce4-goodies x11-xserver-utils dbus-x11 xorg -y
sudo apt install xrdp -y
```

## 3. XRDP & Session Configuration
1. **Change Port:** Set XRDP to port ```3390``` to avoid conflicts with Windows Remote Desktop.

    ```
    sudo sed -i 's/3389/3390/g' /etc/xrdp/xrdp.ini
    ```

2. **Configure Startup:** Create the session script to force X11 and XFCE.

    ```
    cat <<EOF > ~/.xsession
    unset WAYLAND_DISPLAY
    export DISPLAY=:10.0
    export XDG_SESSION_TYPE=x11
    export XDG_CURRENT_DESKTOP=XFCE
    exec dbus-launch --exit-with-session startxfce4
    EOF
    chmod +x ~/.xsession
    ```

3. **Fix Permissions:** Clear ghost sockets and set directory permissions.

    ```
    sudo chmod 1777 /tmp
    sudo mkdir -p /tmp/.ICE-unix
    sudo chmod 1777 /tmp/.ICE-unix
    sudo chown root:root /tmp/.ICE-unix
    ```

## 4. How to Connect
1. **Start Service:** ```sudo service xrdp start```

2. **Open Remote Desktop Connection (mstsc.exe)**.

3. **Computer:** ```localhost:3390```

4. **Display Settings:** Set Color Depth to **16-bit**.

5. **Experience Settings:** Uncheck **Persistent bitmap caching**.

6. **Login:** Use your Ubuntu username and password.

## Troubleshooting

- **Black Screen:** Run ```sudo service xrdp restart``` and ```sudo pkill -u $(whoami)```.
