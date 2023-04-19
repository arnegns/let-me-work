## Guide: Setting up WSL2, Docker, and Docker Compose on Windows

1. **Enable WSL2 and set it as default:**

   Open PowerShell as an Administrator and run the following command:

   ```powershell
   wsl.exe --set-default-version 2
    ```
    _Explanation_: This command enables WSL2 and sets it as the default version for future WSL installations.


2. **Install Ubuntu as WSL:**

    Run the following command in PowerShell:

    ```powershell
    wsl.exe --install -d Ubuntu
    ```
    _Explanation_: This command installs Ubuntu as a WSL distribution on your computer. 
     Wait for the installation of Ubuntu, as it may take some time.


3. **Create the .wslconfig file in C:\Users\USER:**

   Run the following command in PowerShell to create the .wslconfig file:

    ```powershell
    $wslConfigContent = @"
    [wsl2]
    localhostforwarding=true
    "@
    $wslConfigPath = Join-Path -Path $env:USERPROFILE -ChildPath ".wslconfig"
    Set-Content -Path $wslConfigPath -Value $wslConfigContent
    ```
    _Explanation_: Explanation: This command creates a .wslconfig file in your user profile, enabling the setting for localhost forwarding.


4. **Update WSL:**

    Run the following command in PowerShell to update WSL:

    ```powershell
    wsl.exe --update
    ```
   _Explanation_: Explanation: This command ensures you are using the latest version of WSL2 and its components.


5. **Restart your computer:**

    After the update is complete, restart your computer to ensure the changes take effect.

    _Explanation_: A restart is required to ensure all updates have been applied correctly and the WSL2 components are functioning properly.


6. **Run the following commands in WSL:**

   After restarting, open WSL (Ubuntu) and run the following commands:


   - Update the package list:

     ```bash
     sudo apt-get update
     ```

     *Explanation:* This command updates the package list on your Ubuntu WSL, ensuring you have access to the latest package versions and their dependencies.


   - Install Docker and Python3:

     ```bash
     sudo apt-get install -y docker.io python3
     ```

     *Explanation:* This command installs the Docker and Python3 packages on your Ubuntu WSL.


   - Create and configure the `/etc/wsl.conf` file:

     ```bash
     echo "[boot]" | sudo tee /etc/wsl.conf
     echo "systemd=true" | sudo tee -a /etc/wsl.conf
     ```

     *Explanation:* These two commands create and configure the `/etc/wsl.conf` file. The first command creates the file and adds the `[boot]` section. The second command appends `systemd=true` to the file, enabling the use of systemd within WSL.


   - Create the `/etc/systemd/system` directory:

     ```bash
     sudo mkdir -p /etc/systemd/system
     ```

     *Explanation:* This command creates the `/etc/systemd/system` directory if it does not already exist. This is where the Docker systemd service file will be placed.


   - Create the Docker systemd service file:

     ```bash
     echo -e "[Unit]\nDescription=Docker daemon\nAfter=network.target\n\n[Service]\nExecStart=/usr/bin/dockerd -H unix:///var/run/docker.sock -H tcp://0.0.0.0:2375 --iptables=false\nRestart=always\nRestartSec=10s\nLimitNOFILE=infinity\n\n[Install]\nWantedBy=multi-user.target" | sudo tee /etc/systemd/system/dockerd.service
     ```

     *Explanation:* This command creates a Docker systemd service file in `/etc/systemd/system/dockerd.service`. The file contains the configuration for the Docker daemon, such as its startup options and dependencies.


   - Enable the Docker systemd service:

     ```bash
     sudo systemctl enable dockerd.service
     ```

     *Explanation:* This command enables the Docker systemd service, which means that the Docker daemon will start automatically when the WSL starts up.


7. _Install Docker Compose: (In case docker-compose cannot be found)_

    Run the following commands in WSL to install Docker Compose:

    ```bash
    sudo apt-get install -y curl
    sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    sudo chmod +x /usr/local/bin/docker-compose
    ```

   _Explanation_: These commands install Curl, download the Docker Compose binary, and make it executable so it can be used as a command.


8. **Restart WSL:**

   Close WSL and reopen it to apply the changes.

   _Explanation_: Restarting WSL is required to apply the new settings and installations.




