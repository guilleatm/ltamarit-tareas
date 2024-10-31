# Guía de Instalación de HTTPS en Apache en Ubuntu 22.04

Este documento proporciona una guía paso a paso para habilitar HTTPS en un servidor Apache en Ubuntu 22.04 usando un certificado SSL de Let's Encrypt.

---

## Requisitos Previos

- **Sistema Operativo:** Ubuntu 22.04.
- **Apache Instalado:** Asegúrate de tener Apache instalado. Si no está instalado, puedes hacerlo con:
  ```bash
  sudo apt update
  sudo apt install apache2

- Dominio Configurado: Debes tener un dominio apuntando a tu servidor.
- Permisos de Usuario: Asegúrate de tener acceso sudo para ejecutar los comandos necesarios.

Pasos para Instalar HTTPS en Apache
### 1. Obtener un Certificado SSL/TLS
Para que nuestro servidor pueda servir páginas seguras con el protocolo https, necesita un certificado. Dicho certificado permitirá que nuestro servidor utilice cifrado asimétrico para intercambiar las claves de cifrado con los clientes, antes de iniciar una trasmisión segura de información.

Inicialmente, el cliente deberá aceptar el certificado del servidor, ya que generaremos un certificado autofirmado. Si queremos evitarlo, deberíamos contratar un certificado a una entidad certificadora confiable, pero tiene un coste que no merece la pena soportar en un entorno educativo. Para generar nuestro certificado autofirmado, ejecutaremos el comando:

```$sudo make-ssl-cert /usr/share/ssl-cert/ssleay.cnf /etc/ssl/certs/apache2.pem```

Durante la ejecución de comando make-ssl-cert, quizás nos pregunte algunas sencillas preguntas como el host name. Podemos poner nombre de servidor websegura.sercamp.org y dejar en blanco el nombre alternativo. Después se creará el archivo

`/etc/ssl/certs/apache.pem`

que contiene las claves que permitirán al servidor utilizar cifrado asimétrico (compruebalo).

El siguiente paso será configurar un servidor virtual para que utilice dicho certificado.
### 1. Crear servidor virtual seguro en apache
Primero crearemos una carpeta de nombre 'websegura' dentro de '/var/www'.

```sudo mkdir /var/www/websegura/```

Dentro de esta carpeta crea un index.html dando un mensaje de bienvenida a una zona segura.

Dicha carpeta será el raíz de documentos (DocumentRoot) de nuestro servidor virtual seguro, de modo que todo lo que coloquemos en dicha carpeta deba ser accedido vía
'https'. Eso lo indicaremos más adelante mediante el parámetro SSLRequireSSL.

Después debemos crear el servidor virtual en Apache. Dicho servidor virtual dispondrá de una url de acceso diferente a la de nuestra web principal y será accesible mediante
https, por tanto tendremos que habilitar SSL e indicar la ruta del archivo que contiene el certificado

Todo ello lo haremos creando un nuevo host virtual tal y como sabemos, pero esta vez en lugar de copiarnos el archivo 000-default.conf nos copiaremos el default-ssl.conf de
sites-availables
``` cp /etc/apache/sites-available/default-ssl.conf /etc/apache2/sites-available/websegura.conf

En el archivo de configuración .conf de nuestro nuevo sitio configuraremos:

- ServerAdmin
- ServerName

- DocumentRoot
- SSLCertificateFile., aquí indicaremos cómo se llama el certificado que acabamos de crear:

SSLCertificateFile /etc/ssl/certs/apache2.pem

Comentamos con una # la línea:

#SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key


### 3. Habilitar puerto
El protocolo https utiliza el puerto 443, por lo tanto, tendremos que habilitar dicho puerto para que Apache lo utilice. Si ya está habilitado el puerto 443, no hacer nada.

Añadir en /etc/apache2/ports.conf
Listen 443

### 4. Habilitar el módulo ssl del servidor apache:
```sudo a2enmod ssl```

### 5. Y recargamos apache:
```$sudo systemctl reload apache2.service```

### 1. Instalar Certbot

Certbot es una herramienta de Let's Encrypt que facilita la obtención de certificados SSL gratuitos.

```sudo apt update```

```sudo apt install certbot python3-certbot-apache```

El siguiente paso será configurar un servidor virtual para que utilice dicho certificado


# 2. Obtener el Certificado SSL EN PROCESO

Ejecuta Certbot con el plugin de Apache para obtener y configurar automáticamente el certificado SSL. Asegúrate de reemplazar tudominio.com con tu nombre de dominio.

```sudo certbot --apache -d tudominio.com -d www.tudominio.com```

- Durante el proceso, Certbot te pedirá un correo electrónico y aceptación de términos de servicio.
- Certbot configurará Apache para que redirija automáticamente el tráfico HTTP a HTTPS.

### 3. Verificar la Configuración de HTTPS

Una vez completada la configuración, puedes verificar el estado del servicio Apache:

```sudo systemctl status apache2```

Luego, abre tu navegador y visita https://tudominio.com para confirmar que HTTPS esté funcionando.

### 4. Configurar la Renovación Automática

Let's Encrypt SSL tiene una validez de 90 días, pero Certbot incluye un cron job para renovar automáticamente los certificados. Puedes verificar la renovación manualmente con:

```sudo certbot renew --dry-run```

### Resolución de Problemas Comunes

Problema de Firewall: Asegúrate de que el firewall permita tráfico en los puertos 80 y 443.

```sudo ufw allow 'Apache Full'```

Error de Dominio: Asegúrate de que el dominio esté apuntando correctamente a la dirección IP de tu servidor.

