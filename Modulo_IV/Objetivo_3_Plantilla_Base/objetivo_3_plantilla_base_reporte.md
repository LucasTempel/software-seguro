# Objetivo 3: Armado de Plantilla Base de Reporte de Pentesting

`⚠️⚠️⚠️⚠️⚠️⚠️⚠️⚠️⚠️⚠️⚠️`

`Antes que nada, debo reconocer que para formular este md lo hice con IA. Con fines de estructurar bien este md y luego tenerlo bien formulado para futuras consultas a modo de guía.`

`⚠️⚠️⚠️⚠️⚠️⚠️⚠️⚠️⚠️⚠️⚠️`
# PARTE I: PLANTILLA BASE PROFESIONAL (ESTRUCTURA DE TICKET / HALLAZGO)

## 1. Ficha Técnica del Hallazgo
* **Identificador Interno:** `SEC-2026-XXXX`
* **Fecha de Identificación:** `AAAA-MM-DD`
* **Auditor:** `Nombre del Pentester`
* **Severidad Global:** `[ CRÍTICA | ALTA | MEDIA | BAJA | INFORMATIVA ]`
* **Estado:** `[ Nuevo | En Progreso | Pendiente Validación | Mitigado ]`
* **Activo / Endpoint Afectado:** `https://app.ejemplo.com/api/v1/ruta`
* **Módulo / Parámetro Afectado:** `POST /api/v1/ruta (parámetro: "id_usuario")`
* **Clasificación CWE:** `CWE-XXX: Nombre de la debilidad (ej. CWE-89 / CWE-639)`
* **Control OWASP ASVS Afectado:** `V4.1.X / V5.X.X (Nivel 1/2/3)`

---

## 2. Resumen Ejecutivo
*Descripcion a alto nivel*. Incluyendo aspectos como

* ¿Qué ocurre? 
* Impacto en el Negocio
* Riesgo General
---

## 3. Métricas y Puntuación CVSS v3.1

* **Puntaje Base (Base Score):** `X.X / 10.0`
* **Nivel Cualitativo:** `[ Crítica (9.0-10.0) | Alta (7.0-8.9) | Media (4.0-6.9) | Baja (0.1-3.9) ]`
* **Vector CVSS Oficial:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:N`
* **Desglose de Métricas Base:**
  * **Vector de Ataque (AV):** `Network` (Explotable remotamente por Internet).
  * **Complejidad del Ataque (AC):** `Low` (No requiere condiciones especiales).
  * **Privilegios Requeridos (PR):** `None` (No requiere estar autenticado).
  * **Interacción del Usuario (UI):** `None` (No requiere que la víctima haga clic en nada).
  * **Alcance (Scope - S):** `Unchanged` (El impacto queda en el mismo componente).
  * **Impacto en Confidencialidad (C):** `High` (Pérdida total o masiva de privacidad).
  * **Impacto en Integridad (I):** `High` (Modificación total o adulteración de datos).
  * **Impacto en Disponibilidad (A):** `None` (El servicio sigue operativo).

---

## 4. Detalles Técnicos
*Explicación detallada de la arquitectura, causa raíz y mecánica interna que hace posible la falla.*

* Descripción del Problema
* Entorno y Tecnologías Detectadas
* Superficie de Exposición

---

## 5. Prueba de Concepto (PoC) Paso a Paso
*Guía determinista y reproducible para que el equipo de desarrollo o DevOps pueda verificar la falla exactamente como la vio el pentester.*


### Pasos de Reproducción:
1. Iniciar sesión en la plataforma con el usuario de pruebas navegando a `https://app.ejemplo.com/login`.
2. Dirigirse al apartado de facturación en `https://app.ejemplo.com/mis-facturas`.
3. Interceptar la petición HTTP saliente al hacer clic en "Descargar Factura #105":
   ```http
   GET /api/facturas/105 HTTP/1.1
   Host: app.ejemplo.com
   Cookie: session=eyJhbGciOi...
   ```
4. En el interceptor (o mediante cURL), modificar el parámetro `105` por el identificador de otro cliente (ej. `104`):
   ```http
   GET /api/facturas/104 HTTP/1.1
   Host: app.ejemplo.com
   Cookie: session=eyJhbGciOi...
   ```
5. Enviar la petición y observar la respuesta del servidor.

### Evidencia del Resultado Obtenido (Vulnerable):
El servidor responde con código `200 OK` devolviendo el JSON con información privada, domicilio y tarjeta de crédito de otro usuario ajeno:
```json
{
  "factura_id": 104,
  "cliente": "Empresa Victima S.A.",
  "cuit": "30-12345678-9",
  "total": 450000.00,
  "tarjeta_ultimos_4": "8841"
}
```
*(Insertar aquí captura de pantalla o payload de validación).*

---

## 6. Recomendaciones de Remediación
*Instrucciones de corrección aplicables, distinguiendo la solución de raíz de posibles mitigaciones temporales.*

### Solución Recomendada (De Raíz):
* [Explicación de la corrección arquitectónica o de código].
* Implementar control de acceso a nivel de objeto basado en sesión (**ABAC/RBAC**): el backend debe verificar que el `id_usuario` autenticado en la sesión sea el dueño legítimo de la factura solicitada antes de consultar la base de datos.

### Ejemplo de Código / Configuración Segura:
```python
# Ejemplo de verificación segura en backend (Python / Flask)
@app.route('/api/facturas/<int:factura_id>')
@login_required
def obtener_factura(factura_id):
    usuario_actual_id = current_user.id
    
    # Validación estricta de propiedad
    factura = Factura.query.filter_by(id=factura_id, usuario_id=usuario_actual_id).first()
    
    if not factura:
        # Se devuelve 404 o 403 sin dar pistas de existencia
        return jsonify({"error": "Recurso no encontrado o no autorizado"}), 404
        
    return jsonify(factura.to_dict())
```

### Mitigación Temporal / Contención (Workaround):
* Si la modificación del código toma tiempo, configurar reglas en el **WAF (Web Application Firewall)** o rate limiting para alertar sobre patrones secuenciales en IDs numéricos.

### Referencias Oficiales:
* **OWASP Top 10:** A01:2021 – Broken Access Control
* **OWASP ASVS:** V4.1 General Access Control Design
* **CWE:** CWE-639 (Authorization Bypass Through User-Controlled Key)


---

# PARTE II: EXPLICACIÓN DE LA ESTRUCTURA DE LA PLANTILLA

La estructura de esta plantilla fue diseñada siguiendo las mejores prácticas internacionales de auditoría (**PTES, OWASP y NIST SP 800-115**). La separación de secciones responde a un flujo lógico de comunicación en ciberseguridad:

1. **Ficha Técnica y Clasificación Estándar:** Provee trazabilidad inmediata. El uso de códigos CWE y controles de ASVS vincula el hallazgo técnico con taxonomías universales que permiten a las herramientas de gestión (como Jira o ASVS Tracker) catalogar y comparar la madurez del software a lo largo del tiempo.
2. **Resumen Ejecutivo:** Permite a los tomadores de decisiones comprender la gravedad de la falla en términos de negocio (dinero, legal, clientes) sin perderse en tecnicismos.
3. **Métricas CVSS v3.1:** Elimina la subjetividad del auditor (*"para mí esto es gravísimo"*). Al desglosar métricas universales (vector de red, privilegios, confidencialidad, etc.), se obtiene una cifra cuantitativa incuestionable para priorizar presupuestos y fechas límite de parches.
4. **Detalles Técnicos y Causa Raíz:** Aísla el error de concepto, diferenciando la manifestación externa del error de diseño real en el código o arquitectura.
5. **Prueba de Concepto (PoC) Paso a Paso:** Es el núcleo empírico del pentest. Debe ser **completamente reproducible**. Un reporte sin PoC clara genera demoras, discusiones con los desarrolladores y cierre prematuro de tickets por "no reproducible".
6. **Recomendaciones de Remediación y Código de Ejemplo:** Transforma una crítica destructiva en una solución constructiva. Indicar cómo solucionarlo con ejemplos de código y qué controles compensatorios aplicar ayuda al equipo de desarrollo a implementar el parche con rapidez y confianza.

---

# PARTE III: VALOR GENERADO PARA EL USUARIO FINAL

El valor de un ticket generado con esta plantilla radica en que **resuelve simultáneamente las necesidades de las dos audiencias clave de una empresa**, optimizando los tiempos de respuesta y mitigación:

### 1. Valor para Directivos, Gerentes y CISOs (Audiencia No Técnica)
* **Toma de decisiones informada y priorización de presupuesto:** El Resumen Ejecutivo y la métrica CVSS permiten a la gerencia entender al instante si deben pausar un lanzamiento a producción, asignar horas extras al equipo o asumir el riesgo.
* **Cumplimiento legal y normativo:** Documentar controles bajo estándares como OWASP ASVS sirve como evidencia formal de debida diligencia ante auditorías de cumplimiento (ISO 27001, SOC 2, PCI-DSS, regulaciones de bancos o entes gubernamentales).

### 2. Valor para Desarrolladores, Sysadmins y DevOps (Audiencia Técnica)
* **Reducción del MTTR (Mean Time to Remediate):** El tiempo medio de reparación disminuye drásticamente porque el desarrollador no pierde tiempo intentando "adivinar" cómo recrear la vulnerabilidad; tiene los pasos numerados, payloads exactos y peticiones HTTP listas.
* **Certeza en la implementación del parche:** Al incluir recomendaciones de remediación específicas con fragmentos de código seguro y referencias oficiales, el equipo técnico no cae en "falsas soluciones" (como validar solo en el frontend mediante JavaScript) y aplica la corrección definitiva en la capa adecuada.
* **Eliminación de fricción y disputas:** Se eliminan los desacuerdos entre el equipo de seguridad y el equipo de desarrollo gracias a la evidencia tangible e irrefutable de la Prueba de Concepto.
