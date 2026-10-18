## Composición de un JSON Web Token y su firma
Un JSON Web Token (JWT) está compuesto por tres partes separadas por puntos (`Header.Payload.Signature`), cada una codificada en Base64Url:

* **Header:** Contiene los metadatos del token, indicando principalmente el tipo de token(en este caso siempre seran    `JWT`) y el algoritmo criptográfico con el que fue firmado (por ejemplo, `HS256`).
* **Payload:** Guarda los datos o atributos del usuario, como su identificador, nombre o nivel de rol en la plataforma.Los claims.
* **Firma (Signature):** Es lo que garantiza que la información no fue manipulada. El servidor toma la cabecera y el payload codificados y les aplica una función criptográfica junto con una clave secreta que solo él conoce. 
  Si un usuario chistosito intenta modificar algo del payload, la firma original queda inválida y el backend rechaza la petición. (La signature ya no sería valida).

## Uso del algoritmo "None" y bypass de autenticación
El estándar de JWT incluye un algoritmo denominado `"none"`, con una aplicacion interna, donde el servidor ya valido el jwt. La vulnerabilidad crítica ocurre cuando el backend comete el error de confiar en lo que el cliente le indica en la cabecera del token. En pocas palabras no deberiamos permitir tokens de los clientes con el algoritmo none.

El problema surgiría al recibir esta petición, el backend vulnerable lee el header,interpreta que aplica el alg none y que por ende el token no requiere firma y se saltea por completo la verificación criptográfica. 