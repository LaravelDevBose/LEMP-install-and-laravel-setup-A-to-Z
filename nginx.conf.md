```nginx
# Run Nginx as www-data user for security (least privilege principle)
user www-data;

# Automatically set number of worker processes to match CPU cores (2 for your 2 OCPU server)
worker_processes auto;

# Increase maximum number of open files per worker process to handle many concurrent connections
worker_rlimit_nofile 100000;

# Store process ID in this file for service management and monitoring
pid /run/nginx.pid;

events {
    # Maximum number of simultaneous connections each worker process can handle
    worker_connections 20480;
    
    # Allow a worker to accept all new connections at once instead of one at a time (better for high traffic)
    multi_accept on;
    
    # Use efficient epoll event method (optimal for Linux systems)
    use epoll;
    
    # Disable accept mutex to reduce latency in high concurrency scenarios
    accept_mutex off;
}

http {
    ##
    # Basic Settings
    ##
    
    # Use kernel-level file sending for better performance (reduces CPU usage)
    sendfile on;
    
    # Optimize network packets by waiting to send headers and data together
    tcp_nopush on;
    
    # Disable Nagle's algorithm to reduce latency for real-time applications
    tcp_nodelay on;
    
    # Timeout for keep-alive connections (reduces TCP handshake overhead)
    keepalive_timeout 30;
    
    # Maximum number of requests per keep-alive connection
    keepalive_requests 1000;
    
    # Maximum size of types hash tables (improves performance for many server names)
    types_hash_max_size 2048;
    
    # Hide Nginx version in headers for security (prevents targeted attacks)
    server_tokens off;
    
    # Maximum allowed size of client request body (for file uploads)
    client_max_body_size 100M;
    
    # Buffer optimizations for your RAM
    # Size of buffer for reading client request body
    client_body_buffer_size 16K;
    
    # Buffer size for reading client request headers
    client_header_buffer_size 1k;
    
    # Maximum number and size of buffers for large client request headers
    large_client_header_buffers 4 8k;
    
    # Number and size of buffers used for reading response from upstream servers
    output_buffers 2 32k;
    
    # Delay sending data until at least this amount is accumulated (packet optimization)
    postpone_output 1460;
    
    # Timeouts
    # Timeout for reading client request body
    client_body_timeout 12;
    
    # Timeout for reading client request headers
    client_header_timeout 12;
    
    # Timeout for transmitting response to client
    send_timeout 10;
    
    # Close connections that have timed out to free resources
    reset_timedout_connection on;

    ##
    # File Cache Optimizations
    ##
    
    # Cache metadata for up to 50,000 open files
    open_file_cache max=50000 inactive=30s;
    
    # How long to cache file metadata after last access
    open_file_cache_valid 45s;
    
    # Minimum number of file accesses before caching
    open_file_cache_min_uses 2;
    
    # Cache errors for files that couldn't be accessed
    open_file_cache_errors on;

    ##
    # SSL Settings (Critical for HTTPS performance and security)
    ##
    
    # Only allow modern, secure TLS protocols
    ssl_protocols TLSv1.2 TLSv1.3;
    
    # Prefer server's cipher suite order over client's
    ssl_prefer_server_ciphers on;
    
    # List of secure cipher suites in order of preference
    ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
    
    # Supported elliptic curves for ECDHE key exchange
    ssl_ecdh_curve X25519:prime256v1:secp384r1:secp521r1;
    
    # How long SSL sessions remain valid for resumption
    ssl_session_timeout 1d;
    
    # Shared cache for SSL sessions (50MB size) for session resumption
    ssl_session_cache shared:SSL:50m;
    
    # Disable session tickets (better security with session cache)
    ssl_session_tickets off;
    
    # Enable OCSP stapling to improve SSL handshake speed
    ssl_stapling on;
    
    # Verify OCSP responses for stapling
    ssl_stapling_verify on;
    
    # DNS resolvers for OCSP verification with 5-minute cache
    resolver 8.8.8.8 1.1.1.1 valid=300s;
    
    # Timeout for DNS resolution
    resolver_timeout 5s;

    ##
    # Gzip Compression (Reduces bandwidth usage and improves load times)
    ##
    
    # Enable gzip compression
    gzip on;
    
    # Add "Vary: Accept-Encoding" header for proxy caching
    gzip_vary on;
    
    # Enable compression for all proxied requests
    gzip_proxied any;
    
    # Compression level (1-9, 6 is good balance between CPU and compression)
    gzip_comp_level 6;
    
    # Minimum response length to compress (avoid compressing very small files)
    gzip_min_length 1024;
    
    # MIME types to compress (focus on text-based content)
    gzip_types
        text/plain
        text/css
        text/xml
        text/javascript
        application/json
        application/javascript
        application/xml+rss
        application/atom+xml
        image/svg+xml;

    ##
    # Logging (Essential for monitoring and debugging)
    ##
    
    # Custom log format with detailed timing information
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                   '$status $body_bytes_sent "$http_referer" '
                   '"$http_user_agent" "$http_x_forwarded_for" '
                   'rt=$request_time uct="$upstream_connect_time" uht="$upstream_header_time" urt="$upstream_response_time"';
    
    # Access log with buffering (64KB buffer, flush every minute) for better disk I/O performance
    access_log /var/log/nginx/access.log main buffer=64k flush=1m;
    
    # Error log with warning level to capture important messages without being too verbose
    error_log /var/log/nginx/error.log warn;

    ##
    # Virtual Host Configs (Modular configuration structure)
    ##
    
    # Include additional configuration files from conf.d directory
    include /etc/nginx/conf.d/*.conf;
    
    # Include enabled site configurations (symbolic links from sites-available)
    include /etc/nginx/sites-enabled/*;
}
```
