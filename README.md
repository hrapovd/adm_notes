# adm_notes
## Заметки по администрированию, для себя.
1. [nginx](#nginx)
1. [squid](#squid)
1. [postgresql](#postgresql)
1. [python](#python)
1. [dhcpd](#dhcpd)
1. [k8s](#k8s)

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
acl sites dstdom_regex .*
http_access allow net sites
```
[squid.conf](./squid.conf)

## postgresql

### Прибить конкретный запрос:
```sh
Найти pid:
   select * from pg_stat_activity;
Убить запрос:
   select pg_terminate_backend(PID);

   select pg_terminate_backend(pid)
     from pg_stat_activity
	  where datname = 'blog';
```

## python

### Запустить ssh в фоне из Python и получить pid, а после убить
```sh
import subprocess
process = subprocess.Popen(['/usr/bin/ssh', '-N', '-R 127.0.0.1:3131:127.0.0.1:3131', 'node'])
process.pid
process.kill()
del process
```
### Запустить мини web сервер python на определенном порту
```sh
Python2:
python -m SimpleHTTPServer 8000
Python3:
python -m http.server 8000
```
при запуске будет отдаваться содержимое каталога из которого запущена данная команда, если
в каталоге есть index.html, то будет отдаваться он как страница по умолчанию.

### Парсинг даты из названий файлов/каталогов различных видов с определение расстояния
### от текущей даты 
[parse-date-filename.txt](./parse-date-filename.txt)

## dhcpd
```sh
### Настройка статических маршрутов через dhcp
# Эти две строчки отвечают за умение выдавать маршрут по DHCP
# ms-classless-static-routes для MS Windows
# rfc3442-classless-static-routes для тех кто работает в соответствии с RFC
option ms-classless-static-routes code 249 = array of unsigned integer 8;
option rfc3442-classless-static-routes code 121 = array of unsigned integer 8;
subnet 10.1.1.0 netmask 255.255.255.0 {
# range 10.1.1.220 10.1.1.230; # раньше было так сейчас у каждого жосткая привязка IP+MAC
# вот стататические маршруты.
# в данном случае маршрут на сеть 192.168.10.0/24 через 10.1.1.1
# в начале указываем маску сети, потом саму сеть потом gw, всё через запятые!
option ms-classless-static-routes 24, 192,168,10, 10, 1, 1, 1;
option rfc3442-classless-static-routes 24, 192,168,10, 10, 1, 1, 1;
}
```

## java
### Сбор стеков и дампов с тайм-аутом
```sh
jmap -J-Dsun.tools.attach.attachTimeout=120000 -dump:format=b,file=<file> <pid>
jstack -J-Dsun.tools.attach.attachTimeout=120000 -l <pid>
```

## k8s

### Получение данных от k8s кластера с помощью curl
curl --cacert ./ca.crt -H "Authorization: Bearer $TOKEN" "https://metrics-server/apis/metrics.k8s.io/v1beta1/pods/"
TOKEN = содержит токен из сервис аккаунта, от которого идет подключение, найти можно в secrets
