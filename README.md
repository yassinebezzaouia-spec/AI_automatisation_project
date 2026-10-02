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

## Demo Workflow (Proof of Concept)

The included `email-discord-summary.json` workflow serves as a **functional reference implementation**. 

While this example demonstrates automated email parsing, local AI summarization via Ollama, and Discord webhooks, **the underlying infrastructure is completely workflow-agnostic**. Any custom n8n workflow (e.g., Slack alerts, database syncs, webhook processors, or automated incident responses) can leverage this secure, air-gapped proxy setup.


## 🔒 Nginx Reverse Proxy Configuration

The proxy layer filters traffic and injects security headers before forwarding requests to the Ollama local inference engine.

- **Active Config Source:** `/etc/nginx/sites-available/default`
- **Active Configuration Symlink:** `/etc/nginx/sites-enabled/default`
- **System Log File Access:** `/var/log/nginx/error.log`

### Deployment Steps
1. Place your configuration file in `/etc/nginx/sites-available/default`.
2. Create the symlink to activate the configuration:
   ```bash
   sudo ln -s /etc/nginx/sites-available/default /etc/nginx/sites-enabled/



## Zabbix Telemetry & Monitoring

This infrastructure includes active monitoring via Zabbix:
- **Monitored Endpoints:** HTTP health checks for n8n, Nginx proxy status, and Ollama host availability.
- **Metrics Tracked:** CPU/Memory usage, network latency, and open service ports.




## 🛠️ Replicating the Monitoring Setup

This stack relies on standard Zabbix Agents to report telemetry to a Zabbix Server.

### Requirements:
- A running **Zabbix Server** instance.
- **Zabbix Agent** (v6.0+) installed on target nodes.

### Setup Steps:
1. Install Zabbix Agent on the AI Host (`Windows`) and Proxy Host (`Linux/Debian`).
2. Configure `zabbix_agentd.conf` with your Zabbix Server IP:
   ```text
   Server=YOUR_ZABBIX_SERVER_IP
   ServerActive=YOUR_ZABBIX_SERVER_IP



## 📸 Infrastructure Monitoring Status
![Zabbix Hosts](docs/zabbix-hosts.png)