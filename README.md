# Elevate-lab-first-task
Python Script (TCP CONNECT Method)
#!/usr/bin/env python3
import socket
import threading
import queue

def scan_host(host, ports, q):
    for port in ports:
        try:
            with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
                s.settimeout(0.8)
                res = s.connect_ex((host, port))
                if res == 0:
                    q.put((host, port))
        except Exception:
            pass

def scan_network(hosts, start=1, end=1024, threads=100):
    port_queue = queue.Queue()
    for host in hosts:
        threading.Thread(target=scan_host,
                         args=(host, list(range(start, end+1)), port_queue),
                         daemon=True).start()

    # small wait to fill queue
    threading.Event().wait(1)
    results = {}
    while not port_queue.empty():
        host, port = port_queue.get()
        results.setdefault(host, []).append(port)
    return results

if __name__ == "__main__":
    # Example usage
    active_hosts = ['192.168.1.50', '192.168.1.51']  # Replace via Step 2
    found = scan_network(active_hosts, start=1, end=1024)
    for host, ports in found.items():
        print(f"{host}: {sorted(ports)}")
        
