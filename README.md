# Apache

Primero Instalamos Apache en nuestra maquina virtual:

~~~
sudo apt-get install apache2
~~~

Tras esto crearemos una estructura de directorios que alojará los datos del sitio que vamos a proporcionar a nuestros visitantes:

~~~
sudo mkdir -p /var/www/gato.com/html
sudo mkdir -p /var/www/mosquito.com/html
sudo mkdir -p /var/www/escherichiacoli.es/html
sudo mkdir -p /var/www/chip555.org/html
~~~

Ahora damos permisos:

~~~
sudo chmod -R 777 /var/www
~~~

A continuacion creamos los sitios web:

~~~
echo "gato.com" > /var/www/gato.com/html/index.html
echo "mosquito.com" > /var/www/mosquito.com/html/index.html
echo "escherichiacoli.es" > /var/www/escherichiacoli.es/html/index.html
echo "chip555.org" > /var/www/chip555.org/html/index.html
~~~

Apache viene con un archivo virtual host por defecto llamado 000-default.conf. 
Vamos a copiarlo para crear un archivo virtual host para cada uno de nuestros dominios.

~~~
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/gato.com.conf
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/mosquito.com.conf
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/escherichiacoli.es.conf
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/chip555.org.conf
~~~

Configuramos los ficheros:

~~~
<VirtualHost *:80>
    ServerAdmin admin@gato.com
    ServerName gato.com
    ServerAlias www.gato.com
    DocumentRoot /var/www/gato.com/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

<VirtualHost *:80>
    ServerAdmin admin@mosquito.com
    ServerName mosquito.com
    ServerAlias www.mosquito.com
    DocumentRoot /var/www/mosquito.com/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

<VirtualHost *:80>
    ServerAdmin admin@escherichiacoli.es
    ServerName escherichiacoli.es
    ServerAlias www.escherichiacoli.es
    DocumentRoot /var/www/escherichiacoli.es/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

<VirtualHost *:80>
    ServerAdmin admin@chip555.org
    ServerName chip555.org
    ServerAlias www.chip555.org
    DocumentRoot /var/www/chip555.org/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
~~~

Ahora que hemos creado nuestros archivos de virtual host, debemos habilitarlos
Podemos usar la herramienta a2ensite para habilitar cada uno de nuestros sitios:

~~~
sudo a2ensite gato.com.conf
sudo a2ensite mosquito.com.conf
sudo a2ensite escherichiacoli.es.conf
sudo a2ensite chip555.org.conf
~~~

Reiniciamos el Demonio:

~~~
sudo service apache2 restart
~~~

#Autentificación, Autorización y Control de Acceso

Necesitaremos crear un archivo de contraseñas. 
Éste archivo debería colocarlo en algún sitio no accesible mediante la Web.
Para crear un archivo de contraseñas, usaremos la utilidad htpasswd que viene con Apache. 
Para crear el archivo:

~~~
sudo htpasswd -c /var/www/gato.com/passwords usuario1
sudo htpasswd  /var/www/gato.com/passwords usuario2
~~~

Añadiremos a nuestro fuchero de configuración:

~~~
<Directory /var/www/gato.com/html>
	AuthType Basic
	AuthName "ACCESO RESTRINGIDO."
	AuthUserFile /var/www/gato.com/passwords
	Require user usuario1
</Directory>
<Directory /var/www/gato.com/html>        
	Options Indexes FollowSymLinks MultiViews
	AllowOverride  none
	Order Allow,deny
	allow from all
</Directory>

<Directory /var/www/mosquito.com/html>
	AuthType Basic
	AuthName "ACCESO RESTRINGIDO."
	AuthUserFile /var/www/gato.com/passwords
	Require user usuario3
</Directory>
<Directory /var/www/mosquito.com/html>        
	Options Indexes FollowSymLinks MultiViews
	AllowOverride  none
	Order Allow,deny
	allow from all
</Directory>

<Directory /var/www/escherichiacoli.es/html>
	AuthType Basic
	AuthName "ACCESO RESTRINGIDO."
	AuthUserFile /var/www/gato.com/passwords
	Require user usuario1
</Directory>
<Directory /var/www/escherichiacoli.es/html>        
	Options Indexes FollowSymLinks MultiViews
	AllowOverride  none
	Order Allow,deny
	allow from all
</Directory>

<Directory /var/www/chip555.org/html>
	AuthType Basic
	AuthName "ACCESO RESTRINGIDO."
	AuthUserFile /var/www/gato.com/passwords
	Require user usuario1
</Directory>
<Directory /var/www/chip555.org/html>        
	Options Indexes FollowSymLinks MultiViews
	AllowOverride  none
	Order Allow,deny
	allow from all
</Directory>
~~~

Reiniciamos el demonio y sólo tendrá acceso el usuario1.
Si sustituimos 'Require user usuario1' por 'Require valid-user ', tendrán acceso todos los usuarios del fichero passwords.

