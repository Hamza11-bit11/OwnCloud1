# -------------------------
# PASO 1: Actualització del sistema
# -------------------------
# Actualitzem els paquets disponibles i les versions instal·lades al servidor
sudo apt update && sudo apt upgrade -y

# -------------------------
# PASO 2: Instal·lació d'Apache2
# -------------------------
# Instal·lem el servidor web Apache2, que serà el motor per a la nostra aplicació web
sudo apt install -y apache2

# -------------------------
# PASO 3: Instal·lació de MySQL
# -------------------------
# Instal·lem MySQL, el sistema de bases de dades que gestionarà la informació de l'aplicació
sudo apt install -y mysql-server

# -------------------------
# PASO 4: Instal·lació de PHP i llibreries necessàries
# -------------------------
# PHP és el llenguatge principal de l’aplicació, i necessitem algunes llibreries addicionals
sudo apt install -y php libapache2-mod-php php-fpm php-common php-mbstring php-xmlrpc php-soap php-gd php-xml php-intl php-mysql php-cli php-ldap php-zip php-curl

# -------------------------
# PASO 5: Reiniciem Apache2
# -------------------------
# Després de la instal·lació, reiniciem Apache per assegurar-nos que tots els serveis estiguin actius
sudo systemctl restart apache2

# -------------------------
# PASO 6: Configuració de MySQL
# -------------------------
# Accedim a la consola de MySQL com a superusuari per configurar la base de dades i l'usuari
sudo mysql

# A dins de MySQL: Creació de la base de dades per a l'aplicació
CREATE DATABASE bbdd;

# Creació d'un usuari que gestionarà la base de dades (canviar 'password' per una contrasenya segura)
CREATE USER 'usuario'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';

# Assignació de privilegis a l'usuari sobre la nova base de dades
GRANT ALL ON bbdd.* TO 'usuario'@'localhost';

# Sortim de la consola de MySQL
exit

# -------------------------
# PASO 7: Comprovació de la connexió a MySQL
# -------------------------
# Ens assegurem que l'usuari creat pot connectar-se a la base de dades
mysql -u usuario -p

# -------------------------
# PASO 8: Configuració per permetre connexions remotes (opcional)
# -------------------------
# Editem la configuració de MySQL per permetre connexions des d'altres equips
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf

# Dins de l'arxiu, canviem la línia següent:
# bind-address = 127.0.0.1
# Per aquesta:
# bind-address = 0.0.0.0

# Reiniciem el servidor MySQL per aplicar aquests canvis
sudo systemctl restart mysql

# Tornem a la consola de MySQL per crear un usuari amb accés remot (IP de l’equip des del qual es connectarà)
sudo mysql
CREATE USER 'usuario'@'192.168.22.100' IDENTIFIED WITH mysql_native_password BY 'password';
GRANT ALL ON bbdd.* TO 'usuario'@'192.168.22.100';
exit

# -------------------------
# PASO 9: Descarregar i descomprimir l’aplicació web
# -------------------------
# Copiem l'arxiu comprimit de l'aplicació al directori arrel de Apache
sudo cp ~/Baixades/app-web.zip /var/www/html

# Ens dirigim al directori arrel de l'aplicació
cd /var/www/html

# Descomprimim l'arxiu
sudo unzip app-web.zip

# Movem els fitxers de l'aplicació al directori arrel i eliminem la carpeta temporal
sudo cp -R app-web/. /var/www/html
sudo rm -rf app-web/

# -------------------------
# PASO 10: Eliminació del fitxer per defecte d'Apache
# -------------------------
# Suprimim l'index.html per defecte d'Apache per assegurar que es carregui la nostra aplicació
sudo rm -rf /var/www/html/index.html

# -------------------------
# PASO 11: Configuració de permisos per a l’aplicació
# -------------------------
# Assegurem que el directori i els fitxers de l'aplicació tenen els permisos adequats
cd /var/www/html
sudo chmod -R 775 .
sudo chown -R usuario:www-data .

# -------------------------
# PASO 12: Verificació de la instal·lació
# -------------------------
# Finalment, obrim http://localhost al navegador per comprovar que l’aplicació funciona correctament
# Introduïu la següent informació de configuració al navegador (si no heu fet canvis a la configuració):
# * usuari: usuario
# * contrasenya: password
# * base de dades: bbdd
# * domini: localhost
