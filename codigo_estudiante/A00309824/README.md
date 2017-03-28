# Docker-Mirror
<b>Autor:</b> Johan David Ballesteros <br>
<b>Código:</b>A00309824<br>
<b>Repositorio:</b> https://github.com/DavidPDP/sd-docker-assignment/tree/master/codigo_estudiante/A00309824

Primero se procederá a crear el mirror, este se encontrará en una máquina virtual, que se creará con el Vagrantfile. Una vez se levanta la máquina se procede a correr los siguientes comandos para crear el mirror.

## Crear Una Máquina Virtual Para El Mirror
Se creo la máquina virtual con el siguiente archivo.

<a href="https://github.com/DavidPDP/sd-docker-assignment/blob/master/codigo_estudiante/A00309824/Vagrantfile"><b>Vista del VagrantFile</b></a>

## Configurar El Mirror
El primer comando se encarga de generar la llave y el segundo de generar la entropía para la llave.
```sh
# gpg --gen-key
# cat /dev/urandom
```
Importar Keyring
```sh
# gpg --no-default-keyring --keyring /usr/share/keyrings/ubuntu-archive-keyring.gpg --export | gpg --no-default-keyring --keyring trustedkeys.gpg --import
```
### Instalar Aptly
Procedemos ahora a instalar la herramienta Aptly.

Agregamos la siguiente línea al repositorio. 
```txt
deb http://repo.aptly.info/ squeeze main
```
```sh
# vi /etc/apt/sources.list
```
Importamos la llave del servidor de Aptly para poder descargarlo.
```sh
# sudo apt-key adv --keyserver keys.gnupg.net --recv-keys 9E3E53F19C7DE460
```
Finalmente actualizamos e instalamos aptly.
```sh
# apt-get update
# apt-get install aptly
```
### Descargar y Actualizar Paquetes Al Mirror
Una vez ya tenemos aptly instalado procedemos a instalar los paquetes que necesitamos, en este caso el de postgresql.
```sh
# aptly mirror create -architectures=amd64 -filter='Priority (required) | Priority (important) | Priority (standard) | postgresql' -filter-with-deps xenial-main-postgresql http://mirror.upb.edu.co/ubuntu/ xenial main
```
Ahora procedemos actualizar el mirror.
```sh
# aptly mirror update xenial-main-postgresql
```
aptly snapshot create xenial-snapshot-postgresql from mirror xenial-main-postgresql

aptly publish snapshot xenial-snapshot-postgresql

gpg --export --armor > my_key.pub

scp vagrant@ip_solo_anfitrion:/tmp/my_key.pub .












