
## Definiciones

Los ataques de **fuerza bruta** consisten en probar combinaciones de caracteres de forma exhaustiva hasta obtener el resultado deseado, sin ninguna estrategia adicional. Traspolado a un login, significa probar combinaciones indefinidamente hasta dar en la tecla. Sin embargo, estos ataques suelen ser muy ineficientes en la práctica: dependiendo del alfabeto disponible y la longitud de la clave, el dominio de contraseñas posibles es enorme, lo que se traduce en un costo de tiempo y recursos altísimo.

Para resolver esa ineficiencia se emplean los **ataques de diccionario**. En lugar de probar todas las combinaciones posibles letra por letra, utilizan listas preensambladas (formadas muchas veces por filtraciones de bases de datos anteriores) para adivinar la contraseña. En otras palabras, prueban con las contraseñas más utilizadas esperando que la víctima haya elegido una de ellas, reduciendo drásticamente el número de intentos.

Si bien a primera impresión el diccionario parece mejor, la realidad es que explotan debilidades distintas: mientras la fuerza bruta (por su costo) termina siendo útil solo contra contraseñas cortas, el diccionario busca atacar directamente las tendencias humanas y la previsibilidad en la creación de contraseñas.

## Mecanismos de Defensa

Para mitigar estas automatizaciones, los desarrolladores pueden implementar los siguientes controles (apoyados siempre en la supervisión de logs para buscar patrones atípicos):

* **Rate Limiting:** Limita la cantidad de intentos de inicio de sesión permitidos en un periodo de tiempo, ya sea por dirección IP o por cuenta de usuario, el típico contador tras un par de intentos fallados.
* **Bloqueo de cuentas:**  Banea el acceso a una cuenta después de superar un numero x de intentos fallidos.
* **CAPTCHA / reCAPTCHA:** Prueba de Turing en búsqueda de limitar el acceso a bots.
* **Autenticación Multifactor:** Añade una segunda capa de seguridad (como un código al celular o un token).
* **Monitoreo proactivo de anomalías:** bloquear ips sospechosas frente una serie de intentos fallidos en los logs.