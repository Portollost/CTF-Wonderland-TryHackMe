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

No primeiro diretório existem 3 imagens...
![image](https://user-images.githubusercontent.com/83795638/153271256-8c89881a-833b-4bfb-b061-c7713d76ec33.png)
... quando abrimos cada uma delas a primeira vista parecem imagens normais porém não é bem o que elas são
![image](https://user-images.githubusercontent.com/83795638/153271742-8c2c1ddb-0001-4e4e-ac12-1d70fc960b43.png)
![image](https://user-images.githubusercontent.com/83795638/153271784-3d126492-5df5-4c7d-a347-aa20168f1c81.png)
![image](https://user-images.githubusercontent.com/83795638/153271801-6faa53d6-2eac-439a-af10-7d118b1f1976.png)

O segundo diretório se chama Poema e bem... ele tem um poema que aparece no livro o da Alice
```
The Jabberwocky
'Twas brillig, and the slithy toves
Did gyre and gimble in the wabe;
All mimsy were the borogoves,
And the mome raths outgrabe.

“Beware the Jabberwock, my son!
The jaws that bite, the claws that catch!
Beware the Jubjub bird, and shun
The frumious Bandersnatch!”

He took his vorpal sword in hand:
Long time the manxome foe he sought —
So rested he by the Tumtum tree,
And stood awhile in thought.

And as in uffish thought he stood,
The Jabberwock, with eyes of flame,
Came whiffling through the tulgey wood,
And burbled as it came!

One, two! One, two! And through and through
The vorpal blade went snicker-snack!
He left it dead, and with its head
He went galumphing back.

“And hast thou slain the Jabberwock?
Come to my arms, my beamish boy!
O frabjous day! Callooh! Callay!”
He chortled in his joy.

‘Twas brillig, and the slithy toves
Did gyre and gimble in the wabe;
All mimsy were the borogoves,
And the mome raths outgrabe.
```
E finalmente o terceiro e último diretório tem uma mensagem pra gente:

Continue.
"Você poderia me dizer, por favor, que caminho devo seguir a partir daqui?"

E essa mensagem quer basicamente dizer que a gente tá no caminho certo.

Porém olhando o código fonte da página... eu acabei não encontrado nada.

Foi aí que decidi investigar mais os outros diretórios

Se você é interessado em investigação ou computação em geral, já deve ter ouvido falar de metadados.

Metadados são resumidamente dados que descrevem a estrutura de um arquivo e que muitas vezes são omitidos do usuário comum. De diversas formas eles são usados para nos dar pistas sobre a origem e funcionamento daquele arquivo além de darem ótimas dicas para nós jogadores.

E se você lembrar bem, no primeiro diretório nós temos 3 imagens indexadas edisponíveis para download...

Baixando elas para o nosso computador e usando uma ferramenta para a extração de metadados chamada steghide...
```
┌──(portollost)
└─$ steghide extract -sf white_rabbit_1.jpg
Enter passphrase:
wrote extracted data to "hint.txt".
```
...a gente consegue um arquivo chamado hint.txt onde está a nossa segunda dica
```
┌──(liminal㉿DESKTOP-93UJNBQ)-[~/liminal]
└─$ cat hint.txt
follow the r a b b i t
```
