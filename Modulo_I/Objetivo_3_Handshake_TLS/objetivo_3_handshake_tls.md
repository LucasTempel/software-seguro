# Proceso de TLS Handshake y Tipos de Cifrado

El Handshake TLS (Transport Layer Security) es el proceso por el cual un cliente y un servidor establecen una comunicación segura. Surge como reemplazo del SSL.

Esto lo hace estableciendo protocolos de seguridad. Primero, el cliente inicia el handshake enviando el conjunto de cifrados que puede emplear. Con esto, el servidor responde con la elección del cifrado, un identificador de sesión y su certificado digital. Este certificado digital contiene una clave pública (que nos valida la identidad del servidor, evitando un ataque MitM, entre otros), la cual el cliente usará para cifrar los mensajes enviados al servidor.

Una vez recibido el certificado digital, el cliente intercambia las claves y, dependiendo del algoritmo (RSA o DH), establecen el secreto premaster. Con esto, tanto el cliente como el servidor calculan independientemente la clave de sesión, elemental para el cifrado simétrico.

## ¿Por qué usamos cifrados distintos?

Primero, se utiliza el **cifrado asimétrico** (basado en una clave pública y una privada). Su rol es proteger el handshake inicial. Permite que el cliente encripte un secreto con la clave pública del servidor y se lo envíe de forma segura, garantizando que solo el servidor (con su clave privada) pueda leerlo. 

Una vez que ambos tienen esta clave, el handshake termina y se cambia al **cifrado simétrico**. En el cifrado simétrico toda la información que es enviada se encripta y desencripta con la clave de sesión compartida, de una manera más eficiente que la anterior, permitiendo un mejor rendimiento general.