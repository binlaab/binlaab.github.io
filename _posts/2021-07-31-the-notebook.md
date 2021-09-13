Bueno, este es el primer post, voy a intentar no dejar morir la página.

TheNotebook es una Máquina Linux de nivel medio en la que tendremos que cambiar nuestra cookie de sesión a la del todopoderoso admin para conseguir una shell como www-data, robarle la ID a Noah y escapar del contenedor para convertirnos en root.

Como siempre, comenzamos escaneando los puertos de la máquina para saber a qué nos enfrentamos. Yo suelo hacer directamente un escaneo de la versión y servicio de los puertos abiertos.

```
> nmap -p- --open --min-rate 3000 -sS -sC -sV 10.10.10.230 -oN targeted
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
![web](https://user-images.githubusercontent.com/71317374/127770899-030fac83-8883-4f42-8e80-7f723f3a5dc1.png)



Fuzzearla no hace nada :/, así que nos registramos y creamos una nueva nota, pero antes abriendo burpsuite para ver como viajan los datos, y descubrimos una cookie de sesión, en concreto una JWT(Json Web Token)![Cookie de usuario](https://user-images.githubusercontent.com/71317374/127771086-62b6d643-4f76-4fc6-b27d-213679667cad.jpg)
 
Buscando más sobre esto nos encontramos una página en la que podemos encriptar y desencriptar estas cookies, si desencripto la mía aparece: ![PrtScr capture_2](https://user-images.githubusercontent.com/71317374/127771204-cbe65c3e-e81a-441c-a420-59c03db8e181.jpg)

Veo 2 cosas interesantes: el parámetro 'kid' y 'admin_cap'. El parámetro kid(Key ID), que indica qué clave se ha usado para asegurar ese JWT, busca una clave privada en su puerto 7070, pero podríamos cambiarlo a mi IP de atacante para que verifique el token con mi clave privada. Para generar una clave JWT RS256 (que es el algoritmo que usa esta página web) hay que hacerlo con ssh-keygen
```
> ssh-keygen -t rsa -b 4096 -m PEM -f privKey.key

Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in privKey.key
Your public key has been saved in privKey.key.pub
The key fingerprint is:
SHA256:0TXya6jN+ppi3B2PetAK53kFWu70pb5w74Yr+5WQAC8 root@kali
The key's randomart image is:
+---[RSA 4096]----+
|        . . o    |
|         + + .   |
|        E + .    |
|         ooo o   |
|        S=..=    |
|      . ++=..... |
|     . =.B+*.+o  |
|      + =oBo=o.  |
|     . .=*o===o  |
+----[SHA256]-----+
```
Ahora que tengo la clave, la idea sería copiarla al portapapeles para crear mi JWT con permisos de admin y además compartir un servidor HTTP con python para que compruebe que la clave existe.
![Generando el JWT](https://user-images.githubusercontent.com/71317374/127781126-6f3aaf2b-f8ee-4413-8626-b81597469d15.png)

Metemos la clave en Storage y... tenemos acceso al panel de administrador!!!
![permisos de admin](https://user-images.githubusercontent.com/71317374/127781194-9169d3d4-ba62-408f-abe7-7747958c52e3.png)

Si vamos al Admin Panel tenemos 2 apartados, uno para ver notas y otro para subir archivos. En las notas hay 2 muy interesantes: 
![Nota 1](https://user-images.githubusercontent.com/71317374/127781227-14318fa7-54ae-4cf7-ac83-901ba2973f03.png)
![Nota 2](https://user-images.githubusercontent.com/71317374/127781240-ff0496a6-9150-4202-ac2c-f683cee0fe3e.png)

Buenooooo, podemos subir archivos PHP con una reverse shell o una backdoor, y además hay backups de algo, lo que es interesante. 
Vamos a subir una shell en PHP para ejecutar comandos, por qué no?
![subida de archivo PHP](https://user-images.githubusercontent.com/71317374/127781288-7c1232ac-07c9-4a57-81a4-27335ba31b12.png)
Visitamos esa URL y mediante el parámetro cmd le especificamos comandos a ejecutar:
![whoami](https://user-images.githubusercontent.com/71317374/127781309-e0038a75-a5cf-4c63-8582-50f2aa77f389.png)

Y... somos el usuario www-data, y ahora que tenemos ejecución de comandos vamos a entablarnos una Reverse Shell.
![.\_.](https://user-images.githubusercontent.com/71317374/127781347-ae4d05e2-046a-4786-a528-a0de8fa8d086.png)

.\_. Nos han borrado el archivo, así que toca subirlo de nuevo. Estuve un rato probando y no funcionaban los comandos para enviar shells, así que opté por una reverse shell normal, la de PentestMonkey

![shell](https://user-images.githubusercontent.com/71317374/127781758-eb00cd59-ae32-4934-8070-88a7f952b00c.png)

Y ahí está, soy www-data, así que procedo a hacer un tratamiento de la TTY para no hacer ctrl + c y quedarme sin shell(me ha pasado).
Recordando la nota de las backups, voy a investigar por ahí, a ver si encuentro algo. Se suelen guardar en /var/backups, y esta máquina no es menos, las backups están ahí. Me las he movido a /dev/shm para trabajar con ellas. 

![backups](https://user-images.githubusercontent.com/71317374/127781901-814953b0-c657-4f65-b3fb-43194e9c5e88.png)

Ojooooo, tenemos acceso a /home/noah en forma de backup, si listamos los contenidos con ls -la hay un directorio .ssh, y todos sabemos qué significa eso, una id_rsa para entrar como noah.

![id_rsa](https://user-images.githubusercontent.com/71317374/127781965-96f43667-6549-4395-887b-2352ffce10d5.png)

Me la copio a mi máquina y me autentico como él por SSH para leer la user flag:

![imagen](https://user-images.githubusercontent.com/71317374/127782047-86046035-d677-449b-acd1-6ea5b9c32bf2.png)

ez.
Tras entrar a cualquier máquina linux hago sudo -l a ver si pudiera ejecutar como root algún comando, en este caso ¿docker?
```
bash-4.4$ sudo -l
Matching Defaults entries for noah on thenotebook:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User noah may run the following commands on thenotebook:
    (ALL) NOPASSWD: /usr/bin/docker exec -it webapp-dev01*

```
Vamos a buscar la versión a ver si hubiera algún exploit para esa versión:
```
-bash-4.4$ docker --version
Docker version 18.06.0-ce, build 0ffa825
```
Yyyyy justo hay dos de Container Breakout, escapar del contenedor, qué casualidad, no? Ni hecho a posta xD.
https://github.com/Frichetten/CVE-2019-5736-PoC Yo usé este, que es el primero que me encontré :p

Cambiamos el comando a ejecutar en main.go para darle permiso SUID a la /bin/bash y así entrar como noah por SSH para ejecutar `bash -p` y conseguir ser root y construimos la aplicación para que pueda correr en la máquina con `go build "ldflags -s -w" .` para reducir el peso del archivo y abrimos un servidor HTTP con python para que descargue el ejecutable.

![imagen](https://user-images.githubusercontent.com/71317374/127782389-0e0587a1-0ab7-4bc0-952e-6a9ab07580bc.png)

Ya está en la máquina, ahora solo falta correr el ejecutable y seremos el todopoderoso root.

![root](https://user-images.githubusercontent.com/71317374/127782819-7a208abf-3e82-4cd2-b8f3-272c4999e6c6.png)

Y finalmente, máquina rooteada. Me ha gustado bastante la escalada de privilegios, pero no tanto el user, me ha parecido algo simple, pero la máquina en general bien.

Y recuerda, aguante Metasploit













