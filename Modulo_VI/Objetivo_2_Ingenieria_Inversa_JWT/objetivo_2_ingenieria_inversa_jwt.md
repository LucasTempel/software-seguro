### Petición HTTP Interceptada
Al enviar las credenciales en el formulario de inicio de sesión (`/src/login/`), el navegador realiza una petición asíncrona hacia el backend de autenticación:

```http
POST /login HTTP/1.1
Host: app.softwareseguro.com.ar
Content-Type: application/json

{
  "type": "local",
  "email": "lucasgtempel@gmail.com",
  "password": "********"
}
```

El servidor responde con código `200 OK` retornando el token JWT en el cuerpo de la respuesta:

```text
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MTA4OSwiZnVsbG5hbWUiOiJMdWNhcyBHb256w6FsZXogVGVtcGVsIiwiZW1haWwiOiJsdWNhc2d0ZW1wZWxAZ21haWwuY29tIiwicm9sZV9pZCI6MSwicGxhbl9pZCI6MiwicGFnYWRvIjp0cnVlLCJmZWNoYV9wYWdvIjoiMjAyNi0wOS0wM1QyMToyMTowMy4wMDNaIiwiZW1haWxfY29uZmlybWVkIjp0cnVlLCJwZW5kaW5nX3BheW1lbnQiOm51bGwsImNvbmZpcm1hdGlvbl9lbWFpbF91dWlkIjoiOWFhM2Q0YzQtZjY3Ni00OWQ2LThhYTktOTkxNzhhYTY3NzU0IiwiY3JlYXRlZF9hdCI6IjIwMjYtMDktMjBUMjM6NTY6MjcuMjc3WiIsImlzX3N1cGVyYWRtaW4iOmZhbHNlLCJpYXQiOjE3OTIzNTc1NDQsImV4cCI6MTc5MjM3OTE0NH0.rRDkHurNkHTK_75gRIWSauHsQmV3mCdgpnQq7myV930
```
![Captura del trafico](sesion.jpg) 
---

## 2. Estructura Técnica del JWT Interceptado

Un JSON Web Token está compuesto por **tres secciones delimitadas por puntos (`.`)**, serializadas en formato **Base64URL**:


```
[PARTE 1 - HEADER]
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
.

[PARTE 2 - PAYLOAD]
eyJpZCI6MTA4OSwiZnVsbG5hbWUiOiJMdWNhcyBHb256w6FsZXogVGVtcGVsIiwiZW1haWwiOiJsdWNhc2d0ZW1wZWxAZ21haWwuY29tIiwicm9sZV9pZCI6MSwicGxhbl9pZCI6MiwicGFnYWRvIjp0cnVlLCJmZWNoYV9wYWdvIjoiMjAyNi0wOS0wM1QyMToyMTowMy4wMDNaIiwiZW1haWxfY29uZmlybWVkIjp0cnVlLCJwZW5kaW5nX3BheW1lbnQiOm51bGwsImNvbmZpcm1hdGlvbl9lbWFpbF91dWlkIjoiOWFhM2Q0YzQtZjY3Ni00OWQ2LThhYTktOTkxNzhhYTY3NzU0IiwiY3JlYXRlZF9hdCI6IjIwMjYtMDktMjBUMjM6NTY6MjcuMjc3WiIsImlzX3N1cGVyYWRtaW4iOmZhbHNlLCJpYXQiOjE3OTIzNTc1NDQsImV4cCI6MTc5MjM3OTE0NH0
.

[PARTE 3 - SIGNATURE]
rRDkHurNkHTK_75gRIWSauHsQmV3mCdgpnQq7myV930
```

---

## Decodificación e Ingeniería Inversa del Token

![captura de jwtio](image.png)  

### Encabezado (Header)

Al decodificar la primera sección desde Base64URL a JSON en la terminal:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

* **Algoritmo de firma (`alg`):** Utiliza **`HS256`** (**HMAC con SHA-256**).
  
---

### Payload


```json
{
  "id": 1089,
  "fullname": "Lucas González Tempel",
  "email": "lucasgtempel@gmail.com",
  "role_id": 1,
  "plan_id": 2,
  "pagado": true,
  "fecha_pago": "2026-09-03T21:21:03.003Z",
  "email_confirmed": true,
  "pending_payment": null,
  "confirmation_email_uuid": "9aa3d4c4-f676-49d6-8aa9-99178aa67754",
  "created_at": "2026-09-20T23:56:27.277Z",
  "is_superadmin": false,
  "iat": 1792357544,
  "exp": 1792379144
}
```


---

### 3.3. Firma Criptográfica (Signature)

```text
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secretKey
) = rRDkHurNkHTK_75gRIWSauHsQmV3mCdgpnQq7myV930
```
