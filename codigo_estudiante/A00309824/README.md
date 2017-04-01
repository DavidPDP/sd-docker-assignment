# Docker-Mirror
<b>Autores:</b> Dylan Torres y Johan David Ballesteros <br>
<b>Códigos:</b>A00265772 - A00309824<br>
<b>Repositorio:</b> https://github.com/DavidPDP/sd-docker-assignment/tree/master/codigo_estudiante/A00309824

## Objetivo
* Crear un mirror que contenga los archivos de Postgresql.
* Levantar un contenedor que descargue Postgresql desde el mirror creado.

Primero se procederá a crear el mirror, este se encontrará en una máquina virtual, que se creará con un Vagrantfile. Una vez se levanta la máquina se procede a correr los siguientes comandos para crear y configurar el mirror.

## Crear Una Máquina Virtual Para El Mirror
Para crear la máquina virtual se utilizará el siguiente archivo.

<a href="https://github.com/DavidPDP/sd-docker-assignment/blob/master/codigo_estudiante/A00309824/Vagrantfile"><b>Vista del VagrantFile</b></a>

## Configurar El Mirror
Para configurar el mirror empezamos generando una llave, que nos permitirá una transmisión de datos de manera segura e incriptada. El primer comando se encarga de generar la llave y el segundo de generar la entropía para la llave.
```sh
$ gpg --gen-key
$ cat /dev/urandom
```
Importar Keyring
```sh
$ gpg --no-default-keyring --keyring /usr/share/keyrings/ubuntu-archive-keyring.gpg --export | gpg --no-default-keyring --keyring trustedkeys.gpg --import
```
### Instalar Aptly
Procedemos ahora a instalar la herramienta Aptly, una herramienta que permite la administración de repositorios Debian, reflejar repositorios remotos y crear snapshots.

Agregamos el repositorio de Aptly al archivo sources list, que es el archivo donde Apt guarda la lista de repositorios o canales de software, para esto empleamos el siguiente comando.
```sh
echo deb http://repo.aptly.info/ squeeze main > /etc/apt/sources.list
```
Importamos la llave del servidor de Aptly para poder descargarlo.
```sh
$ sudo apt-key adv --keyserver keys.gnupg.net --recv-keys 9E3E53F19C7DE460
```
Finalmente actualizamos Apt e instalamos Aptly.
```sh
# apt-get update
# apt-get install aptly
```
### Descargar y Actualizar Paquetes Al Mirror
Una vez ya tenemos Aptly instalado, procedemos a crear el mirror descargando los paquetes que necesitamos, en este caso el de postgresql.
```sh
$ aptly mirror create -architectures=amd64 -filter='Priority (required) | Priority (important) | Priority (standard) | postgresql' -filter-with-deps xenial-main-postgresql http://mirror.upb.edu.co/ubuntu/ xenial main
```
Ahora actualizamos el mirror, para que pueda correr la configuración anterior y descargar lo indicado.
```sh
$ aptly mirror update xenial-main-postgresql
```
### Exponer Paquetes
Para poder exponer los paquetes y que puedan ser descargados desde otras máquinas crearemos un snapshot. 
```sh
$ aptly snapshot create xenial-snapshot-postgresql from mirror xenial-main-postgresql
```
Una vez creado lo publicamos.
```sh
$ aptly publish snapshot xenial-snapshot-postgresql
```
Ahora exportamos la llave que creamos a la máquina que va a emplear el mirror.
```sh
$ gpg --export --armor > my_key.pub
```
```sh
$ scp vagrant@192.168.131.3:/tmp/my_key.pub .
```
Por último iniciamos el mirror.
```sh
$ aptly serve
```
## Crear El Contenedor Con Docker 
Ya se tiene el mirror con los paquetes que necesitamos, ahora procedemos a crear el contenedor virtual apuntando al mirror para descargar Postgresql. Para esto creamos un Dockerfile.

<a href="https://github.com/DavidPDP/sd-docker-assignment/blob/master/codigo_estudiante/A00309824/Vagrantfile"><b>Dockerfile Utilizado</b></a>

### Construir La Imagen
Construimos la imagen con el siguiente comando asignado un nombre y una versión.
```sh
$ docker build -t ubuntu_postgresql:0.0.1 .
```
Comprobamos que no halla ningún error y verificamos que haya quedado registrado en la imagenes de Docker.
```sh
$ docker images
```
### Crear Contenedor Desde La Imagen
Ya teniendo la imagen procedemos a levantar el contenedor virtual con el siguiente comando, que nos permite también abrir el bash del contenedor creado.
```sh
$ docker run -it --rm ubuntu_postgresql:0.0.1 /bin/bash
```
### Verificar Instalación De Postgresql
Estando en el bash del contenedor verificamos que efectivamente instaló Postgresql.
```sh
$ psql --version
```
![alt text](https://github.com/DavidPDP/LoadBalancer-Distribuidos/blob/master/images/tree.png)















