## Prerequisites
- Service Layout: Client/User > Cloudfare > UFW/Fail2ban > Nginx/Certbot > Webapp(s)
  - Cloudfare protects http/https ports externally (off the VPS)
  - Fail2ban protects SSH port
  - UFW Protects every other port
  - Nginx listens to http/https ports and forwards traffic to the correct webapp
- Webapp Structure:
  - Git is used to pull/install infra repo
  - Doppler is used to start dockerized apps and inject secrets into each app's runtime environment
  - Infra repo utilizes docker-compose to run the correct docker images and orchestrate them together

## Linux Packages
- UFW for Ubuntu
- Fail2ban (supplemental security tool, prevents repeated attempts to access + DOS attacks)
- nginx (reverse proxy / server front-desk)
- certbot (SSL)
- git (for pulling )
- doppler (secrets manager, keeps secrets off vps)
- docker 

## First-time VPS Setup
- ### Generate an SSH Key and Connect to the VPS:
  - On you local machine, generate an ssh key-pair on your development machine (desktop or laptop): `ssh-keygen -t ed25519 -a 100 -f ~/.ssh/id_ed25519_<key_name>`
  - copy the entire contents of the `~/.ssh/id_ed25519_<key_name>` file
  - log into the dashboard for your vps and navigate to the page/tab that allows you to add SSH Public Keys
  - paste the contents into the ssh text field and give it a name
  - add an identity to your development machine's `~/.ssh/config` following this format:
    ```
      Host <alias/identity>
        HostName <IP address or Domain>            # replace with your VPS IP
        User <username>                            # usually "root" or ubuntu/rocky/etc.
        IdentityFile ~/.ssh/id_ed25519_<key_name>  # the file for the new key you generated
        Port <port number>                         # only use if host uses custom port (default is 22)
    ```
  - test connecting to vps using ssh command: `ssh <alias/identity>`
  - you will be asked for a password for the linux username you are connecting as (check your email for information to retrieve the password)
- ### Change the Root User Password
  - On your VPS, as the root user, run `passwd`
  - Enter the new password
  - Logout of VPS using `logout`
  - Reconnect to the VPS using `ssh <alias>` and using the new password to log in to the root user, when prompted
- ### Update the VPS
  - Run `sudo apt update` to retrieve updates
  - Run `sudo apt upgrade` to install all updates
- ### Change the Default SSH Port (Security)
  - Firstly, your VPS provider may have already changed the port. **If you received a port number in an email from your vps provider, and you used that port number in the `~/.ssh/config` file for your vps alias, then you do NOT need to change the port**
  - On the VPS, use the nano to open the ssh daemon config file: `sudo nano /etc/ssh/sshd_config`
  - Adjust the file so it has the following values:
    ```
      Port <portNumber> 
      PasswordAuthentication no
      PubkeyAuthentication yes
    ```
    - Note: many of these values will be commented out with '#', just delete the '#' and change the value as necessary
    - Note: **port number between 49152 and 65535**
  - Exit and Save the config file: `ctrl + x` to exit, then `y` to confirm changes
  - Use nano to open the ssh socket file `sudo nano /lib/systemd/system/ssh.socket`
  - Edit the `ListenStream` properties, underneath the `[Socket]` title:
    - ListenStream=0.0.0.0:<new-port-number>
    - ListenStream=[::]:<new-port-number> (this one may not be present)
  - Exit and Save the socket config file: `ctrl + x` to exit, then `y` to confirm changes
  - Reboot the System with: `sudo reboot` (you will be disconnected, and return to your local machine)
  - On your local machine, **update your `~/.ssh/config` with a port number (see above for format)**
  - Test reconnecting to the VPS with `ssh <alias>` and entering the root user password
- ### Setup UFW Firewall (Ubuntu)
  - Note: UFW is native to Ubuntu, and it blocks all traffic, except for the ports you enable
  - Install UFW with: `sudo apt install ufw -y` (it may already be installed, which is fine)
  - Check UFW status: `sudo ufw status` (likely: `Status: inactive`)
  - **Important:** Allow SSH port first (critical):
    - If using default ssh port 22, enable with: `sudo ufw allow OpenSSH`
    - If using custom ssh port (recommended), enable with: `sudo ufw allow <port>/tcp`
  - Allow Public Facing Ports:
    - Port 80 (HTTP traffic): `sudo ufw allow 80/tcp`
    - Port 443 (HTTPS traffic): `sudo ufw allow 443/tcp`
    - (Optional and Not Recommended) Enable Custom App Ports. Note: Nginx reverse proxy receives traffic on ports 80 and 443, then routes traffic internally. So, if you are using this configuration, there is no need to enable any additional ports
  - Enable UFW (activate firewall): `sudo ufw enable`, then answer `y` to confirmation prompt. (it may interrupt your ssh connection, so it is asking if you want to proceed)
  - Verify the UFW rules with: `sudo ufw status verbose`:
    - It should display To (port) | Action | From for each of the ports you allowed
    - Check that the ssh port, port 80, and port 443 are enabled (for v6 and unspecified version/v4)
    - **In other words, each port should have 2 entries stating "Allow In" and "Anywhere"**
  - (optional) Ensure UFW is denying incoming and allowing outgoing on every other port:
    - `sudo ufw default deny incoming`
    - `sudo ufw default allow outgoing`
    - Note: UFW does this by default, but if you want to explicitly set this rule, it isn't a bad idea
- ### Setup Fail2ban to Supplement Security
  - Note: Fail2ban protects the ssh port only with this config, it will need to be changed to protect ports 80/443 if you are NOT USING CLOUDFARE
  - Install fail2ban with: `sudo apt install fail2ban -y`
  - Enable fail2ban with: `sudo systemctl enable fail2ban`
  - Start fail2ban with: `sudo systemctl start fail2ban`
  - Check the status of fail2ban with: `sudo systemctl status fail2ban`, you should see `active (running)` somewhere in the readout
  - Create a new jail.local file with: `sudo touch /etc/fail2ban/jail.local`
    - Note: there is a `/etc/fail2ban/jail.conf` file, but it is best not to change it, and instead work from a fresh jail file
  - Use Nano to open the new file and begin editing with: `sudo nano /etc/fail2ban/jail.local`
  - Ensure the following items are present/uncommented:
    ```conf
      [DEFAULT]

      backend = systemd
      logencoding = auto

      bantime = 5m
      bantime.increment = true
      bantime.factor = 5
      bantime.maxtime = 24h
      banaction = ufw

      findtime = 10m
      maxretry = 5
      ignoreip = 127.0.0.1/8 ::1


      [sshd]

      enabled = true
      port = 56789
    ```
    - Note: this tells fail2ban to watch the SSH logs. When brute force is detected, it will ban the IP by inserting rules into UFW
    - Note: do not have duplicate headers (`[header]`), or fail2ban will crash
  - Exit and Save the jail.local file with: `ctrl + x`, then `y`
  - Restart Fail2Ban with: `sudo systemctl restart fail2ban`
  - Check the Jail Status with: `sudo fail2ban-client status`
- ### Create Linux Usergroups
  - Decide how you are structuring  permissions. Here is a good default for small organizations:
    - **Root/Ubuntu** - Master User, not to be used after setup is complete. Think of it as the OS system itself
    - **admin** - Standard user with sudo access on anything and everything. This is the top level for human users
    - **network-ops** - Standard user with sudo access on anything related to Nginx (or other reverse proxy), SSH cert, Firewall, Fail2ban, SSHD / SSH config, or otherwise network related.
    - **developer-ops** - Standard user with sudo access on anything related to git, docker, doppler, db backups etc.
  - Add the desired usergroups, using: 
    - admin: `sudo groupadd admin` (most-likely already installed, no harm regardless)
    - network-ops: `sudo groupadd network-ops`
    - developter-ops: `sudo groupadd network-ops`
- ### Define Linus Usergroup Permissions With Sudoers.d/ Direcotry Files
  - For each usergroup (except admin), create a new sudo config file and edit it with the permissions below, using: `sudo visudo -f /etc/sudoers.d/<usergroup>`
  - Note: `visudo` is a special code editor specifically for writing sudo permissions. **It automatically validates the syntax and the policy becomes active when you exit and save.**
  - admin permissions:
    - Note: admin should already have the correct permissions
    - `Confirm admin has the correct permissions by....`
  - **network-ops permissions:**
    ```
      # Members of netops can run a few system commands as root
      %netops ALL=(root) PASSWD: \
        /usr/sbin/service nginx *, \
        /usr/bin/systemctl reload nginx, \
        /usr/bin/systemctl restart nginx, \
        /usr/bin/certbot *, \
        /usr/bin/systemctl status ufw, \
        /usr/bin/systemctl is-enabled ufw, \
        /usr/bin/systemctl start ufw, \
        /usr/bin/systemctl restart ufw, \
        /usr/sbin/service fail2ban *, \
        /usr/sbin/systemctl reload sshd

        # Need to add cloudfare script controls here (example, not confirmed, below)
        /usr/sbin/systemctl enable cloudflare-ip-update.service, \
        /usr/sbin/systemctl disable cloudflare-ip-update.service, \
        /usr/sbin/systemctl start cloudflare-ip-update.service, \
        /usr/sbin/systemctl stop cloudflare-ip-update.service, \
        /usr/local/bin/update-cloudflare-ips.sh
    ```
  - **developer-ops permissions:**
    ```
    # Members of developer-ops can manage app-level operations via sudo
    %developer-ops ALL=(root) PASSWD: \
      # Edit only .env files (app-specific runtime config; no secrets)
      sudoedit /srv/*/app/env.prod, \
      sudoedit /srv/*/app/env.dev, \

      # Git (repo control limited to /srv/*)
      /usr/bin/git -C /srv/* pull, \
      /usr/bin/git -C /srv/* fetch --all, \
      /usr/bin/git -C /srv/* checkout *, \
      /usr/bin/git -C /srv/* reset --hard *, \

      # Docker Compose (app stacks only)
      /usr/bin/docker compose -f /srv/*/docker-compose.prod.yml *, \
      /usr/bin/docker compose -f /srv/*/docker-compose.dev.yml *, \

      # (Optional) basic docker observability
      /usr/bin/docker ps, \
      /usr/bin/docker logs *, \

      # Doppler CLI (runtime env injection and secret handling)
      /usr/bin/doppler *, \

      # Run (not edit) repo-managed scripts & migrations
      /srv/*/scripts/*, \
      /srv/*/db/migrations/*, \

      # Backups: prune + copy off the VPS (source fixed; destination unrestricted)
      /usr/bin/find /srv/*/db/backups -type f -delete, \
      /bin/rm -rf /srv/*/db/backups/*, \
      /usr/bin/rsync -a /srv/*/db/backups/ *, \
      /bin/cp -a /srv/*/db/backups/* *
    ```
  - NOTE: You may want to test each user's permissions with various commands (not discussed here)
  - NOTE: You may want to add multiple usergroups to a user to grant more full control. In this case, with both network-ops + developer-ops the user would be able do almost everything an admin can do, except adjust the firewall
- ### Add Linux Users and Assign Usergroups
  - Add your desired users with: `sudo adduser <username>` 
    - You will be asked for details like:
      - Password (mandatory, can be a temporary password)
      - Full Name (recommended)
      - Room Number
      - Work Phone
      - Home Phone
      - Other
  - Add specific users to specific usergroups as desired: `sudo usermod -aG <usergroup>,sudo <username>`
    - Note: users obtain permissions from their usergroups, so this defines what a user can and cannot do.
    - Note: users can have multiple usergroups. In this case having network-ops + developer-ops would grant similar permissions compared to admin usergroup. the main difference would be the dual-ops user does not have permissions for changing firewall settings and would have less access to files/editing/etc. overall.
- ### Test Users' Permissions (optional)
  - **Admin User:**
    - Switch to admin user and confirm they have the correct group assignment:
      - Switch users with: `su - <username>` (su = substitute/switch user)
      - Your CLI should say `<username>@vps:~$`
      - Check which groups your new user is associated with: `groups`, it should include `sudo` and `admin`
      - Lastly, run `sudo whoami` and receive a response stating `root`
  - **Network-Ops User:**
    - TBD
  - **Developer-Ops User:**
    - TBD


    







## GIT INSTALLATION
### Git Precautions on VPS:
- How to limit vps to pull-only operations and disable repo editing???
- Make sure the git repo is inside a directory owned by the root user, so deploy users cannot change anything inside it
- Do not add global github identity (git `user.email` and `user.name`)


## NGINX & Cloudfare Configuration (or was it UFW? I think it is nginx, cause all traffic goes through 2 http/https ports)
- nginx must be configured to only accept traffic from cloudfare's registered ip addresses
- There needs to be a linux script that keeps an updated list via cloudfare api, and ensures that only those ip addresses are allowed to connect