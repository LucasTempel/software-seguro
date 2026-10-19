Objetivo 2: Análisis de Manipulación Transaccional (Cantidades Negativas)
Analiza el siguiente escenario teórico: Un e-commerce permite agregar productos al carrito. Al interceptar la petición, cambias la cantidad del producto de 1 a -5. El servidor lo procesa y el total del carrito se vuelve un número negativo, lo que termina sumando saldo a tu cuenta en lugar de cobrarte. Explica técnicamente por qué ocurre esta falla y cómo debería el desarrollador validar estrictamente este campo del lado del servidor para evitarlo. Formato de entrega: Enlace al archivo .md.



Esta vulnerabilidad es una Falla en la lógica de negocio (Business Logic Flaw) derivada de una validación inadecuada de entradas.

El problema ocurre porque el sistema comete un error de diseño,confia ciegamente en el frontend, multiples veces estas validaciones como el rango numerico se realizan unicamente en el frontend, y cuando nosotros interceptamos las peticiones y enviamos unas modificadas, estas validaciones no son consideradas dando lugar a estas situaciones.  

### Cómo debería validar el desarrollador en el servidor?

Para solucionar este fallo de forma definitiva, el backend nunca debe confiar en lo que envía el cliente y debe aplicar una validación estricta: 

* **Validación de tipo y rango positivo:** El campo `cantidad` debe forzarse en el servidor a ser un número entero estrictamente positivo (`entero >= 1`), además de fijar un límite máximo coherente con el negocio o el stock (por ejemplo, `1 <= cantidad <= 10`). Si entra un valor `<= 0`, un número decimal o un texto, el backend debe rechazar la petición.
* **Recálculo en el servidor:** Los precios unitarios y los subtotales jamás deben recibirse desde el cliente; el backend siempre debe consultar el precio directamente en la base de datos.
* **Control de sanidad sobre el total:** Como regla de defensa en profundidad, antes de procesar el pago o impactar en la pasarela, el sistema debe verificar que el monto total de la transacción sea coherente (`total > 0`), descartando cualquier operación que resulte en números negativos (en la mayoria de los dominios).
