# Puertos y Servicios Esenciales

A continuación, se detalla la lista de los puertos fundamentales y los servicios que corren por defecto en cada uno de ellos:

*   **Puertos 20 y 21 (FTP - File Transfer Protocol):** Sirven para conexiones con servidores FTP, permitiendo el intercambio y transferencia de archivos entre equipos. El puerto 20 se usa comúnmente para la transferencia de datos y el 21 para el control de la conexión.
*   **Puerto 22 (SSH - Secure Shell / SFTP):** Es el puerto de escucha para conexiones seguras. Permite el acceso remoto a servidores y la transferencia de archivos de manera cifrada, garantizando la confidencialidad de los datos enviados.
*   **Puerto 23 (Telnet):** Protocolo heredado que se utilizaba para conectarse a otro equipo mediante línea de comandos y controlarlo remotamente. Actualmente está en desuso por su falta de seguridad, ya que la autenticación y todo el tráfico de datos se envían en texto plano (sin cifrar).
*   **Puerto 25 (SMTP - Simple Mail Transfer Protocol):** Protocolo estándar utilizado de forma principal para el envío y enrutamiento de correos electrónicos. (Alternativamente, a veces se usan los puertos 26 o 2525 para el mismo fin).
*   **Puerto 53 (DNS - Domain Name System):** Sistema de nombres de dominio. Se encarga de traducir (o resolver) nombres de dominio legibles por humanos en sus correspondientes direcciones IP numéricas.
*   **Puerto 80 (HTTP - Hypertext Transfer Protocol):** Permite la navegación web estándar, transmitiendo el código de las páginas web y los datos de manera directa, sin cifrado.
*   **Puerto 110 (POP3 - Post Office Protocol version 3):** Empleado por los clientes y gestores de correo electrónico para conectarse y descargar los mensajes recibidos localmente desde el servidor de correo.
*   **Puerto 443 (HTTPS - Hypertext Transfer Protocol Secure):** Permite la navegación web de forma segura. Todo el tráfico de las páginas web enviado a través de este puerto viaja cifrado mediante protocolos como TLS.
*   **Puerto 3306 (MySQL / MariaDB):** Es el puerto predeterminado que utilizan los motores de bases de datos relacionales (como MySQL) para gestionar las conexiones entrantes y el procesamiento de consultas.