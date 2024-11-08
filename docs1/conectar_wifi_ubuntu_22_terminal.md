
# Instrucciones para Conectar a una Red Inalámbrica en Ubuntu 22.04 desde la Terminal

---

## Prerrequisitos

1. Asegúrate de que tu sistema tenga una tarjeta de red inalámbrica y que esté activada.
2. Es necesario tener `nmcli` (Network Manager Command Line Interface), una herramienta de administración de redes que viene instalada por defecto en Ubuntu.

## Pasos para Conectar a una Red Wi-Fi

### 1. Verificar la Interfaz de Red Inalámbrica

Primero, comprueba el nombre de la interfaz de red inalámbrica ejecutando el siguiente comando:

```bash
nmcli device status
```

Busca una interfaz de tipo `wifi` en la lista, generalmente nombrada como `wlan0` o `wlp2s0`.

### 2. Escanear Redes Disponibles

Para ver una lista de las redes Wi-Fi disponibles, usa:

```bash
nmcli device wifi list
```

Esto mostrará todas las redes Wi-Fi disponibles junto con detalles como SSID, señal, seguridad, etc.

### 3. Conectar a la Red Wi-Fi

Usa el siguiente comando para conectarte a una red Wi-Fi específica:

```bash
nmcli device wifi connect "SSID" password "contraseña"
```

- **SSID**: Reemplázalo con el nombre de la red Wi-Fi a la que deseas conectarte.
- **contraseña**: Reemplázalo con la contraseña de la red.

**Ejemplo:**

```bash
nmcli device wifi connect "MiRedWiFi" password "MiContraseñaSegura"
```

### 4. Verificar la Conexión

Para confirmar que te has conectado correctamente, puedes ejecutar:

```bash
nmcli connection show --active
```

Este comando mostrará las conexiones activas. Si estás conectado a la red, debería aparecer en la lista.

---

## Solución de Problemas

- Si recibes un error indicando que `NetworkManager` no está funcionando, inicia el servicio con:

  ```bash
  sudo systemctl start NetworkManager
  ```

- Si necesitas desconectar una red, puedes hacerlo con:

  ```bash
  nmcli connection down id "SSID"
  ```

---

## Más Información

Para obtener más detalles y opciones avanzadas, puedes consultar la ayuda de `nmcli`:

```bash
nmcli --help
```
