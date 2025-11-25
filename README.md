#Instalacion De Lamp

### 1. Primero debes actualizar el sistema por si hay actualizaciones las cuales te pueden solucionar futuros problemas
El comando para hacerlo es el siguiente.

```bash
sudo apt update && sudo apt upgrade -y
```
### 2.Como siguiente paso instalaremos el apache con el siguiente comando.

```bash
sudo apt install apache2 -y
```
> Activa y inicialo

```bash
sudo systemctl enable apache2
sudo systemctl start apache2
```
> Verfica el estado

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






