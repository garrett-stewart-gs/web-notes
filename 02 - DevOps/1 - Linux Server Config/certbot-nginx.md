## Installing Certbot for NGINX
- Ensure Linux and Packages are up-to-date: `sudo apt update`
- Install Certbot for Nginx: `sudo apt install -y certbot python3-certbot-nginx`

## Using Certbot for NGINX
- Generate SSL Certs with: `sudo certbot --nginx -d <domain> -d www.<domain> --redirect --hsts --staple-ocsp`
  for example: `sudo certbot --nginx -d google.com -d www.google.com`
  - `--nginx` - configures certbot for nginx, allows it to write the tls blocks / inject directives
  - `--redirect` - changes http connection to https with ssl encryption. http requests -> https
  - `--hsts` - enforces https connection (enables strict-transport-security)
  - `--staple-ocsp` - creates faster trust checks (enables OCSP)
- Using the above command stores the SSL Cert/Key in `/etc/letsencrypt/live/<domain>/`
  - There are 2 files created:
    - fullchain.pem - contains leaf certificate + the intermediate CA cert(s). These are presented to clients so browser's can build a full chain of trust
    - privkey.pem (private key, owned by root:root. Keep it a secret, nginx uses it during the TLS handshake)

## Auto Renewal for SSL Certificates, Using Certbot
- Auto Renewal is installed automatically by certbot
- By Default:
  - SSL Certs are valid for 90 days
  - Certbot renews every 60 days (which gives 30 day buffer)
  - Automatic Renewal is managed by systemd timer like certbot.timer (or cron on older distros) scheduling the command `certbot renew`
  - The symlinks in `/etc/letsencrypt/live/<domain>/` are updated directly, and there is no need to edit the nginx config files 
- `Certbot.timer` usually runs twice daily, calls `certbot-renew.service`, which runs `certbot renew`


## Manual Certbot Controls
- `sudo certbot certificates` - Lists all certs and expiries
- `sudo certbot renew --dry-run` - Do a practice certificate renewal, to ensure it will actually work when it needs to renew (automatically)
- `sudo status certbot.timer` - checks the status of the timer
- `echo | openssl s_client -connect app1.com:443 -servername app1.com 2>/dev/null | openssl x509 -noout -dates -issuer -subject` - checks expiry/chain from outside perspective (sees what the client sees when they make an https request) 

## Common Certbot Issues
- Port 80 blocked (firewall/security)
- Domain no longer points the the current VPS
- Behind other proxy (like cloudfare), with strict rules
-  