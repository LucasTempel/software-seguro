# Objetivo 3: Directory Listing
Cuando un usuario o un atacante navega hacia una ruta o carpeta del servidor que no cuenta con un archivo de índice (como `index.html`, `index.php`, etc.) y la opcion **Directory Listing** esta activa, el servidor web interpreta la petición y genera dinámicamente una página HTML con la estructura de directorios que residen en esa ruta física, como si fuese un file manager
Esto antes venía habilitado por defecto en muchos servidores web, o es usado en entornos locales o de desarrollo para compartir o revisar archivos de forma cómoda, pero dejarla activa en producción es una  mala configuración.

Dentro de los archivos que se exponen estan :

* Archivos de entorno y configuración: Archivos como `.env` o `config.php`. Con hasta credenciales de acceso a la base de datos, claves de la app, tokens de APIs y contraseñas.
* Copias de seguridad (Backups): exponen tablas completas, registros de usuarios, transacciones o datos del negocio.
* Carpetas de control de versiones: Directorios como `.git/`.
* Archivos de registro (Logs): Ficheros `.log`

## Como lo mitigamos

Para eliminar este problema de raíz tenemos que deshabilitar la opcion directamente en la configuración del servidor web:

1. **En Apache:**
   Se debe desactivar la opción `Indexes`.
   ```apache
   Options -Indexes
   ```
2. **En Nginx:**
   En Nginx el listado se controla a través de la directiva `autoindex`. Se debe asegurar que esté deshabilitada (`off`)
   ```nginx
   autoindex off;
   ```

* **Colocar archivos índice vacíos:** si necesitamos que esta opc esta activada por algun motivo,  podemos colocar un archivo `index.html` o `index.php` totalmente vacío. De manera que el navegador cargue esa página en blanco en lugar de mostrar los archivos.
* **Auditoría y escaneo preventivo:** Si queremos comprobar que no hayn rutas que expongan esta vulne, hay herramientas de fuzzing, de escaneo (como **Gobuster**, **ffuf**) que permiten rastrear directorios y alertar si alguna ruta lo devuelve. 
