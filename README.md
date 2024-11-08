OwnCloud1
Instal·lació i configuració d'aplicacions web

Per instal·lar una aplicació web, el primer que hem de fer és descarregar el seu codi font i traslladar-lo a la carpeta arrel del nostre servidor web, que en aquest cas és apache2. Quan instal·lem apache2, aquest crea una carpeta a /var/www/html, que és la ubicació per defecte on el servidor busca les aplicacions.

Així que, un cop haguem copiat l'aplicació al directori /var/www/html, podrem accedir a l'aplicació mitjançant l'adreça http://localhost.
Instal·lació d'apache2, mysql i algunes llibreries al contenidor

    Actualització de la màquina.

sudo apt update

sudo apt upgrade

    Instal·lació del servidor web apache2.

sudo apt install -y apache2

    Instal·lació del servidor de bases de dades mysql-server.

sudo apt install -y mysql-server

    Instal·lació d'algunes llibreries de php, el llenguatge principal que utilitzen les aplicacions.

sudo apt install -y php libapache2-mod-php

sudo apt install -y php-fpm php-common php-mbstring php-xmlrpc php-soap php-gd php-xml php-intl php-mysql php-cli php-ldap php-zip php-curl

    Reiniciem el servidor apache2

sudo systemctl restart apache2

Configuració de MySQL
Accedim a la consola de MySQL

Un cop tinguem el terminal amb permisos de root, accedim a la consola de MySQL per gestionar les bases de dades.

sudo mysql

Creació de la base de dades:

A la consola de MySQL, creem una nova base de dades per a l'aplicació web, anomenada bbdd.

CREATE DATABASE bbdd;

Creació d'un usuari

A continuació, creem un usuari per accedir a la base de dades. En aquest cas, l'usuari es diu usuario i l'autenticació es farà amb la contrasenya password. L'usuari estarà restringit a connectar-se des de localhost.

CREATE USER 'usuario'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';

Donem privilegis a l'usuari:

Perquè l'usuari pugui treballar amb la base de dades, li donem tots els privilegis sobre la base de dades bbdd.

GRANT ALL ON bbdd.* TO 'usuario'@'localhost';

Sortim de la base de dades

Un cop feta la configuració, sortim de la consola de MySQL.

exit

Probem la connexió a la base de dades

Des d'un terminal amb un usuari sense privilegis, comprovem que podem connectar-nos a la base de dades amb l'usuari creat.

mysql -u usuario -p

Extra: permetre la connexió des d'una màquina remota

Per motius de seguretat, MySQL només permet connexions des de la mateixa màquina (localhost) per defecte. Si volem que altres màquines puguin connectar-se, hem de modificar la configuració.
Canviem l'accés per defecte a la nostra màquina

Editarem la configuració de MySQL per permetre connexions des de qualsevol màquina. Obrim el fitxer /etc/mysql/mysql.conf.d/mysqld.cnf.

vim /etc/mysql/mysql.conf.d/mysqld.cnf

Busquem la línia següent:

bind-address = 127.0.0.1

I la canviem per:

bind-address = 0.0.0.0

Reiniciem el servidor

Perquè els canvis tinguin efecte, reiniciem el servidor de MySQL.

systemctl restart mysql

Creació d'un usuari per a accedir des d'una màquina remota

Si volem permetre connexions des d'una màquina remota, crearem un altre usuari amb els privilegis per connectar-se des de l'adreça IP de la màquina remota.

CREATE USER 'usuario'@'192.168.22.100' IDENTIFIED WITH mysql_native_password BY 'password';

Assignem els permisos a l'usuari per a la connexió remota:

GRANT ALL ON bbdd.* TO 'usuario'@'192.168.22.100';

Sortim de MySQL

Finalment, sortim de la consola de MySQL.

exit

Descarreguem els fitxers de l'aplicació web

Ara anem al directori /var/www/html i copiem els fitxers de l'aplicació web. Si el fitxer de l'aplicació web es diu app-web.zip, l'hem de moure a aquest directori. Si el sistema no està en català, la carpeta Baixades pot tenir un altre nom, així que ajustem la ruta segons sigui necessari.

sudo cp ~/Baixades/app-web.zip /var/www/html

Un cop copiat el fitxer, ens movem al directori /var/www/html.

cd /var/www/html

Descomprimim el fitxer descarregat.

sudo unzip app-web.zip

Copiem tots els fitxers descomprimits a la carpeta /var/www/html. Hem de substituir app-web pel nom de la carpeta generada quan descomprimiu l'arxiu.

sudo cp -R app-web/. /var/www/html

Un cop fet això, eliminem la carpeta creada per la descompressió.

sudo rm -rf app-web/

Eliminem el fitxer index.html de l'apache2

Per defecte, Apache crea un fitxer index.html. El fem fora per evitar que interfereixi amb la nostra aplicació.

sudo rm -rf /var/www/html/index.html

Aplicació de permisos a les nostres aplicacions web

Després d'haver descomprimit i mogut els fitxers al directori /var/www/html, és necessari aplicar els permisos adequats perquè Apache pugui accedir a aquests fitxers correctament.

cd /var/www/html

sudo chmod -R 775 .

sudo chown -R usuario:www-data .

Accedim al navegador per veure que tot funciona

Ara obrim un navegador i entrem a l'adreça http://localhost. Si tot està configurat correctament, hauria d'aparèixer la pantalla d'instal·lació de l'aplicació web. Aquí ens demanarà crear un usuari admin i introduir la informació de la base de dades.

Si no heu canviat la informació per defecte, les dades seran les següents:

    usuari: usuario
    contrasenya: password
    base de dades: bbdd
    domini: localhost
