Bueno, este es el primer post, voy a intentar no dejar morir la página.(POST EN PROGRESO)

TheNotebook es una Máquina Linux de nivel medio en la que tendremos que cambiar nuestra cookie de sesión a la del todopoderoso admin para conseguir una shell como www-data, robarle la ID a Noah y escapar del contenedor para convertirnos en root.

Como siempre, comenzamos escaneando los puertos de la máquina para saber a qué nos enfrentamos. Yo suelo hacer directamente un escaneo de la versión y servicio de los puertos abiertos.

```
nmap -p- --open --min-rate 3000 -sS -sC -sV 10.10.10.230 -oN targeted
```

```
Nmap 7.91 scan initiated Sat Jul 17 13:53:17 2021 as: nmap -p- --open --min-rate 3000 -sS -sC -sV 10.10.10.230 -oN targeted
Nmap scan report for 10.10.10.230
Host is up (0.047s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 86:df:10:fd:27:a3:fb:d8:36:a7:ed:90:95:33:f5:bf (RSA)
|   256 e7:81:d6:6c:df:ce:b7:30:03:91:5c:b5:13:42:06:44 (ECDSA)
|_  256 c6:06:34:c7:fc:00:c4:62:06:c2:36:0e:ee:5e:bf:6b (ED25519)
80/tcp open  http    nginx 1.14.0 (Ubuntu)
|_http-server-header: nginx/1.14.0 (Ubuntu)
|_http-title: The Notebook - Your Note Keeper
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done at Sat Jul 17 13:53:27 2021 -- 1 IP address (1 host up) scanned in 9.49 seconds
```

Bueno, puertos 22(SSH, nada por ahí) y 80(HTTP)

En el HTTP encontramos una página web con un panel de inicio de sesión y registro. 
![](https://github.com/binlaab/binlaab.github.io/raw/main/_posts/images/thenotebook/web.png "Página web")

Fuzzearla no hace nada :/, así que nos registramos y creamos una nueva nota, pero antes abriendo burpsuite para ver como viajan los datos, y descubrimos una cookie de sesión, en concreto una JWT(Json Web Token), encodeada en base64.

