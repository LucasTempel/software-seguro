## Condicion de carrera

Una condición de carrera ocurre cuando el resultado de una acción depende del orden o los tiempos en los que se ejecutan múltiples procesos en el servidor. 

Esta vulne esta presente en las transacciones. Donde en particular, podemos presenciar dos momentos principales:

1. **Check (Verificación):** Consulta la base de datos para ver si el la situacion.
2. **Update (Actualización):** Si el chequeo fue exitoso, aplica la actividad de la transaccion (como aplicar el descuento o restar el stock) y recién ahí actualiza el registro en la base de datos.

El problema de seguridad aparece porque entre que el servidor lee que el elemento está disponible y el momento en que escribe en la base de datos que ya se usó/vendió/consumió, existe una pequeña ventana de tiempo (unos pocos milisegundos). Si enviamos peticiones una detrás de otra de forma secuencial, la primera valida y obtiene el elemento, y las siguientes van a rebotar. Pero si logramos meter varias peticiones en paralelo dentro de esa misma milésima de segundo, todas van a leer que el elemento "sigue disponible" antes de que cualquiera de ellas lo dé de baja, logrando obtener el elemento múltiples veces.

## Explotación con herramientas y sincronización (Last-Byte vs Single-Packet)

En la práctica, abrir 50 pestañas o mandar 50 peticiones comunes no suele funcionar debido al *jitter* de red (la variación de milisegundos que sufre cada paquete al viajar por Internet, haciendo que uno siempre llegue antes que el otro). Para resolver esto y lograr que las 50 solicitudes golpeen al servidor al mismo tiempo, se emplean herramientas especializadas como **Turbo Intruder** o el envío en paralelo del **Repeater de Burp Suite**:

* **Sincronización por último byte (HTTP/1.1):** En HTTP/1 cada petición viaja por una conexión TCP separada. La herramienta abre las 50 conexiones, envía casi toda la petición HTTP (cabeceras y cuerpo), pero retiene el último byte. El servidor se queda esperando ese dato final sin procesar nada. Cuando las 50 conexiones están listas, la herramienta libera ese último byte en todas a la vez, minimizando la diferencia de llegada. Para desarrollar este ataque podemos usar las herramientas: 
  * Turbo Intruder (usando compuertas con gate en sus scripts).
  * Race-the-Web (herramienta por CLI en Go que retiene el último byte a nivel de sockets). 



* **Single-Packet Attack (HTTP/2):** Es la técnica más moderna y precisa. Como HTTP/2 permite multiplexar múltiples peticiones dentro de una misma conexión TCP, la herramienta empaqueta las 50 solicitudes dentro de **un único paquete TCP**. Al llegar al servidor, el sistema operativo lee el paquete entero y despierta a todos los hilos del backend en el mismo instante, reduciendo el jitter prácticamente a cero.
  * Burp Suite Repeater (con la función nativa "Send group in parallel (single-packet attack)" desde la interfaz gráfica).
  *   Turbo Intruder (configurando el motor en modo Engine.HTTP2 para meter los streams en un único paquete TCP).
