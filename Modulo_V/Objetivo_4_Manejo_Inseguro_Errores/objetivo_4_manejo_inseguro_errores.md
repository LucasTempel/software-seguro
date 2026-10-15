# Objetivo 4 

La diferencia principal entre un **mensaje de error genérico** y un **Stack Trace** está en la información que le otorgamos al cliente:

* **Error genérico:** Es una respuesta neutral y limpia pensada para el cliente (por ejemplo: "Ocurrió un error inesperado, intente más tarde" o un código 500 por ej). Cumple con avisar que algo falló, pero sin filtrar ningún dato técnico ni interno del sistema.
* **Stack Trace (traza de la pila):** Es el reporte técnico completo que muestra la secuencia de ejecución: la pila de métodos, clases, archivos y los números de línea exactos donde la aplicación se rompió o atrapó la excepción.

Exponer un stack trace detallado en producción es recontra peligroso porque le facilita al  atacante muchas cosas, como:

* **Revela versiones y componentes:** Expone las versiones exactas del framework, del lenguaje o de las bibliotecas utilizadas. Con esa información, el atacante simplemente busca los CVEs conocidos de esa versión para atacarla de forma directa.
* **Divulga rutas internas y arquitectura:** Muestra la estructura de carpetas del servidor y rutas del sistema de archivos, lo cual facilita planificar ataques como un Path Traversal o ubicar archivos de configuración.
* **Confirma vectores de inyección:** Si la falla fue provocada por una consulta rota, el stack trace muchas veces devuelve la estructura de la consulta o el motor de base de datos que se usa

### Como lo mitigamos?? 
La solución principal es desactivar el modo de depuracion (debug) en el entorno de producción. Los errores deben gestionarse con bloques/middlewares estructurados de excepciones para devolver siempre respuestas neutrales: ni muy confusas para el usuario legítimo, ni con información técnica valiosa para un atacante. Toda la traza completa y el detalle técnico deben quedar guardados únicamente en logs internos dentro del servidor.
