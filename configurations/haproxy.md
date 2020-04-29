# konfigurasi http https haproxy (dengan algoritma roundrobin)

### __1. install packet__
```
# apt-get install haproxy
```

### __2. konfigurasi main file haproxy__
```
# nano /etc/haproxy/haproxy.cfg
```
tambahkan konfigurasi ini dibagian paling bawah
```	
# frontend dari http
frontend ft_http
    mode http
    bind :80
    default_backend bk_http

# frontend dari https
frontend ft_https
    mode tcp
    bind :443
    default_backend bk_https

# backend dari http
backend bk_http
    mode http
    balance roundrobin
    option httpclose
    server srv1 10.1.1.10:80 check
    server srv2 10.1.1.20:80 check


    # tambahkan konfig dibawah jika ingin memberikan http request (OPTIONAL)
        # http-request set-method <fmt> [<condition>]
        # http-request add-header <name> <fmt> [<condition>]
        # http-request set-header <name> <fmt> [<condition>]
        # contoh
            # http-request set-header X-Served-By %[src]

# backend dari htpps
backend bk_htpps
    mode tcp
    balance roundrobin
    option httpclose
    server srv1 10.1.1.10:443 check
    server srv2 10.1.1.20:443 check


    # tambahkan konfig dibawah jika ingin memberikan http request (OPTIONAL)
        # http-request set-method <fmt> [<condition>]
        # http-request add-header <name> <fmt> [<condition>]
        # http-request set-header <name> <fmt> [<condition>]
        # contoh
            # http-request set-header X-Served-By %[src]
```

### __3. restart haproxy__
```
# service haproxy restart
```
---

# konfigurasi haproxy yang nanti akan diredirect langsung dari server HA

### __1. instal packet__
```
# apt-get install haproxy
```

### __2. konfigurasi main file haproxy__
```
# nano /etc/haproxy/haproxy.cfg
```

tambahkan konfigurasi ini dibagian paling bawah

```	
frontend ft_www
    mode http
    bind *:80
    bind *:443 ssl crt /etc/ca/itnsa.pem
    redirect scheme https if !{ ssl_fc }
    default_backend bk_www

backend bk_www
    mode http
    balance roundrobin
    option forwardfor
    server srv1 10.1.1.10:443 ssl verify none
    server srv2 10.1.1.20:443 ssl verify none
    
    # tambahkan konfig dibawah jika ingin memberikan http request (OPTIONAL)
        # http-request set-method <fmt> [<condition>]
        # http-request add-header <name> <fmt> [<condition>]
        # http-request set-header <name> <fmt> [<condition>]
        # contoh
            # http-request set-header X-Served-By %[src]
```
3. restart haproxy
```
# service haproxy restart
```
