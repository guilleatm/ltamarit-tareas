
# Instrucciones para Conectar a una Red Inalámbrica en Windows 11 desde la Línea de Comandos

Este documento ofrece instrucciones detalladas para conectarse a una red Wi-Fi en Windows 11 utilizando la línea de comandos (CMD).

---

## Prerrequisitos

- Asegúrate de que tu dispositivo tenga una tarjeta de red inalámbrica y que esté activada.
- Abre la línea de comandos con privilegios de administrador para evitar problemas de permisos en algunos comandos.

## Pasos para Conectar a una Red Wi-Fi

### 1. Verificar el Estado de la Interfaz Wi-Fi

Para comprobar el estado de tu adaptador inalámbrico, abre la línea de comandos y ejecuta:

```cmd
netsh interface show interface
```

Busca una interfaz de tipo `Wi-Fi`. Si el estado es `Desconectado` o `Deshabilitado`, asegúrate de habilitar el adaptador antes de proceder.

### 2. Listar Redes Wi-Fi Disponibles

Para ver las redes Wi-Fi disponibles, ejecuta el siguiente comando:

```cmd
netsh wlan show networks
```

Este comando mostrará todas las redes disponibles junto con el nombre (SSID) y el tipo de seguridad.

### 3. Crear un Perfil de Red Wi-Fi

Si es la primera vez que te conectas a esta red, deberás crear un perfil. Usa el siguiente comando, reemplazando `SSID` y `contraseña` con el nombre de la red y la clave de acceso:

```cmd
netsh wlan add profile filename="C:\Ruta\al\perfil.xml"
```

Alternativamente, puedes conectar directamente sin un perfil (esto requiere ejecutar con permisos elevados):

```cmd
netsh wlan connect name="SSID" ssid="SSID" keyMaterial="contraseña"
```

**Ejemplo:**

```cmd
netsh wlan connect name="MiRedWiFi" ssid="MiRedWiFi" keyMaterial="MiContraseñaSegura"
```

### 4. Confirmar la Conexión

Para confirmar que estás conectado correctamente, ejecuta:

```cmd
netsh wlan show interfaces
```

Este comando mostrará los detalles de la conexión actual, incluyendo el SSID, la intensidad de la señal y otros datos relevantes.

---

## Solución de Problemas

- Si recibes un mensaje de error, verifica que el nombre de red y la contraseña sean correctos.
- Si no puedes conectarte, intenta reiniciar el servicio WLAN con los siguientes comandos:

  ```cmd
  net stop wlansvc
  net start wlansvc
  ```

- Si necesitas desconectarte de una red, usa:

  ```cmd
  netsh wlan disconnect
  ```

---

## Información Adicional

Para obtener más detalles y opciones avanzadas, puedes consultar la ayuda de `netsh wlan`:

```cmd
netsh wlan help
```

---

Con estos pasos, deberías poder conectarte a una red Wi-Fi en Windows 11 usando solo la línea de comandos.
