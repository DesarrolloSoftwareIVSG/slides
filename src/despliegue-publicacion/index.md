---
marp: true
theme: alo
paginate: true
---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00054_.png);
}
</style>
# Despliegue y publicación
## Contenidos
- 7.1 Despliegue de aplicaciones web
  - Empaquetado · Hospedaje · Servidores web · Dominios · Despliegue
- 7.2 Publicación de aplicaciones móviles

> **Vue + Vite** y **Laravel 13** en la web · **Ionic + Capacitor** en móvil <br>Curso *Desarrollo de Software IV* · II Ciclo 2026

---

## ¿Qué significa "desplegar"?

- **Desplegar** es llevar el código desde la máquina del equipo hasta un lugar donde **los usuarios reales** puedan usarlo
- **Publicar** (en móvil) es hacerlo llegar a una **tienda** que lo distribuye a los dispositivos

```text
 código ──► empaquetado ──► artefacto ──► hospedaje ──► servidor web ──► dominio ──► 👤 usuario
 (repo)     (build)         (dist/,       (dónde        (quién sirve      (cómo lo
                             imagen,       vive)         las peticiones)   encuentra)
                             AAB / IPA)
```

<div class="grid">
<div>

### 📦 Empaquetado
Transformar el código fuente en algo **ejecutable y optimizado**
</div>
<div>

### 🏠 Hospedaje
Elegir **dónde** corre: CDN, PaaS, VPS, contenedores
</div>
<div>

### 🌐 Servidor web
El programa que **atiende** HTTP/HTTPS: Nginx, Apache, Caddy
</div>
<div>

### 🔤 Dominio
El **nombre** que se traduce, vía DNS, a una dirección IP
</div>
</div>

---

## Entornos: no todo se prueba en producción

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.88rem;">
<div>

| Entorno | Para qué | Datos |
|:--|:--|:--|
| 💻 **Local** | Desarrollar | Ficticios (*seeders*) |
| 🧪 **Pruebas / CI** | Ejecutar las pruebas automáticas | Efímeros |
| 🎭 **Staging** | Ensayo general, idéntico a producción | Anonimizados |
| 🚀 **Producción** | Usuarios reales | Reales |

</div>
<div>

- Cada entorno tiene su **propio** `.env`, base de datos y claves
- *Staging* debe parecerse a producción lo **más posible**: misma versión de PHP, Node, BD y servidor web
- 🚩 "En mi máquina sí funciona" casi siempre es una **diferencia de entorno**
- 💡 Los **12 factores** ([12factor.net](https://12factor.net/es/)): la configuración vive en **variables de entorno**, no en el código

</div>
</split-slide>

---

## ¿Qué desplegamos en el proyecto del curso?

<div class="grid">
<div>

### 🖼️ Front-end SPA (Vue + Vite)
Archivos **estáticos**: HTML, CSS, JS e imágenes. Cualquier CDN los sirve
</div>
<div>

### ⚙️ API (Laravel 13)
Proceso **dinámico**: PHP-FPM + base de datos + colas. Necesita un servidor
</div>
<div>

### 🧭 Sitio SSR / SSG (Astro, Nuxt)
SSG = estático. SSR = necesita un **runtime** (Node, *edge*)
</div>
<div>

### 📱 App móvil (Ionic + Capacitor)
Un **AAB** para Google Play y un **IPA** para la App Store, firmados
</div>
</div>

- 🔑 La pregunta que decide todo: **¿hay código que se ejecuta en el servidor por cada petición?** Si no, el despliegue es mucho más simple y barato

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00055_.png);
}
</style>
# 7.1.1 Empaquetado
## Contenidos
- Qué hace un *build* de producción
- Vite: `dist/`, variables de entorno y `base`
- Empaquetado de Laravel
- Contenedores como artefacto

---

## ¿Qué hace un *build* de producción?

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.88rem;">
<div>

### 🔧 Transformaciones
- **Transpilar**: TypeScript, `.vue`, JSX → JavaScript que entiende el navegador
- **Empaquetar** (*bundling*): cientos de módulos → pocos archivos
- **Minificar**: quitar espacios, acortar nombres
- **Tree shaking**: eliminar código que nunca se usa
- **Code splitting**: un *chunk* por ruta con `import()` dinámico
- **Optimizar** imágenes y CSS

</div>
<div>

### 🏷️ Nombres con *hash*

```text
dist/
├── index.html
└── assets/
    ├── index-B4x9kQ2a.js
    ├── index-D8fLm1zc.css
    └── PedidosView-Cq3pT7.js
```

- El *hash* cambia **solo si cambia el contenido**
- Permite caché agresiva: `Cache-Control: max-age=31536000, immutable`
- `index.html` **nunca** se cachea: es el que apunta a los nuevos archivos

</div>
</split-slide>

- 🔁 Lo medimos en *Rendimiento web*: el tamaño del *bundle* impacta directamente en LCP e INP

---

## Build con Vite

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.85rem;">
<div>

```bash
pnpm run build     # vite build → genera dist/
pnpm run preview   # sirve dist/ localmente
```

```js
// vite.config.js
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';

export default defineConfig({
  plugins: [vue()],
  base: '/',            // '/repo/' en GitHub Pages
  build: {
    outDir: 'dist',
    sourcemap: false,   // no publicar el código fuente
  },
});
```

</div>
<div>

- 🚩 **Nunca** se despliega lo que sirve `pnpm dev`: es un servidor de desarrollo, sin optimizar
- **Siempre** probar con `preview` antes de subir: aparecen errores de rutas y de `base`
- `base` es la **ruta** donde vivirá el sitio. En GitHub Pages es `/nombre-repo/` (lo vimos en *JS avanzado*)
- Los *source maps* ayudan a depurar, pero exponen el código: súbelos a la herramienta de errores (Sentry), no al público

</div>
</split-slide>

---

## Variables de entorno en el front-end

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.85rem;">
<div>

```bash
# .env.development
VITE_API_URL=http://localhost:8000/api

# .env.production
VITE_API_URL=https://api.proyecto.ucr.ac.cr/api
```

```js
const api = import.meta.env.VITE_API_URL;
```

```bash
vite build                  # usa .env.production
vite build --mode staging   # usa .env.staging
```

</div>
<div>

- Vite **reemplaza** las variables `VITE_*` en el momento del *build*: quedan **escritas** en el JS final
- ⚠️ Por eso **no son secretas**: cualquiera las lee con las DevTools
- Solo van aquí datos **públicos**: URL de la API, clave pública de mapas, nombre del entorno
- Cambiar una variable exige **volver a compilar** el front-end
- 🔐 Los secretos viven **solo** en el servidor (`.env` de Laravel), nunca en el *bundle*

</div>
</split-slide>

---

## Empaquetado de una aplicación Laravel

<split-slide style="--left: 52%; --right: 48%; --font-size: 0.82rem;">
<div>

```bash
# 1. Dependencias de PHP, sin las de desarrollo
composer install --no-dev --optimize-autoloader

# 2. Recursos del front-end (si Laravel los sirve)
pnpm install --frozen-lockfile
pnpm run build          # public/build/ + manifest

# 3. Cachés de producción
php artisan optimize    # config, rutas, vistas y eventos

# 4. Base de datos
php artisan migrate --force

# 5. Enlace del almacenamiento público
php artisan storage:link
```

</div>
<div>

- El **artefacto** es la carpeta del proyecto **sin** `node_modules`, `tests/` ni `.env`
- `--no-dev` deja fuera Pest, Faker y herramientas de depuración
- `optimize` acelera el arranque, pero tras cambiar `.env` hay que **volver a ejecutarlo** (`optimize:clear`)
- `--force` es obligatorio: en producción Artisan **pide confirmación** antes de migrar
- 🔁 Recuerda de *Seguridad*: `APP_ENV=production` y `APP_DEBUG=false`

</div>
</split-slide>

---

## Contenedores: el artefacto que corre igual en todos lados

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.8rem;">
<div>

```dockerfile
# Etapa 1: compilar el front-end
FROM node:24-alpine AS front
WORKDIR /app
COPY package.json pnpm-lock.yaml ./
RUN corepack enable && pnpm install --frozen-lockfile
COPY . .
RUN pnpm run build

# Etapa 2: servirlo con Nginx
FROM nginx:1.28-alpine
COPY --from=front /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
```

```bash
docker build -t proyecto-web:1.4.0 .
docker run -p 8080:80 proyecto-web:1.4.0
```

</div>
<div>

- Una **imagen** empaqueta el código **y** su entorno: SO base, runtime, dependencias
- **Multi-etapa**: la imagen final no lleva Node ni `node_modules`, solo el resultado
- Etiquetar con la **versión**, no solo `latest`
- `.dockerignore` para no copiar `.env`, `.git` ni `node_modules`
- 🐳 **Docker Compose** levanta API + BD + Redis en local con un solo comando

</div>
</split-slide>

---

## Versionado semántico del artefacto

- Cada despliegue debe poder responder: **¿qué versión está en producción?**

<div class="grid">
<div>

### 🔢 SemVer
`MAYOR.MENOR.PARCHE` → `2.3.1`
Mayor rompe compatibilidad, menor agrega, parche corrige
</div>
<div>

### 🏷️ Etiquetas de Git
`git tag v2.3.1` + `git push --tags`
La etiqueta apunta al commit **exacto** desplegado
</div>
<div>

### 📝 Changelog
Qué cambió en cada versión. Se puede generar desde *Conventional Commits*
</div>
<div>

### ↩️ Reversión
Si el artefacto está versionado, volver atrás es **redesplegar** la versión anterior
</div>
</div>

- 🔁 Lo vimos en *Control de versiones*: `semver` y `package.json`

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00056_.png);
}
</style>
# 7.1.2 Hospedaje
## Contenidos
- Modelos de hospedaje
- Proveedores en 2026
- Cómo elegir
- Modelo de responsabilidad compartida

---

## Modelos de hospedaje

<style scoped>
table { font-size: 0.78em; }
</style>

| Modelo | Qué te dan | Ejemplos | Ideal para |
|:--|:--|:--|:--|
| 🌍 **Estático / CDN** | Archivos servidos desde cientos de nodos | GitHub Pages, Netlify, Cloudflare, Vercel | SPA, SSG, documentación |
| 🧩 **PaaS** | Subes el código, la plataforma ejecuta y escala | Laravel Cloud, Render, Railway, Heroku, Fly.io | APIs, equipos pequeños |
| ⚡ **Serverless / Edge** | Funciones que corren por petición, pagas por uso | Vercel Functions, Cloudflare Workers, AWS Lambda | SSR, picos de tráfico |
| 🐳 **Contenedores** | Corres tu imagen; la plataforma la orquesta | Google Cloud Run, AWS ECS, Azure Container Apps, Kubernetes | Varios servicios |
| 🖥️ **VPS / IaaS** | Una máquina virtual con *root*: todo lo instalas tú | DigitalOcean, Hetzner, AWS EC2, Linode | Control total, costo fijo |
| 🏚️ **Compartido** | Un espacio en un servidor con cPanel | Hostinger, hosting local | Sitios PHP sencillos |

- 🔁 Moverse hacia **abajo** en la tabla = **más control** y **más responsabilidad**

---

## El modelo de responsabilidad compartida

```text
                    On-premise     IaaS / VPS     PaaS          Serverless     SaaS
 Aplicación y datos    TÚ             TÚ           TÚ            TÚ            proveedor*
 Runtime (PHP, Node)   TÚ             TÚ           proveedor     proveedor     proveedor
 Sistema operativo     TÚ             TÚ           proveedor     proveedor     proveedor
 Virtualización        TÚ             proveedor    proveedor     proveedor     proveedor
 Hardware y red        TÚ             proveedor    proveedor     proveedor     proveedor
```

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.85rem;">
<div>

- En un **VPS** tú actualizas el SO, configuras el firewall, instalas PHP y renuevas certificados
- En un **PaaS** la plataforma se encarga de eso; tú solo del código y la configuración

</div>
<div>

- ⚠️ En **todos** los modelos, tus **datos** y el **control de acceso** son tu responsabilidad
- \* Incluso en SaaS sigues decidiendo quién tiene acceso

</div>
</split-slide>

---

## Opciones para el proyecto del curso

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.85rem;">
<div>

### 🖼️ Front-end (Vue + Vite)
- **GitHub Pages** — gratis, ideal para repos públicos; ruta `/repo/`
- **Netlify / Vercel** — despliegue por cada `push`, URL de vista previa por cada PR
- **Cloudflare Workers** (*static assets*) — Cloudflare ya recomienda Workers sobre Pages para proyectos nuevos

### 📱 PWA
- Igual que un sitio estático, pero **exige HTTPS** para el *service worker*

</div>
<div>

### ⚙️ API (Laravel 13)
- **Laravel Cloud** — PaaS oficial, plan inicial por uso, BD y colas administradas
- **Laravel Forge** — administra **tu propio** VPS (DigitalOcean, Hetzner, AWS)
- **Render / Railway / Fly.io** — con un `Dockerfile`
- **VPS manual** — Nginx + PHP-FPM + MySQL/PostgreSQL: el más didáctico

### 🗄️ Base de datos
- Administrada (Neon, Supabase, PlanetScale, RDS) o en el mismo VPS

</div>
</split-slide>

- 💰 Los planes gratuitos **cambian seguido** y suelen "dormir" la app tras inactividad: revisa los términos vigentes

---

## Cómo elegir un hospedaje

<div class="grid">
<div>

### ⚙️ Tipo de app
¿Estática, SSR, API con BD, colas, *websockets*?
</div>
<div>

### 💰 Costo
Fijo (VPS) vs. por uso (serverless). Cuidado con la **transferencia** de datos y los límites
</div>
<div>

### 📍 Región y latencia
Cerca de los usuarios. Para Costa Rica: regiones de EE. UU. este o México
</div>
<div>

### 📈 Escalado
¿Escala solo? ¿Escala a cero? ¿Cuánto tarda el arranque en frío?
</div>
<div>

### 🧑‍🔧 Operación
¿Quién parcha el SO, hace respaldos y renueva TLS?
</div>
<div>

### 🔒 Cumplimiento
Dónde se guardan los datos personales (**Ley 8968**), SLA, soporte
</div>
</div>

- 🚩 Evitar el **encierro** con el proveedor (*vendor lock-in*): un `Dockerfile` y variables de entorno hacen la app **portable**

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00057_.png);
}
</style>
# 7.1.3 Servidores web
## Contenidos
- Qué hace un servidor web
- Nginx, Apache, Caddy y FrankenPHP
- Configuración para una SPA y para Laravel
- Proxy inverso, HTTPS, compresión y caché

---

## ¿Qué hace un servidor web?

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.85rem;">
<div>

```text
 navegador ──HTTPS:443──► ┌───────────────┐
                          │  Servidor web │──► archivos estáticos (dist/)
                          │    (Nginx)    │
                          └───────┬───────┘
                                  │ proxy / FastCGI
                     ┌────────────┴───────────┐
                     ▼                        ▼
               PHP-FPM (Laravel)       Node (SSR, NestJS)
                     │
                     ▼
                  MySQL / PostgreSQL
```

</div>
<div>

- **Escucha** en los puertos 80 (HTTP) y 443 (HTTPS)
- **Termina TLS**: descifra HTTPS y gestiona el certificado
- **Sirve estáticos** muy rápido, sin pasar por PHP ni Node
- **Proxy inverso**: reenvía las peticiones dinámicas a la aplicación
- Comprime, cachea, limita peticiones y agrega **encabezados** de seguridad
- 🔁 La aplicación (Express, Laravel) **no** debería exponerse directamente a Internet

</div>
</split-slide>

---

## Servidores web más usados

<div class="grid">
<div>

### 🟩 Nginx
El más usado en la web. Basado en **eventos**, muy eficiente con miles de conexiones. Proxy inverso por excelencia
</div>
<div>

### 🪶 Apache HTTP Server
El clásico del stack **LAMP**. Configuración por carpeta con `.htaccess` (común en hosting compartido)
</div>
<div>

### 🔒 Caddy
Escrito en Go. **HTTPS automático** con Let's Encrypt sin configurar nada. Archivo de configuración mínimo
</div>
<div>

### 🐘 FrankenPHP
Servidor moderno sobre Caddy que **incrusta PHP**. Soportado por Laravel Octane para mantener la app en memoria
</div>
<div>

### 🪟 IIS / Kestrel
IIS en Windows Server; **Kestrel** es el servidor de ASP.NET Core (detrás de un proxy)
</div>
<div>

### ☁️ Gestionados
Los CDN y PaaS traen su propio servidor: **no** lo configuras tú
</div>
</div>

---

## Nginx para una SPA

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.8rem;">
<div>

```nginx
server {
    listen 80;
    server_name proyecto.ucr.ac.cr;
    root /var/www/proyecto/dist;
    index index.html;

    # Rutas del cliente: /pedidos/42 → index.html
    location / {
        try_files $uri $uri/ /index.html;
    }

    # Archivos con hash: caché de un año
    location /assets/ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # index.html siempre fresco
    location = /index.html {
        add_header Cache-Control "no-cache";
    }

    gzip on;
    gzip_types text/css application/javascript image/svg+xml;
}
```

</div>
<div>

- 🚩 **El error clásico**: la app funciona al navegar, pero al **recargar** `/pedidos/42` da **404**
- La causa: ese archivo no existe en disco; la ruta la resuelve **Vue Router** en el navegador
- `try_files` devuelve `index.html` para cualquier ruta desconocida
- En Netlify: `_redirects` con `/* /index.html 200`
- En GitHub Pages: copiar `index.html` como `404.html` o usar `createWebHashHistory`

</div>
</split-slide>

---

## Nginx para Laravel

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.8rem;">
<div>

```nginx
server {
    listen 80;
    server_name api.proyecto.ucr.ac.cr;
    root /var/www/api/public;      # ⚠️ solo public/

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";
    index index.php;
    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ ^/index\.php(/|$) {
        fastcgi_pass unix:/var/run/php/php8.4-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
        fastcgi_hide_header X-Powered-By;
    }

    location ~ /\.(?!well-known).* {
        deny all;                  # .env, .git
    }
}
```

</div>
<div>

- La **raíz** es `public/`: así `.env`, `storage/` y el código **no** son accesibles por URL
- **PHP-FPM** es un proceso aparte que ejecuta PHP; Nginx le habla por **FastCGI**
- Toda petición que no sea un archivo real entra por `public/index.php`
- Bloquear archivos ocultos excepto `/.well-known/` (lo usa Let's Encrypt)
- Base: la configuración recomendada en la [documentación de Laravel](https://laravel.com/docs/13.x/deployment)

</div>
</split-slide>

---

## Proxy inverso para Node y Caddy

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.82rem;">
<div>

### 🟩 Nginx delante de Node (SSR, NestJS)

```nginx
server {
    server_name app.proyecto.ucr.ac.cr;
    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

- El proceso Node lo mantiene vivo **PM2** o **systemd**; Nginx solo reenvía

</div>
<div>

### 🔒 La misma SPA con Caddy

```text
proyecto.ucr.ac.cr {
    root * /var/www/proyecto/dist
    encode zstd gzip
    try_files {path} /index.html
    file_server
}

api.proyecto.ucr.ac.cr {
    reverse_proxy 127.0.0.1:8000
}
```

- Con solo poner el **dominio**, Caddy obtiene y **renueva** el certificado HTTPS

</div>
</split-slide>

---

## HTTPS con Let's Encrypt

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.85rem;">
<div>

```bash
# Ubuntu + Nginx
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d proyecto.ucr.ac.cr \
                     -d api.proyecto.ucr.ac.cr

# Probar la renovación automática
sudo certbot renew --dry-run
```

```nginx
# Certbot agrega la redirección 80 → 443
server {
    listen 80;
    return 301 https://$host$request_uri;
}
```

</div>
<div>

- **Let's Encrypt**: autoridad certificadora **gratuita** y automatizada (protocolo **ACME**)
- Verifica que controlas el dominio con un archivo en `/.well-known/acme-challenge/` o un registro DNS
- Los certificados duran **90 días** y el ecosistema avanza hacia vigencias **más cortas**: la renovación **debe** ser automática
- HTTPS es requisito para PWA, *service workers*, geolocalización, cámara y cookies `Secure`
- 🔁 Luego agregar **HSTS** (tema de *Seguridad*) y verificar con [SSL Labs](https://www.ssllabs.com/ssltest/)

</div>
</split-slide>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00058_.png);
}
</style>
# 7.1.4 Dominios
## Contenidos
- Anatomía de un nombre de dominio
- Cómo funciona DNS
- Tipos de registro
- Registrar y conectar un dominio

---

## Anatomía de un nombre de dominio

```text
   https://api.proyecto.ac.cr/pedidos
           │   │        │  └─ TLD de país (ccTLD): .cr
           │   │        └──── segundo nivel: .ac.cr (académico)
           │   └───────────── nombre registrado
           └───────────────── subdominio (lo creas tú, gratis)
```

<div class="grid">
<div>

### 🌐 gTLD
Genéricos: `.com`, `.org`, `.net`, `.dev`, `.app`
</div>
<div>

### 🇨🇷 ccTLD
De país: `.cr`, `.co.cr`, `.ac.cr`, `.go.cr`, `.or.cr`
</div>
<div>

### 🏢 Registro (*registry*)
Administra un TLD. Para `.cr`: **NIC Costa Rica** ([nic.cr](https://www.nic.cr))
</div>
<div>

### 🛒 Registrador (*registrar*)
Te vende el dominio: Cloudflare, Namecheap, Porkbun…
</div>
</div>

- 🔁 En *Fundamentos de la web* vimos las partes de una **URL**; el dominio es solo una de ellas

---

## ¿Cómo funciona DNS?

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.82rem;">
<div>

```text
 navegador: ¿IP de api.proyecto.ac.cr?
     │
     ▼
 ① caché del navegador / del SO
     │ no está
     ▼
 ② resolvedor recursivo (ISP, 1.1.1.1, 8.8.8.8)
     │
     ├──► ③ servidor raíz (.)      → "pregunta a .cr"
     ├──► ④ servidor de .cr        → "pregunta a NS de proyecto.ac.cr"
     └──► ⑤ servidor autoritativo  → "A 203.0.113.10"
     │
     ▼
 navegador abre conexión TCP/TLS con 203.0.113.10
```

</div>
<div>

- **DNS** es la "agenda telefónica" de Internet: traduce **nombres** a **direcciones IP**
- El servidor **autoritativo** es el que tiene la respuesta oficial; lo define el registro **NS**
- Cada respuesta se guarda en caché según su **TTL** (segundos)
- 🕒 La famosa **"propagación"** no es más que esperar a que expiren las cachés
- 💡 Antes de migrar, **baja el TTL** (p. ej. a 300 s) un día antes
- 🔁 `dns-prefetch` y `preconnect` (*Rendimiento web*) adelantan este paso

</div>
</split-slide>

---

## Tipos de registro DNS

<style scoped>
table { font-size: 0.8em; }
</style>

| Registro | Para qué | Ejemplo |
|:--|:--|:--|
| **A** | Nombre → dirección **IPv4** | `api  A  203.0.113.10` |
| **AAAA** | Nombre → dirección **IPv6** | `api  AAAA  2001:db8::10` |
| **CNAME** | Alias de **otro nombre** (no se usa en la raíz) | `www  CNAME  usuario.github.io.` |
| **ALIAS / ANAME** | Como CNAME pero en la raíz (lo ofrecen algunos proveedores) | `@  ALIAS  proyecto.netlify.app.` |
| **MX** | Servidores de **correo** del dominio | `@  MX  10 mail.proveedor.com.` |
| **TXT** | Texto: verificación de dominio, SPF, DKIM, DMARC | `@  TXT  "v=spf1 include:_spf.google.com ~all"` |
| **NS** | Qué servidores son **autoritativos** | `@  NS  ana.ns.cloudflare.com.` |
| **CAA** | Qué autoridades pueden emitir certificados | `@  CAA  0 issue "letsencrypt.org"` |

- `@` representa el dominio raíz (*apex*): `proyecto.ac.cr`

---

## Registrar y conectar un dominio

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.84rem;">
<div>

### 1️⃣ Registrar
- Buscar disponibilidad y pagar **por año** (renovación anual)
- `.cr` y `.co.cr` se registran en **NIC Costa Rica** ([dominios.cr](https://www.dominios.cr)); exige al menos **dos** servidores DNS
- Activar **renovación automática** y bloqueo de transferencia
- Datos de contacto reales: si vence, **otro puede comprarlo**

### 2️⃣ Delegar el DNS
- Usar los DNS del registrador **o** apuntar los **NS** a otro proveedor (Cloudflare, Route 53)

</div>
<div>

### 3️⃣ Crear los registros

```text
@     A      203.0.113.10        ; API / VPS
www   CNAME  proyecto.ac.cr.
app   CNAME  equipo.github.io.   ; front-end
```

### 4️⃣ Verificar

```bash
dig proyecto.ac.cr A +short
dig app.proyecto.ac.cr CNAME
nslookup proyecto.ac.cr 1.1.1.1
```

- Después configurar el dominio en el proveedor (GitHub Pages → *Custom domain*) y activar **HTTPS**

</div>
</split-slide>

---

## Dominio, subdominios y CORS

<div class="grid">
<div>

### 🌐 Front y API en subdominios
`app.proyecto.ac.cr` y `api.proyecto.ac.cr`: **mismo sitio**, distinto **origen**
</div>
<div>

### 🔀 CORS
La API debe permitir **explícitamente** el origen del front-end (nunca `*` con credenciales)
</div>
<div>

### 🍪 Cookies
Sanctum SPA necesita `SANCTUM_STATEFUL_DOMAINS` y `SESSION_DOMAIN=.proyecto.ac.cr`
</div>
<div>

### 📱 App móvil
Sus orígenes son `capacitor://localhost` (iOS) y `https://localhost` (Android): agrégalos a CORS
</div>
</div>

- 🚩 Un cambio de dominio rompe **CORS**, cookies, enlaces profundos de la app y el `redirect_uri` de OAuth: planifícalo

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00059_.png);
}
</style>
# 7.1.5 Despliegue
## Contenidos
- Del despliegue manual a CI/CD
- GitHub Actions: front-end y API
- Estrategias de despliegue
- Reversión, verificación y monitoreo

---

## Formas de desplegar

<div class="grid">
<div>

### 🧳 Manual
Copiar archivos por FTP/SFTP o `scp`. Propenso a errores y no repetible. **Evitar**
</div>
<div>

### 🌿 Por rama
`gh-pages -d dist` publica la carpeta en una rama (lo usamos en este curso)
</div>
<div>

### 🔗 Integración con Git
Netlify, Vercel, Laravel Cloud: cada `push` a `main` **despliega solo**, y cada PR tiene su URL de vista previa
</div>
<div>

### 🤖 CI/CD propio
Un *pipeline* (GitHub Actions, GitLab CI) compila, prueba y despliega
</div>
</div>

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.85rem;">
<div>

- **CI** (*integración continua*): cada cambio se compila y se **prueba** automáticamente

</div>
<div>

- **CD** (*entrega / despliegue continuo*): lo que pasa las pruebas queda **listo** o se **despliega** solo

</div>
</split-slide>

---

## GitHub Actions: SPA a GitHub Pages

<split-slide style="--left: 58%; --right: 42%; --font-size: 0.75rem;">
<div>

```yaml
# .github/workflows/deploy.yml
name: Desplegar front-end
on:
  push:
    branches: [main]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
      - uses: pnpm/action-setup@v4
      - uses: actions/setup-node@v5
        with: { node-version: 24, cache: pnpm }
      - run: pnpm install --frozen-lockfile
      - run: pnpm test
      - run: pnpm run build
        env:
          VITE_API_URL: ${{ vars.VITE_API_URL }}
      - uses: actions/upload-pages-artifact@v4
        with: { path: dist }

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment: github-pages
    steps:
      - uses: actions/deploy-pages@v4
```

</div>
<div>

- Se dispara con cada `push` a `main`
- **Si las pruebas fallan, no se despliega**
- `permissions` con **mínimo privilegio**
- `--frozen-lockfile`: instala **exactamente** lo del *lockfile*
- Valores públicos en `vars`; secretos en `secrets`
- Activar en *Settings → Pages → Source: GitHub Actions*
- 🔁 En *Seguridad* vimos fijar acciones de terceros por **hash**

</div>
</split-slide>

---

## Despliegue de Laravel sin tiempo fuera de servicio

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.8rem;">
<div>

```text
/var/www/api/
├── releases/
│   ├── 20261103-1530/
│   ├── 20261110-0915/
│   └── 20261110-1420/   ← nueva versión
├── shared/
│   ├── .env
│   └── storage/
└── current → releases/20261110-1420   (enlace)
```

```bash
# Script de despliegue (Forge, Envoy, Deployer)
cd releases/$NUEVA
composer install --no-dev --optimize-autoloader
ln -s ../../shared/.env .env
php artisan migrate --force
php artisan optimize
ln -sfn releases/$NUEVA ../current   # cambio atómico
php artisan queue:restart
sudo systemctl reload php8.4-fpm
```

</div>
<div>

- Cada versión en **su propia carpeta**; `current` es un enlace simbólico
- El cambio de enlace es **atómico**: no hay un momento con la app a medias
- `.env` y `storage/` viven en `shared/` y se **comparten** entre versiones
- `queue:restart`: los *workers* cargan el código nuevo
- **Revertir** = apuntar `current` a la carpeta anterior
- ⚠️ Las migraciones deben ser **compatibles hacia atrás**: primero agregar columnas, después borrar las viejas en otro despliegue
- Herramientas: Laravel Forge, Envoyer, [Deployer](https://deployer.org)

</div>
</split-slide>

---

## Estrategias de despliegue

<div class="grid">
<div>

### 🔁 Recrear
Se apaga la versión vieja y se levanta la nueva. Simple, pero hay **tiempo fuera de servicio**
</div>
<div>

### 🌊 Rodante (*rolling*)
Se reemplazan las instancias **de a poco**. Conviven dos versiones un rato
</div>
<div>

### 🔵🟢 Azul / verde
Dos entornos idénticos; se cambia el tráfico **de golpe**. Revertir es instantáneo
</div>
<div>

### 🐤 Canario
La versión nueva recibe primero un **5 %** del tráfico; si las métricas están bien, se amplía
</div>
<div>

### 🚩 *Feature flags*
El código se despliega **apagado** y se activa por usuario o porcentaje (Laravel **Pennant**)
</div>
<div>

### 👀 Vista previa
Un despliegue **temporal** por cada PR para revisarlo antes de fusionar
</div>
</div>

- 💡 **Desplegar ≠ liberar**: con *feature flags* se puede desplegar el lunes y activar la función el jueves

---

## Después del despliegue

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.85rem;">
<div>

### ✅ Verificar
- **Health check**: Laravel trae la ruta `/up`
- Pruebas de humo: login, listado, crear un recurso
- Revisar la consola del navegador y los registros del servidor
- Comprobar HTTPS, encabezados y CORS desde el dominio real

### ↩️ Plan de reversión
- Saber **antes** cómo volver a la versión anterior, y haberlo **probado**

</div>
<div>

### 📈 Monitorear
- **Errores**: Sentry, Flare, Laravel Nightwatch
- **Disponibilidad**: UptimeRobot, Better Stack
- **Rendimiento real**: Core Web Vitals de usuarios reales
- Alertas ante picos de 5xx o de latencia

### 💾 Respaldos
- BD respaldada **antes** de migrar, automáticos y con restauración probada

</div>
</split-slide>

---

## Lista de verificación de despliegue web

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.82rem;">
<div>

### 📦 Antes
- [ ] Pruebas en verde en CI
- [ ] `pnpm run build` y `preview` sin errores
- [ ] `.env` de producción: `APP_DEBUG=false`, `APP_URL` correcta
- [ ] `composer install --no-dev` + `php artisan optimize`
- [ ] Migraciones compatibles hacia atrás y respaldo de la BD
- [ ] Versión etiquetada en Git

</div>
<div>

### 🚀 Después
- [ ] Dominio resuelve (`dig`) y HTTPS válido
- [ ] Recargar una ruta interna de la SPA **no** da 404
- [ ] CORS permite solo los orígenes del front y la app
- [ ] `/.env` y `/.git/` devuelven 403/404
- [ ] `/up` responde 200
- [ ] Errores llegando a la herramienta de monitoreo

</div>
</split-slide>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00077_.png);
}
</style>
# 7.2 Publicación de aplicaciones móviles
## Contenidos
- Compilación de *release* con Capacitor
- Versionado y firma
- Google Play
- App Store
- Actualizaciones, PWA y distribución alternativa

---

## Del código a la tienda

```text
 pnpm build ──► npx cap sync ──► build release ──► firmar ──► subir ──► pruebas ──► revisión ──► 🏪 publicada
  (dist/)       (copia a          (Gradle /          (keystore /  (Play Console /  (cerradas /   (horas a
                android/ e ios/)   Xcode)             certificado)  App Store       TestFlight)   días)
                                                                    Connect)
```

<div class="grid">
<div>

### 🤖 Android
Artefacto **AAB** (*Android App Bundle*). Play genera los APK optimizados para cada dispositivo
</div>
<div>

### 🍎 iOS
Artefacto **IPA**, generado con **Xcode en una Mac**, o en la nube (Xcode Cloud, Codemagic, Bitrise)
</div>
<div>

### 🆔 Identificador
`cr.ac.ucr.pedidos`: **permanente**. Si cambia, para la tienda es otra app
</div>
<div>

### ⏱️ Tiempo real
Una publicación **no** es inmediata: planifica días, no minutos
</div>
</div>

---

## Compilar una versión de *release* con Capacitor

<split-slide style="--left: 52%; --right: 48%; --font-size: 0.8rem;">
<div>

```bash
# 1. Web de producción
pnpm run build

# 2. Copiar a los proyectos nativos
npx cap sync

# 3a. Android: AAB firmado desde la terminal
npx cap build android \
  --androidreleasetype AAB \
  --keystorepath ../release.jks \
  --keystorealias upload

# 3b. o con Gradle / Android Studio
cd android && ./gradlew bundleRelease
#   → app/build/outputs/bundle/release/app-release.aab

# 4. iOS: abrir Xcode → Product → Archive
npx cap open ios
```

</div>
<div>

- 🚩 Antes de compilar: quitar `server.url` de `capacitor.config.ts` (recarga en vivo) o la app apuntará a tu laptop
- `VITE_API_URL` debe apuntar a la API **pública** con HTTPS
- Íconos y pantalla de inicio: `@capacitor/assets generate` desde un logo de 1024×1024
- Revisar los **permisos** declarados: cada uno se justifica ante la tienda
- Probar el *release* en un **dispositivo real**, no solo en el emulador

</div>
</split-slide>

---

## Versionado de la app

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.84rem;">
<div>

### 🤖 Android — `android/app/build.gradle`

```groovy
defaultConfig {
    applicationId "cr.ac.ucr.pedidos"
    minSdkVersion 24
    targetSdkVersion 36
    versionCode 12          // entero, siempre sube
    versionName "1.4.0"     // lo que ve el usuario
}
```

### 🍎 iOS — Xcode → *General*
- **Version** (`CFBundleShortVersionString`): `1.4.0`
- **Build** (`CFBundleVersion`): `12`

</div>
<div>

- Hay **dos** números: uno **para personas** (SemVer) y uno **interno** que la tienda usa para ordenar
- `versionCode` / *Build* debe **aumentar** en cada subida, aunque solo cambie un texto
- 🚩 La tienda **rechaza** un archivo con un número que ya usaste
- Automatizar: el número de *build* puede ser el número de ejecución de CI
- Mantener la misma `versionName` en web, Android e iOS facilita el soporte

</div>
</split-slide>

---

## Firma de la aplicación

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.82rem;">
<div>

### 🤖 Android

```bash
keytool -genkey -v -keystore release.jks \
  -alias upload -keyalg RSA -keysize 2048 \
  -validity 10000
```

- Con **Play App Signing**, Google guarda la **clave de la app** y tú firmas con una **clave de subida**
- Si pierdes la clave de subida, Google puede **restablecerla**; sin Play App Signing, perderla significa no poder actualizar **nunca**
- `release.jks` y su contraseña: **fuera del repositorio**, en un gestor de secretos

</div>
<div>

### 🍎 iOS
- **Certificado** de distribución: identifica al desarrollador
- **Perfil de aprovisionamiento**: une certificado + *App ID* + capacidades (push, Apple Pay…)
- Xcode con *Automatically manage signing* los crea y renueva por ti
- En CI se usan claves de la **API de App Store Connect** (Fastlane `match`)

### 🔁 De *Seguridad*
La firma garantiza que la actualización viene **del mismo autor**: es un control de integridad

</div>
</split-slide>

---

## Google Play: requisitos

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.83rem;">
<div>

### 👤 Cuenta de desarrollador
- **Play Console**: pago único de **USD 25**
- **Verificación de identidad** obligatoria (documento; dirección y D-U-N-S en cuentas de organización)
- **Cuentas personales nuevas**: prueba cerrada con al menos **12 testers** durante **14 días seguidos** antes de pedir acceso a producción
- Las cuentas de **organización** no tienen ese requisito

</div>
<div>

### 📋 Requisitos técnicos (2026)
- Formato **AAB**
- Desde el 31 de agosto de 2026, apps nuevas y actualizaciones deben apuntar a **Android 16 (API 36)**
- Las apps existentes deben apuntar al menos a **API 35** para seguir visibles a usuarios nuevos
- Firmado con Play App Signing
- 🆕 **Verificación de desarrolladores de Android**: Google la exige también para apps distribuidas **fuera** de Play (primero en algunos países desde septiembre de 2026, global desde 2027)

</div>
</split-slide>

---

## Google Play: la ficha y las declaraciones

<div class="grid">
<div>

### 🖼️ Ficha de la tienda
Nombre (30 car.), descripción corta y larga, ícono 512×512, gráfico destacado 1024×500 y capturas
</div>
<div>

### 🔐 Seguridad de los datos
Qué datos recolecta y comparte la app, y si viajan **cifrados**. Debe coincidir con la realidad
</div>
<div>

### 📜 Política de privacidad
URL pública **obligatoria**. En Costa Rica, acorde con la **Ley 8968**
</div>
<div>

### 🔞 Clasificación de contenido
Cuestionario IARC: violencia, compras, interacción entre usuarios
</div>
<div>

### 🎯 Público objetivo
Si incluye niños, se aplican las políticas de **Familias**
</div>
<div>

### 🔑 Acceso para revisores
Si hay login, credenciales de **prueba** para que Google pueda revisar la app
</div>
</div>

- 🗑️ Si la app permite crear cuentas, debe ofrecer **eliminarlas** desde la app y desde la web

---

## Google Play: tracks de prueba y lanzamiento

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.84rem;">
<div>

```text
 Prueba interna  ──►  Prueba cerrada  ──►  Prueba abierta  ──►  Producción
  hasta 100            listas de             cualquiera           todos
  personas, sin        correos o grupos      puede unirse         (o un %)
  revisión larga       de Google
```

</div>
<div>

- **Interna**: el equipo, en minutos. Ideal para cada *sprint*
- **Cerrada**: clientes o el profesor; aquí se cumplen los 12 testers × 14 días
- **Abierta**: beta pública desde la ficha de la tienda
- **Producción** con **lanzamiento por etapas**: 5 % → 20 % → 50 % → 100 %, y se puede **detener** si aparecen fallos
- 📊 Revisar *Android vitals*: *crashes* y ANR (app que no responde)

</div>
</split-slide>

---

## App Store: requisitos

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.83rem;">
<div>

### 👤 Cuenta
- **Apple Developer Program**: **USD 99 al año** (organizaciones necesitan número D-U-N-S)
- Si la membresía vence, las apps **salen** de la tienda
- Programa de exención de cuota para instituciones educativas y ONG

### 🛠️ Requisitos técnicos (2026)
- Desde el 28 de abril de 2026, compilar con **Xcode 26** y el **SDK de iOS 26**
- Requiere una **Mac** (o un servicio de compilación en la nube)
- *Privacy manifest* (`PrivacyInfo.xcprivacy`) en la app y en los SDK de terceros que lo exigen

</div>
<div>

### 🧭 Flujo
1. Crear la app en **App Store Connect** con el *Bundle ID*
2. Xcode → *Archive* → *Distribute App* → subir el *build*
3. Probar con **TestFlight**: hasta 100 testers internos y 10 000 externos (los externos pasan una revisión breve)
4. Completar ficha, capturas, **etiquetas de privacidad** y clasificación por edad
5. Enviar a **revisión** (normalmente 24–48 h)
6. Publicar manual, automática o **por fases** en 7 días

</div>
</split-slide>

---

## La revisión de Apple: motivos de rechazo frecuentes

<div class="grid">
<div>

### 🌐 "Solo un sitio web"
Guía **4.2**: una app que solo envuelve una web sin valor nativo se rechaza. Usa capacidades del dispositivo
</div>
<div>

### 🐞 Fallos y enlaces rotos
La app se cae, hay pantallas vacías o botones que no hacen nada
</div>
<div>

### 🔑 Sin cuenta de prueba
El revisor no puede entrar: siempre dar usuario y contraseña de demostración
</div>
<div>

### 🔐 Permisos sin justificar
Cada `NS…UsageDescription` debe explicar **para qué** se usa la cámara o la ubicación
</div>
<div>

### 🗑️ Sin borrar la cuenta
Si se puede crear una cuenta, debe poder **eliminarse** dentro de la app
</div>
<div>

### 💳 Pagos externos
Bienes digitales se cobran con compras dentro de la app, salvo las excepciones por región
</div>
</div>

- 📖 Leer las [App Review Guidelines](https://developer.apple.com/app-store/review/guidelines/) **antes** de diseñar, no antes de publicar

---

## Google Play vs. App Store

<style scoped>
table { font-size: 0.8em; }
</style>

| | 🤖 Google Play | 🍎 App Store |
|:--|:--|:--|
| **Costo de la cuenta** | USD 25, pago único | USD 99 al año |
| **Artefacto** | AAB | IPA (vía Xcode) |
| **Herramienta de compilación** | Android Studio en cualquier SO | Xcode, **solo macOS** |
| **Consola** | Play Console | App Store Connect |
| **Pruebas** | Interna, cerrada, abierta | TestFlight interno y externo |
| **Requisito 2026** | Apuntar a API 36 | SDK de iOS 26 |
| **Revisión** | Automatizada + humana; horas a días | Humana; normalmente 24–48 h |
| **Lanzamiento gradual** | Por porcentaje, se puede detener | Por fases en 7 días |
| **Comisión** | 15 % el primer millón al año, 30 % después | 15 % (Small Business Program) / 30 % |
| **Fuera de la tienda** | Permitido, con verificación del desarrollador | Solo en la UE (mercados alternativos) |

---

## Actualizaciones después de publicar

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.84rem;">
<div>

### 🏪 Por la tienda
- Necesarias si cambia **código nativo**: plugins, permisos, versión de Capacitor, íconos
- Pasan otra vez por **revisión**
- Recordatorio dentro de la app: plugin de *in-app updates* de Android o aviso propio que consulta la versión mínima a la API

</div>
<div>

### ⚡ En vivo (*live updates*, OTA)
- Como la UI de Ionic es **web**, se puede descargar un nuevo `dist/` sin pasar por la tienda (Capgo, Capawesome Cloud)
- ✅ Permitido para **correcciones** de HTML/CSS/JS
- 🚩 **No** se permite cambiar el propósito de la app ni agregar funciones que eludan la revisión
- Firmar los paquetes y poder **revertir**

</div>
</split-slide>

- 🔁 La API debe ser **compatible hacia atrás**: siempre habrá usuarios con versiones viejas de la app

---

## Otras formas de distribuir

<div class="grid">
<div>

### 📲 PWA
Se "publica" **desplegando** la web con HTTPS y un *manifest*. Se instala desde el navegador, sin tienda ni comisiones
</div>
<div>

### 📦 PWA en tiendas
Con **Bubblewrap** / **PWABuilder** se empaqueta como TWA para Google Play o para Microsoft Store
</div>
<div>

### 🏢 Distribución empresarial
**Managed Google Play** y **Apple Business Manager** para apps internas de una organización
</div>
<div>

### 🌏 Tiendas alternativas
Huawei AppGallery, Samsung Galaxy Store, F-Droid (código abierto), Amazon Appstore
</div>
<div>

### 🧪 Para pruebas
**Firebase App Distribution** o un APK directo para el equipo (Android)
</div>
<div>

### 🇪🇺 Unión Europea
La Ley de Mercados Digitales permite mercados alternativos en iOS
</div>
</div>

---

## Lista de verificación de publicación móvil

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.82rem;">
<div>

### 🔧 Técnico
- [ ] `server.url` eliminado de `capacitor.config.ts`
- [ ] API de producción con HTTPS y CORS para `capacitor://localhost` y `https://localhost`
- [ ] `versionCode` / *Build* incrementado
- [ ] `targetSdkVersion 36` · Xcode 26
- [ ] Firmado con la clave de subida; clave **fuera** del repo
- [ ] Probado en un Android de gama baja y en un iPhone reales
- [ ] Ningún secreto en el *bundle*

</div>
<div>

### 📋 Tienda
- [ ] Ícono, capturas y textos
- [ ] Política de privacidad publicada
- [ ] Seguridad de los datos / etiquetas de privacidad
- [ ] Clasificación de contenido
- [ ] Cuenta de prueba para los revisores
- [ ] Opción para eliminar la cuenta
- [ ] Justificación de cada permiso
- [ ] Prueba cerrada / TestFlight completada

</div>
</split-slide>

---

## Errores más frecuentes

<div class="grid">
<div>

### 🚩 404 al recargar
Falta `try_files` o la regla de reescritura para la SPA
</div>
<div>

### 🚩 Pantalla en blanco
`base` incorrecta en Vite: los JS se piden en la ruta equivocada
</div>
<div>

### 🚩 La API "no responde"
`VITE_API_URL` apunta a `localhost` en producción o CORS no incluye el dominio real
</div>
<div>

### 🚩 Contenido mixto
Página HTTPS que llama a una API HTTP: el navegador lo **bloquea**
</div>
<div>

### 🚩 `.env` cacheado
Cambiaste `.env` pero no ejecutaste `php artisan optimize` de nuevo
</div>
<div>

### 🚩 Clave de firma perdida
Sin Play App Signing, la app ya **no** se puede actualizar
</div>
<div>

### 🚩 Dominio vencido
Nadie activó la renovación automática
</div>
<div>

### 🚩 Desplegar el viernes a las 5 p. m.
Sin monitoreo ni plan de reversión 😅
</div>
</div>

---

## Actividad de cierre

<steps>
<step>

<div class="grid">
<div>

### 1️⃣ Front-end
Desplegar la SPA del proyecto con **GitHub Actions** y verificar que recargar una ruta interna funciona
</div>
<div>

### 2️⃣ API
Desplegar la API Laravel en un PaaS o VPS con HTTPS, `APP_DEBUG=false` y `/up` respondiendo 200
</div>
<div>

### 3️⃣ DNS
Con `dig`, documentar los registros **A / CNAME** del front y la API (dominio propio o subdominio del proveedor)
</div>
<div>

### 4️⃣ Móvil
Generar un **AAB firmado** y subirlo a la **prueba interna** de Play (o un *build* a TestFlight)
</div>
</div>

</step>
<step>

- 📋 **Entregable**: URL pública del front y de la API, captura del *pipeline* en verde, salida de `dig` y captura del *track* de prueba
- 🎯 Bonus: un **segundo despliegue** que cambie algo visible, y una **reversión** a la versión anterior documentada

</step>
</steps>

---

## Referencias

- [Vite — Building for Production](https://vite.dev/guide/build) · [Deploying a Static Site](https://vite.dev/guide/static-deploy)
- [Laravel 13 — Deployment](https://laravel.com/docs/13.x/deployment) · [Laravel Cloud](https://cloud.laravel.com) · [Laravel Forge](https://forge.laravel.com)
- [Nginx — Documentación](https://nginx.org/en/docs/) · [Caddy — Documentación](https://caddyserver.com/docs/) · [FrankenPHP](https://frankenphp.dev)
- [Let's Encrypt — Cómo funciona](https://letsencrypt.org/es/how-it-works/) · [Certbot](https://certbot.eff.org)
- [MDN — ¿Qué es un nombre de dominio?](https://developer.mozilla.org/es/docs/Learn_web_development/Howto/Web_mechanics/What_is_a_domain_name) · [Cloudflare — ¿Qué es DNS?](https://www.cloudflare.com/es-es/learning/dns/what-is-dns/)
- [NIC Costa Rica](https://www.nic.cr) · [The Twelve-Factor App](https://12factor.net/es/)
- [GitHub Actions — Publicar en GitHub Pages](https://docs.github.com/es/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages)
- [Capacitor — Publicar en Google Play](https://capacitorjs.com/docs/android/deploying-to-google-play) · [Publicar en la App Store](https://capacitorjs.com/docs/ios/deploying-to-app-store)
- [Google Play — Requisito de nivel de API objetivo](https://developer.android.com/google/play/requirements/target-sdk) · [Requisitos de prueba para cuentas personales](https://support.google.com/googleplay/android-developer/answer/14151465)
- [Apple — Requisitos próximos](https://developer.apple.com/news/upcoming-requirements/) · [App Review Guidelines](https://developer.apple.com/app-store/review/guidelines/) · [TestFlight](https://developer.apple.com/testflight/)
- [Seguridad web y móvil](../seguridad-web-movil/) · [Desarrollo Móvil](../desarrollo-movil/) · [Desarrollo Backend](../backend/) — temas del curso relacionados

<script src="../assets/steps.js"></script>
<script src="../assets/image-modal.js"></script>
