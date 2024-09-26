## Tarea DNS bind

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
![Imagen bind](/img/dns1.png)

Con esta opción se especifica que las consultas que no pueda responder nuestro servidor sean reenviadas a la IP 8.8.8.8.

Comenta la línea que pone dnssec-verification auto; o pon no en vez de auto para que funcione la resolución de nombres públicos de Internet,

Edita el archivo /etc/bind/named.conf y comenta la linea que hace referencia a named.conf.default-zones.

El servidor DNS tiene muchas más opciones que permiten ajustar la configuración.

### PASO 3. Crear una zona primaria. Vamos a crear la zona sercamp.org

Edita el archivo /etc/bind/named.conf.local. Ejemplo:

![Imagen bind](/img/dns2.png)

Recuerda: todas las lineas que comienzan por // son comentarios, no se procesan

Con Type especifico que voy a crear una zona primaria

Con file especifico el fichero que contiene los datos de la zona

Ahora tenemos que crear los archivos de la zona:

Para crear los archivos de zona nos copiaremos el archivo de zona db.empty:
```
cp /etc/bind/db.empty /etc/bind/sercamp.org.db
cp /etc/bind/db.empty /etc/bind/db.20.168.192
```
### PASO 4. Creación de las zonas.

Vamos a tener los siguientes FQDN:  
- El servidor DNS con la IP que tiene el servidor 192.168.20.5 se llama dns.sercamp.org
- El servidor web llamado www.sercamp.org con la IP: 192.168.20.10. Es un servidor ficticio que de momento no existe.
- Un servidor ftp que se llame ftp.sercamp.org y que está también en 192.168.20.10 (esto es ficticio)
- Servidor para recibir los correos que se llama mail.sercamp.org y que está en 192.168.20.11 (esto es ficticio)

Además queremos nombrar a tres clientes:  
- aula5pc2.sercamp.org (192.168.20.202) (ficticio)
- aula5pc3.sercamp.org (192.168.20.203) (ficticio)
- aula5pc4.sercamp.org (192.168.20.204) (ficticio)

Recuerda: Con el atributo CNAME especificamos un alias para la dirección 192.168.20.10 (la misma IP que tenga el servidor www), que atenderá a los nombres: www y ftp.

#### Vamos a crear la zona directa

Editamos el archivo /etc/bind/sercamp.org.db para definir todos los registros. Este ejemplo está incompleto, sólo es una referencia, debes completarlo con todos los registros. (Al servidor lo he llamado “dns” y quiero que las notificaciones lleguen al usuario root)

![Imagen bind](/img/dns3.png)

El carácter @ puede sustituir a la zona, es decir es lo mismo poner

sercamp.org. IN SOA dns.sercamp.org root.sercamp.org

@ IN SOA dns.sercamp.org root.sercamp.org

SOA es un registro obligatorio

NS es el servidor de nombres, aunque aparece en el SOA es necesario especificarlo de nuevo. Podría haber varios NS.

A: se han especificado dos direcciones IP, la primera se refiere al servidor de nombres, la segunda es un equipo. Los nombres se pueden especificar aquí con el FQDN o no. En el ejemplo aparecen con el FQDN pero es lo mismo poner cualquiera de estas dos cosas:

dns.sercamp.org. IN A 192.168.20.5
dns IN A 192.168.20.5

Si no lleva el punto final quiere decir que no está completamente cualificado (FQDN). A la hora de procesar el fichero es lo mismo poner

dns
dns.sercamp.org.

Es decir, si no lleva el punto final, no está completamente cualificado, así que se le añadirá el dominio en nuestro caso sercamp.org

El registro «MX» nos indica que el servidor de correo del dominio sercamp.org es mail.sercamp.org podríamos definir varios servidores de correo con la misma o distinta prioridad (10). El registro «A» de esta misma zona DNS nos indica que mail.sercamp.org. se encuentra en la IP 192.168.20.5.

No es posible apuntar los registros MX directamente a una IP. Lo que debe hacerse es definir primero un registro «A» y posteriormente apuntar el registro MX a ese registro «A».

#### Vamos a crear la zona de resolución inversa.

Editamos el archivo: /etc/bind/db.20.168.192. Ejemplo incompleto
![Imagen bind](/img/dns4.png)

