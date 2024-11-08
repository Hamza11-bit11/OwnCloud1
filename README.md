# Actualización del sistema
sudo apt update
sudo apt upgrade

# Instalación del servidor web apache2
sudo apt install -y apache2

# Instalación del servidor de bases de datos mysql-server
sudo apt install -y mysql-server

# Instalación de PHP y librerías adicionales necesarias
sudo apt install -y php libapache2-mod-php
sudo apt install -y php-fpm php-common php-mbstring php-xmlrpc php-soap php-gd php-xml php-intl php-mysql php-cli php-ldap php-zip php-curl

# Reiniciamos apache2
sudo systemctl restart apache2

# Accedemos a la consola de MySQL
sudo mysql <<EOF
# Creación de la base de datos
CREATE DATABASE bbdd;

# Creación de un usuario para la base de datos
CREATE USER 'usuario'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';

# Asignación de permisos al usuario en la base de datos
GRANT ALL ON bbdd.* TO 'usuario'@'localhost';

# Salimos de MySQL
EXIT;
EOF

# Prueba de conexión a la base de datos (se debe ejecutar manualmente con la contraseña)
# mysql -u usuario -p

# Configuración opcional para permitir conexiones remotas a MySQL
# Modificación de la configuración de MySQL para aceptar conexiones externas
sudo sed -i 's/bind-address\s*=\s*127.0.0.1/bind-address = 0.0.0.0/' /etc/mysql/mysql.conf.d/mysqld.cnf

# Reinicio de MySQL para aplicar cambios
sudo systemctl restart mysql

# Creación de un usuario remoto (cambiar la IP 192.168.22.100 por la necesaria)
sudo mysql <<EOF
CREATE USER 'usuario'@'192.168.22.100' IDENTIFIED WITH mysql_native_password BY 'password';
GRANT ALL ON bbdd.* TO 'usuario'@'192.168.22.100';
EXIT;
EOF

# Descarregar i moure els fitxers de l'aplicació web a /var/www/html
sudo cp ~/Baixades/app-web.zip /var/www/html
cd /var/www/html
sudo unzip app-web.zip
sudo cp -R app-web/. /var/www/html
sudo rm -rf app-web/

# Eliminació de l'index.html per defecte de apache2
sudo rm -rf /var/www/html/index.html

# Assignació de permisos a l'aplicació web
cd /var/www/html
sudo chmod -R 775 .
sudo chown -R usuario:www-data .
