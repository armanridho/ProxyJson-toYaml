import requests
import yaml
import sys

def fetch_and_convert():
    url = "https://cdn.jsdelivr.net/gh/proxifly/free-proxy-list@main/proxies/all/data.json"
    
    try:
        print("[*] Fetching data dari Proxifly...")
        response = requests.get(url, timeout=10)
        response.raise_for_status()
        data = response.json()
    except Exception as e:
        print(f"[!] Gagal narik data: {e}")
        sys.exit(1)

    clash_proxies = {"proxies": []}
    count = 0

    for item in data:
        # Filter: Score > 0 DAN Anonymity harus 'elite'
        # Kita pake .lower() biar aman dari perbedaan case sensitivity
        anonymity = item.get('anonymity', '').lower()
        score = item.get('score', 0)

        if score > 0 and anonymity == 'elite':
            protocol = item['protocol'].lower()
            
            # Mapping protokol agar sesuai standar Clash
            # SOCKS4/5 di Clash tipenya 'socks4' atau 'socks5'
            # HTTP tetap 'http'
            proxy_entry = {
                "name": f"{item['geolocation']['country']}-{protocol}-{item['ip']}-{item['port']}",
                "type": protocol,
                "server": item['ip'],
                "port": int(item['port']),
                "udp": True if protocol.startswith('socks') else False # HTTP biasanya gak support UDP di proxy jadul
            }
            
            clash_proxies["proxies"].append(proxy_entry)
            count += 1
            
    # Simpan ke p1.yaml
    with open('p1.yaml', 'w') as f:
        # Kita tambahin header 'proxies:' manual di paling atas file
        yaml.dump(clash_proxies, f, default_flow_style=False, sort_keys=False)

    print(f"[+] Berhasil mengonversi {count} proxy Elite ke p1.yaml")

if __name__ == "__main__":
    fetch_and_convert()
    
