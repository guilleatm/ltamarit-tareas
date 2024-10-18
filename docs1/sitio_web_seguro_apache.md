
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

Primero, necesitas un certificado SSL/TLS. Puedes obtener uno gratuito de **Let's Encrypt** o comprar uno de una autoridad certificadora como **DigiCert** o **Comodo**.

- Para obtener uno de Let's Encrypt, puedes usar una herramienta como **Certbot**, que automatiza el proceso de instalación del certificado.

### 2. Instalar el Certificado SSL en Apache

Una vez que tienes el certificado, lo instalas en tu servidor Apache.

1. **Habilitar el módulo SSL en Apache**:
   
   Ejecuta el siguiente comando para habilitar SSL:

   ```bash
   sudo a2enmod ssl
   sudo systemctl restart apache2
   ```

2. **Crear un VirtualHost seguro en Apache**:

   Edita o crea el archivo de configuración de tu dominio en `/etc/apache2/sites-available/`. Por ejemplo, si tu sitio es `sercamp.org`, podrías crear o modificar el archivo `sercamp.org-le-ssl.conf`:

   ```bash
   sudo nano /etc/apache2/sites-available/sercamp.org-le-ssl.conf
   ```

   Y añade la configuración del VirtualHost con SSL habilitado:

   ```apache
   <VirtualHost *:443>
       ServerName www.websegura.sercamp.org
       ServerAlias webegura.sercamp.org

       DocumentRoot /var/www/websegura

       SSLEngine on
       SSLCertificateFile /etc/letsencrypt/live/websegura.sercamp.org/fullchain.pem
       SSLCertificateKeyFile /etc/letsencrypt/live/websegura.sercamp.org/privkey.pem

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
