# [SEC-2026-0051] Gestión Insegura de Sesión: Emisión de Cookies de Autenticación sin Banderas Secure ni HttpOnly y Ausencia de HSTS en Portal Central SSO (Autogestión)

---

## 1. Ficha Técnica del Hallazgo

* **Identificador Interno:** `SEC-2026-0051`
* **Fecha de Evaluación:** `2026-10-18`
* **Auditor / Evaluador:** `Lucas`
* **Severidad Global:** `ALTA (CVSS 8.1)`
* **Estado:** `Nuevo (Pendiente Mitigación Inmediata)`
* **Activo Evaluado:** `https://sso.frc.utn.edu.ar/` (Portal Central de Autenticación Single Sign-On - Universidad Tecnológica Nacional, Facultad Regional Córdoba)
* **Plataforma / Flujo Afectado:** Flujo de Autenticación y Control de Sesiones de Autogestión 4 (`https://a4.frc.utn.edu.ar/` -> `https://sso.frc.utn.edu.ar/?site=autogestion`)
* **Parámetro / Cabecera Afectada:** Cabecera de respuesta `Set-Cookie` (identificador `ASPSESSIONIDACCADBCS`), ausencia de `Strict-Transport-Security`, ausencia de `X-Frame-Options` y exposición de `Server: Microsoft-IIS/6.0`.
* **Clasificación CWE:** 
  * `CWE-614: Sensitive Cookie in HTTPS Session Without 'Secure' Attribute`
  * `CWE-1004: Sensitive Cookie Without 'HttpOnly' Flag`
  * `CWE-384: Session Fixation / Improper Session Management`
  * `CWE-319: Cleartext Transmission of Sensitive Information`
  * `CWE-1021: Improper Restriction of Rendered UI Layers or Frames (Clickjacking)`
  * `CWE-200: Exposure of Sensitive Information to an Unauthorized Actor`
* **Estándar OWASP:** 
  * `OWASP Top 10:2021 – A07:2021: Identification and Authentication Failures`
  * `OWASP Top 10:2021 – A05:2021: Security Misconfiguration`
* **Controles OWASP ASVS v4.0.3:** 
  * `V3.4.1` (Garantizar que todas las cookies de sesión tengan asignada la bandera 'Secure')
  * `V3.4.2` (Garantizar que todas las cookies de sesión tengan asignada la bandera 'HttpOnly')
  * `V3.4.3` (Garantizar que las cookies de sesión cuenten con el atributo 'SameSite=Lax' o 'Strict')
  * `V14.4.1` (Implementación obligatoria de HTTP Strict Transport Security - HSTS)
  * `V14.4.4` (Protección contra Clickjacking en formularios de autenticación)
  * `V14.3.3` (Supresión de banners de versión y servidores obsoletos)

---

## 2. Resumen Ejecutivo (Para Directivos)

Durante una auditoría de seguridad de carácter **estrictamente pasivo** sobre el flujo de navegación de las plataformas institucionales de uso cotidiano de la **Universidad Tecnológica Nacional - Facultad Regional Córdoba** (específicamente el portal de **Autogestión** y el sistema central de inicio de sesión **SSO - Single Sign-On** en `https://sso.frc.utn.edu.ar/?site=autogestion`), se detectó una vulnerabilidad de **SEVERIDAD ALTA** en la gestión de credenciales y tokens de sesión de los usuarios.

Al iniciar el flujo de autenticación, el servidor genera y envía al navegador la cookie de sesión activa (`ASPSESSIONID...`) **omitiendo en su totalidad las banderas obligatorias de seguridad `Secure`, `HttpOnly` y `SameSite`**. Asimismo, el portal de autenticación carece de la cabecera **HSTS (HTTP Strict Transport Security)** y de protección contra encuadre malicioso (**Clickjacking**).

**Impacto en la Institución:**
1. **Secuestro de Sesión en Redes Compartidas (Session Hijacking):** La ausencia del atributo `Secure` permite que la cookie de sesión viaje en texto claro por la red si el usuario realiza alguna petición no cifrada o si se encuentra conectado a una red Wi-Fi no confiable (como la red pública del campus universitario o espacios comerciales). Un tercero en la misma red local puede interceptar el token de sesión y usurpar la identidad de alumnos, profesores o personal administrativo.
2. **Exfiltración de Sesión mediante Cross-Site Scripting:** La omisión de la bandera `HttpOnly` expone la cookie al contexto de ejecución de JavaScript (`document.cookie`), posibilitando que cualquier vulnerabilidad de inyección o script malicioso robe inmediatamente la sesión del usuario.
3. **Pérdida Crítica de Integridad y Confidencialidad Académica:** Al controlar la sesión del usuario en Autogestión, un atacante obtiene acceso no autorizado al expediente académico completo, notas de exámenes, inscripciones a cursado, pagos y trámites administrativos de la víctima.
4. **Obsolescencia Tecnológica Severa:** La cabecera expuesta `Server: Microsoft-IIS/6.0` evidencia que el núcleo de autenticación opera sobre Windows Server 2003 / IIS 6.0, un sistema operativo fuera de soporte por el fabricante desde julio de 2015, carente de parches de seguridad para cientos de fallas críticas documentadas a nivel de infraestructura.

---

## 3. Métricas y Puntuación CVSS v3.1

### Vector Oficial y Calificación Cualitativa

* **Puntuación Base (Base Score):** **8.1 (ALTA)**
* **Vector CVSS v3.1:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:R/S:U/C:H/I:H/A:N`

```
  +-----------------------------------------------------------------------+
  |  CVSS v3.1 Base Score: 8.1 / 10.0   |   Nivel de Severidad: ALTA      |
  +-----------------------------------------------------------------------+
```

### Justificación Técnica de Métricas Base:

* **Vector de Ataque (AV) — Network (N):** La falla es accesible y explotable de manera remota a través del protocolo HTTP/S sobre Internet, sin necesidad de acceso físico al servidor.
* **Complejidad del Ataque (AC) — Low (L):** No existen barreras de mitigación o configuraciones defensivas que impidan el abuso. La cookie sensible carece de protecciones de forma estructural y la sesión es transmitida en texto claro ante cualquier interacción insegura.
* **Privilegios Requeridos (PR) — None (N):** El atacante no necesita disponer de una cuenta de usuario previa ni estar autenticado en el sistema.
* **Interacción del Usuario (UI) — Required (R):** Requiere que el usuario legítimo interactúe con el portal SSO para generar la sesión o que navegue en la red compartida donde actúa el atacante.
* **Alcance (Scope - S) — Unchanged (U):** El impacto de la sesión secuestrada se concentra en la propia plataforma de Autogestión y servicios integrados al SSO institucional.
* **Impacto en Confidencialidad (C) — High (H):** Pérdida total de la confidencialidad de la sesión del usuario. El atacante adquiere visibilidad completa del historial académico, calificaciones, datos personales y documentación estudiantil.
* **Impacto en Integridad (I) — High (H):** El atacante adquiere la facultad de operar en nombre de la víctima: inscribir o dar de baja materias, modificar postulaciones, alteración de datos de perfil y firmas de actas en caso de perfiles docentes.
* **Impacto en Disponibilidad (A) — None (N):** El ataque no interrumpe la disponibilidad general del servidor.

### Desglose de Deficiencias de Seguridad Asociadas al Activo

| Vulnerabilidad Específica | CWE Asociado | Control ASVS | CVSS 3.1 Base | Severidad |
| :--- | :---: | :---: | :---: | :---: |
| **Cookie de Sesión sin banderas `Secure` ni `HttpOnly`** | CWE-614 / CWE-1004 | V3.4.1 / V3.4.2 | `CVSS:3.1/AV:N/AC:L/PR:N/UI:R/S:U/C:H/I:H/A:N` | **8.1** (Alta) |
| **Ausencia de HSTS (Riesgo de SSL Stripping en Red Local)** | CWE-319 | V14.4.1 | `CVSS:3.1/AV:A/AC:H/PR:N/UI:R/S:U/C:H/I:H/A:N` | **6.4** (Media) |
| **Ausencia de `X-Frame-Options` en Formulario de Login SSO** | CWE-1021 | V14.4.4 | `CVSS:3.1/AV:N/AC:L/PR:N/UI:R/S:U/C:H/I:N/A:N` | **6.5** (Media) |
| **Exposición de Servidor Obsoleto EOL (`Microsoft-IIS/6.0`)** | CWE-200 / CWE-497 | V14.3.3 | `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N` | **5.3** (Media) |

---

## 4. Detalles Técnicos y Causa Raíz

### 4.1. Arquitectura del Flujo de Autenticación
El portal de autogestión de alumnos (`https://a4.frc.utn.edu.ar/`) implementa un esquema de autenticación centralizada delegando el proceso de inicio de sesión en el servicio Single Sign-On situado en `https://sso.frc.utn.edu.ar/?site=autogestion`.

Al examinar pasivamente la respuesta generada por el servidor `sso.frc.utn.edu.ar`, se constata la emisión de la cabecera:
```http
Set-Cookie: ASPSESSIONIDACCADBCS=EJMGPEPBKMHFIELKFEMHJIHJ; path=/
```

### 4.2. Análisis de las Debilidades en la Cookie de Sesión

1. **Omisión de la bandera `Secure` (CWE-614):**
   * El atributo `Secure` le ordena al navegador cliente que bajo ninguna circunstancia transmita la cookie si la conexión no se realiza mediante un canal cifrado TLS/HTTPS.
   * Al no incluir este atributo, si el usuario navega a cualquier recurso del dominio `frc.utn.edu.ar` a través de HTTP sin cifrar, o si un atacante en la red inyecta una referencia de imagen o enlace hacia `http://sso.frc.utn.edu.ar/recurso`, el navegador enviará automáticamente la cookie `ASPSESSIONID` en texto plano a través de la red física.
   * En redes Wi-Fi de acceso público (muy comunes en universidades y facultades), cualquier persona ejecutando un sniffer pasivo de paquetes puede capturar el token de sesión sin necesidad de desencriptar tráfico TLS.

2. **Omisión de la bandera `HttpOnly` (CWE-1004):**
   * La bandera `HttpOnly` bloquea el acceso a la cookie desde interfaces de programación del lado del cliente, tales como la propiedad `document.cookie` en JavaScript.
   * En su estado actual, la cookie sensible es completamente accesible por scripts ejecutados en el navegador. Si la plataforma institucional sufre cualquier falla de Cross-Site Scripting (XSS) en algún subsistema, el atacante puede extraer el valor de `ASPSESSIONID` de forma directa y silenciosa.

3. **Omisión de la bandera `SameSite` (CWE-384):**
   * La cookie carece de los atributos `SameSite=Lax` o `SameSite=Strict`. Esto habilita que la cookie se incluya de manera predeterminada en peticiones de origen cruzado (*cross-origin*), incrementando la superficie para ataques de Cross-Site Request Forgery (CSRF).

4. **Ausencia de HSTS y Puerto 80 no redirigido en SSO:**
   * La plataforma `sso.frc.utn.edu.ar` no envía la cabecera `Strict-Transport-Security`. Adicionalmente, el puerto 80 sin cifrar (`http://sso.frc.utn.edu.ar/`) no implementa una redirección 301 obligatoria hacia HTTPS, sino que sirve una página Apache independiente con código 200 OK, lo que favorece ataques de degradación (*SSL Stripping*).

5. **Infraestructura con Fin de Soporte (EOL):**
   * La cabecera `Server: Microsoft-IIS/6.0` confirma que el portal de login está alojado en Windows Server 2003 con IIS 6.0. Esta plataforma carece de soporte oficial de seguridad de Microsoft desde el **14 de julio de 2015**, encontrándose desactualizada frente a estándares criptográficos modernos y expuesta a fallas de desbordamiento de búfer e inyecciones de cabeceras conocidas a nivel de sistema operativo.

---

## 5. Prueba de Concepto (PoC) Paso a Paso (Metodología Pasiva)

> **Nota Metodológica:** La verificación se realizó de forma **estrictamente pasiva**, inspeccionando el intercambio estándar de encabezados HTTP realizado por el navegador web o herramientas pasivas (`cURL -i`), sin alterar peticiones, sin enviar payloads maliciosos ni perturbar la normal operación del servicio.

### Paso 1: Petición Pasiva al Portal de Autenticación SSO
Se realiza una consulta pasiva al endpoint de inicio de sesión de Autogestión:

```http
GET /?site=autogestion HTTP/1.1
Host: sso.frc.utn.edu.ar
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/129.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Connection: keep-alive
```

### Paso 2: Evidencia del Resultado Obtenido en la Respuesta del Servidor
El servidor responde con código `200 OK` y emite el token de sesión con la siguiente cabecera cruda:

```http
HTTP/1.1 200 OK
Date: Sun, 18 Oct 2026 12:02:26 GMT
Server: Microsoft-IIS/6.0
Pragma: no-cache
Cache-Control: no-store
Content-Length: 29691
Content-Type: text/html
Expires: Sun, 18 Oct 2026 12:01:26 GMT
Cache-control: no-cache
Set-Cookie: ASPSESSIONIDACCADBCS=EJMGPEPBKMHFIELKFEMHJIHJ; path=/
Vary: Accept-Encoding
```

### Paso 3: Matriz de Verificación de Atributos de la Cookie de Sesión

| Atributo / Bandera | Estado en `sso.frc.utn.edu.ar` | Requerimiento OWASP ASVS | Diagnóstico |
| :--- | :---: | :---: | :---: |
| **Nombre de Cookie** | `ASPSESSIONIDACCADBCS` | Identificador aleatorio y robusto | Válido |
| **Bandera `Secure`** | **AUSENTE** | Obligatorio (Control V3.4.1) | **VULNERABLE (Fuga en texto claro)** |
| **Bandera `HttpOnly`** | **AUSENTE** | Obligatorio (Control V3.4.2) | **VULNERABLE (Accesible por JavaScript)** |
| **Bandera `SameSite`** | **AUSENTE** | Recomendado: `Lax` o `Strict` (V3.4.3) | **VULNERABLE (Riesgo de CSRF)** |
| **Alcance (`Path`)** | `path=/` | Raíz del dominio | Permisivo |

### Paso 4: Verificación de Cabeceras Defensivas en el Portal SSO

```
[+] Inspección de Encabezados de Seguridad en https://sso.frc.utn.edu.ar/:
    - Strict-Transport-Security: NO PRESENTE (Vulnerable a SSL Stripping)
    - X-Frame-Options:           NO PRESENTE (Vulnerable a Clickjacking de formulario de login)
    - X-Content-Type-Options:    NO PRESENTE
    - Content-Security-Policy:   NO PRESENTE
    - Server:                    Microsoft-IIS/6.0 (Divulgación de software obsoleto EOL)
```

---

## 6. Recomendaciones de Remediación

### 6.1. Remediación en el Servidor de Aplicaciones e IIS

Para asegurar que las cookies de sesión no sean transmitidas en texto claro ni manipuladas por scripts maliciosos, se deben implementar las siguientes configuraciones de forma perentoria:

#### 1. Activación de Banderas `Secure`, `HttpOnly` y `SameSite` en ASP / IIS:
Si la aplicación utiliza Active Server Pages (ASP clásico) o .NET en IIS, configurar la emisión automática de banderas en el archivo `web.config` o mediante un script de inclusión global:

```xml
<configuration>
  <system.web>
    <!-- Forzar que todas las cookies de sesión requieran SSL/TLS y HttpOnly -->
    <httpCookies httpOnlyCookies="true" requireSSL="true" sameSite="Lax" />
    <authentication mode="Forms">
      <forms requireSSL="true" protection="All" cookieless="UseCookies" />
    </authentication>
  </system.web>
</configuration>
```

Para entornos de ASP clásico en IIS, incorporar en el archivo global de cabecera (`global.asa` o include):
```asp
<%
Response.AddHeader "Set-Cookie", "ASPSESSIONID=" & Session.SessionID & "; path=/; Secure; HttpOnly; SameSite=Lax"
%>
```

#### 2. Reescritura / Hardening de Cabeceras en el Proxy Inverso Frontal (Nginx / Apache):
Si delante del servidor IIS existe un proxy inverso (como Nginx o Apache), se puede interceptar y sanitizar la cabecera `Set-Cookie` para inyectar los atributos ausentes de forma transparente:

* **En Nginx:**
  ```nginx
  # Inyectar banderas obligatorias a todas las cookies recibidas del backend
  proxy_cookie_path / "/; Secure; HttpOnly; SameSite=Lax";

  # Ocultar version de servidor del backend
  proxy_hide_header Server;

  # Cabeceras de endurecimiento obligatorias
  add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;
  add_header X-Frame-Options "SAMEORIGIN" always;
  add_header X-Content-Type-Options "nosniff" always;
  ```

### 6.2. Migración y Aislamiento de Infraestructura Obsoleta
* **Plan de Migración:** Diseñar un plan de modernización urgente para desacoplar el portal central de autenticación de Windows Server 2003 / IIS 6.0, migrándolo a una versión moderna con soporte activo (ej. Windows Server 2022 o contenedores Linux con Keycloak/OpenID Connect/SAML).
* **Workaround Temporal (Contención):** Si la migración inmediata no es viable, colocar el servidor detrás de un **Web Application Firewall (WAF)** y un reverse proxy moderno que aplique terminación TLS 1.3 estricta, redirección forzada del puerto 80 a 443, HSTS y bloqueo de peticiones maliciosas dirigidas a vulnerabilidades conocidas de IIS 6.0.

### 6.3. Verificación Post-Remediación
Tras la aplicación de las medidas correctivas, la cabecera `Set-Cookie` emitida por `https://sso.frc.utn.edu.ar/` debe reflejar la siguiente estructura higienizada:

```http
HTTP/1.1 200 OK
Set-Cookie: ASPSESSIONIDACCADBCS=...; path=/; Secure; HttpOnly; SameSite=Lax
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
X-Frame-Options: SAMEORIGIN
X-Content-Type-Options: nosniff
```
Garantizando el cumplimiento total de los controles **OWASP ASVS V3.4.1, V3.4.2, V3.4.3, V14.4.1 y V14.4.4**.
