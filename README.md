# Practica-02-pila-LAMP-con-Vagrant

>IES Celia Viñas (Almería) - Curso 2018/2019  
>Módulo: IAW - Implantación de Aplicaciones Web  
>Ciclo: CFGS Administración de Sistemas Informáticos en Red  
>Alumno: Luis Miguel Belmonte Hernández

## Instalar Virtualbox y vagrant
Para realizar esta práctica sera necesario instalar [Virtualbox](https://www.virtualbox.org/wiki/Downloads) y [vagrant](https://www.vagrantup.com/downloads.html) sobre el sistema operativo en el que vayamos a realizarla.

## Crear Vagrantfile para ubuntu server 18.04
Creamos un directorio donde contendremos las carpetas para nuestras maquinas virtuales en vagrant.
-  `mkdir VMvagrant`
-  `cd VMvagrant`
-  `mkdir maquina-01`

Desde consola nos situamos dentro de la carpeta "maquina-01" y lanzamos la siguientes líneas.

- `vagrant init`

Con esta línea de comando ya tendremos creado el archivo de configuracion "Vagrantfile" que nos permitira configurar nuestro ubuntu server 18.04 el contendio de archivo es el siguiente:

```
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.network "private_network", ip: "192.168.33.10"
  config.vm.provision "shell", path: "script.sh"
end
```

## Crear script para realizar "provision" 

Dentro del archivo `Vagrantfile` definimos la ruta del script para realizar "provision" en nuestra maquina virtual. El contenido del script es el siguiente:

```bash
#! /bin/bash
apt-get update

# Instalación de apache
apt-get install -y apache2
cp /vagrant/index.php /var/www/html
apt-get install -y php libapache2-mod-php php-mysql

# Instalación  de las utilidades dconf
apt-get -y install debconf-utils

# Instalación de la contraseña root
DB_ROOT_PASSWD=luismiluismi
debconf-set-selections <<< "mysql-server mysql-server/root_password password $DB_ROOT_PASSWD"
debconf-set-selections <<< "mysql-server mysql-server/root_password_again password $DB_ROOT_PASSWD"

# Instalación de mysql
apt-get install -y mysql-server

# Clonar el repositorio de la aplicacion web
apt-get install -y git
rm -f /var/www/html/index.html
cd /tmp
git clone https://github.com/josejuansanchez/iaw-practica-lamp.git
cp iaw-practica-lamp/src/. /var/www/html/ -R
cd /var/www/html
chown www-data:www-data * -R 
mysql -u root -p$DB_ROOT_PASSWD < /tmp/iaw-practica-lamp/db/database.sql
```

## Inicio de la máquina virtual 

- `vagrant up`

## Conexión por ssh con la máquina virtual

- `vagrant ssh`
