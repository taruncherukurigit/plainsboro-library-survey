# Deployment — packetgeist.tarunc.com

This site is a **second vhost on the existing DMZ container** built during the
Cherwood Corporation project. No new container, no new tunnel, no new firewall rule.

## What already exists

From the Cherwood build (Part 10.5):

- LXC container on the T14, network interface on **VLAN 40 (DMZ)**, `10.10.40.0/24`
- `nginx` serving the Cherwood site
- `cloudflared` running a **named tunnel** against the Cloudflare account
- FortiGate policy: `DMZ → Internet` ACCEPT, `DMZ → Trusted/Servers/Guest` DENY,
  no inbound WAN→DMZ rule at all

All of that is reused as-is. The tunnel is outbound-initiated, so publishing a
second hostname opens nothing new on the perimeter.

## What to add

Three things, all inside the existing container.

### 1. The site files

```bash
mkdir -p /var/www/packetgeist
# copy the repo contents in — scp, rsync, or a one-off git clone.
# Exclude deploy/ and .git; nothing else needs excluding.
chown -R www-data:www-data /var/www/packetgeist
```

### 2. The nginx vhost

```bash
cp deploy/nginx-packetgeist.conf /etc/nginx/sites-available/packetgeist
ln -s /etc/nginx/sites-available/packetgeist /etc/nginx/sites-enabled/
nginx -t && systemctl reload nginx
```

Both sites now listen on port 80 and nginx selects between them by
`server_name`. Confirm the existing Cherwood vhost has an explicit
`server_name cherwood.tarunc.com;` — if it is currently the catch-all default,
set that explicitly now, otherwise whichever loads first will answer for both.

```bash
curl -s -H 'Host: packetgeist.tarunc.com' http://127.0.0.1/ | head -5
curl -s -H 'Host: cherwood.tarunc.com'    http://127.0.0.1/ | head -5
```

### 3. The tunnel hostname

Add an ingress rule to the existing tunnel config, above the catch-all:

```yaml
# /etc/cloudflared/config.yml
ingress:
  - hostname: cherwood.tarunc.com
    service: http://127.0.0.1:80

  - hostname: packetgeist.tarunc.com     # <-- new
    service: http://127.0.0.1:80

  - service: http_status:404
```

Then create the DNS record and restart:

```bash
cloudflared tunnel route dns <tunnel-name> packetgeist.tarunc.com
systemctl restart cloudflared
```

`tunnel route dns` writes the CNAME into Cloudflare itself. Do not also add a
manual record — you will end up with two conflicting answers.

## Verify

```bash
systemctl status cloudflared --no-pager
curl -sI https://packetgeist.tarunc.com | head -1
curl -sI https://cherwood.tarunc.com    | head -1   # confirm you didn't break it
```

## Updating the site later

Copy the changed files into `/var/www/packetgeist` and reload nginx. It is a
static site; there is no build step and nothing to restart beyond nginx.

The GitHub repository is not a deployment mechanism — it is the public,
always-available copy for anyone reading this work while the lab is powered off.
