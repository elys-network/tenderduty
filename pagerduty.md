# Pagerduty Config

Source: [https://github.com/blockpane/tenderduty/blob/main/docs/pagerduty.md](https://github.com/blockpane/tenderduty/blob/main/docs/pagerduty.md)

The Pagerduty application offers two types of keys that can access their API. Tenderduty uses an API key, not an OAuth key. It will detect that it has been provided an OAuth key at startup and refuse to start. Here is how to get the correct type of key:

## To generate your pagerduty integration key

1. Within PagerDuty, go to Services --> Service Directory --> New Service

<img src="https://raw.githubusercontent.com/elys-network/tenderduty/main/img/pd-services.png">
<img src="https://raw.githubusercontent.com/elys-network/tenderduty/main/img/pd-newservice.png">

2. Give your service a name, select an escalation policy, and set an alert grouping preference

<img src="https://raw.githubusercontent.com/elys-network/tenderduty/main/img/pd-create.png">
<img src="https://raw.githubusercontent.com/elys-network/tenderduty/main/img/pd-policy.png">


3. Select the PagerDuty Events API V2 Integration, hit Create Service

<img src="https://raw.githubusercontent.com/elys-network/tenderduty/main/img/pd-integrate.png">

4. Copy the 32 character ``Integration Key`` into the config.yml file

<img src="https://raw.githubusercontent.com/elys-network/tenderduty/main/img/pd-key.png">
