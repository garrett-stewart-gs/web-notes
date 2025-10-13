## Prerequisites
- Service Layout: Client/User > Cloudfare > UFW/Fail2ban > Nginx/Certbot > Webapp(s)
  - Cloudfare (DDOS protection, proxy-orange-cloud, caching, cdn, SSL Cert)
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
- git (for pulling )
- doppler (secrets manager, keeps secrets off vps)
- docker 

## Required Repos
- VPS App Management Scripts Repo - contains system scripts for managing services, like deploy-app and update-cloudfare-ips. These scripts handle anything sensitive, like root user files/configs or secrets. For example, the deploy-app script will be initiated by github actions, but the script's execution is handled by the root user.
- App Repo(s) - infra repo(s) for each app that will run on the server

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
- ### Rename Your VPS (root@vpsName)
  - if you rename your vps, you should do it early to avoid any issues
  - change the name of your vps using: `sudo hostnamectl set-hostname staging-vps`
  - link the new vps name to the machine using: `echo "127.0.1.1 staging-vps" | sudo tee -a /etc/hosts >/dev/null`
    - Note: This allows the vps to resolve `vpsName` locally (it just helps everything understand what the new vps name is)
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
    - **github-actions** - Standard user with sudo access to anything required for github actions to update the local repo, install and run updated images, etc. 
    - **network-ops** (optional) - Standard user with sudo access on anything related to Nginx (or other reverse proxy), SSH cert, Firewall, Fail2ban, SSHD / SSH config, or otherwise network related.
    - **developer-ops** (optional) - Standard user with sudo access on anything related to git, docker, doppler, db backups etc.
    - NOTE: You want to use 
  - Add the desired usergroups, using: 
    - admin: `sudo groupadd admin` (most-likely already installed, no harm regardless)
    - github-actions: `sudo groupadd github-actions`
    - network-ops: `sudo groupadd network-ops`
    - developter-ops: `sudo groupadd network-ops`
- ### Define Linus Usergroup Permissions With Sudoers.d/ Direcotry Files
  - For each usergroup (except admin), create a new sudo config file and edit it with the permissions below, using: `sudo visudo -f /etc/sudoers.d/<usergroup>`
  - Note: `visudo` is a special code editor specifically for writing sudo permissions. **It automatically validates the syntax and the policy becomes active when you exit and save.**
  - admin permissions:
    - Note: admin should already have the correct permissions
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
  - **github-actions permissions:**
    ```
    # Safe PATH for root via sudo (avoid weird PATH picks)
    Defaults:github-actions secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"

    # this relies on you installing scripts for "github" user (recommended)
    Cmnd_Alias GH_ACTION_SCRIPTS = /usr/local/bin/vps-scripts/github-helpers/*
    github ALL=(root) NOPASSWD: GH_ACTION_SCRIPTS
      
    ```
  - NOTE: You may want to test each user's permissions with various commands (not discussed here)
  - NOTE: You may want to add multiple usergroups to a user to grant more full control. In this case, with both network-ops + developer-ops the user would be able do almost everything an admin can do, except adjust the firewall
- ### Add Linux Users and Assign Usergroups
  - Add your desired users with: `sudo adduser <username>` 
    - You will be asked for details like:
      - Password (recommended, except for "github" user. **Make sure github can connect without password!**)
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
- ### Install Git
  - Ensure your OS is updated with: `sudo apt update` and `sudo apt upgrade`
  - Install git with: `sudo apt install -y git`
  - Verify git was installed with: `git --version`
- ### Configure Git
  - Check git configuration with: `git config --list`
    - It will probably be empty = no diplay output
    - Enter these 3 commands to change the config:
      - `git config --global pull.rebase false` (makes pull do a proper merge, not rebase)
      - `git config --global color.ui auto` (makes all git console logs color-coded, enhances readability)
      - `git config --global core.editor "nano"` (makes nano the default text editor for git messages or merge conflicts)
- ### Generate Admin and "github" SSH Key Pairs(for pulling repos)
  - With linux, each user has their own `~/.ssh/*` directory, so they each have their own set of ssh key pairs!
  - You will need to log into both your own admin account and the "github" user account and complete the following procedure: l
    - Generate an SSH keypair named github with: `ssh-keygen -t ed25519 -a 100 -f ~/.ssh/id_ed25519_github`
    - Read the public key with: `cat ~/.ssh/id_ed25519_github.pub`
    - This public key belongs to your admin user (you should already be logged in as them)
    - Copy the entire contents of the public key (we will add that to github shortly)
    - Switch to the "github" user using: `su - github` 
      - `-` ensures you are at the home directory for "github"
      - "github" user should be passwordless
    - generate an SSH keypare named github with: `ssh-keygen -t ed25519 -a 100 -f ~/.ssh/id_ed25519_github`
    - Read the public key with: `cat ~/.ssh/id_ed25519_github.pub`
    - This public key belongs to your "github" user
    - Copy the entire contents of the public key (we will add that to github shortly)
- ### Add Identity/Alias Configuration For Both Admin and "github" users
  - One requirement for git operations to connect, is to define what ssh key should be used for github. This is accomplished in the `~/.ssh/config` file
  - You will need to follow this procedure for both admin user and "github" user:
    - Create and open a config file using nano ~/.ssh/config
    - Paste the following into the file:
      ```
      Host github.com
        HostName github.com
        User git
        IdentityFile ~/.ssh/id_ed25519_github
        IdentitiesOnly yes
      ```
    - Note: Each user has their own ~/.ssh/ directory, their own ssh key pairs, and ssh config!!
- ### Link Admin Public Key and "github" Public Key To Github (for pulling repos)
  - Log into your github account and navigate to account settings > SSH and GPG keys
  - For both admin and "github" public keys, do the following: 
    - click "New SSH key"
    - name the key <vpsName>-<user>-Key in the Title input
      - ie. ovh-garrett-key
      - ie. ovh-github-key
    - paste the correct public key in the Key input
    - click "Add SSH key"
- ### Test The Connection To Github On Admin User and "github" user
  - First Test:
    - Verify Github SSH works with: `ssh -T git@github.com` as your admin user and "github" user individually
    - You may be asked if you want to continue connecting, because the key is not known by any other names. Just say yes.
    - If it works, you should see `Hi <user>! You've successfully authenticated, but GitHub does not provide shell access.`
  - Test your github connection by pulling any small repo (this is just a test)
    - clone the desired repo with: `git clone git@github.com:<owner>/<repo>.git`. You can a specific ssh address on your github/repo page as well.
    - check out the repo, make sure it was cloned properly
    - delete the repo with `rm -rf <repoName>`, because it was a throw away (also it was not installed in a proper directory)
- ### Setup Secrets For Github Actions To SSH Into Your VPS
  - Firstly, it is a good idea to have an organization setup on github to use secrets across repos. (its free)
    - Note: if you want all your repos' github actions to ssh into your vps, it will need the private key. Normally, the private key is saved in the repo settings secrets. With an organization, you can set a "global" secret for all your repos.  
    - In other words, the default would be 1 private key PER REPO vs. 1 private key for the organization and its repos
    - Note: the organization must OWN the repo for the repo github actions to be able to access organization secrets
  - **Generate an SSH key pair in rsa (base 64) format:**
    - **Note: github action servers use libcrypto to parse secrets, which can cause \n (new line) characters to screw up the key. Generating a base64 key keeps everything on one line and solves that issues**
    - On your local machine, enter command: ssh-keygen -t rsa -b 4096 -C "github_actions" -N "" -f github_actions
      - Note: The public and private keys get **stored in your user's home directory** (not embedded in `~/.ssh/*` like before)
    - Convert the private key to base64 with: `base64 -w0 github_actions > github_actions.b64` (creates 3rd file at home root)
    - Display both keys in your cli: 
      - `cat github_actions.b64`
      - `cat github_actions.pub`
      - copy both keys as necessary
  - **Add the public key to the "github" user's authorized_keys:**
    - Open the authorized keys file with: `nano ~/.ssh/authorized_keys` (it will create the file if it doesn't exist, when you save it)
    - Paste your entire public key on the bottom line of the file. Do not edit or delete any public keys listed in this file (each line = 1 public key)
    - Exit nano and save the file using `ctrl + x` and `y`
    - **Ensure that the `~/.ssh/` and the `~/.ssh/authorized_keys` have the correct rules/permissions:**
      - enter command: `chmod 600 ~/.ssh/authorized_keys`
      - enter command: `chmod 700 ~/.ssh`
      - enter command: `chmod 755 /home/github`
      - Note: your github action runner will most likely reject connection if these permissions/rules aren't set
  - **Add the private key to your github organizations secrets:**
    - **Firstly, you can add a doppler token if you are managing multiple secrets per project/environment**
      - Note: this is covered the doppler installation section 
    - in your browser, navigate to `https://github.com/organizations/<organizationName>/settings/secrets/actions`
    - click "New Organization Secret"
    - enter the name of the key and the private key
    - select the private repositories option 
    - click "Add secret"
    - Note: the github action will need access to the following secrets:
      - SSH Private Key 
      - VPS Username (`github`)
      - VPS Host (vps public ip address)
      - VPS SSH Port
  - **(optional) Test github actions to confirm it can connect to your vps**
    - Note: this procedure assumes you know how to write github action yml code 
    - **Note: You can wait until after you install your helper scripts to test github actions as well**
    - On any simple repo, create a `repo/.github/workflows/smoke.yml` file
      - the test should retrieve vps secrets (github organization secrets OR doppler)
      - then use the secrets to ssh into the desired vps
      - then make a folder called "github actions was here" or something obvious like that
    - Trigger the github action ( workflow dispatch ):
      - Go to Your Org → Your Repo → Actions tab.
      - In the left sidebar, click your workflow (e.g., “Doppler-env + SSH Test”).
      - Click the green Run workflow button (top-right).
      - Pick the branch (usually main) and hit Run workflow.
    - Once the test is complete, log into your "github" user on the vps and check if there is a "github_action_was_here" directory
- ### Install App Managment Scripts (admin infra repo)
  - It is a good idea to have a github repo with a collection of utility scripts for managing repeated operations, like deploying apps, configuring Nginx, installing doppler access tokens, etc.
  - You can tie certain folders to certain user groups, granting them access via `sudo`
    - sudoers.d 
  - On your vps with your admin user: 
    - `git clone git@github.com:<githubUser>/<repoName>.git`
    - `cd repoName`
    - `chmod +x install-scripts` (this command makes install-scripts script executable)
    - `sudo ./install-scripts ` (this script installs its child scripts at `/usr/local/bin/vps-scripts/`)
    - `chmod +x list-installed-scripts` (this command makes the list-installed scripts executable)
    - `sudo ./list-installed-scripts` (this command lists all scripts by section that are currently installed in `/usr/local/bin/vps-scripts/*`)
  - Test if you can run any installed scripts: `/usr/local/bin/vps-scripts/github-helpers/smoke-test`
    - Note: it is a good idea to define what users can do strictly though scripts organized by user-group 
    - Note: only admin users and github_actions users can run scripts inside the github-helpers directory (if your permissions are correct)
- ### Ensure Root's `srv/` Directory Is Readable By All Users, But Its Contents Are Locked
  - As an admin user, run: 
    - `sudo chmod 755 /srv`
    - `sudo chown root:root /srv`
    - `sudo chown -R root:root /srv/*`
    - Note: this ensures all users can see the apps inside root's srv with: `ls /srv`, but they cannot directly edit it
- ### Install Doppler
  - on your admin user, install docker using this command:
    - `(curl -Ls --tlsv1.2 --proto "=https" --retry 3 https://cli.doppler.com/install.sh || wget -t 3 -qO- https://cli.doppler.com/install.sh) | sudo sh`
  - That is all you need to do on the vps for installation! From here, you just need to manage Doppler Access Tokens on the vps. The best way to do that is with the helper scripts inside your "vps-scripts" repo
- ### Store Doppler Access Token(s) On VPS
  - Note: Doppler Access Tokens for the vps environment should be stored in the root's `/etc/doppler/tokens/<environment>/<appname>.token` or somewhere similar (must be organized)
  - Use a helper script from vps-scripts to install the doppler token safely: `/usr/local/bin/vps-scripts/admin-helpers/install-doppler-token <environment> <appName>`
    - Note: this helper avoids broadcasting the token value = safer
    - Using a script like this ensures the tokens will always be organized consistently and quickly
    - It will overwrite existing tokens
  - You also have the following scripts available to you in vps-scripts:
    - list-doppler-tokens
    - install-doppler-token
    - delete-doppler-token
- ### Install Docker
  - install docker using command: `curl -fsSL https://get.docker.com | sudo bash`
  - generate a PAT token in github: `https://github.com/settings/tokens` (it only needs read access to `packages`)
  - In your vps cli, as an admin, run this command:
    ```
    sudo -i (switches to root user)
    read -rsp "GHCR PAT: " PAT; echo (creates a prompt for you to safely paste your PAT token into)
    printf '%s' "$PAT" | docker login ghcr.io -u garrett-stewart-gs --password-stdin (logs into github with PAT token)
    ```
    - Note: this will log docker into your github account with read-only privileges on packages (container images)
  - Use the vps-scripts to manage the dockerized containers/repos:
    - start-app <appname> <environment>
    - stop-app <appname> <environment>
    - install-app-repo <appname> <repoaddress>
    - update-app-repo <appname>
    - delete-app-repo <appname>
- ### Install SSL Certificate
  - Notes on SSL:
    - You must have your dns service pointed at your vps in order to perform the test that grants an ssl certificate!!
    - SSL certificates should be stored at `/etc/ssl/cloudfare`
    - An SSL Certificate can cover many domains, but they need to be listed when you "apply" for the cert.
    - It is best to cover 1 domain + its subdomains in 1 cert. Don't have too many domains on 1 cert, because it can be harder to renew and it isn't good if renewal fails and your site is no longer secure!
    - You can generate the necessary SSL keys/certs on cloudfare, and they are valid for 15yrs by default (if you leave cloudfare = get new SSL)
  - create the root's directory that the certs will be stored in: `sudo install -d -m 0755 -o root -g root /etc/ssl/cloudflare`
  - create and edit the cert file: `sudo nano /etc/ssl/cloudflare/<domain>.com.origin.crt`
    Note: this is a public key that cloudfare trusts (nobody else does)
  - create and edit the private key file: `sudo nano /etc/ssl/cloudflare/domain.com.origin.key`
    Note: this is a private key that you can use to verify cloudfare
- ### Install Nginx
  - install nginx with: `sudo apt install nginx -y`
    - Note: this installs the nginx binary + default configs (/etc/nginx/sites-available/ + /etc/nginx/sites-enabled/)
    - also systemd nginx.service (daemon)
  - verify nginx is installed with `nginx -v`
  - check if nginx is running with: `sudo systemctl status nginx`
  - start nginx with: `sudo systemctl start nginx`
  - enable nginx (starts on boot) with: `sudo systemctl enable nginx`
- ### Configure Nginx as Reverse Proxy
  - Nginx should be structured/configured something like this:
      /etc/nginx/
      ├─ nginx.conf # for global settings
      ├─ sites-available/
      │  ├─ app1.conf # for app specific settings
      │  ├─ app2.conf # for app specific settings
      │  └─ app3.conf # for app specific settings
      └─ sites-enabled/ # symlinks to enabled vhosts
  - Configure Nginx' Global Config:
    - create a full draft of you global config file
    - open the global nginx config file with: `sudo nano /etc/nginx/nginx.conf`
    - clear the entire file and paste your draft inside
    - exit and save
  - Configure Nginx's Per-App Config
    - Per-app configs are located at `/etc/nginx/sites-available/*`
    - create a full draft of your per-app config
    - Unlink the default file: `sudo unlink /etc/nginx/sites-enabled/default`
    - Create a new per-app config file: `sudo nano /etc/nginx/sites-available/<appName>.conf`
    - Paste your draft inside the empty conf file
    - exit and save
    - Link the new app config using `sudo ln -s /etc/nginx/sites-available/<appname>.conf /etc/nginx/sites-enabled/`
      Optional: determine which resolver dns you should put into the http block for ssl stapling with: `resolvectl dns` or `grep ^nameserver /etc/resolv.conf`
  - Verify the nginx syntax using: `sudo nginx -t` (this will fail if certbot is not installed and/or if the ssl file is missing!)
  - restart the nginx service to set the changes:
    - `sudo systemctl daemon-reload`
    - `sudo systemctl restart nginx`
  - You can check nginx' status with `sudo systemctl status nginx`
  - ### Configure Nginx To Accept Traffic Through Cloudfare Exclusively
    - To do this, we need to use the cloudfare api to obtain a current list of cloudfare IPs
    - Then we need to make an nginx config file/edit that tells nginx to only accept connections from the specified IP addresses
    - ***instructions go here***