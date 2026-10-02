# Air-Gapped AI Automation Stack

An isolated, self-hosted n8n automation pipeline reverse-proxied through Nginx with custom header validation and Zabbix telemetry.

## System Architecture

```text
                                [ REVERSE PROXY VM ]
                               +--------------------+
[ Email Provider ] ----------> |                    |
                               |  Docker Engine     |
[ Discord Webhook ] <--------- |   └─> n8n Container |
                               |          |         |
                               |   (Header Validation/
                               |    Reverse Proxy)  |
                               +----------|---------+
                                          |
                                          v Internal Loopback / Private LAN
                               +--------------------+
                               | Protected AI Host  |
                               | (Local Ollama/LLM) |
                               +--------------------+
                                          ^
                                          |
                        [ ZABBIX MONITORING NODE ]
                  (Monitors CPU, Memory, Ports & HTTP Services)