## Docker Health Check
  - The health check runs repeatedly in the background, while the app is running.
  - The service will not wait for the health check by default. You can configure it to wait for a successful health check using the `depends_on` parameter 
  - The interval property defines how much time passes between health checks. A new health check does not run until the previous health check is finished

### Docker Health Check Parameters
  - test
  - interval
  - timeout
  - retries

### Test Parameters
  - CMD-Shell - a heathcheck.test mode that runs the test command through a shell **inside the container**. This **enables shell features** like `&&` and `||`, pipes, globbing, or evn-var expansion
  - pg_isready - a tiny cli app that ships with postgresql's client tools. It checks whether the psql server is accepting connections by establishing a connection briefly (it does not launch any queries though). When pg_isready returns 0, it is successful. If it returns any other integer value, there is a corresponding error.

### pg_isready Command
  - a tiny cli app that ships with postgresql's client tools.
  - It checks whether the psql server is accepting connections by establishing a connection briefly (it does not launch any queries though).
  - When pg_isready returns 0, it is successful. If it returns any other integer value, there is a corresponding error.
  - Common Flags:
    - `-h` - host. commonly omitted. If you are using this tag, you can use 127.0.0.1 to test firewall/port/dns. If the test is initiated from a different container, you can use `-h {serviceName}`
    - `-p` - port. usually 5432
    - `-d` - db. Use: `-d {dbName}`
    - `-U` - User. usually `-U postgres`, because postgres commonly uses self-named user
    - `-t` - timeout (sec).
    - `-q` - quiet. not recommended, because it suppresses descriptive messaging about the service's status. It still allows you to see the response code though.
    - example:
      ```bash
      pg_isready -h db -p {portNumber} -U {psqlUsername} -d {dbName} -t {timeoutDuration} -q
      ```
    - Note: Improper credentials 