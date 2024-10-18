
# ¿Qué es un sitio web seguro y cómo configurarlo en Apache?

Un **sitio web seguro** es aquel que protege la información entre el usuario y el servidor, evitando que sea robada o alterada durante la transmisión. La seguridad se basa en varias prácticas, pero una de las más importantes es el uso de HTTPS (Protocolo de Transferencia de Hipertexto Seguro), que cifra la información utilizando un certificado SSL/TLS.

## Características de un sitio web seguro:

1. **Cifrado (HTTPS):** La información que viaja entre el navegador y el servidor está cifrada. Esto evita que terceros puedan leer los datos.
2. **Certificado SSL/TLS:** Un certificado SSL/TLS garantiza que el sitio web es auténtico y que la información enviada está protegida. Lo notarás por el candado en la barra de direcciones del navegador.
3. **Actualizaciones y parches:** El servidor y las aplicaciones del sitio deben estar actualizados para evitar vulnerabilidades.
4. **Políticas de seguridad (CSP, HSTS):** Añaden reglas que protegen contra ataques como la inyección de código o el secuestro de sesiones.

## Configurar un sitio web seguro en Apache

Para configurar un sitio web seguro en **Apache** (el servidor web), los pasos clave son los siguientes:

### 1. Obtener un Certificado SSL/TLS

Para que nuestro servidor pueda servir páginas seguras con el protocolo https, necesita un certificado. Dicho certificado permitirá que nuestro servidor utilice cifrado asimétrico para intercambiar las claves de cifrado con los clientes, antes de iniciar una trasmisión segura de información.

Inicialmente, el cliente deberá aceptar el certificado del servidor, ya que generaremos un certificado autofirmado. Si queremos evitarlo, deberíamos contratar un certificado a una entidad certificadora confiable, pero tiene un coste que no merece la pena soportar en un entorno educativo. Para generar nuestro certificado autofirmado, ejecutaremos el comando:

$sudo make-ssl-cert /usr/share/ssl-cert/ssleay.cnf /etc/ssl/certs/apache2.pem

Durante la ejecución de comando make-ssl-cert, quizás nos pregunte algunas sencillas preguntas como el nombre del servidor, etc. Podemos poner nombre de servidor websegura.sercamp.org y dejar en blanco el nombre alternativo. Después se creará el archivo /etc/ssl/certs/apache.pem que contiene las claves que permitirán al servidor utilizar cifrado asimétrico (compruebalo).

El siguiente paso será configurar un servidor virtual para que utilice dicho certificado.

### 2. Instalar el Certificado SSL en Apache

Una vez que tienes el certificado, lo instalas en tu servidor Apache.

1. **Habilitar el módulo SSL en Apache**:
   
   Ejecuta el siguiente comando para habilitar SSL:

   ```bash
   sudo a2enmod ssl
   sudo systemctl restart apache2
   ```

2. **Crear un VirtualHost seguro en Apache**:

   Primero crearemos una carpeta de nombre 'websegura' dentro de '/var/www/html'.

   $sudo mkdir /var/www/html/websegura/

Dentro de esta carpeta crea un index.html dando un mensaje de bienvenida a una zona segura. Dicha carpeta será el raíz de documentos (DocumentRoot) de nuestro servidor virtual
seguro, de modo que todo lo que coloquemos en dicha carpeta deba ser accedido vía 'https'. Eso lo indicaremos más adelante mediante el parámetro SSLRequireSSL.

   Edita o crea el archivo de configuración de tu dominio en `/etc/apache2/sites-available/`. Por ejemplo, si tu sitio es `websegura.sercamp.org`, podrías crear o modificar el archivo `websegura.conf`:

   ```bash
   sudo nano /etc/apache2/sites-available/websegura.conf
   ```

   Y añade la configuración del VirtualHost con SSL habilitado:

   ```apache
   <VirtualHost *:443>
       ServerName www.websegura.sercamp.org
       ServerAlias websegura.sercamp.org

       DocumentRoot /var/www/websegura

       SSLEngine on
       SSLCertificateFile /etc/ssl/certs/apache2.pem
       
       <Directory /var/www/websegura>
           Options Indexes FollowSymLinks
           AllowOverride All
           Require all granted
       </Directory>

       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>
   ```

   - **SSLCertificateFile**: Es el archivo del certificado.
   - **SSLCertificateKeyFile**: Es la clave privada del certificado.
   - El puerto `443` es el puerto estándar para HTTPS.

### 2. Reiniciar Apache

Después de hacer estos cambios, reinicia el servicio Apache para que los cambios surtan efecto:

```bash
sudo systemctl restart apache2
```
