### Diferencias entre una inyeccion Boolean-Based-Blin vs una Time-Based Blind

Una inyección SQL ciega ocurre cuando laapp  es vulnerable pero no devuelve datos de las tablas ni errores en pantalla en la response, obligándonos a inferir la información mediante preguntas y condicionales. Dentro de las tecnicas podemos encontrar:

* **Boolean-Based:** Se basa en inyectar condiciones lógicas de verdadero o falso (`1=1` vs `1=2`). Cuando esto funciona, la aplicación reacciona de manera diferente según el resultado.
* **Time-Based (Basada en tiempo):** Se utiliza cuando la aplicación es completamente ciega y estática (como el caso del objetivo  que siempre muestra una pantalla genérica de "Cargando..."). Como la interfaz no cambia nunca, usamos el tiempo como respuesta: obligamos al motor de la base de datos a pausarse/dormirse unos segundos solo si la condición que probamos se cumple. Si la web tarda 5 segundos en responder, sabemos que la condición fue verdadera; si responde al instante, fue falsa.

## Extracción letra por letra ante una pantalla genérica

Cuando la aplicación solo responde con una pantalla fija de "Cargando...", la técnica booleana no sirve porque no hay ningún cambio visual que podamos distinguir. La única forma de sacar información es mediante *Time-Based*, adivinando carácter por carácter con funciones de texto como `SUBSTRING()`:

1. Usamos la función para recortar una posición específica del texto que queremos averiguar, por ejemplo `SUBSTRING(database(), 1, 1)` para apuntar a la primera letra del nombre de la base de datos.
2. Le decimos a la base de datos: Por ej. *"Si la primera letra es una 'a', dormite 5 segundos"*.
   * Si la respuesta tarda 5 segundos, confirmamos que la primera letra es efectivamente la 'a'.
   * Si responde de inmediato en milisegundos, significa que no es esa letra y pasamos a probar con la siguiente ('b', 'c', etc.).
3. Una vez confirmada la primera letra, pasamos a la segunda posición cambiando el índice a `SUBSTRING(database(), 2, 1)` y repetimos el ciclo con todo el abecedario hasta sacar el nombre completo de la base de datos.
