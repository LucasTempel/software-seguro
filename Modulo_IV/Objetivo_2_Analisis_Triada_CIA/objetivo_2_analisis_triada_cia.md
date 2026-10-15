# Análisis de la Tríada CID (Confidencialidad, Integridad y Disponibilidad)

## 1. Confidencialidad
Este pilar se enfoca en todos los esfuerzos y medidas que empleamos para garantizar que los datos y recursos dentro de una organización se mantengan privados y en secreto. El objetivo es asegurar que solo aquellos usuarios que realmente necesitan el acceso cuenten con los privilegios necesarios, y que nadie no autorizado pueda llegar a ver esa información.

Un ejemplo clásico de ataque que atenta directamente contra este pilar es el de **Hombre en el medio (Man in the Middle)**, donde un atacante se posiciona en el flujo de la comunicación para interceptar el tráfico y espiar o robar datos privados (como credenciales de acceso).

## 2. Integridad
La integridad busca que la información y los recursos se mantengan auténticos, precisos y confiables, sin haber sufrido modificaciones o alteraciones indebidas. Siguiendo con la idea anterior, si un atacante en el medio no solo se limita a robar los datos sino que además los modifica en el camino, ahí se quiebra este pilar, ya que el receptor recibe datos alterados que ya no son legítimos ni confiables.

## 3. Disponibilidad
Este pilar describe cómo los recursos y servicios deben garantizar un acceso oportuno y continuo, de forma que siempre estén disponibles para los usuarios y clientes legítimos que los requieran. Si un servicio se cae o sufre fallas cuando alguien lo necesita utilizar, se pierde la disponibilidad.

El ataque más conocido contra este pilar es el **DoS (Denegación de Servicio)**, el cual busca saturar o sobrecargar el servidor mediante una avalancha de peticiones a modo de tirar el sistema o degradar considerablemente su rendimiento para impedir que los usuarios reales puedan utilizarlo.

---

## Ejemplos Concretos de Vulnerabilidades Web por Pilar

### Confidencialidad: IDOR (Insecure Direct Object References)
La aplicación web confía directamente en los identificadores que envía el usuario desde el navegador o cliente, sin validar si realmente tiene permisos para acceder a ese objeto.

   Por ejemplo: En una app cuya URL para visualizar un comprobante de pago es `https://app.ejemplo.com/factura?id=105`. Si cambio manualmente el valor del parametro por `id=104` y la web me devuelve la factura con los datos personales, compras y dirección de otro usuario, se vulnera directamente la **confidencialidad**. 

### Integridad: Inyección SQL (SQLi)
Esto sucede porque el backend consulta los datos ingresados por el usuario directamente en una consulta a la base de datos sin sanitizarlos ni usar consultas preparadas

Por ejemplo: En un formulario donde un usuario edita su perfil, si el campo de entrada es vulnerable a SQLi, un atacante podría colar una sentencia como `' , rol = 'admin' WHERE id = 12 --`. Al procesarse en el backend, la base de datos actualiza sus permisos y pasa a ser administrador. 

### Disponibilidad: Agotamiento de Recursos por falta de Rate Limiting
Esta vulne en particular la podemos explotar cuando nuestra app ofrece funcionalidades que demandan bastante procesamiento (o no necesariamente, lo importante es hacer multiples peticiones que se traduzcan luego en un enorme costo de procesamiento) pero no tiene ningún control que limite la cantidad de peticiones que un usuario puede hacer por minuto.

Por ejemplo: Un atacante crea un script para enviar miles de solicitudes por segundo a un endpoint para la exportación de reportes. Como el servidor no frena el flujo con un límite de tasa (Rate Limit), la CPU, la memoria RAM y se agotan los hilos de conexión(y todo el serv). 
