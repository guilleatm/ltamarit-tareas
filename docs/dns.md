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
