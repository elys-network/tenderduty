# Tenderduty monitoring tool for Elys Network validators
### Building from Source and running as a systemd service

NOTE: We recommand using a VPS (2 GB RAM, 20 GB Hard disk). Also, make sure to open the port 26657 on your validator server.

## Create a new User

```
# add user
adduser node

# add user to sudoers
usermod -aG sudo node

# login as user
su - node
```

## Install Prerequisites

```
sudo apt update
sudo apt install pkg-config build-essential libssl-dev curl jq git libleveldb-dev -y
sudo apt-get install manpages-dev -y
```

## Install go
```
curl https://dl.google.com/go/go1.19.5.linux-amd64.tar.gz | sudo tar -C/usr/local -zxvf -

# Update environment variables to include go
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF

source $HOME/.profile

# check go version
go version
```

## Install Tenderduty

```
cd
git clone https://github.com/blockpane/tenderduty
cd tenderduty
go install
cd
```

## Create config.yml and add Elsys Testnet details, notification methods etc. 
NOTE: You can delete the existing ``config.yml`` file and create a new file using ``vim`` or ``nano``.

In the Config, add the following:
1. Replace "PagerDuty-Events-API-V2-Integration-Key" with PagerDuty Integration key
2. Replace "valoper-address" with your Valooper Address
3. Replace tcp://ip-address:26657 with your validator ip with the correct port (Default is 26657

```
---

enable_dashboard: yes
listen_port: 8888
hide_logs: no
node_down_alert_minutes: 3
node_down_alert_severity: critical

prometheus_enabled: yes
prometheus_listen_port: 28686

pagerduty:
  enabled: no
  api_key: <PagerDuty-Events-API-V2-Integration-Key>
  default_severity: alert

discord:
  enabled: no
  webhook: https://discord.com/api/webhooks/999999999999999999/zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz

telegram:
  enabled: no
  api_key: '5555555555:AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA'
  channel: "-666666666"

slack:
  enabled: no
  webhook: https://hooks.slack.com/services/AAAAAAAAAAAAAAAAAAAAAAA/bbbbbbbbbbbbbbbbbbbbbbbb

chains:

  "Elys Testnet":
    chain_id: elystestnet-1
    valoper_address: <valoper-address>
    public_fallback: yes

    alerts:
      stalled_enabled: yes
      stalled_minutes: 10

      consecutive_enabled: yes
      consecutive_missed: 5
      consecutive_priority: critical

      percentage_enabled: yes
      percentage_missed: 10
      percentage_priority: warning

      alert_if_inactive: yes
      alert_if_no_servers: yes

      pagerduty:
        enabled: yes
        api_key: "" # uses default if blank

      discord:
        enabled: no
        webhook: "" # uses default if blank

      telegram:
        enabled: no
        api_key: "" # uses default if blank
        channel: "" # uses default if blank

      slack:
          enabled: no
          webhook: "" # uses default if blank

    nodes:
      - url: tcp://<ip-address>:26657
        alert_if_down: yes

```

## Create Service File

# Create Service
Make sure to add the correct path for ``config.yml`` file.
```
sudo tee /etc/systemd/system/tenderduty.service > /dev/null <<EOF
[Unit]
Description=Tenderduty Daemon
#After=network.target
StartLimitInterval=350
StartLimitBurst=10

[Service]
Type=simple
User=node
ExecStart=tenderduty -f /home/node/config.yml
Restart=on-abort
RestartSec=30

[Install]
WantedBy=multi-user.target

[Service]
LimitNOFILE=1048576
EOF
```

## Enable & start the Service

```
# Enable service
sudo systemctl enable tenderduty

# Start service
sudo service tenderduty start

# Check logs
sudo journalctl -fu tenderduty
```

## Browser URL:
To visit Tenderduty page -
http://<ip-address>:8888


