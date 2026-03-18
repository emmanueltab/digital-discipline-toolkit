# Hosts File Blocklist

The `/etc/hosts` file maps domain names to IP addresses at the system level, before DNS is even consulted. By pointing unwanted domains to `0.0.0.0` (nowhere), they become completely unreachable across all browsers and apps on your device.

This works even if someone changes their DNS settings, making it a stronger layer of protection than DNS alone.

---

## How it works

When you type a URL into a browser, your device first checks `/etc/hosts` before asking a DNS server. If the domain is listed there pointing to `0.0.0.0`, the request never leaves your machine.

---

## Setup

Open the hosts file:
```bash
sudo nano /etc/hosts
```

Scroll to the bottom and add your blocked domains in this format:
```
0.0.0.0 example.com
0.0.0.0 www.example.com
```

Save with `Ctrl+O` → `Enter` → `Ctrl+X`.

Flush the DNS cache so changes take effect immediately:
```bash
sudo resolvectl flush-caches
```

---

## Pre-built blocklists

Instead of building your list from scratch, use a community maintained blocklist.
StevenBlack's hosts repo is the most comprehensive and regularly updated option available:

[https://github.com/StevenBlack/hosts](https://github.com/StevenBlack/hosts)

It includes multiple categories such as adware, malware, adult content, social media, and more. You can download a combined list and paste it directly into your `/etc/hosts` file.

---

## Adding your own entries

Add domains specific to your own needs on top of any pre-built list:
```
0.0.0.0 example.com
0.0.0.0 www.example.com
```

---

## Tips

- Always block both the root domain and the `www` subdomain
- Block CDN domains too so content cannot load even if the main domain is missed
- Keep a backup of your hosts file
- Review and update regularly as new sites emerge

---

## Verify a block is working
```bash
curl -I https://domain-you-blocked.com
```

A blocked domain will fail to connect.

---

## Limitations

- Only blocks exact domains — subdomains must be listed separately
- Anyone with sudo access can edit the file
- Does not block IP-based access to sites
- Use alongside DNS filtering for stronger coverage
