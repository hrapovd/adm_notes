# adm_notes
## Заметки по администрированию, для себя.
1. [nginx](#nginx)
1. [squid](#squid)

## nginx

### Разрешение web sockets
```sh
proxy_pass http://127.0.01:8080;
proxy_http_version 1.1;
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "Upgrade";
```

## squid

### squid минимальный конфиг
```sh
http_port EXT_IP1:LISTEN_PORT
http_port EXT_IP2:LISTEN_PORT
visible_hostname web
logformat squid %tl %6tr %>a:%>p %<a:%<p %>st %<st %rm %ru %rv
acl net src EXT_NET1/CIDR_PREFIX
acl sites dstdom_regex *
http_access allow net sites
```
