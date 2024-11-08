# Actualitzem la màquina
sudo apt update
sudo apt upgrade

# Instal·lació d'Apache2
sudo apt install -y apache2

# Instal·lació del servidor MySQL
sudo apt install -y mysql-server

# Instal·lació de PHP i llibreries necessàries
sudo apt install -y php libapache2-mod-php php-fpm php-common php-mbstring php-xmlrpc php-soap php-gd php-xml php-intl php-mysql php-cli php-ldap php-zip php-curl

# Reiniciem el servidor Apache2
sudo systemctl restart apache2

# Accedim a MySQL
sudo mysql

# Dins de la consola MySQL: Creació de la base de dades
CREATE DATABASE bbdd;

# Creació d'un usuari
CREATE USER 'usuario'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';

# Assignem privilegis a l'usuari
GRANT ALL ON bbdd.* TO 'usuario'@'localhost';

# Sortim de MySQL
exit

# Prova de connexió amb l'usuari creat
mysql -u usuario -p

# Configuració per permetre connexions remotes (opcional)
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf

# Dins de l'arxiu, canviem la línia següent:
# bind-address = 127.0.0.1
# Per aquesta línia:
# bind-address = 0.0.0.0

# Reiniciem el servidor MySQL per aplicar canvis
sudo systemctl restart mysql

# Creació d'un usuari per accés remot (opcional)
sudo mysql
CREATE USER 'usuario'@'192.168.22.100' IDENTIFIED WITH mysql_native_password BY 'password';
GRANT ALL ON bbdd.* TO 'usuario'@'192.168.22.100';
exit

# Copiem l'arxiu de l'aplicació a /var/www/html
sudo cp ~/Baixades/app-web.zip /var/www/html

# Ens dirigim al directori /var/www/html
cd /var/www/html

# Descomprimim l'arxiu de l'aplicació
sudo unzip app-web.zip

# Copiem els fitxers al directori arrel i eliminem la carpeta temporal
sudo cp -R app-web/. /var/www/html
sudo rm -rf app-web/

# Eliminem el fitxer index.html d'Apache2
sudo rm -rf /var/www/html/index.html

# Apliquem permisos al directori de l'aplicació
cd /var/www/html
sudo chmod -R 775 .
sudo chown -R usuario:www-data .
