
# Reenvío de Puertos en VirtualBox

El reenvío de puertos en VirtualBox permite acceder a servicios dentro de una máquina virtual desde el host (o incluso desde otros equipos en la red). Es especialmente útil cuando necesitas acceder a un servidor web o SSH en la máquina virtual.

## Pasos para configurar el reenvío de puertos:

1. **Abrir VirtualBox y seleccionar la máquina virtual:**
   - Inicia VirtualBox y selecciona la máquina virtual en la que deseas configurar el reenvío de puertos.
   
2. **Acceder a la configuración de red de la máquina virtual:**
   - Haz clic derecho sobre la máquina virtual y selecciona **Configuración**.
   - En el panel de configuración, selecciona la opción **Red**.

3. **Configurar el adaptador de red:**
   - Asegúrate de que el **Adaptador 1** está habilitado y configurado en modo **NAT** (Network Address Translation).

4. **Acceder a las opciones avanzadas:**
   - En la sección del adaptador de red, haz clic en el botón de **Avanzadas** para desplegar más opciones.

5. **Agregar reglas de reenvío de puertos:**
   - Haz clic en **Reenvío de puertos** para abrir el menú de configuración.
   - En esta ventana, puedes agregar nuevas reglas de reenvío de puertos.

6. **Configurar una regla de reenvío de puertos:**
   - En el campo de **Nombre**, puedes escribir un nombre descriptivo para la regla.
   - En **Protocolo**, selecciona TCP o UDP dependiendo de tu necesidad.
   - En el campo **IP del anfitrión**, puedes dejarlo en blanco o usar `127.0.0.1` si solo deseas acceder desde el host.
   - En **Puerto del anfitrión**, introduce el puerto del host que se usará para redirigir el tráfico.
   - En **IP del invitado**, puedes usar `10.0.2.15` (IP por defecto de NAT).
   - En **Puerto del invitado**, introduce el puerto en la máquina virtual al que deseas redirigir el tráfico (por ejemplo, el puerto 80 para un servidor web o el 22 para SSH).

7. **Guardar y reiniciar la máquina virtual:**
   - Una vez configurado el reenvío de puertos, guarda los cambios y reinicia la máquina virtual para que la nueva regla surta efecto.

## Ejemplo de configuración

Supongamos que quieres acceder a un servidor web que corre en el puerto 80 de tu máquina virtual:

- **Protocolo:** TCP
- **IP del anfitrión:** 127.0.0.1
- **Puerto del anfitrión:** 8080 (esto permitirá acceder al servidor web desde `http://localhost:8080`)
- **IP del invitado:** 10.0.2.15
- **Puerto del invitado:** 80

Con esta configuración, cualquier tráfico dirigido a `localhost:8080` en el sistema host será reenviado al puerto 80 de la máquina virtual, donde el servidor web está corriendo.

## Comprobación

Para comprobar que el reenvío de puertos funciona, abre un navegador en tu máquina host y accede a `http://localhost:<puerto del anfitrión>`. Si el servidor en la VM está correctamente configurado, deberías ver la respuesta del servidor web.

---

¡Y eso es todo! Ahora puedes acceder a servicios que corren en la máquina virtual desde tu máquina host usando reenvío de puertos.
