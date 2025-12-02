# Instalacion De Lamp

### 1. Primero debes actualizar el sistema por si hay actualizaciones las cuales te pueden solucionar futuros problemas
El comando para hacerlo es el siguiente.

```bash
sudo apt update && sudo apt upgrade -y
```
### 2.Como siguiente paso instalaremos el apache con el siguiente comando.

```bash
sudo apt install apache2 -y
```
Activa y inicialo

```bash
sudo systemctl enable apache2
sudo systemctl start apache2
```
Verfica el estado

```bash
sudo systemctl status apache2
```

Visita la página de defecto de Apache `http://localhost`

### 3. Ahora pasaremos a instalar el MYSQL

```bash
sudo apt install mysql-server mysql-client -y
```
>Inicia y habilita el servicio

```bash
sudo systemctl enable mysql
sudo systemctl start mysql
```
**Configura de MySQL:**
## Acceder a su consola 

```bash
sudo mysql
```
### Creación de su base de datos 
```SQL
CREATE DATABASE bbdd;
```
### Ahora haremos la creación del usuario local dentro de la consola
```SQL
CREATE USER 'usuario'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
GRANT ALL PRIVILEGES ON bbdd.* TO 'usuario'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
> **Nota:** Recuerda que este usuario nomás puede conecatrse desde el servidor local (`localhost`), cosa que es suficiente si la aplicación web wn la base de datos está en el mismo servidor.ç

### 4. **Instalación de PHP i extensiones comunes**

Ubuntu 24.04 incluye PHP 8.3 y el repositorio estándar

```ìntento
sudo apt install php libapache2-mod-php php-mysql php-curl php-gd php-mbstring php-xml php-zip php-json php-cli -y
```
**Reinicia apache para cargar el PHP:**
```bash
sudo systemctl restart apache2
```
**Verifica la versión del PHP**
```bash
php -v
```
**Crea un fichero de prueba**
```ìntento
echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/info.php
```
Visita `http://localhost/info.php` para consultar la información del PHP

# Configuració de VirtualHost amb apache2
El servidor apache permite el asignar diversos lugares web de forma independiente en una misma máquina. Esta funcionalidad está asociada con **Virtal Host**.

Una vez configurado, podras acceder a su sitio web desde un navegador utilizando su nombre de dominio que tiene establecido (por ejemplo www.domini.local).


## 1. Creación de la estructura de directores

```ìntento
sudo mkdir -p /var/www/domini.local
```

## 2. Definició del VirtualHost

```bash
sudo nano /etc/apache2/sites-available/domini.local.conf
```
Agrega la configuración siguiente

```apache
<VirtualHost *:80>
    ServerAdmin admin@domini.local
    ServerName www.domini.local
    ServerAlias domini.local
    DocumentRoot /var/www/domini.local
    ErrorLog ${APACHE_LOG_DIR}/domini.local_error.log
    CustomLog ${APACHE_LOG_DIR}/domini.local_access.log combined
</VirtualHost>
```
> **Recomanació:** Utilitzeu fitxers de registre separats per a cada VirtualHost per facilitar la depuració.

## 3. Habilitar el VirtualHost

```bash
sudo a2ensite domini.local.conf
```
Este comando crea un enlace simbòlico desde  de `/etc/apache2/sites-available/` cap a `/etc/apache2/sites-enabled/`.

## 4. Reiniciar Apache2

Despúes de modificar la configuración, reinicie el servicio para aplicar los lienzos 

```bash
sudo systemctl restart apache2
```
> **Alternativa:** `sudo service apache2 restart` (funciona, pero `systemctl` es el estandard moderno en sistemas basados en systemd).

## 5. Modificar `/etc/hosts` per resoldre el domini localment

```bash
sudo nano /etc/hosts
```
Agrega la linea siguiente

```
127.0.0.1   www.domini.local domini.local
```

Esto permite que el navegador encuentre su sitio web en el sentido de que necesita un servidor DNS externo.

## 6. Comprobar el funcionamiento

Abri un navegador y acceder a:

```
http://www.domini.local
```
Si el directorio `/var/www/domini.local` está construido, Apache puede mostrar un error 403 o una lista de directorios ( según la configuración). Para probar wur funciona, crea un ajuste de prueba:

```bash
echo "<h1>Hola, benvingut domini.local</h1>" | sudo tee /var/www/domini.local/index.html
```
Vuelve a cargar la página y verás el mensaje

## 7. Solució de problemes: Registres d’Apache2

Si el bloqueo no funciona consulte los registros de apache

### Registro de errores

Contiene mensajes sobre errores de configuración, permisos, ficheros, etc.
```bash
sudo tail -f /var/log/apache2/domini.local_error.log
```
### Registro de acceso

Muestra todas las peticiones recibidas por el servidor

```bash
sudo tail -f /var/log/apache2/domini.local_access.log
```
>**Consejo: utiliza `tail -f`para ver las entradas en tiempo real mientras pruebs el lugar

## 8. Assignación de permisos

Apache se ejecuta normalmente con el usuario `www-data`, para asi evitar problemas con permisos, configurar el propietario y los permisos de director de su lugar:

### Cambio de propietario

Permite que el usuario pueda editar ficheros y que el apache los peueda leer:

```bash
sudo chown -R $USER:www-data /var/www/domini.local
```

### Establir permisos adequats

Asegúrate de que el propietario y el grupo tengan acceso completamente, y que los demás usuarios solo puedan leer

```bash
sudo chmod -R 775 /var/www/domini.local
```
### Resumen de los pasos claves 

| Crear directorios | `sudo mkdir -p /var/www/domini.local` |
| Configurar VirtualHost | Editar `/etc/apache2/sites-available/domini.local.conf` |
| Habilitar lugar | `sudo a2ensite domini.local.conf` |
| Reiniciar Apache | `sudo systemctl restart apache2` |
| Añadir dominio a hosts | `127.0.0.1 www.domini.local` a `/etc/hosts` |
| Verificar permisos | `chown` i `chmod` com s’indica |
| Depurar errores | Consultar `error.log` i `access.log` |

# Guia d’instal·lació i configuració de plataformes cloud (Nextcloud / ownCloud)  

**Dentro de un host virtual preconfigurado (/var/www/domini.local`)**

En esta guia explicaré la instalación ar **Nextcloud** o **ownCloud** enun entorno donde tienes un **host virtual** apuntante a ant a `/var/www/domini.local` (cómo por ejemplo, `domini.local`). 

## 1. Descarga y instalación de la plataforma cloud

### 1.1. Enllaços oficials

**Nextcloud**: [https://www.nextcloud.com](https://www.nextcloud.com)  
  Descarga directa:  
  [https://download.nextcloud.com/server/releases/latest.zip](https://download.nextcloud.com/server/releases/latest.zip)

**ownCloud**: [https://www.owncloud.org](https://www.owncloud.org)  
  Desccarga directa (versión estable):  
  [https://download.owncloud.com/server/stable/owncloud-complete-20240724.zip](https://download.owncloud.com/server/stable/owncloud-complete-20240724.zip)

> **Nota**: Nextcloud és compatible amb PHP 8.1+, mientras que **ownCloud encara requereix PHP 7.4** en muchas versionses estables. Assegurate de tener la versión de PHP adecuada antes de instalarla.

### 1.2. Pasos de instalación

**Mueve al directorio del host virtual**:
   ```bash
   cd /var/www/domini.local
   ```
**LImpia el contenido actual** (si hay):
   > Assegurate que no hay datos importantes antes de ejecutarlo.

 ```bash
   sudo rm -rf *
   ```

**Descarga el fichero` .cremallera`** de la plataforma elegida (Nextcloud o ownCloud) a tu sistema.

 ```bash
    wget https://download.nextcloud.com/server/releases/latest.zip
   ```

**Descomprime el archivo directamente al directorio**

```bash
   sudo unzip /ruta/al/arxiu.zip
   ```

 > Si el archivo crea una carpeta interna (ej: `nextcloud/` o `owncloud/`), asegúrate que el contenido se mueva **al nivel  arrel** del host virtual

```bash
   sudo mv nextcloud/* . && sudo rmdir nextcloud
   # o
   sudo mv owncloud/* . && sudo rmdir owncloud
   ```

*Puedes hacer eso directamente si tienes que descomprimirlo desde `Descargas`:**
    ```bash
   cp -R ~/Descargas/nextcloud/. /var/www/domini.local/.```
   
   Elimineu la carpeta `nextcloud` i l'arxiu `latest.zip`
    ```bash
    sudo rm -rf ~/Descargas/nextcloud && sudo rm -rf ~/Descargas/latest.zip
    ```

     **Puedes hacer eso directamente si lo tienes descomprimido en `/var/www/domini.local`:**
   ```bash
   cp -R /var/www/domini.local/nextcloud/. /var/www/domini.local/.
   ```

 Elimina la carpeta `nextcloud` y el archivo `latest.zip`
    ```bash
    sudo rm -rf /var/www/domini.local/nextcloud && sudo rm -rf /var/www/domini.local/latest.zip
    ```

    **Asegura los permisos correctos**:
   ```bash
   sudo chown -R www-data:www-data /var/www/domini.local
   sudo chmod -R 755 /var/www/domini.local

**Accede a la interfaz web**:
   abre el navegador y visita:
   ```
   http://domini.local
   ```
   Segueix les instruccions de configuració assistida:
   - Crea un usuario administrador.
   - Configura la base de datos (recomendada: MariaDB/MySQL).
   - Verifica que todos los requisitos del sistema estén completos.



