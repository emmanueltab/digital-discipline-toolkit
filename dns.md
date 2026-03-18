# DNS Setup

DNS (Domain Name System) is what translates a domain name like `youtube.com` into an IP address your device can connect to. By changing your DNS provider to one that filters content, you can block entire categories of websites before they ever reach your browser.

There are two levels of DNS blocking. Use both for maximum coverage.

---

## Level 1: Network-Level (Router)

This blocks content for every device on your home network. Best for people who own or control their router.

### Setup with OpenDNS

1. Log into your router admin panel. Usually found at `192.168.1.1` or `192.168.0.1` in your browser.
2. Find the DNS settings. Usually under WAN, Internet, or Advanced settings.
3. Set the primary and secondary DNS to:
   - Primary: `208.67.222.222`
   - Secondary: `208.67.220.220`
4. Save and restart your router.

### Configure filtering categories

1. Create a free account at [opendns.com](https://www.opendns.com)
2. Add your network's IP address to your OpenDNS dashboard
3. Under Web Content Filtering, select the categories you want to block
4. Changes take effect within minutes

---

## Level 2: Device-Level (Local DNS)

This blocks content directly on your device regardless of what network you are on. Works on public WiFi, school networks, or any network you do not control.

### Setup on Linux (systemd-resolved)

Edit the resolved config:
```bash
sudo nano /etc/systemd/resolved.conf
```

Find or add these lines:
```
[Resolve]
DNS=208.67.222.222 208.67.220.220
FallbackDNS=8.8.8.8
```

Save and restart the service:
```bash
sudo systemctl restart systemd-resolved
```

Verify it is working:
```bash
resolvectl status
```

You should see the OpenDNS addresses listed under DNS Servers.

### Prevent apps from bypassing local DNS

Some browsers like Firefox have their own DNS settings that can bypass system DNS. Disable this in Firefox:

1. Open Firefox
2. Go to `about:config` in the address bar
3. Search for `network.trr.mode`
4. Set the value to `5`

This forces Firefox to use the system DNS instead of its own.

---

## Verify blocking is working
```bash
curl -I https://example-blocked-site.com
```

A blocked site will fail to connect or return no response. An unblocked site will return a normal HTTP response.

---

## Limitations

- Device-level DNS can be changed by anyone with admin access to the device
- A determined person can switch to a different DNS provider manually
- Use this in combination with the hosts file blocklist for a stronger defense
