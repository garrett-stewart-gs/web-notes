## Nginx Installation  
- Update the package list: `sudo apt update`
- Install Nginx: `sudo apt install nginx -y`
  Note: `-y` flag automatically answers "yes" to any prompts during the installation, making the process non-interactive.
- Verify the installation: `nginx -v`
- Configure Linux to run nginx on boot: `sudo systemctl enable nginx`
  - (Optional) Start nginx: `sudo systemctl start nginx`
  - (Optional) Check nginx status: `sudo systemctl status nginx` to display:
      - Whether the service is active or inactive.  
      - The process ID (PID) of the running Nginx master process.  
      - Any recent log messages related to the service.  
      - The uptime of the service since it was last started.  
