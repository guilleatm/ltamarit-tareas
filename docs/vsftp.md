## Instalar y configurar el Servidor vsftpd en Ubuntu desde el fichero de configuración 
Vsftpd es un servidor ftp para Linux sencillo de instalar y configurar. Es el recomendado por Debian y Ubuntu, tiene una configuración muy sencilla en un solo fichero y es bastante ligero y seguro.

Su página oficial http://vsftpd.beasts.org

### Instalación del paquete vsftpd: 

desde la línea de comandos:
```
sudo apt-get install vsftpd
```
Al instalar vsftpd en el sistema se crean los siguientes archivos y directorios en el sistema: 

- /etc/init.d/vsftpd: fichero que inicia el servidor ftp. 
- /usr/bin/vsftpd: archivo ejecutable. 
- /etc/vsftpd.conf: es el fichero de configuración del servidor. 
- /etc/ftpusers: fichero que contiene una lista de usuarios que no se podrán conectar al servidor. 

### Configuración
El fichero de configuración del servidor se llama vsftpd.conf y se encuentra en el directorio /etc. Las directivas más importantes de este fichero son las siguientes: 
#### anonymous_enable 
Si el valor de esta directiva es YES cualquier usuario se puede conectar al servidor dando el nombre de usuario anonymous o ftp. Por razones de seguridad se debe poner NO (anonymous_enable=NO) 
#### local_enable 
Esta línea indica si se permite o no el acceso de usuarios locales a sus respectivas carpetas privadas. Si se permite el acceso habría que poner 
local_enable=YES (local_enable=NO en caso contrario). 
#### chroot_local_user 
Permite enjaular a los usuarios dentro de su propio directorio personal. Si en el fichero de configuración aparece chroot_local_user=NO, entonces el usuario tiene acceso a todo el sistema de archivos, en función de los permisos asignados. Cuando un usuario local se conecta y en el fichero de configuración aparece chroot_local_user=YES, entonces enjaulamos a los usuarios dentro de su propio directorio personal, sin posibilidad de acceder a todo el sistema de ficheros. Se mejora por tanto la seguridad.

Con esta configuración ya tendríamos enjaulados a los usuarios en su carpeta home pero no podremos escribir necesitamos añadir:
* allow_writeable_chroot=YES *
 
#### write_enable 
Con esta directiva se puede permitir o denegar la subida de ficheros al servidor FTP. Si se permite que los usuarios suban archivos al servidor FTP habría que poner write_enable=YES. 
local_umask 
Se pueden establecer los permisos con los que quedará el archivo al subirlo al servidor FTP. local_umask=022 indicará que los permisos de los archivos serán 644, es decir, lectura y escritura para el propietario del fichero, y sólo lectura para el grupo y los demás. 
anon_upload_enable 
Esta directiva indica si los usuarios anónimos pueden cargar archivos en el servidor. anon_upload_enable=YES permite que los usuarios anónimos puedan subir ficheros. 
anon_mkdir_write_enable 
Si esta directiva tiene el valor YES se permitirá la creación de directorios en el servidor. 
ftpd_banner 
Con esta directiva se puede mostrar un mensaje de bienvenida cuando un usuario se conecte al servidor FTP. ftpd_banner=”Bienvenido a mi servidor FTP”. 
anon_max_rate 
Se utiliza para limitar la tasa de transferencia a usuarios anónimos. En el siguiente ejemplo se limita la tasa de transferencia a los usuarios anónimos a 10Kb/s: 
anon_max_rate=1024 
local_max_rate 
Se utiliza para limitar la tasa de transferencia en bytes por segundo a los usuarios locales del servidor. En el siguiente ejemplo se limita la tasa de transferencia a 10Kb/s: 
local_max_rate=1024 
max_clients 
Indica el número máximo de clientes que podrán conectarse simultáneamente al servidor. En el siguiente ejemplo son 5: max_clients=5 

Hay que tener en cuenta que cada vez que se haga un cambio en el fichero de configuración de vsftpd se debe reiniciar el servidor con la siguiente orden: 
	# service vsftpd restart  o
# /etc/init.d/vsftpd restart

Otras opciones disponibles son stop (parar), reload (recarga), start (inicio).
