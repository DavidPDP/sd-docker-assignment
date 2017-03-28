# Docker-Mirror
<b>Autor:</b> Johan David Ballesteros <br>
<b>Código:</b>A00309824<br>
<b>Repositorio:</b> https://github.com/DavidPDP/sd-docker-assignment/tree/master/codigo_estudiante/A00309824

Primero se procederá a crear el mirror, este se encontrará en una máquina virtual, que se creará con el Vagrantfile. Una vez se levanta la máquina se procede a correr los siguientes comandos para crear el mirror.

El primer comando se encarga de generar la llave y el segundo de generar la entropía para la llave.
```sh
# gpg --gen-key
# cat /dev/urandom
```
Importar Keyring
```sh
# gpg --no-default-keyring --keyring /usr/share/keyrings/ubuntu-archive-keyring.gpg --export | gpg --no-default-keyring --keyring trustedkeys.gpg --import
```
Procedemos ahora a instalar la herramienta Aptly.

Agregamos la siguiente línea al repositorio. 
```txt
deb http://repo.aptly.info/ squeeze main
```
```sh
# vi /etc/apt/sources.list
```
```sh
# sudo apt-key adv --keyserver keys.gnupg.net --recv-keys 9E3E53F19C7DE460
```
```sh
# apt-get update
# apt-get install aptly
```
Una vez ya tenemos aptly instalado procedemos a instalar los paquetes que necesitamos, en este caso el de postgresql.
```sh
# aptly mirror create -architectures=amd64 -filter='Priority (required) | Priority (important) | Priority (standard) | postgresql' -filter-with-deps xenial-main-postgresql http://mirror.upb.edu.co/ubuntu/ xenial main
```
```sh
# aptly mirror update xenial-main-postgresql
```






