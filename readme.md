# socket-server

### Dependencies
```csv
laravel/laravel
beyondcode/laravel-websockets
```


### Usage
supervisor process
```bash
php artisan websocket:serve
```

cron process, run every minute
```bash
php artisan schedule:run
```

### Improve server performance
Remove the file descriptor limit
```bash
$ cat /etc/security/limits.d/laravel-echo.conf
laravel-echo		soft		nofile		10000
```

Change the event loop to increase maximum simultaneous connected user
```bash
sudo pecl install event
```

### Server configuration

```text
server {
  listen        443 ssl;
  listen        [::]:443 ssl;
  server_name   socket.yourapp.tld;

  # Start the SSL configurations
  ssl                  on;
  ssl_certificate      /etc/letsencrypt/socket.yourapp.tld/fullchain.pem;
  ssl_certificate_key  /etc/letsencrypt/socket.yourapp.tld/privkey.pem;

  location / {
    proxy_pass             http://127.0.0.1:6001;
    proxy_read_timeout     60;
    proxy_connect_timeout  60;
    proxy_redirect         off;

    # Allow the use of websockets
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
  }
}
```