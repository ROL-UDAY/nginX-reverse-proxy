# NGINX Reverse Proxy Docker Configuration

This Docker-compose configuration sets up an NGINX reverse proxy with a custom configuration file.

## Installation

1. Ensure you have Docker installed on your system. If not, download and install it from [here](https://docs.docker.com/get-docker/).

2. Clone this repository to your local machine:
   ```bash
   git clone https://github.com/yourusername/nginx_reverse_proxy.git
   ```

3. Navigate to the directory containing the Docker-compose file:
   ```bash
   cd nginx_reverse_proxy
   ```

4. Create a custom NGINX configuration file named `nginx.conf` and place it in the root directory of this project. You can use the provided configuration as a template and modify it according to your requirements.

5. Modify the `proxy_pass` directive in the NGINX configuration to point to your desired upstream server.

6. Update the Dockerfile to specify the path for the NGINX configuration file:
   ```Dockerfile
   FROM nginx:latest

   COPY nginx.conf /etc/nginx/nginx.conf
   ```

7. Once you have your custom configuration ready, start the NGINX reverse proxy using Docker-compose:
   ```bash
   docker-compose up -d
   ```

## Usage

- The NGINX reverse proxy is now running and listening on port `8000` of your host machine.
- Incoming requests to this port will be forwarded to the upstream server specified in the NGINX configuration.
- Access the NGINX reverse proxy by visiting `http://localhost:8000` in your web browser.

## Customization

- Customize the NGINX configuration according to your specific needs by editing the `nginx.conf` file.
- Reload the NGINX configuration after making any changes:
  ```bash
  docker-compose exec nginx nginx -s reload
  ```

## NGINX Configuration Explanation

The `nginx.conf` file contains the following configuration:

```nginx
events {
    worker_connections 1024;
}

http {
    server {
        listen 8000;
        server_name _;

        location / {
            proxy_pass http://192.168.50.15:8080/;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}
```

- `events`: This block defines parameters affecting connection processing. Here, `worker_connections` is set to `1024`, specifying the maximum simultaneous connections each worker process can handle.

- `http`: This block contains the HTTP server configuration.

  - `server`: Defines a virtual server block. In this configuration:
    - `listen 8000`: Specifies NGINX to listen for incoming HTTP connections on port `8000`.
    - `server_name _;`: Matches any server name (wildcard), handling requests for any domain name.
    - `location /`: Defines configuration for requests to the root URL (`/`). Inside this block:
      - `proxy_pass http://192.168.50.15:8080/;`: Sets the backend server to which NGINX should forward requests. Replace `http://192.168.50.15:8080/` with your upstream server's address.

## Troubleshooting

- If issues arise, check NGINX logs for error messages:
  ```bash
  docker-compose logs -f nginx
  ```
