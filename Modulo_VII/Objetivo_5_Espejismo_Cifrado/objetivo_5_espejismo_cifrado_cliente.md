Si bien, la informacion provista en el frontend puede ser brillante, "muy" segura, intentar frenar a un atacante cifrando en el frontend no sirve de nada, porque no necesita descifrar el tráfico que ve en Burp Suite: tiene el control de la memoria y del código del navegador antes de que el paquete sea cifrado y enviado a la red.

## Como se realizaria el "ataque"?


1. **Ingeniería inversa con DevTools (F12):** Abre la pestaña *Sources* del navegador, busca el archivo JavaScript que procesa el formulario y lo desminifica. Con esto identifica la función que realiza el cifrado (por ejemplo, `encryptPayload()`) y las claves o certificados públicos que utiliza.
2. **Manipulación previa al cifrado:** En lugar de interceptar la petición en el proxy cuando ya viaja encriptada, coloca un punto medio en el navegador justo antes de que se ejecute la función de cifrado. Cuando la ejecución se detiene, modifica las variables en memoria (como cambiar el monto a transferir o un identificador de cuenta) y le da reanudar. El propio navegador se encargará de cifrar los datos ya manipulados con el formato legítimo.
3. **Reutilización del algoritmo:** El atacante también puede invocar la función de cifrado directamente desde la consola del navegador pasándole los valores que desee, o copiar la lógica a un script externo en Python/Node para automatizar el envío de payloads cifrados directamente al backend.

Si el backend se limita a descifrar el paquete y no valida la coherencia de los datos ni los permisos del usuario en el servidor, procesará la transacción manipulada dando por hecho que es válida simplemente "porque venía cifrada".

