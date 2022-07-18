# knoking-port

## Задание

1. реализовать knocking port
centralRouter может попасть на ssh inetrRouter через knock скрипт пример в материалах.
2. добавить inetRouter2, который виден(маршрутизируется (host-only тип сети для виртуалки)) с хоста или форвардится порт через локалхост.
запустить nginx на centralServer.
пробросить 80й порт на inetRouter2 8080.
дефолт в инет оставить через inetRouter. Формат сдачи ДЗ - vagrant + ansible
реализовать проход на 80й порт без маскарадинга

## Решение 

Стенд с решением разворачивается командой ```vagrant up``` . 

Проверка knocking port

```
[vagrant@centralRouter ~]$ bash knocknkock.sh 192.168.50.10 8881 7777 9991

Starting Nmap 6.40 ( http://nmap.org ) at 2022-07-18 15:49 UTC
Warning: 192.168.50.10 giving up on port because retransmission cap hit (0).
Nmap scan report for 192.168.50.10
Host is up (0.00079s latency).
PORT     STATE    SERVICE
8881/tcp filtered unknown
MAC Address: 08:00:27:36:60:32 (Cadmus Computer Systems)

Nmap done: 1 IP address (1 host up) scanned in 0.56 seconds

Starting Nmap 6.40 ( http://nmap.org ) at 2022-07-18 15:49 UTC
Warning: 192.168.50.10 giving up on port because retransmission cap hit (0).
Nmap scan report for 192.168.50.10
Host is up (0.00049s latency).
PORT     STATE    SERVICE
7777/tcp filtered cbt
MAC Address: 08:00:27:36:60:32 (Cadmus Computer Systems)

Nmap done: 1 IP address (1 host up) scanned in 0.48 seconds

Starting Nmap 6.40 ( http://nmap.org ) at 2022-07-18 15:49 UTC
Warning: 192.168.50.10 giving up on port because retransmission cap hit (0).
Nmap scan report for 192.168.50.10
Host is up (0.00097s latency).
PORT     STATE    SERVICE
9991/tcp filtered issa
MAC Address: 08:00:27:36:60:32 (Cadmus Computer Systems)

Nmap done: 1 IP address (1 host up) scanned in 0.47 seconds
[vagrant@centralRouter ~]$ ssh vagrant@192.168.50.10
The authenticity of host '192.168.50.10 (192.168.50.10)' can't be established.
ECDSA key fingerprint is SHA256:we2HVcIh4xVsZMwL49139VV6Yy47iQajcGlcs2uNeig.
ECDSA key fingerprint is MD5:0e:0e:c0:a3:75:c0:d5:85:12:31:36:43:47:17:5a:98.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.50.10' (ECDSA) to the list of known hosts.
Permission denied (publickey,gssapi-keyex,gssapi-with-mic).
[vagrant@centralRouter ~]$ ssh vagrant@192.168.50.10
```


Проверка nginx 

```
root@ubuntu:~/knocking-port# curl localhost:8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

[root@centralRouter ~]# curl 192.168.255.13:8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
....



[vagrant@centralServer ~]$ sudo tcpdump -i eth1
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth1, link-type EN10MB (Ethernet), capture size 262144 bytes
20:39:16.916937 IP 192.168.255.13.54390 > centralServer.http: Flags [S], seq 212480001, win 65535, options [mss 1460], length 0
20:39:16.917031 IP centralServer.http > 192.168.255.13.54390: Flags [S.], seq 1242289374, ack 212480002, win 29200, options [mss 1460], length 0
20:39:16.918421 IP 192.168.255.13.54390 > centralServer.http: Flags [.], ack 1, win 65535, length 0
20:39:16.918476 IP 192.168.255.13.54390 > centralServer.http: Flags [P.], seq 1:79, ack 1, win 65535, length 78: HTTP: GET / HTTP/1.1
20:39:16.918495 IP centralServer.http > 192.168.255.13.54390: Flags [.], ack 79, win 29200, length 0
20:39:16.921652 IP centralServer.http > 192.168.255.13.54390: Flags [P.], seq 1:239, ack 79, win 29200, length 238: HTTP: HTTP/1.1 200 OK
20:39:16.921974 IP centralServer.http > 192.168.255.13.54390: Flags [P.], seq 239:854, ack 79, win 29200, length 615: HTTP
20:39:16.923514 IP 192.168.255.13.54390 > centralServer.http: Flags [.], ack 239, win 65535, length 0
20:39:16.923537 IP 192.168.255.13.54390 > centralServer.http: Flags [.], ack 854, win 65535, length 0
20:39:16.923780 IP 192.168.255.13.54390 > centralServer.http: Flags [F.], seq 79, ack 854, win 65535, length 0
20:39:16.923886 IP centralServer.http > 192.168.255.13.54390: Flags [F.], seq 854, ack 80, win 29200, length 0
20:39:16.924831 IP 192.168.255.13.54390 > centralServer.http: Flags [.], ack 855, win 65535, length 0
```

