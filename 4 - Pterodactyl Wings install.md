# Pterodactyl Wings install

[Back to README](README.md)

## Dependencies

### Installing Docker

```sh
curl -sSL https://get.docker.com/ | CHANNEL=stable bash
```

Start Docker on Boot

```sh
systemctl enable --now docker
```

```sh
docker info
```

Check if swap is enabled, if it outputs `WARNING: No swap limit support` than we will need to enable swap

```sh
cd /etc/default/
```

```sh
nano grub
```

find the line `GRUB_CMDLINE_LINUX_DEFAULT=""` and add `swapaccount=1`

save and exit nano

```sh
sudo update-grub
```

```sh
sudo reboot
```

confirm that swap is anabled

```sh
docker info
```

### Installing Wings

#### Directory & Wings Executable

```sh
mkdir -p /etc/pterodactyl
```

```sh
curl -L -o /usr/local/bin/wings https://github.com/pterodactyl/wings/releases/download/v1.1.1/wings_linux_amd64
```

```sh
chmod u+x /usr/local/bin/wings
```

### Configure

Once you have done the above go into the panel & create a new node

after making the new node

```sh
cd /etc/pterodactyl
```

```sh
nano config.yml
```

Copy and paste the text in configuration on the panel

#### Starting Wings

```sh
cd /etc/pterodactyl
```

```sh
sudo wings --debug
```

after letting it start up use `CTRL+C` to terminate the process

#### Daemonizing

```sh
cd /etc/systemd/system
```

```sh
nano wings.service
```

paste below into wings.service

```sh
[Unit]
Description=Pterodactyl Wings Daemon
After=docker.service

[Service]
User=root
WorkingDirectory=/etc/pterodactyl
LimitNOFILE=4096
PIDFile=/var/run/wings/daemon.pid
ExecStart=/usr/local/bin/wings
Restart=on-failure
StartLimitInterval=600

[Install]
WantedBy=multi-user.target
```

reload systemd and start wings

```sh
systemctl enable --now wings
```
