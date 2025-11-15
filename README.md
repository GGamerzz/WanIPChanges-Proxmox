# WAN IP Checker

A Python script to monitor your public (WAN) IP address and expose it as a Prometheus metric for visualization and alerting in Grafana. Ideal for dynamic IP environments or home labs.

---

## Features

- Checks your current public IP via a reliable web service
- Compares the current IP with the last known IP
- Writes a Prometheus-compatible `.prom` file (`wan_ip.prom`) for `node_exporter` textfile collector
- Indicates IP changes: `wan_ip_change = 1` if changed, `wan_ip_change = 0` if unchanged
- Stores the last known IP in `last_ip.txt`
- Can be scheduled to run periodically using cron

---

## How It Works

1. Queries `http://checkip.dyndns.com/` to get your current public IP
2. Reads the previous IP from `last_ip.txt` (creates it on first run if missing)
3. Sets `wan_ip_change` to:
   - `1` if the IP has changed, updating `last_ip.txt`
   - `0` if the IP is unchanged
4. Writes the `.prom` file in the directory monitored by `node_exporter`
5. Prometheus scrapes the metric, and Grafana can visualize or trigger alerts

---

## Installation

### Clone the Repository

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
```

---

## Usage

### Run Manually

To run the script manually:

```bash
python3 update_wan_ip.py
```

### Configure Node Exporter

Mount the directory where `wan_ip.prom` is written for the textfile collector:

```
/root/wan_ip_checker/textfiles
```

**Example command-line flag:**

```bash
--collector.textfile.directory=/root/wan_ip_checker/textfiles
```

### Schedule with Cron

To run the script every 12 hours, add this to your crontab:

```bash
0 */12 * * * python3 /root/wan_ip_checker/update_wan_ip.py
```

---

## Integration

### Prometheus & Grafana

- **Prometheus** scrapes the metric from `node_exporter`
- **Grafana** can display the current WAN IP and trigger alerts when `wan_ip_change == 1`

---

## When to Use

- Monitoring dynamic WAN IPs
- Home labs or servers behind residential ISPs
- Integrating WAN IP tracking into a Prometheus + Grafana stack
- Triggering alerts or automations on IP changes

---

## License

This project is licensed under the GNU General Public License v3.0 - see the LICENSE file for details.
