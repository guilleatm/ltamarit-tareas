#### Tarea DNS bind

Queremos instalar un servidor DNS local en nuestra intranet que nos permita gestionar los nombres de las máquinas y recursos de nuestra red. Las características del servidor DNS que queremos instalar son las siguientes:

El servidor es el servidor Linux con una IP fija, la IP ha de ser acorde con tu red local. En nuestro caso usaremos la máquina configurada en la primera quincena SerLApellido con la IP: 192.168.20.5

### PASO 1. Instalar bind en el servidor.
```
apt-get install bind9
```
Recuerda: en ocasiones aparecen problemas en la instalación de paquetes porque no tenemos actualizada la base de datos:
```
apt-get update
```
### PASO 2. Configurar archivos de bind9

Los ficheros de configuración de bind están en el directorio /etc/bind.

Ficheros db: contienen 3 zonas inversas: 0.in-addr.arpa, 127.in-addr.arppa y 255.in-addr.arpa. Estas tres zonas están configuradas y hacen referencia al nodo local. Además contiene una zona directa vacía db.empty que usaremos como punto de partida.

Ficheros named: hacen referencia a los ficheros de configuración del servidor. Podría configurarse todo en el fichero named.conf pero no se hará así, el fichero named.conf no se debe tocar, editaremos el named.conf.options para configurar.

Importante señalar que el proceso del servidor DNS se llama named y se ejecuta como el usuario named.

Editar el archivo /etc/bind/named.conf.options y en la sección forwarders añadir las IPs de dos servidores DNS (por ejemplo los proporcionados por tu proveedor de Internet) donde redirigir las peticiones DNS.

Recuerda que para editar existen varios programas: nano, vi, gedit
```
$sudo nano /etc/bind/named.conf.options
```

Con esta opción se especifica que las consultas que no pueda responder nuestro servidor sean reenviadas a la IP 8.8.8.8.

Comenta la línea que pone dnssec-verification auto; o pon no en vez de auto para que funcione la resolución de nombres públicos de Internet,

Edita el archivo /etc/bind/named.conf y comenta la linea que hace referencia a named.conf.default-zones.

El servidor DNS tiene muchas más opciones que permiten ajustar la configuración.
