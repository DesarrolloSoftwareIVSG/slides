---
marp: true
theme: alo
paginate: true
---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00028_.png);
}
</style>
# Seguridad web y móvil
## Contenidos
- Vulnerabilidades comunes y cómo mitigarlas
- Autenticación y autorización
- Seguridad en el manejo de datos y en el código
- Seguridad en las APIs y en el despliegue
- Herramientas y recursos en la web

> **Laravel 13** en el back-end · **Vue / Ionic + Capacitor** en el cliente <br>Curso *Desarrollo de Software IV* · II Ciclo 2026

---

## ¿Por qué un tema entero de seguridad?

- La seguridad **no es una funcionalidad** que se agrega al final: es una propiedad de **todo** el sistema

<div class="grid">
<div>

### 🌐 Web
El navegador ejecuta código de terceros, guarda cookies y confía en lo que el servidor le envía
</div>
<div>

### 📱 Móvil
La app vive en un dispositivo que **el atacante puede tener en la mano**: se descompila, se inspecciona, se modifica
</div>
<div>

### 🔌 API
Es la puerta común de web y móvil: un fallo aquí afecta a **todos** los clientes
</div>
<div>

### 🚀 Despliegue
Un `.env` publicado o un puerto abierto anulan todo lo demás
</div>
</div>

- 🔑 Regla de oro: **nunca confíes en el cliente**. Todo lo que llega por HTTP puede haber sido fabricado

---

## Principios que atraviesan todo el tema

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.9rem;">
<div>

### 🧅 Defensa en profundidad
Varias capas de control: si una falla, la siguiente detiene el ataque

### 🎯 Mínimo privilegio
Cada usuario, token, proceso y contenedor tiene **solo** los permisos que necesita

### 🚫 Denegar por defecto
Lo que no está explícitamente permitido, está prohibido
</div>
<div>

### 🔒 Seguro por defecto
La configuración inicial debe ser la segura; abrir requiere una decisión consciente

### 🧱 Reducir la superficie de ataque
Menos endpoints, menos dependencias, menos permisos, menos datos

### 🙈 Sin seguridad por oscuridad
Ocultar una URL o "minificar" el código **no** protege nada
</div>
</split-slide>

---

## Advertencia ética y legal

- ⚖️ Todas las pruebas de este tema se realizan **únicamente sobre la aplicación del propio equipo**, en entorno **local** o de pruebas
- Probar sistemas de terceros sin autorización escrita es una **falta grave** con consecuencias legales
- 🇨🇷 En Costa Rica el **Código Penal** tipifica el acceso no autorizado a sistemas informáticos, y la **Ley 8968** protege los datos personales
- Si encuentras una vulnerabilidad en un sistema ajeno: **divulgación responsable** al dueño (busca su `/.well-known/security.txt`)

<br>

> *"Con autorización es una auditoría. Sin autorización es un delito."*

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00068_.png);
}
</style>
# 6.1 Vulnerabilidades comunes
## Contenidos
- OWASP Top 10:2025
- Inyección (SQL y comandos)
- XSS, CSRF y SSRF
- Asignación masiva y *clickjacking*

---

<style scoped>
table { font-size: 0.8em; }
</style>

## OWASP Top 10:2025

| # | Categoría | Ejemplo en el proyecto |
|:--|:--|:--|
| **A01** | Pérdida de control de acceso | `GET /api/pedidos/42` devuelve el pedido de otra persona |
| **A02** | Configuración de seguridad incorrecta | `APP_DEBUG=true` en producción, CORS `*` |
| **A03** | 🆕 Fallas en la cadena de suministro de software | Un paquete de npm comprometido |
| **A04** | Fallas criptográficas | Contraseñas con `md5`, HTTP sin TLS |
| **A05** | Inyección | SQL construido por concatenación |
| **A06** | Diseño inseguro | Recuperar contraseña con "pregunta secreta" |
| **A07** | Fallas de autenticación | Sin límite de intentos, tokens eternos |
| **A08** | Fallas en la integridad del software o de los datos | CDN sin SRI, deserializar datos del cliente |
| **A09** | Fallas en el registro, alerta y monitoreo | Nadie se entera de 10 000 intentos de login |
| **A10** | 🆕 Manejo inadecuado de condiciones excepcionales | Un error que deja la transacción a medias o "falla abierto" |

- 📈 La lista se actualiza cada ~4 años con datos reales. Cambios clave frente a 2021: la **cadena de suministro** sube al 3.º lugar y la **configuración** al 2.º

---

## Inyección SQL

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.8rem;">
<div>

```php
// ❌ Vulnerable: el dato se vuelve código SQL
$email = $request->input('email');
DB::select("SELECT * FROM users WHERE email = '$email'");

// Entrada maliciosa:   ' OR '1'='1
// Resultado:           ... WHERE email = '' OR '1'='1'
```

```php
// ✅ Parámetros vinculados
DB::select('SELECT * FROM users WHERE email = ?', [$email]);

// ✅ Eloquent / Query Builder ya vinculan
User::where('email', $email)->first();
```

```php
// 🚩 Aun con ORM se puede romper
User::orderByRaw($request->orden)->get();      // ❌
User::orderBy(                                  // ✅
    in_array($r->orden, ['nombre','fecha']) ? $r->orden : 'id'
)->get();
```
</div>
<div>

- El problema es **mezclar datos con código**
- La solución es **separarlos**: el motor recibe la consulta y los parámetros por canales distintos
- 🚩 Los métodos `*Raw()` (`whereRaw`, `orderByRaw`, `DB::raw`) **no** escapan nada
- Nombres de columnas y direcciones de orden **no** se pueden parametrizar: se validan contra una **lista blanca**

<br>

- 💡 Mismo patrón en **inyección de comandos** (`exec`, `shell_exec`) y **NoSQL**: nunca construyas la orden con texto del usuario
</div>
</split-slide>

---

## Cross-Site Scripting (XSS)

- El atacante logra que **su JavaScript** se ejecute en el navegador de otra persona, con su sesión

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.8rem;">
<div>

### Tres variantes
- **Almacenado** — el script queda en la base de datos (un comentario, un nombre de producto)
- **Reflejado** — viaja en la URL y el servidor lo devuelve
- **Basado en DOM** — el propio JavaScript del cliente lo inserta

```js
// ❌ Inserta HTML: ejecuta <img src=x onerror=...>
div.innerHTML = comentario.texto;

// ✅ Inserta texto plano
div.textContent = comentario.texto;
```
</div>
<div>

### Mitigación
- **Escapar al mostrar**: Blade `{{ $x }}`, Vue `{{ x }}` y React `{x}` ya escapan
- 🚩 `{!! $x !!}`, `v-html` y `dangerouslySetInnerHTML` desactivan la protección
- Si hay que mostrar HTML del usuario: **sanear** con **DOMPurify**
- **CSP** como segunda línea: bloquea scripts *inline* y de orígenes no autorizados
- Cookies de sesión `HttpOnly`: JavaScript no puede leerlas
</div>
</split-slide>

---

## Cross-Site Request Forgery (CSRF)

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.82rem;">
<div>

```html
<!-- En sitio-malicioso.com -->
<form action="https://banco.com/transferir" method="POST">
  <input type="hidden" name="destino" value="atacante">
  <input type="hidden" name="monto" value="100000">
</form>
<script>document.forms[0].submit()</script>
```

- El navegador **adjunta las cookies** de `banco.com` automáticamente
- El servidor ve una petición **legítima** de la víctima
</div>
<div>

### Mitigación
- **Token CSRF** en cada formulario (`@csrf` en Blade)
- Cookies `SameSite=Lax` o `Strict`
- Verificar el encabezado `Origin` (Laravel 13: `PreventRequestForgery`)
- Nunca cambiar estado con `GET`

<br>

- 💡 Una API con **token Bearer** en `Authorization` no es vulnerable a CSRF: el navegador **no** envía ese encabezado solo. Sí lo es si usa **cookies** (Sanctum SPA)
</div>
</split-slide>

---

## SSRF, asignación masiva y *clickjacking*

<div class="grid">
<div>

### 🔁 SSRF
El servidor descarga una URL que **elige el usuario**: `http://169.254.169.254/` expone credenciales de la nube.
**Mitigar**: lista blanca de dominios, bloquear IP privadas, no seguir redirecciones
</div>
<div>

### 📦 Asignación masiva
`User::create($request->all())` permite enviar `"rol": "admin"`.
**Mitigar**: `$fillable` + `$request->validated()`
</div>
<div>

### 🖼️ *Clickjacking*
Tu sitio cargado en un `<iframe>` invisible bajo un botón trampa.
**Mitigar**: `frame-ancestors 'none'` en la CSP
</div>
<div>

### 🔀 Redirección abierta
`/login?next=https://phishing.com`.
**Mitigar**: redirigir solo a rutas internas
</div>
<div>

### 📁 Recorrido de rutas
`?archivo=../../.env`.
**Mitigar**: nunca usar el nombre del usuario como ruta; usar identificadores
</div>
<div>

### 📤 Carga de archivos
Un `.php` subido como "foto".
**Mitigar**: validar tipo real (`mimes`), renombrar, guardar fuera de `public/`
</div>
</div>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00023_.png);
}
</style>
# 6.2 Autenticación y autorización
## Contenidos
- ¿Quién eres? vs. ¿qué puedes hacer?
- Contraseñas, MFA y *passkeys*
- Sesiones, tokens y JWT
- Roles, políticas y defensa en profundidad

---

## Autenticación ≠ Autorización

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.92rem;">
<div>

### 🪪 Autenticación — *¿quién eres?*
- Verificar la identidad
- Contraseña, token, *passkey*, biometría
- Falla → **401 Unauthorized**
</div>
<div>

### 🔑 Autorización — *¿qué puedes hacer?*
- Verificar los permisos de una identidad **ya conocida**
- Roles, políticas, permisos por recurso
- Falla → **403 Forbidden**
</div>
</split-slide>

- 🚩 *A01 Pérdida de control de acceso* es la vulnerabilidad **n.º 1**: casi siempre es una autorización que **no** se verificó a nivel de recurso
- 📚 El detalle con Sanctum, políticas y pruebas está en el tema [Desarrollo Backend](../backend/)

---

## Factores de autenticación

<div class="grid">
<div>

### 🧠 Algo que sabes
Contraseña, PIN. El factor más débil: se reutiliza, se filtra, se adivina
</div>
<div>

### 📱 Algo que tienes
App TOTP (Google/Microsoft Authenticator), llave física (YubiKey), el teléfono
</div>
<div>

### 👆 Algo que eres
Huella, rostro. En móvil, **siempre** a través del sistema operativo, nunca procesado por la app
</div>
</div>

- 🔐 **MFA** = combinar al menos dos factores **distintos**. Una contraseña + una pregunta secreta **no** es MFA
- 🚩 El SMS es el segundo factor más débil: vulnerable a *SIM swapping*
- 🆕 **Passkeys (WebAuthn / FIDO2)**: un par de claves por sitio, guardado en el dispositivo. **Inmunes al *phishing***: la clave solo responde al dominio legítimo

---

## Contraseñas: lo que dice NIST SP 800-63B

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.85rem;">
<div>

### ✅ Hacer
- Mínimo **8** caracteres (15 si es el único factor), permitir hasta **64**
- Aceptar **espacios, Unicode** y pegar desde el gestor de contraseñas
- Comparar contra listas de contraseñas **filtradas**
- Limitar intentos (*rate limiting*)
- Guardar con `argon2id` o `bcrypt` (con sal)
</div>
<div>

### ❌ No hacer
- Forzar reglas de composición arbitrarias
- Obligar a cambiarla **periódicamente** sin motivo
- Preguntas de seguridad
- Pistas de contraseña
- Guardarla en texto plano, cifrada reversible o con `md5`/`sha1`
</div>
</split-slide>

```php
'password' => ['required', 'confirmed', Password::min(12)->uncompromised()],
// casts(): 'password' => 'hashed'     → bcrypt / argon2 automático
```

---

## Sesiones, tokens y JWT

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.82rem;">
<div>

### 🍪 Cookie de sesión
- Ideal para la **web del mismo dominio**
- Atributos obligatorios:

```http
Set-Cookie: sesion=abc123;
  HttpOnly; Secure; SameSite=Lax;
  Path=/; Max-Age=7200
```

- Regenerar el ID al iniciar sesión (*session fixation*)

### 🎫 Token opaco (Sanctum)
- Cadena aleatoria guardada en la BD
- ✅ Revocación inmediata
</div>
<div>

### 🪙 JWT
`encabezado.carga.firma` en Base64URL

- La carga **no está cifrada**: cualquiera la lee en [jwt.io](https://jwt.io)
- 🚩 Nunca meter datos sensibles en la carga
- 🚩 Rechazar `alg: none` y fijar el algoritmo esperado
- 🚩 Validar `exp`, `iss` y `aud`
- Revocar es difícil: expiración **corta** (minutos) + *refresh token* con **rotación**

<br>

- 💡 **OAuth 2.0 / OpenID Connect** para "Iniciar sesión con…": en apps móviles y SPA, flujo **Authorization Code + PKCE**
</div>
</split-slide>

---

## Autorización: roles, políticas y dos capas

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.78rem;">
<div>

```php
class PedidoPolicy
{
    public function view(User $user, Pedido $pedido): bool
    {
        return $user->hasRole('admin')
            || $pedido->cliente->user_id === $user->id;
    }
}
```

```php
// Capa 1 — controlador
$this->authorize('view', $pedido);

// Capa 2 — servicio
Gate::forUser($actor)->authorize('view', $pedido);
```
</div>
<div>

- **RBAC** (por rol): *¿puede ver pedidos?*
- **Por recurso** (política): *¿puede ver **este** pedido?*
- **ABAC** (por atributos): hora, ubicación, estado del recurso

<br>

- 🧅 Verificar en **dos capas**: un comando, una cola u otro controlador pueden llamar al servicio sin pasar por la ruta
- 🚩 Ocultar el botón en el front-end **no** es autorización
</div>
</split-slide>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00050_.png);
}
</style>
# 6.3 Seguridad en el manejo de datos
## Contenidos
- Clasificación y minimización
- Cifrado en tránsito y en reposo
- Datos en el dispositivo móvil
- Privacidad y Ley 8968

---

## Clasificar antes de proteger

| Nivel | Ejemplos | Tratamiento |
|:--|:--|:--|
| 🟢 **Público** | Catálogo, precios | Puede cachearse y exponerse |
| 🟡 **Interno** | Estadísticas, configuración | Solo usuarios autenticados |
| 🟠 **Confidencial** | Nombre, correo, teléfono, dirección | Control de acceso por recurso, no aparece en bitácoras |
| 🔴 **Sensible / restringido** | Contraseñas, tokens, cédula, salud, tarjetas | Derivación o cifrado, enmascarado, acceso auditado |

- 🧹 **Minimización**: el dato más seguro es el que **no se recolecta**. ¿Realmente necesitas la fecha de nacimiento completa?
- ⏳ **Retención**: define cuánto tiempo se guarda y bórralo después
- 💳 Tarjetas: **nunca** las guardes tú; usa la pasarela (tokenización) y quedas fuera del alcance de PCI DSS

---

## Cifrado en tránsito y en reposo

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.82rem;">
<div>

### 🚚 En tránsito
- **HTTPS en todo**, también en desarrollo móvil contra la API
- TLS **1.2+** (idealmente 1.3)
- **HSTS** obliga al navegador a usar HTTPS
- Certificados gratuitos con **Let's Encrypt**
- Conexión **cifrada** también a la BD y a otros servicios internos
</div>
<div>

### 💾 En reposo
- Contraseñas → **derivación** (`argon2id`, `bcrypt`), no cifrado
- Datos que hay que leer después → **cifrado simétrico** (AES-256-GCM)
- Respaldos cifrados y probados

```php
// Laravel: cifrado transparente con APP_KEY
protected function casts(): array
{
    return [
        'cedula'   => 'encrypted',
        'telefono' => 'encrypted',
    ];
}
```
</div>
</split-slide>

- 🚩 **No inventes criptografía**: usa las bibliotecas del framework. Derivar ≠ cifrar ≠ codificar (Base64 **no** es cifrado)

---

## Datos que salen de la API

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.8rem;">
<div>

```php
// ❌ Devuelve TODAS las columnas del modelo
return User::find($id);

// ✅ Recurso explícito: solo lo necesario
class UsuarioResource extends JsonResource
{
    public function toArray($request): array
    {
        return [
            'id'     => $this->id,
            'nombre' => $this->name,
            'correo' => $this->when(
                $request->user()->can('verCorreo', $this->resource),
                $this->email),
        ];
    }
}
```
</div>
<div>

- 🙈 `$hidden` en el modelo es una red de seguridad, **no** la estrategia
- 🎭 Enmascarar en pantalla: `**** **** **** 4242`
- 📋 Bitácoras **sin** contraseñas, tokens ni datos personales
- 🚫 Errores sin trazas de pila ni mensajes de la BD
- 🔎 El front-end **no** debe recibir datos "para filtrarlos después": lo que llega al cliente, el atacante lo ve en DevTools
</div>
</split-slide>

---

## Datos en el dispositivo móvil

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.8rem;">
<div>

### ❌ Inseguro
- `localStorage` / `@capacitor/preferences`: texto plano
- SQLite sin cifrar
- Archivos en almacenamiento **externo** compartido
- Datos sensibles en **bitácoras** (`console.log` → `adb logcat`)
- Capturas automáticas al pasar a segundo plano
- Portapapeles con datos sensibles
</div>
<div>

### ✅ Seguro
- Tokens y secretos en **Keychain** (iOS) / **Keystore** (Android) mediante un plugin de *secure storage*
- SQLite cifrado (SQLCipher) si hay datos sensibles offline
- Ocultar la vista al ir a segundo plano (`FLAG_SECURE` en Android)
- Borrar los datos al cerrar sesión
- Biometría para **desbloquear** la clave del Keystore, no como contraseña
</div>
</split-slide>

- 📱 Supón que el dispositivo puede **perderse, rootearse o tener *malware***: OWASP Mobile **M9 — Almacenamiento inseguro de datos**

---

## Privacidad: Ley 8968 y buenas prácticas

<div class="grid">
<div>

### 📝 Consentimiento informado
Explicar **qué** datos, **para qué** y **por cuánto** tiempo antes de recolectarlos
</div>
<div>

### 🧾 Derechos ARCO
Acceso, Rectificación, Cancelación y Oposición: la app debe permitir ejercerlos
</div>
<div>

### 🏛️ PRODHAB
Agencia de Protección de Datos de los Habitantes: supervisa la Ley 8968 en Costa Rica
</div>
<div>

### 📍 Permisos móviles
Pedir **solo** los necesarios, **cuando** se necesitan, explicando el motivo (`Info.plist`, `AndroidManifest.xml`)
</div>
<div>

### 🌍 Alcance internacional
Usuarios en Europa → **RGPD**; tiendas de apps exigen **política de privacidad** y etiquetas de datos
</div>
<div>

### 🕵️ Seudonimizar
Analítica y pruebas con datos **anonimizados**, nunca con la copia de producción
</div>
</div>

- OWASP Mobile **M6 — Controles de privacidad inadecuados**

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00069_.png);
}
</style>
# 6.4 Seguridad en el código
## Contenidos
- Validación de entradas
- Secretos fuera del código
- Dependencias y cadena de suministro
- Revisión y análisis estático

---

## Validación: entradas y salidas

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.8rem;">
<div>

```php
// Form Request: el controlador nunca ve datos sin validar
public function rules(): array
{
    return [
        'cantidad'  => ['required', 'integer', 'between:1,100'],
        'correo'    => ['required', 'email:rfc,dns'],
        'estado'    => ['required', Rule::in(['borrador', 'enviado'])],
        'foto'      => ['image', 'mimes:jpg,png', 'max:2048'],
    ];
}

// Solo los campos validados
$pedido = Pedido::create($request->validated());
```
</div>
<div>

- ✅ **Lista blanca** (qué se acepta) mejor que lista negra (qué se rechaza)
- Validar **tipo, longitud, rango y formato**
- La validación del cliente es para la **experiencia**; la del servidor es para la **seguridad**
- **Codificar la salida** según el contexto: HTML, URL, JS, SQL
- OWASP Mobile **M4 — Validación insuficiente de entradas/salidas**
</div>
</split-slide>

---

## Manejo de errores y condiciones excepcionales

- 🆕 **A10:2025** — un error mal manejado puede dejar el sistema en un estado inseguro

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.82rem;">
<div>

```php
// ❌ Falla ABIERTO: si el servicio cae, deja pasar
try {
    $permitido = $autorizador->verificar($user);
} catch (Throwable $e) {
    $permitido = true;
}

// ✅ Falla CERRADO
try {
    $permitido = $autorizador->verificar($user);
} catch (Throwable $e) {
    report($e);
    $permitido = false;
}
```
</div>
<div>

- 🔒 **Fallar cerrado**: ante la duda, denegar
- 🔄 Usar **transacciones**: o se hace todo o nada
- 🙊 Al usuario, un mensaje genérico; el detalle, a la bitácora
- 🧯 Manejador **centralizado** de excepciones (`bootstrap/app.php`)
- ⏱️ *Timeouts* en llamadas externas
</div>
</split-slide>

---

## Secretos fuera del código

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.82rem;">
<div>

```bash
# .gitignore
.env
.env.*
!.env.example
*.pem
*.key
google-services.json   # si contiene claves privadas
```

```bash
# Detectar secretos antes de que lleguen al repo
gitleaks detect --source .
gitleaks protect --staged   # en un hook pre-commit
```
</div>
<div>

- 🚩 Un secreto que llegó a Git **está filtrado**, aunque lo borres en el siguiente commit: hay que **rotarlo**
- 📱 **En la app móvil no hay secretos**: el APK/IPA se descompila en minutos. Cualquier clave en el bundle (`VITE_*`, `import.meta.env`) es **pública**
- Las claves privadas de terceros (pagos, correo) viven **solo** en el back-end
- En producción: variables de entorno o un **gestor de secretos** (Vault, AWS Secrets Manager, GitHub Secrets)
- OWASP Mobile **M1 — Uso inadecuado de credenciales**
</div>
</split-slide>

---

## Cadena de suministro de software

- 🆕 **A03:2025** y Mobile **M2**: tu código incluye cientos de dependencias que **no escribiste**

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.8rem;">
<div>

### Ataques reales
- **axios** comprometido en npm (marzo 2026): versiones con troyano de acceso remoto
- *Typosquatting*: `expres` en vez de `express`
- Scripts `postinstall` que roban variables de entorno
- Mantenedores que venden o abandonan el paquete
</div>
<div>

### Defensas
```bash
pnpm audit          # npm audit
composer audit
```

```yaml
# pnpm-workspace.yaml — esperar 24 h
minimumReleaseAge: 1440
```

- Confirmar el **lockfile** (`pnpm-lock.yaml`, `composer.lock`)
- Instalar en CI con `--frozen-lockfile`
- **Dependabot** / **Renovate** para actualizaciones
- **SRI** para scripts de CDN
</div>
</split-slide>

---

## Integridad: SRI y firmas

<split-slide style="--left: 58%; --right: 42%; --font-size: 0.82rem;">
<div>

```html
<!-- Si el CDN es comprometido, el navegador NO ejecuta el archivo -->
<script
  src="https://cdn.jsdelivr.net/npm/dompurify@3.2.6/dist/purify.min.js"
  integrity="sha384-..."
  crossorigin="anonymous"></script>
```

```bash
# Calcular el hash
curl -s URL | openssl dgst -sha384 -binary | openssl base64 -A
```
</div>
<div>

- **A08:2025** — integridad del software y de los datos
- Firmar los artefactos: APK/AAB con la clave de la tienda, imágenes de contenedor con **cosign**
- Generar un **SBOM** (lista de componentes) en el pipeline
- 🚩 Nunca deserializar objetos que vengan del cliente (`unserialize()` en PHP)
</div>
</split-slide>

---

## Revisión de código y análisis estático (SAST)

<div class="grid">
<div>

### 👀 Revisión entre pares
Pull requests obligatorios. Lista de verificación: autorización, validación, secretos, consultas crudas
</div>
<div>

### 🔍 SAST
Analiza el código fuente sin ejecutarlo: **Semgrep**, **SonarQube**, **CodeQL**
</div>
<div>

### 🧹 Linters de seguridad
`eslint-plugin-security`, **PHPStan** / **Larastan**, **Psalm** (*taint analysis*)
</div>
<div>

### 🧪 Pruebas de seguridad
Pruebas que verifican **403** al acceder a recursos ajenos y **429** al superar el límite
</div>
</div>

```bash
semgrep scan --config p/owasp-top-ten --config p/php
```

- 💡 *Shift left*: cuanto antes se detecta una falla, más barato es corregirla

---

## Seguridad del código móvil

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.82rem;">
<div>

### 🧩 Protección del binario (M7)
- Activar **ofuscación** y *minify* en el build de release (R8 / ProGuard en Android)
- Desactivar `debuggable` y el inspector del WebView en producción
- Detectar *root* / *jailbreak* para funciones críticas (sin depender solo de eso)
- **Play Integrity API** / **App Attest** para que la API sepa que habla con la app legítima
</div>
<div>

### 🌐 WebView (Capacitor / Ionic)
- Cargar **solo** contenido local de la app; no navegar a URLs arbitrarias
- `allowNavigation` restringido en `capacitor.config.ts`
- **CSP** también dentro de la app
- Validar los *deep links*: un enlace `miapp://` puede venir de cualquier sitio
- Plugins nativos: revisar qué permisos agregan
</div>
</split-slide>

- 🚩 La ofuscación **retrasa** al atacante, no lo detiene: la seguridad real sigue estando en el servidor

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00033_peticiones.png);
}
</style>
# 6.5 Seguridad en las APIs
## Contenidos
- OWASP API Security Top 10:2023
- Limitación de tasa y consumo de recursos
- CORS y encabezados
- Comunicación segura desde el móvil

---

## OWASP API Security Top 10:2023

| # | Riesgo | Mitigación |
|:--|:--|:--|
| **API1** | Autorización rota a nivel de **objeto** (BOLA) | Política por recurso: `authorize('view', $pedido)` |
| **API2** | Autenticación rota | Sanctum/JWT bien configurado, `throttle` en login |
| **API3** | Autorización rota a nivel de **propiedad** | Recursos de API + `$fillable` / `validated()` |
| **API4** | Consumo de recursos sin restricción | *Rate limiting*, tope de paginación, tamaño máximo |
| **API5** | Autorización rota a nivel de **función** (BFLA) | `DELETE /api/usuarios/7` solo para admin |
| **API6** | Acceso sin restricción a flujos de negocio sensibles | Anti-automatización en compra, registro, cupones |
| **API7** | *Server-Side Request Forgery* (SSRF) | Lista blanca de destinos |
| **API8** | Configuración de seguridad incorrecta | CORS explícito, sin `APP_DEBUG`, encabezados |
| **API9** | Gestión inadecuada del inventario | Retirar `/api/v1` viejas, documentar todo en OpenAPI |
| **API10** | Consumo inseguro de APIs de terceros | Validar sus respuestas como si vinieran del usuario |

---

## BOLA: la vulnerabilidad n.º 1 de las APIs

<steps>
<step>

```http
GET /api/pedidos/42        Authorization: Bearer <token de Ana>   → 200 ✅ (es de Ana)
GET /api/pedidos/43        Authorization: Bearer <token de Ana>   → 200 🚩 (es de Luis)
```

</step>
<step>

```php
// ✅ Opción 1 — política
public function show(Pedido $pedido) {
    $this->authorize('view', $pedido);      // 403 si no es suya
    return new PedidoResource($pedido);
}

// ✅ Opción 2 — consultar siempre desde el dueño
public function show(int $id) {
    return new PedidoResource(
        auth()->user()->pedidos()->findOrFail($id)   // 404 si no es suya
    );
}
```

- 🚩 Usar UUID en vez de enteros **dificulta** adivinar, pero **no** reemplaza la verificación

</step>
</steps>

---

## Consumo de recursos y limitación de tasa

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.8rem;">
<div>

```php
// AppServiceProvider
RateLimiter::for('api', fn ($r) =>
    Limit::perMinute(60)->by($r->user()?->id ?: $r->ip()));

RateLimiter::for('login', fn ($r) => [
    Limit::perMinute(5)->by($r->ip()),
    Limit::perMinute(5)->by($r->input('email')),
]);
```

```php
// Tope de página
$porPagina = min((int) $request->input('per_page', 15), 100);
return Pedido::paginate($porPagina);
```
</div>
<div>

- Respuesta **429 Too Many Requests** con `Retry-After`
- Limitar **por usuario y por IP**
- Tope a: tamaño del cuerpo, archivos, elementos por página, profundidad de consultas (GraphQL)
- *Timeouts* en consultas y trabajos
- Protege contra **fuerza bruta**, *scraping* y costos inesperados en la nube
</div>
</split-slide>

---

## CORS y encabezados de seguridad

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.72rem;">
<div>

```php
// config/cors.php
'paths'                => ['api/*'],
'allowed_origins'      => ['https://proyecto.ucr.ac.cr',
                           'capacitor://localhost',
                           'https://localhost'],
'allowed_methods'      => ['GET','POST','PUT','DELETE'],
'allowed_headers'      => ['Authorization','Content-Type'],
'supports_credentials' => false,
```

```http
Content-Security-Policy: default-src 'self'; frame-ancestors 'none'
Strict-Transport-Security: max-age=31536000; includeSubDomains
X-Content-Type-Options: nosniff
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: geolocation=(), camera=()
Cache-Control: no-store            ← respuestas con datos personales
```
</div>
<div>

### 🚩 Malentendido frecuente
> *CORS **no es un control de autorización**: protege al navegador de la persona usuaria, no a la API. `curl` o Postman lo ignoran.*

<br>

- `*` con credenciales es rechazado por los navegadores
- Quitar encabezados que revelan versiones: `X-Powered-By`, `Server`
- Verifica tu sitio en **securityheaders.com**
</div>
</split-slide>

---

## Comunicación segura desde la app móvil

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.8rem;">
<div>

### 🔐 M5 — Comunicación insegura
- **Solo HTTPS**: Android bloquea HTTP por defecto desde la API 28, iOS con **ATS**
- No desactivar la validación de certificados "para que funcione en desarrollo"

```xml
<!-- res/xml/network_security_config.xml -->
<network-security-config>
  <base-config cleartextTrafficPermitted="false" />
  <!-- solo en debug: 10.0.2.2 para el emulador -->
</network-security-config>
```
</div>
<div>

### 📌 *Certificate pinning*
- La app solo acepta **el** certificado (o clave pública) del servidor propio
- Protege contra proxies maliciosos y CA comprometidas
- ⚠️ Requiere plan de rotación: si el certificado cambia, la app deja de funcionar
- Reservado para apps de alto riesgo (banca, salud)

<br>

- 💡 La app es un cliente **más**: la API aplica **las mismas** reglas que a la web
</div>
</split-slide>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00070_.png);
}
</style>
# 6.6 Seguridad en el despliegue
## Contenidos
- Configuración por entorno
- Servidor, contenedores y red
- CI/CD seguro (DevSecOps)
- Registro, monitoreo y respuesta

---

## Configuración por entorno

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.82rem;">
<div>

```bash
# .env de producción — no negociable
APP_ENV=production
APP_DEBUG=false
APP_URL=https://proyecto.ucr.ac.cr
SESSION_SECURE_COOKIE=true
LOG_LEVEL=warning
```

```bash
# Despliegue de Laravel
composer install --no-dev --optimize-autoloader
php artisan config:cache
php artisan route:cache
```
</div>
<div>

- **A02:2025 — Configuración incorrecta** sube al **2.º** lugar
- 🚩 `APP_DEBUG=true` muestra variables de entorno, rutas y consultas a cualquiera
- Sin dependencias de desarrollo en producción
- Documentación interactiva (Swagger UI, Telescope, phpMyAdmin) **apagada o protegida**
- Credenciales por defecto cambiadas
- Entornos **separados**: datos, claves y BD distintos en desarrollo, pruebas y producción
</div>
</split-slide>

---

## Servidor, contenedores y red

<div class="grid">
<div>

### 🌐 Servidor web
Solo `public/` es la raíz. `.env`, `storage/` y `.git/` **nunca** accesibles por URL
</div>
<div>

### 🧱 Red
Firewall: solo 80/443 abiertos. La BD **no** escucha en Internet
</div>
<div>

### 🐳 Contenedores
Imagen base mínima y fijada por versión, usuario **no root**, sin secretos en la imagen
</div>
<div>

### 🔄 Parches
Sistema operativo, PHP, Node y dependencias **actualizados**. Versiones con soporte de seguridad
</div>
<div>

### 🛡️ WAF / CDN
Cloudflare u otro proxy filtra ataques comunes y absorbe DDoS
</div>
<div>

### 💾 Respaldos
Automáticos, cifrados, **fuera** del servidor y con restauración **probada**
</div>
</div>

```dockerfile
FROM php:8.4-fpm-alpine
RUN adduser -D app
USER app            # nunca correr como root
```

---

## CI/CD seguro: DevSecOps

```text
 commit ──► pre-commit ──► CI ───────────────────────────────► despliegue ──► producción
           gitleaks        ├─ pruebas (Pest / Vitest)          secretos del    DAST (ZAP)
                           ├─ SAST (Semgrep / CodeQL)          entorno, no     monitoreo
                           ├─ auditoría de dependencias        del repo        alertas
                           ├─ escaneo de imagen (Trivy)
                           └─ build firmado + SBOM
```

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.82rem;">
<div>

- Rama `main` **protegida**: PR + revisión + CI en verde
- Secretos del pipeline en **GitHub Secrets**, con mínimo privilegio
- Acciones de terceros fijadas por **hash** del commit
</div>
<div>

- 📱 Móvil: la **clave de firma** (keystore / certificado de Apple) nunca en el repo; usar *Play App Signing*
- Builds de release **reproducibles** desde el pipeline, no desde la laptop de alguien
</div>
</split-slide>

---

## Registro, monitoreo y respuesta a incidentes

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.82rem;">
<div>

### 📋 Qué registrar (A09)
- Inicios de sesión exitosos **y fallidos**
- Accesos denegados (401 / 403)
- Cambios de permisos y de contraseña
- Errores 5xx y validaciones rechazadas en masa
- Con fecha, usuario, IP y un **ID de correlación**

### 🙈 Qué **no** registrar
Contraseñas, tokens, números de tarjeta, datos personales completos
</div>
<div>

### 🚨 Alertar
Un registro que nadie mira no sirve: alertas ante picos de 401, 429 o 5xx (Sentry, Grafana, Logtail)

### 🧯 Plan de respuesta
1. **Detectar** y contener
2. **Rotar** credenciales afectadas
3. **Corregir** y desplegar
4. **Notificar** a los afectados (PRODHAB si hay datos personales)
5. **Aprender**: análisis post-incidente sin culpables
</div>
</split-slide>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00071_.png);
}
</style>
# 6.7 Herramientas y recursos
## Contenidos
- Escáneres y proxies
- Análisis de dependencias y código
- Herramientas móviles
- Guías y estándares de referencia

---

## Herramientas de análisis

| Categoría | Herramienta | Para qué |
|:--|:--|:--|
| **DAST / proxy** | [OWASP ZAP](https://www.zaproxy.org), [Burp Suite Community](https://portswigger.net/burp/communitydownload) | Interceptar y atacar la app en ejecución |
| **SAST** | [Semgrep](https://semgrep.dev), [SonarQube](https://www.sonarsource.com/products/sonarqube/), [CodeQL](https://codeql.github.com) | Encontrar patrones inseguros en el código |
| **Dependencias** | `pnpm audit`, `composer audit`, [Dependabot](https://docs.github.com/code-security/dependabot), [Snyk](https://snyk.io), [OSV-Scanner](https://google.github.io/osv-scanner/) | Vulnerabilidades conocidas en librerías |
| **Secretos** | [Gitleaks](https://github.com/gitleaks/gitleaks), [TruffleHog](https://github.com/trufflesecurity/trufflehog), *secret scanning* de GitHub | Claves filtradas en el repositorio |
| **Contenedores** | [Trivy](https://trivy.dev) | Vulnerabilidades en imágenes y configuración |
| **Móvil** | [MobSF](https://github.com/MobSF/Mobile-Security-Framework-MobSF), [Frida](https://frida.re), [jadx](https://github.com/skylot/jadx) | Análisis estático/dinámico de APK e IPA |
| **API** | Postman, [Bruno](https://www.usebruno.com), pruebas Pest | Probar BOLA, 401/403/429 a mano y automatizado |

---

## Verificadores en línea

<div class="grid">
<div>

### 🧾 [securityheaders.com](https://securityheaders.com)
Califica los encabezados de seguridad de un sitio
</div>
<div>

### 🔒 [SSL Labs](https://www.ssllabs.com/ssltest/)
Configuración TLS y certificado del servidor
</div>
<div>

### 🦊 [Mozilla Observatory](https://developer.mozilla.org/observatory)
Encabezados, cookies, CSP y buenas prácticas
</div>
<div>

### 🧪 [CSP Evaluator](https://csp-evaluator.withgoogle.com)
Detecta debilidades en una política CSP
</div>
<div>

### 🔓 [Have I Been Pwned](https://haveibeenpwned.com)
¿Mi correo o contraseña aparecen en una filtración?
</div>
<div>

### 🪙 [jwt.io](https://jwt.io)
Decodificar un JWT (¡nunca pegues tokens de producción!)
</div>
</div>

- ⚖️ Escanea **solo** tus propios dominios

---

## Estándares, guías y práctica

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.85rem;">
<div>

### 📚 Estándares OWASP
- [OWASP Top 10:2025](https://top10.owasp.org/2025/es/)
- [OWASP API Security Top 10](https://owasp.org/API-Security/)
- [OWASP Mobile Top 10](https://owasp.org/www-project-mobile-top-10/)
- [ASVS](https://owasp.org/www-project-application-security-verification-standard/) — lista de verificación para web
- [MASVS / MASTG](https://mas.owasp.org) — estándar y guía de pruebas móviles
- [Cheat Sheet Series](https://cheatsheetseries.owasp.org) — recetas concretas por tema
</div>
<div>

### 🏋️ Para practicar (legalmente)
- [PortSwigger Web Security Academy](https://portswigger.net/web-security) — laboratorios gratuitos
- [OWASP Juice Shop](https://owasp.org/www-project-juice-shop/) — tienda vulnerable a propósito
- [DVWA](https://github.com/digininja/DVWA) — app PHP vulnerable
- [OWASP UnCrackable Apps](https://mas.owasp.org/crackmes/) — retos móviles

### 📰 Mantenerse al día
- [CVE](https://www.cve.org) / [NVD](https://nvd.nist.gov), avisos de seguridad de Laravel y Node
</div>
</split-slide>

---

## Lista de verificación del proyecto

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.8rem;">
<div>

### 🔐 API
- [ ] Toda ruta privada con `auth:sanctum`
- [ ] Política por recurso (prueba con el ID de otro usuario → 403/404)
- [ ] `throttle` en login y en la API general
- [ ] Form Requests + `validated()`
- [ ] Recursos de API, nunca el modelo
- [ ] CORS con orígenes explícitos
- [ ] Encabezados de seguridad
</div>
<div>

### 📱 Cliente y despliegue
- [ ] Token en almacenamiento seguro, no en `localStorage`
- [ ] Ningún secreto en el bundle web/móvil
- [ ] Sin `v-html` con datos del usuario
- [ ] `.env` fuera del repo, `APP_DEBUG=false`
- [ ] `pnpm audit` / `composer audit` sin altas
- [ ] HTTPS con HSTS
- [ ] Bitácora de accesos denegados
</div>
</split-slide>

---

## Actividad de cierre

<div class="grid">
<div>

### 1️⃣ Atacar
Con **ZAP** sobre `localhost`, escanear la API del equipo y listar los hallazgos
</div>
<div>

### 2️⃣ BOLA
Con dos usuarios, intentar leer y borrar los recursos del otro. Documentar el código de respuesta
</div>
<div>

### 3️⃣ Dependencias
Ejecutar `pnpm audit`, `composer audit` y `gitleaks`. Corregir lo crítico
</div>
<div>

### 4️⃣ Encabezados
Agregar CSP y HSTS, y verificar con las DevTools del navegador
</div>
</div>

- 📋 Entregable: tabla **vulnerabilidad → evidencia → mitigación → prueba que lo demuestra**

---

## Referencias

- [OWASP Top 10:2025](https://top10.owasp.org/2025/es/)
- [OWASP API Security Top 10:2023](https://owasp.org/API-Security/editions/2023/es/0x00-header/)
- [OWASP Mobile Top 10:2024](https://owasp.org/www-project-mobile-top-10/)
- [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org)
- [MDN — Seguridad web](https://developer.mozilla.org/es/docs/Web/Security)
- [MDN — Content Security Policy](https://developer.mozilla.org/es/docs/Web/HTTP/Guides/CSP)
- [NIST SP 800-63B — Identidad digital](https://pages.nist.gov/800-63-4/sp800-63b.html)
- [Laravel — Seguridad: autenticación, autorización, cifrado y hashing](https://laravel.com/docs/13.x/authentication)
- [Capacitor — Guía de seguridad](https://capacitorjs.com/docs/guides/security)
- [Ley 8968 — Protección de la persona frente al tratamiento de sus datos personales (PRODHAB)](https://www.prodhab.go.cr)
- [Desarrollo Backend](../backend/) · [Desarrollo Móvil](../desarrollo-movil/) — temas del curso relacionados

<script src="../assets/steps.js"></script>
<script src="../assets/image-modal.js"></script>
