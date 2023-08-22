# Tenderduty monitoring tool for Elys Network validators

NOTE: We recommand using a VPS (2 GB RAM, 20 GB Hard disk). Also, make sure to open the port 26657 on your validator server.

## Install ``docker``

### 1. Update the package database:
```
sudo apt update
```
### 2. Install required dependencies:
```
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```
### 3. Add Docker's GPG key:
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
### 4. Add Docker's repository:
```
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
### 5. Update the package database again:
```
sudo apt update
```
### 6. Install ``docker``:
```
sudo apt install docker-ce
```



## Install ``docker-compose``

### 1. Download the Docker Compose v1.29.2:
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
### 2. Make the Docker Compose binary executable:
```
sudo chmod +x /usr/local/bin/docker-compose
```
### 3. Verify the installation by checking the version:
```
docker-compose --version
```


## Install ``tenderduty``
```
mkdir tenderduty && cd tenderduty
docker run --rm ghcr.io/blockpane/tenderduty:latest -example-config >config.yml
```

## Edit config.yml and add Elsys Testnet details, notification methods etc. 
NOTE: You can delete the existing ``config.yml`` file and create a new file using ``vim`` or ``nano``.

In this example, we are using PagerDuty service to get an alert in form of Phone Call. You will need to register a free account with PagerDuty, 
1. Register a free account at PagerDuty
2. Add Contact info including a phone number
3. Create Escalation Policy and add your profile to the Notify section
4. Create a new Service under Service Directory and select the Escalation Policy created
5. Select "Events API V2" option and create Service.
6. Get the "Integration Key"

There are many Youtube tutorials which will help you to achieve the above.

In the following Config, add the following:
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

## Start Tenderduty

```
docker run -d --name tenderduty -p "8888:8888" -p "28686:28686" --restart unless-stopped -v $(pwd)/config.yml:/var/lib/tenderduty/config.yml ghcr.io/blockpane/tenderduty:latest
```


