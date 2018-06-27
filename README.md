# adm_notes
## Заметки по администрированию, для себя.
1. [nginx](#nginx)

## nginx

### Разрешение web sockets
```sh
proxy_pass http://127.0.01:8080;
proxy_http_version 1.1;
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "Upgrade";
```
