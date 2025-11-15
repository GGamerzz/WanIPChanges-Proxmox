from urllib.request import urlopen
import re
import os

# Paths for node_exporter textfile collector
TEXTFILE_DIR = "/wan_ip_checker/textfiles"
METRIC_FILE = os.path.join(TEXTFILE_DIR, "wan_ip.prom")
LAST_IP_FILE = os.path.join(TEXTFILE_DIR, "last_ip.txt")

def get_current_ip():
    data = str(urlopen('http://checkip.dyndns.com/').read())
    match = re.search(r'Address: (\d+\.\d+\.\d+\.\d+)', data)
    return match.group(1) if match else None

def get_last_ip():
    if not os.path.exists(LAST_IP_FILE):
        return None
    with open(LAST_IP_FILE, "r") as f:
        return f.read().strip()

def save_ip(ip):
    with open(LAST_IP_FILE, "w") as f:
        f.write(ip)

def update_metric(current_ip, changed):
    metric_content = f'wan_ip_change{{ip="{current_ip}"}} {changed}\n'
    with open(METRIC_FILE, "w") as f:
        f.write(metric_content)

def main():
    current_ip = get_current_ip()
    if not current_ip:
        print("Could not retrieve WAN IP")
        return

    last_ip = get_last_ip()
    changed = 1 if current_ip != last_ip else 0

    if changed:
        save_ip(current_ip)

    update_metric(current_ip, changed)
    print(f"Current IP: {current_ip}, Changed: {changed}")

if __name__ == "__main__":
    main()
