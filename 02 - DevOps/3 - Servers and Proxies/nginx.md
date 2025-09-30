- **Installing Nginx**:  
  - Update the package list:  
    ```bash
    sudo apt update
    ```
    
  - Install Nginx:  
    ```bash
    sudo apt install nginx -y
    ```
    This command installs the Nginx web server. The `-y` flag automatically answers "yes" to any prompts during the installation, making the process non-interactive.

  - Verify the installation:  
    ```bash
    nginx -v
    ```
    
  - Start and enable Nginx to run on boot:  
    ```bash
    sudo systemctl start nginx
    sudo systemctl enable nginx 
    ```
    The START command tells linux to launch the nginx web server immediately.
    The ENABLE command tells linux to launch the nginx web server on system boot.

  - Check the status of Nginx:  
    ```bash
    sudo systemctl status nginx
    ```
    The status command will tell you:  
      - Whether the service is active or inactive.  
      - The process ID (PID) of the running Nginx master process.  
      - Any recent log messages related to the service.  
      - The uptime of the service since it was last started.  

  
- **Nginx Configuration**:  
  - Nginx uses `server` blocks (also known as virtual hosts) to route incoming HTTP requests to different ports or projects.  
  - Each `server` block specifies a `server_name` (domain or subdomain) and a `location` directive to define how requests are handled.  
  - Example:  
    ```nginx
    server {
        listen 80;
        server_name example.com;

        location / {
            proxy_pass http://localhost:3000;
        }
    }
    ```
  - This configuration routes requests for `example.com` to a service running on port 3000.