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

__anonymous_enable__ Si el valor de esta directiva es YES cualquier usuario se puede conectar al servidor dando el nombre de usuario anonymous o ftp. Por razones de seguridad se debe poner NO (anonymous_enable=NO) 

__local_enable__ Esta línea indica si se permite o no el acceso de usuarios locales a sus respectivas carpetas privadas. Si se permite el acceso habría que poner 
local_enable=YES (local_enable=NO en caso contrario). 

__chroot_local_user__ Permite enjaular a los usuarios dentro de su propio directorio personal. Si en el fichero de configuración aparece chroot_local_user=NO, entonces el usuario tiene acceso a todo el sistema de archivos, en función de los permisos asignados. Cuando un usuario local se conecta y en el fichero de configuración aparece chroot_local_user=YES, entonces enjaulamos a los usuarios dentro de su propio directorio personal, sin posibilidad de acceder a todo el sistema de ficheros. Se mejora por tanto la seguridad.

Con esta configuración ya tendríamos enjaulados a los usuarios en su carpeta home pero no podremos escribir necesitamos añadir: __allow_writeable_chroot=YES__
 
__write_enable__ Con esta directiva se puede permitir o denegar la subida de ficheros al servidor FTP. Si se permite que los usuarios suban archivos al servidor FTP habría que poner write_enable=YES.

__local_umask__ Se pueden establecer los permisos con los que quedará el archivo al subirlo al servidor FTP. local_umask=022 indicará que los permisos de los archivos serán 644, es decir, lectura y escritura para el propietario del fichero, y sólo lectura para el grupo y los demás.

__anon_upload_enable__ Esta directiva indica si los usuarios anónimos pueden cargar archivos en el servidor. anon_upload_enable=YES permite que los usuarios anónimos puedan subir ficheros. 

__anon_mkdir_write_enable__ Si esta directiva tiene el valor YES se permitirá la creación de directorios en el servidor. 

__ftpd_banner__ Con esta directiva se puede mostrar un mensaje de bienvenida cuando un usuario se conecte al servidor FTP. ftpd_banner=”Bienvenido a mi servidor FTP”. 

__anon_max_rate__ Se utiliza para limitar la tasa de transferencia a usuarios anónimos. En el siguiente ejemplo se limita la tasa de transferencia a los usuarios anónimos a 10Kb/s: 
anon_max_rate=1024

__local_max_rate__ Se utiliza para limitar la tasa de transferencia en bytes por segundo a los usuarios locales del servidor. En el siguiente ejemplo se limita la tasa de transferencia a 10Kb/s: 
local_max_rate=1024 

__max_clients__ Indica el número máximo de clientes que podrán conectarse simultáneamente al servidor. En el siguiente ejemplo son 5: max_clients=5 

Hay que tener en cuenta que cada vez que se haga un cambio en el fichero de configuración de vsftpd se debe reiniciar el servidor con la siguiente orden: 
```service vsftpd restart```  o ```/etc/init.d/vsftpd restart```

Otras opciones disponibles son stop (parar), reload (recarga), start (inicio).
#### INTERESANTE 
Es interesante averiguar si ya existe un servidor ftp instalado en nuestro servidor, para ello ejecutamos la orden siguiente (con un usuario con privilegios de administrador): 
```update-alternatives --list ftp ```
en nuestro caso obtenemos el siguiente resultado: 







Si quisiéramos incluir el nuevo servicio FTP en la lista de los disponibles, se ejecutará la siguiente orden: 
```update-alternatives --config ftp ```
y obtendríamos las posibles opciones o alternativas, debiendo seleccionar la que deseemos dejar como activa. 

### CONFIGURACIÓN DE NUESTRO FICHERO 
#### Ejecución del servidor. 
El servidor vsftpd puede ejecutarse como un proceso independiende, es decir, en standalone. Para ejecutarse el servidor como standalone deberá estar activada la siguiente directiva: 
__listen=YES__
Nota: Pero debemos ir con cuidado, ya que, dicha directiva no debe estar activada simultáneamente con la directiva __listen_ipv6__, que sirve para atender peticiones también como proceso independiente, pero para sockets ipv6. 
Recordar que socket podemos definirlo de forma sencilla como un mecanismo que identifica una conexión mediante una dirección IP y un número de puerto (bien sea con el protocolo IP versión 6 o versión 4). 
#### Gestión de usuarios. 
En este servidor, por defecto, los usuarios que pueden utilizar el servicio son los anónimos y los usuarios con cuenta en el sistema. Las siguientes líneas nos servirán para establecer ambos tipos de usuarios, es decir, por un lado los usuarios anónimos y por otro los usuarios que poseen cuenta en la máquina local. 

__anonymous_enable=YES__

__local_enable=YES__

Podemos observar que en el fichero de configuración, por defecto, la línea con la directiva para habilitar a los usuarios locales están comentada, es decir, que en caso de desear que los usuarios locales pueden acceder al servidor FTP, deberemos descomentarla. 

Volviendo a las 2 directivas anteriores, comentar que la primera línea, como podemos suponer, indica que cualquier usuario se puede conectar al servidor dando el nombre de usuario anonymous o ftp, y contraseña en blanco.

La segunda línea indica que los usuarios locales (usuarios con una cuenta local en la máquina) pueden conectarse al servidor. Para no permitir la conexión de estos usuarios podremos hacer 2 cosas, comentar la línea, ó igualar la directiva al valor NO. 

#### Descarga de archivos. 
Cuando un usuario anónimo (anonymous ó ftp) se conecta al servidor FTP, dicho usuario entrará al directorio home del usuario ftp. Por defecto el directorio home de dicho usuario es __/srv/ftp__.

Como se ha comentado anteriormente, dicho usuario se ejecuta bajo el entorno o ambiente de una “jaula” chroot, por tanto dicho directorio es el raíz para el usuario.

Los usuarios podrán descargarse archivos en la estructura del directorio /srv/ftp, según los permisos que posea el directorio y/o subdirectorios que se encuentren por debajo de él, y de los permisos de los archivos que se encuentren allí. 

Por otra parte cuando un usuario local se conecta tendremos 2 opciones para configurar su inicio de sesión: 

- __Se conecta como usuario del sistema__, en cuyo caso su directorio home es el propio que posee en la máquina, y tiene como directorio raíz el propio directorio raíz del sistema operativo completo. La descarga de archivos de este usuario vendrá dada por los permisos que posea cada fichero, pero podrán navegar y descargarse ficheros de cualquier sitio, siempre y cuando los permisos de los mismos se lo permitan. 
- __Se conecta como un usuario del sistema, pero se le cambia su directorio raíz a su directorio home__, con lo que no tiene disponible todo el directorio o árbol de directorios, sino que sólo podrá ver los archivos que existan en su directorio. Es decir lo que le vamos a hacer es “enjaularlo” dentro de su directorio home, al igual que hacíamos con los usuarios anónimos. 

Para poder activar la segunda opción, es decir, el enjaular al usuario local dentro de su propio directorio home, deberemos activar la siguiente directiva: 

__chroot_local_user=YES__

Como podemos ver en el fichero de configuración, dicha directiva esta comentada, obviamente es conveniente activarla. 

Si en el sistema nos interesa que ciertos usuarios no se queden “enjaulados” dentro de su propio directorio home, podríamos solucionarlo mediante la inclusión de dichas cuentas de usuario en un archivo usando las directivas siguientes: 

__chroot_list_enable=YES__ 

__chroot_list_file=/etc/vsftpd.chroot_lista__

La primera directiva indicará que existe una lista de usuarios que al conectarse no deben ser enjaulados mediante chroot, y la segunda directiva nos indica el nombre del fichero donde se encuentran dichos usuarios. Por defecto estas directivas están comentadas, y el fichero especificado no existe en nuestro sistema.
 
Por último, decir que también podemos habilitar la descarga de archivos en formato ASCII en el servidor, para conseguir este objetivo deberemos habilitar la directiva siguiente:

__ascii_download_enable=YES__

#### Carga de archivos para usuarios locales. 
Si queremos permitir que los usuarios locales suban o carguen archivos al servidor FTP habrá que habilitar la siguiente directiva: 
__Write_enable=YES__

Pero cuando un usuario cargue un archivo en el servidor, ¿qué permisos tendrá el fichero?. 
La contestación a esta pregunta es bastante fácil, nosotros podremos establecer mediante una directiva los permisos con los que quedarán los archivos que suban los usuarios. Dicha directiva es:

__local_umask=022__

La directiva hace referencia a la máscara (umask), y al igual que el comando umask nos indicará los permisos a la inversa. Es decir, esto nos indicará que los permisos de los archivos subidos serán 644 (lectura y escritura para el usuario propietario, y sólo lectura para el grupo propietario y para el resto de usuarios). Podríamos preguntarnos por qué no es 755, recordar que por defecto en los archivos no se va a activar el bit de ejecución, por tanto los permisos nos quedan como 644 (rwr—r).

También podemos habilitar la carga de archivos en formato ASCII en el servidor, para conseguir este objetivo deberemos habilitar la directiva siguiente:

__ascii_upload_enable=YES__

#### Carga de archivos para usuarios anónimos. 
Por defecto, la carga de archivos para los usuarios anónimos está desactivada. Para activarla deberemos habilitar la siguiente directiva:

__anon_upload_enable=YES__

Deberemos crear un directorio (por ejemplo entrada ó incoming), que tenga los permisos suficientes para que los usuarios puedan escribir en él. Con las siguientes órdenes se crea el directorio, se le da permisos y se visualizan dichos permisos (siempre como usuario root):

```mkdir /srv/ftp/incoming```

```chmod 777 /srv/ftp/incoming ```

```ls -l /srv/ftp```

Por defecto, los archivos cargados lo harán con permisos 600, y si queremos que un usuario anónimo pueda descargarlos se deberán cambiar los permisos 644. Es decir cuando coloquemos allí los archivos con los permisos 600, los usuarios anónimos no tendrán permisos para realizar ninguna tarea sobre dichos archivos, y mediante el establecimiento de los permisos 644 (rwr—r), estamos dando permisos de lectura para dichos archivos. Para hacer esto tenemos la directiva siguiente: 

__anon_umask=022__

Si desamos que los usuarios anónimos modifiquen los archivos que pertenecen a los usuarios anónimos y puedan crear directorios, tendremos que activar las siguientes directivas: 
__anon_other_write_enable=YES__
__anon_mkdir_write_enable=YES__

Tanto la directiva anon_umask como la directiva anon_other_write_enable no se encuentran en el fichero de configuración básico, pero podríamos añadirlas al fichero. 

#### Visualizar mensajes. 
Al conectarse un usuario al servicio FTP y hacer una cambio de directorio dentro del directorio raíz de descargas (en nuestro caso el directorio incoming), se puede visualizar un mensaje cuyo texto se almacena en el archivo .mensaje (podría llamarse cualquier otro nombre). 
Para hacer esto deberemos seguir los siguientes pasos:

- Crear un archivo .mensaje e incluir en él el texto que deseemos visualizar. 
- Incluir las siguientes directivas dentro del fichero de configuración __dirmessage_enable=YES message_file=.mensaje__

Por defecto en nuestro fichero de configuración si que nos viene activada la opción de visualización de un mensaje, pero no nos viene la opción de particularización del texto del mensaje. La directiva sobre el fichero del mensaje se la añadimos a mano. Recordar que deberemos crear el fichero e introducir el texto que deseemos. 

También se puede establecer un mensaje de bienvenida al servidor FTP cuando un usuario se conecta al mismo. Esto se realiza con la siguiente directiva: 
__ftpd_banner=”Bienvenido al servidor FTP de nombre”__

En este caso esta directiva está desactivada, y por tanto nos saldrá un mensaje por defecto al iniciar una conexión sobre nuestro servidor FTP. 

#### Registro de actividad. 
Mediante el registro de actividad podremos llevar un control sobre la actividad desarrollada en nuestro servidor FTP. Dicho registro se realiza mediante el archivo 
__/var/log/vsftpd.log__ y las directivas: 
__xferlog_enable=YES__
__xferlog_file=/var/log/vsftpd.log__
 
Para visualizar la información de actividad deberemos visualizar el fichero indicado, incluso lo podemos tener abierto visualizando online las últimas líneas del fichero, es decir, la actividad que se está registrando en este mismo momento. 

La segunda directiva en el fichero de configuración por defecto está comentada, dicho fichero es el estándar para almacenar los logs. En caso de que el log quisieramos guardarlo en otro fichero si que deberíamos descomentarla y modificar el nombre del fichero. 

#### Tiempos de conexión. 
También son importantes una serie de directivas que hacen referencia a las opciones de configuración sobre los tiempos de conexión de las diferentes conexiones de los usuarios.

Algunas de las directivas en cuestión son las siguientes: 

__accept_timeout=60__

__data_connection_timeout=120__

__idle_session_timeout=600__

La primera directiva no se encuentra en el fichero de configuración ejemplo, las otras 2 directivas si que se encuentran, pero están comentadas por defecto. La explicación de dichas directivas es la siguiente: 
● accept_timeou t: indica, en segundos, el tiempo para establecer una conexión en modo pasivo de un usuario remoto. Por defecto son 60 segundos. 
● data_connection_timeou t: indica, en segundos, el tiempo máximo que el servidor espera cuando una transferencia no progresa. Por defecto son 300 segundos. 
● idle_session_timeou t: indica, en segundos, el tiempo máximo que se concede a un usuario remoto que no está activo, es decir, ejecutando órdenes válidas para un servidor FTP. Pasado el tiempo indicado por esta directiva sin detectarse ejecuciones, se procederá a cortar la conexión. Por defecto son 300 segundos. 
Estas directivas existirán en cualquier servidor FTP, aunque los tiempo por defectos dependerán del servidor en cuestión, de la distribución, etc. 
Listado recursivo. 
El paquete vsftpd es bastante sencillo y no dispone de excesivas opciones para moverse por dentro del directorio raíz. Existe una directiva que nos permitirá el listado recursivo dentro de un directorio y que puede ser útil en algunos casos. Dicha directiva es: 
ls_recurse_enable=YES 

EJECUCIÓN DEL SERVICIO VSFTPD. 
Tras haber realizado la configuración del servidor FTP, sólo nos queda lanzar el servicio, y ésto lo haremos mediante la orden: 
# service vsftpd start     o
# /etc/init.d/vsftpd start 
Otras opciones que tenemos sobre dicho servidor son: 
● stop : nos servirá para parar el servicio. 
● reload : nos servirá para recargar los parametros de configuración. 
● restar t: nos servirá para parar y volver a iniciar el servicio en una sola orden. 
Una vez ejecutado el comando anterior, el servidor FTP ya lo tenemos ejecutándose y escuchando peticiones FTP en el puerto TCP 21. 

A continuación 2 ejemplos de 2 conexiones contra un servidor FTP, una conexión se realiza mediante modo comando, y otra utilizando como cliente un navegador web. 
