# Linux Web Server Overview  

## Structure of a Linux Web Server  
- **Nginx**: Acts as the front desk, routing HTTP requests to the appropriate port or application.  
- **Docker and Multi-App Setup**:  
  - You can have 1 Nginx server running globally, configured to serve multiple dockerized webapps (generally preferred)
  - Or, you can have multiple Nginx servers running in their own dockerized containers alongside individual web apps

