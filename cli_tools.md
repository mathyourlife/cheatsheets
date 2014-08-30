# CLI Tools

<!--- start_TOC -->

* [CLI Tools](#cli-tools)
	* [Socket Statistics (ss)](#socket-statistics-ss)
		* [Sources](#sources)
		* [Display Sockets Summary](#display-sockets-summary)
		* [Display All Open Network Ports](#display-all-open-network-ports)
		* [Process named using open socket:](#process-named-using-open-socket)
		* [Who is responsible for opening socket / port # 4949:](#who-is-responsible-for-opening-socket--port--)
		* [Display All TCP Sockets](#display-all-tcp-sockets)
		* [Display All UDP Sockets](#display-all-udp-sockets)
		* [Display All Established SMTP Connections](#display-all-established-smtp-connections)
		* [Display All Established HTTP Connections](#display-all-established-http-connections)
		* [Find All Local Processes Connected To X Server](#find-all-local-processes-connected-to-x-server)
		* [List All The Tcp Sockets in State FIN-WAIT-1](#list-all-the-tcp-sockets-in-state-fin-wait-)
		* [Filter Sockets Using TCP States?](#filter-sockets-using-tcp-states)
			* [closing sockets:](#closing-sockets)
		* [Match Remote Address And Port Numbers](#match-remote-address-and-port-numbers)
		* [Show all ports connected from remote 192.168.1.5](#show-all-ports-connected-from-remote-)
		* [Show all ports connected from remote 192.168.1.5:http port](#show-all-ports-connected-from-remote-http-port)
		* [Show all connection made by remote 123.1.2.100:http to our local virtual servers:](#show-all-connection-made-by-remote-http-to-our-local-virtual-servers)
		* [Match Local Address And Port Numbers](#match-local-address-and-port-numbers)
			* [Show all ports connected to local 75.126.153.214](#show-all-ports-connected-to-local-)
			* [http (80) port only](#http--port-only)
			* [smtp (25) port only](#smtp--port-only)
		* [Compare Local and/or Remote Port To A Number](#compare-local-andor-remote-port-to-a-number)
			* [Compares remote port to a number](#compares-remote-port-to-a-number)
			* [Compares local port to a number](#compares-local-port-to-a-number)
			* [Examples](#examples)

<!--- end_TOC -->

## Socket Statistics (ss)

### Sources

http://www.cyberciti.biz/tips/linux-investigate-sockets-network-connections.html

### Display Sockets Summary

```bash
ss -s
```

```
Total: 734 (kernel 904)
TCP:   1415 (estab 112, closed 1259, orphaned 11, synrecv 0, timewait 1258/0), ports 566
Transport Total     IP        IPv6
*         904       -         -
RAW       0         0         0
UDP       15        12        3
TCP       156       134       22
INET      171       146       25
FRAG      0         0         0
```

### Display All Open Network Ports


```bash
ss -l
```

```
Recv-Q Send-Q                                                  Local Address:Port                                                      Peer Address:Port
0      0                                                           127.0.0.1:smux                                                                 *:*
0      0                                                           127.0.0.1:10024                                                                *:*
0      0                                                           127.0.0.1:10025                                                                *:*
0      0                                                                   *:3306                                                                 *:*
0      0                                                                   *:http                                                                 *:*
0      0                                                                   *:4949                                                                 *:*
0      0                                                                   *:domain                                                               *:*
0      0                                                                   *:ssh                                                                  *:*
0      0                                                                   *:smtp                                                                 *:*
0      0                                                           127.0.0.1:rndc                                                                 *:*
0      0                                                           127.0.0.1:6010                                                                 *:*
0      0                                                               *:https                                                                *:*
0      0                                                                  :::34571                                                               :::*
0      0                                                                  :::34572                                                               :::*
0      0                                                                  :::34573                                                               :::*
0      0                                                                 ::1:rndc                                                                :::*
```

### Process named using open socket:

```bash
ss -pl
```

### Who is responsible for opening socket / port # 4949:

```bash
ss -lp | grep 4949
```

```
0      0                            *:4949                          *:*        users:(("munin-node",3772,5))
```

munin-node (PID # 3772) is responsible for opening port # 4949. You can get more information about this process (like memory used, users, current working directory and so on) visiting /proc/3772 directory:
```
cd /proc/3772
ls -l
```

### Display All TCP Sockets

```bash
ss -t -a
```

### Display All UDP Sockets

```bash
ss -u -a
```

### Display All Established SMTP Connections

```bash
ss -o state established '( dport = :smtp or sport = :smtp )'
```

### Display All Established HTTP Connections

```bash
ss -o state established '( dport = :http or sport = :http )'
```

### Find All Local Processes Connected To X Server

```bash
ss -x src /tmp/.X11-unix/*
```

### List All The Tcp Sockets in State FIN-WAIT-1

List all the TCP sockets in state -FIN-WAIT-1 for our httpd to network 202.54.1/24 and look at their timers:
```bash
ss -o state fin-wait-1 '( sport = :http or sport = :https )' dst 202.54.1/24
```

### Filter Sockets Using TCP States?


tcp ipv4
```bash
ss -4 state FILTER-NAME-HERE
```

tcp ipv6
```bash
ss -6 state FILTER-NAME-HERE
```

Where `FILTER-NAME-HERE` can be any one of the following,

* established
* syn-sent
* syn-recv
* fin-wait-1
* fin-wait-2
* time-wait
* closed
* close-wait
* last-ack
* listen
* closing
* all : All of the above states
* connected : All the states except for listen and closed
* synchronized : All the connected states except for syn-sent
* bucket : Show states, which are maintained as minisockets, i.e. time-wait and syn-recv.
* big : Opposite to bucket state.

#### closing sockets:

```bash
ss -4 state closing
```

```
Recv-Q Send-Q                                                  Local Address:Port                                                      Peer Address:Port
1      11094                                                  75.126.153.214:http                                                      175.44.24.85:4669
```

### Match Remote Address And Port Numbers

```bash
ss dst ADDRESS_PATTERN
```

### Show all ports connected from remote 192.168.1.5

```bash
ss dst 192.168.1.5
```

### Show all ports connected from remote 192.168.1.5:http port

```bash
ss dst 192.168.1.5:http
ss dst 192.168.1.5:smtp
ss dst 192.168.1.5:443
```

### Show all connection made by remote 123.1.2.100:http to our local virtual servers:

```bash
ss dst 123.1.2.100:http
```

```
State      Recv-Q Send-Q                                             Local Address:Port                                                 Peer Address:Port
ESTAB      0      0                                                 75.126.153.206:http                                               123.1.2.100:35710
ESTAB      0      0                                                 75.126.153.206:http                                               123.1.2.100:35758
```

### Match Local Address And Port Numbers

```
ss src ADDRESS_PATTERN
```

#### Show all ports connected to local 75.126.153.214

```bash
ss src 75.126.153.214
```

#### http (80) port only

```bash
ss src 75.126.153.214:http
ss src 75.126.153.214:80
```

#### smtp (25) port only

```bash
ss src 75.126.153.214:smtp
ss src 75.126.153.214:25
```

### Compare Local and/or Remote Port To A Number

#### Compares remote port to a number

```bash
ss dport OP PORT
```

#### Compares local port to a number

```
ss sport OP PORT
```

Where OP can be one of the following:

* `<=` or `le` : Less than or equal to port
* `>=` or `ge` : Greater than or equal to port
* `==` or `eq` : Equal to port
* `!=` or `ne` : Not equal to port
* `<` or `gt` : Less than to port
* `>` or `lt` : Greater than to port

Note: le, gt, eq, ne etc. are use in unix shell and are accepted as well.

#### Examples

Note: Do not forget to escape special characters when typing them in command line

```bash
ss  sport = :http
ss  dport = :http
ss  dport \> :1024
ss  sport \> :1024
ss sport \< :32000
ss  sport eq :22
ss  dport != :22
ss  state connected sport = :http
ss \( sport = :http or sport = :https \)
ss -o state fin-wait-1 \( sport = :http or sport = :https \) dst 192.168.1/24
```
