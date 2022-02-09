# CTF-Wonderland-TryHackMe
CTF baseado em alice no país das maravilhas

A primeira coisa que eu gosto de fazer em um CTF temático é olhar se ele tem um serviço web rodando na porta 80
faço isso porque CTFs assim geralmente contam histórias através de uma página web.

Não precisa rodar o nmap para isso, eu apenas abro uma nova guia e digito: <ip_maquina>:80
![image](https://user-images.githubusercontent.com/83795638/153250226-7393d8b6-1b4f-4a33-9599-3cd33bb748f2.png)
Eu já tenho em mente duas coisas que podemos fazer aqui, mas por enquanto vamos fazer uma análise mais detalhada da máquina...

Executando o nmap com -sV podemos ver que há um serviço http e um ssh rodando nas portas 22 e 80 respectivamente
```
┌──(portollost@)
└─$ nmap -sC -sV 10.10.194.31
Starting Nmap 7.92 ( https://nmap.org ) at 2022-02-09 13:58 -03
Nmap scan report for 10.10.194.31
Host is up (0.22s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT     STATE    SERVICE      VERSION
22/tcp   open     ssh          OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 8e:ee:fb:96:ce:ad:70:dd:05:a9:3b:0d:b0:71:b8:63 (RSA)
|   256 7a:92:79:44:16:4f:20:43:50:a9:a8:47:e2:c2:be:84 (ECDSA)
|_  256 00:0b:80:44:e6:3d:4b:69:47:92:2c:55:14:7e:2a:c9 (ED25519)
80/tcp   open     http         Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Follow the white rabbit.
3283/tcp filtered netassistant
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 49.53 seconds
```

Agora que sabemos os serviços da que estão rodando na máquina, eu quero me aprofundar na porta 80
Assim que descobrimos uma aplicação web em um servidor, existem duas coisas que são interessantes de fazermos:

A primeira é dar uma olhada no código fonte para descobrir algo estranho...
![image](https://user-images.githubusercontent.com/83795638/153253364-1825581c-0868-46aa-bda8-94a94aab75dc.png)

Como não há nada de mais no código decidi seguir com a seguir com a segunda coisa a se fazer...
ENUMERAR DIRETÓRIOS!
```
┌──(portollost@)
└─$ gobuster dir -q -u http://10.10.194.31/ -w /usr/share/seclists/Discovery/Web-Content/big.txt
/img                  (Status: 301) [Size: 0] [--> img/]
/poem                 (Status: 301) [Size: 0] [--> poem/]
/r                    (Status: 301) [Size: 0] [--> r/]
```
Vamos analisar cada diretório em partes pra termos uma boa direção

