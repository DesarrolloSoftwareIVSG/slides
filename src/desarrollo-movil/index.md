---
marp: true
theme: alo
paginate: true
---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00077_.png);
}
</style>
# Desarrollo Móvil
## Contenidos
- Introducción al desarrollo móvil
- Ecosistemas, desarrollo nativo y multiplataforma
- Diseño, herramientas y frameworks
- Interfaz, navegación, datos y hardware

> **Ionic 9** + **Capacitor 8** como stack principal <br>Ejemplos con **Vue 3** (`<script setup>` + TypeScript) <br>Curso *Desarrollo de Software IV* · II Ciclo 2026

---

## ¿Qué construimos en este tema?

- Una **aplicación móvil** instalable en Android e iOS que consume la **API REST en Laravel** del tema anterior

<div class="grid">
<div>

### 📱 Introducción
Qué hace distinta a una app móvil y qué ecosistemas existen
</div>
<div>

### ⚖️ Enfoques
Nativo, multiplataforma, híbrido y PWA: cuándo elegir cada uno
</div>
<div>

### ⚡ Ionic + Capacitor
UI con componentes web + puente hacia el sistema operativo
</div>
<div>

### 🔌 Hardware
Cámara, GPS, almacenamiento, red y notificaciones desde TypeScript
</div>
</div>

- 🎯 Al final del tema cada equipo tendrá una **app móvil autenticada** contra su API, con al menos **un recurso de hardware** integrado

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00064_.png);
}
</style>
# Introducción al desarrollo móvil
## Contenidos
- Conceptos básicos
- Ecosistemas móviles
- Desarrollo nativo y multiplataforma
- Lenguajes en tendencia

---

## ¿Qué es una aplicación móvil?

- Software diseñado para ejecutarse en **teléfonos y tabletas**, aprovechando sus capacidades y respetando sus limitaciones

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.92rem;">
<div>

### 📐 Restricciones del medio
- Pantalla **pequeña** y de tamaños muy variados
- Entrada **táctil**: dedos, gestos, teclado virtual
- **Batería** y memoria limitadas
- Conectividad **intermitente** (Wi-Fi ↔ datos ↔ sin señal)
- **Interrupciones**: llamadas, notificaciones, cambio de app
</div>
<div>

### 🚀 Oportunidades del medio
- **Sensores**: GPS, acelerómetro, giroscopio, brújula
- **Cámara**, micrófono, biometría
- **Notificaciones** push
- Siempre **con el usuario**: uso frecuente y en movimiento
- **Instalación** en el dispositivo, ícono en la pantalla de inicio
</div>
</split-slide>

- 💡 Diseñar para móvil no es "achicar la web": es diseñar para **otro contexto de uso**

---

## Conceptos clave

<div class="grid">
<div>

### 📦 Paquete
Android: **APK** / **AAB** (formato de publicación en Play) · iOS: **IPA**
</div>
<div>

### 🆔 Identificador de app
Único y permanente, estilo dominio invertido: `cr.ac.ucr.pedidos`
</div>
<div>

### ✍️ Firma
Toda app se **firma** con un certificado del desarrollador. Sin firma no se instala ni se publica
</div>
<div>

### 🔐 Permisos
El usuario **autoriza** el acceso a cámara, ubicación, contactos… y puede revocarlo
</div>
<div>

### 🏪 Tienda
Google Play y App Store **revisan** las apps antes de publicarlas
</div>
<div>

### 🧪 Emulador / simulador
Dispositivo virtual en la computadora para probar sin hardware físico
</div>
</div>

---

## Ciclo de vida de una aplicación

<split-slide style="--left: 45%; --right: 55%; --font-size: 0.88rem;">
<div>

```text
   ┌──────────────┐
   │  No iniciada │
   └──────┬───────┘
          │ el usuario toca el ícono
          ▼
   ┌──────────────┐  llamada, cambio de app
   │  En primer   │ ───────────────┐
   │    plano     │                ▼
   └──────────────┘ ◀─────  ┌──────────────┐
          ▲   regresa       │  En segundo  │
          │                 │    plano     │
          │                 └──────┬───────┘
          │                        │ el SO necesita memoria
          └──── se relanza ── ┌────▼─────────┐
                              │  Terminada   │
                              └──────────────┘
```
</div>
<div>

- El **sistema operativo** decide cuándo pausar o matar la app, no el desarrollador
- En segundo plano la app **no debería** consumir CPU, red ni batería
- Si el SO la termina, el usuario espera **volver donde estaba**: hay que guardar el estado

```ts
import { App } from '@capacitor/app';

App.addListener('appStateChange', ({ isActive }) => {
  if (!isActive) guardarBorrador();
});
```
</div>
</split-slide>

---

## Tipos de aplicaciones móviles

<div class="grid">
<div>

### 🧱 Nativa
Un proyecto por plataforma con el SDK oficial: **Kotlin** (Android) y **Swift** (iOS)
</div>
<div>

### 🎨 Multiplataforma con UI propia
Un código, el framework **dibuja** o **mapea** sus componentes: **Flutter**, **React Native**, **.NET MAUI**
</div>
<div>

### 🌐 Híbrida
App web dentro de un **WebView** nativo con acceso a APIs del SO: **Ionic + Capacitor**, Cordova
</div>
<div>

### 📲 PWA
App web **instalable** desde el navegador, con *service worker* y *manifest*. No pasa por la tienda (salvo empaquetado)
</div>
</div>

- 🔁 Ya vimos en *Fundamentos de la Web* la comparación **web vs. nativa**; aquí profundizamos en el **cómo** se construye cada una

---

## Ecosistemas móviles

| | 🤖 Android | 🍎 iOS / iPadOS |
|:--|:--|:--|
| **Empresa** | Google (base abierta: AOSP) | Apple (cerrado) |
| **Cuota mundial** (StatCounter, ago 2026) | **≈ 67,6 %** | **≈ 32,4 %** |
| **Fabricantes** | Samsung, Xiaomi, Motorola, Pixel… | Solo Apple |
| **Lenguaje oficial** | Kotlin (antes Java) | Swift (antes Objective-C) |
| **UI declarativa** | Jetpack Compose | SwiftUI |
| **IDE** | Android Studio (Windows, macOS, Linux) | Xcode (**solo macOS**) |
| **Tienda** | Google Play (pago único USD 25) + tiendas alternativas | App Store (USD 99 / año) |
| **Guía de diseño** | Material Design 3 | Human Interface Guidelines |

- 🇨🇷 En Costa Rica y Latinoamérica Android domina con mayor margen que el promedio mundial; en EE. UU. iOS ronda la mitad del mercado

---

## Ecosistemas: lo que no se ve en la tabla

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.9rem;">
<div>

### 🤖 Android
- **Fragmentación**: miles de modelos, tamaños, versiones del SO y capas de fabricante
- Muchos usuarios en versiones **viejas** del sistema
- Instalación fuera de la tienda (*sideloading*) permitida
- Probar en un **gama baja** es obligatorio: ahí aparecen los problemas de rendimiento
</div>
<div>

### 🍎 iOS
- Pocos modelos y **adopción rápida** de nuevas versiones
- Revisión de la App Store **más estricta**
- Compilar y firmar requiere una **Mac** con Xcode
- Usuarios con mayor disposición a **pagar** dentro de apps

### 🌏 Otros
- **HarmonyOS NEXT** (Huawei) en China, ya sin compatibilidad Android
- Relojes, TV y autos: Wear OS, watchOS, Android Auto, CarPlay
</div>
</split-slide>

---

## Desarrollo nativo vs. multiplataforma

| Criterio | 🧱 Nativo | 🎨 Multiplataforma (Flutter, RN) | 🌐 Híbrido (Ionic + Capacitor) |
|:--|:--|:--|:--|
| **Código** | Uno por plataforma | Uno compartido | Uno compartido **+ web** |
| **Lenguaje** | Kotlin / Swift | Dart / JS-TS | HTML, CSS, TS |
| **Rendimiento** | Máximo | Muy cercano al nativo | Bueno; limitado por el WebView |
| **Acceso a APIs nuevas** | Inmediato | Vía plugins o código nativo | Vía plugins o código nativo |
| **Look & feel** | 100 % nativo | Propio o mapeado | Componentes que **imitan** cada plataforma |
| **Curva para un dev web** | Alta | Media | **Baja** |
| **Reutilizar la web** | No | Parcial (RN Web, Flutter Web) | **Sí, es la misma app** |
| **Costo / equipo** | Dos equipos | Un equipo | Un equipo web |

---

## ¿Cómo funciona cada enfoque por dentro?

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.78rem;">
<div>

### 🧱 Nativo
```text
  Código Kotlin / Swift
          │
          ▼
  SDK de la plataforma
          │
          ▼
  Componentes nativos
          │
          ▼
       Sistema
```
</div>
<div>

### 🎨 Flutter / React Native
```text
   Código Dart / JS
          │
          ▼
  Motor del framework
 (dibuja o mapea vistas)
          │
          ▼
  Canales / JSI ──▶ APIs
          │
          ▼
       Sistema
```
</div>
</split-slide>

<div style="font-size:0.78rem">

### 🌐 Ionic + Capacitor
```text
  HTML + CSS + TS (Angular / React / Vue)  →  WebView nativo  →  Puente de Capacitor  →  Plugin nativo (Kotlin / Swift)  →  Sistema
```
</div>

- 🧭 No hay un enfoque "mejor": hay uno **adecuado** al equipo, al presupuesto y a lo que la app necesita del hardware

---

## ¿Cuándo elegir qué?

<steps>
<step>

<div class="grid">
<div>

### 🧱 Nativo si…
Juegos, AR, procesamiento intensivo de audio/video, o necesitas la API de la plataforma **el día que sale**
</div>
<div>

### 🎨 Flutter / RN si…
Animaciones complejas, UI muy personalizada y un equipo dispuesto a aprender otro stack
</div>
<div>

### 🌐 Ionic + Capacitor si…
Apps de **gestión, formularios, listas y datos de una API**; el equipo ya domina la web y quiere **un solo código** para web, Android e iOS
</div>
<div>

### 📲 PWA si…
No necesitas tienda ni APIs nativas avanzadas y priorizas el alcance por URL
</div>
</div>

</step>
<step>

- 🎯 **¿Por qué Ionic + Capacitor en este curso?**
  - Reutiliza **todo** lo visto: HTML, CSS, JavaScript, módulos, componentes web, `fetch`, asincronía
  - El mismo proyecto genera la **web** y las apps **Android/iOS**
  - Consume la **misma API Laravel** con tokens de Sanctum
  - Se puede bajar a **Kotlin/Swift** cuando un plugin no alcance

</step>
</steps>

---

## Lenguajes de programación en tendencia

<div class="grid">
<div>

### 🟣 Kotlin
Oficial de Android desde 2019. **Jetpack Compose** para UI y **Kotlin Multiplatform** para compartir lógica con iOS
</div>
<div>

### 🟠 Swift
Lenguaje de Apple; **SwiftUI** para UI declarativa. Seguro por defecto (opcionales, *concurrency* estricta)
</div>
<div>

### 🔵 Dart
Lenguaje de **Flutter**. Compila a código nativo ARM y a la web
</div>
<div>

### 🟦 TypeScript / JavaScript
**React Native**, **Ionic**, **Capacitor**, NativeScript. El lenguaje más usado del mundo llevado al móvil
</div>
<div>

### 🟪 C#
**.NET MAUI** (sucesor de Xamarin) para quienes vienen del ecosistema Microsoft
</div>
<div>

### ☕ Java / Objective-C
Legado: aún hay muchísimo código existente, pero los proyectos nuevos usan Kotlin y Swift
</div>
</div>

- 📈 La tendencia común: **UI declarativa** (Compose, SwiftUI, Flutter, React) y **compartir lógica** entre plataformas

---

## Actividad 1 — Elegir el enfoque

<div class="grid">
<div>

### 🛒 Caso A
App de pedidos para una soda universitaria: menú, carrito, pago en caja y notificación cuando el pedido está listo
</div>
<div>

### 🎮 Caso B
Juego de realidad aumentada que coloca objetos 3D sobre el campus usando la cámara
</div>
<div>

### 🏥 Caso C
App interna de un hospital para registrar visitas, que debe funcionar **sin conexión** y sincronizar después
</div>
<div>

### 📰 Caso D
Sitio de noticias que quiere aparecer en la pantalla de inicio y enviar alertas
</div>
</div>

- Para cada caso: ¿nativo, multiplataforma, híbrido o PWA? Justifiquen con **dos criterios** de la tabla comparativa

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00070_.png);
}
</style>
# Diseño de aplicaciones móviles
## Contenidos
- Principios de diseño móvil
- Material Design y Human Interface Guidelines
- Patrones de navegación
- Estilo adaptativo de Ionic

---

## Principios de diseño móvil

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.9rem;">
<div>

### 👆 Diseño para el dedo
- Objetivos táctiles de **≥ 48 dp** (Android) / **≥ 44 pt** (iOS)
- Acciones principales en la **zona del pulgar** (mitad inferior)
- Espacio entre elementos tocables para evitar errores

### 🎯 Una tarea por pantalla
- Jerarquía clara: **un** botón primario
- Formularios cortos, con el **teclado correcto** (`type="email"`, `inputmode="numeric"`)
</div>
<div>

### 📱 Respetar el dispositivo
- **Áreas seguras**: muesca, isla dinámica, barra de gestos
- Orientación vertical y horizontal
- **Modo oscuro** y tamaño de letra del sistema

### ⏳ Retroalimentación inmediata
- *Skeletons* y *spinners* mientras se cargan datos
- Mensajes de error **accionables**
- Estados vacíos con una acción sugerida
- **Vibración** (háptica) en confirmaciones importantes
</div>
</split-slide>

---

## Material Design vs. Human Interface Guidelines

| | 🤖 Material Design 3 (Google) | 🍎 Human Interface Guidelines (Apple) |
|:--|:--|:--|
| **Navegación principal** | *Navigation bar* inferior o *drawer* lateral | *Tab bar* inferior |
| **Acción principal** | **FAB** (botón flotante) | Botón en la barra de navegación |
| **Volver** | Botón/gesto **atrás del sistema** | Botón "‹ Atrás" y deslizar desde el borde |
| **Títulos** | Alineados a la izquierda | Centrados; *large titles* que se encogen al hacer scroll |
| **Diálogos** | Diálogos y *bottom sheets* | *Alerts* y *action sheets* |
| **Personalización** | Color dinámico desde el fondo de pantalla | Colores del sistema y materiales translúcidos |

- 💡 El usuario de cada plataforma **espera** las convenciones de la suya. Ionic las aplica automáticamente

---

## Patrones de navegación

<div class="grid">
<div>

### 🗂️ Pestañas (tabs)
3–5 secciones de **igual importancia** siempre visibles. El patrón más común en apps de consumo
</div>
<div>

### 📚 Pila (stack)
Lista → detalle → edición. Cada pantalla se **apila** sobre la anterior y "atrás" la desapila
</div>
<div>

### ☰ Menú lateral (drawer)
Muchas secciones o de uso poco frecuente. Esconde la navegación: úsalo con cuidado
</div>
<div>

### 🪟 Modal
Tarea **autocontenida** que interrumpe el flujo: crear un registro, filtros, confirmar
</div>
</div>

- 🧩 Lo normal es **combinarlos**: pestañas en la raíz y una pila dentro de cada pestaña

---

## Estilo adaptativo de Ionic

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.88rem;">
<div>

- Cada componente de Ionic tiene **dos modos**: `ios` y `md` (Material Design)
- Por defecto se elige según la **plataforma** donde corre la app
- El mismo HTML se ve **"nativo"** en cada sistema

```html
<!-- Igual en ambas plataformas -->
<ion-button>Guardar</ion-button>

<!-- Forzar un modo en un componente -->
<ion-toolbar mode="ios">…</ion-toolbar>
```

```ts
// main.ts — forzar un modo global
createApp(App).use(IonicVue, { mode: 'md' })
```
</div>
<div>

### Qué cambia entre modos
- Tipografía (San Francisco vs. Roboto)
- Alineación de títulos
- Animaciones de transición entre páginas
- Forma de botones, *toggles* y *checkboxes*
- Gesto de **deslizar para volver** (activo en `ios`)

<br>

- 🧪 En el navegador se puede probar con `?ionic:mode=ios` en la URL
</div>
</split-slide>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00007_terminal.png);
}
</style>
# Herramientas y entornos de desarrollo
## Contenidos
- IDEs y SDKs
- Emuladores y dispositivos físicos
- Recarga en vivo y depuración remota

---

## IDEs y herramientas

<div class="grid">
<div>

### 💙 Visual Studio Code
Donde escribimos **todo** el código web. Extensión oficial **Ionic** para generar páginas, correr en dispositivos y gestionar plugins
</div>
<div>

### 🤖 Android Studio
Trae el **Android SDK**, el JDK, el emulador (AVD) y el *Logcat*. Abre la carpeta `android/` generada por Capacitor
</div>
<div>

### 🍎 Xcode
Simulador de iOS, firma y publicación. **Solo en macOS**. Abre `ios/App/App.xcodeproj`
</div>
<div>

### ⌨️ CLIs
`@ionic/cli` (crear, servir, generar) y `@capacitor/cli` (`npx cap …`) para sincronizar con los proyectos nativos
</div>
</div>

- 🪟 En Windows/Linux se desarrolla **Android** completo; iOS requiere una Mac (propia, de laboratorio o en la nube)

---

## Requisitos del entorno — Capacitor 8

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.9rem;">
<div>

### Para todos
- **Node.js 22** o superior
- VS Code + extensión Ionic
- Ionic CLI:

```bash
npm i -g @ionic/cli
ionic --version
```

### 🤖 Android
- **Android Studio 2025.2.1** o superior
- SDK Platform **API 24+** desde el *SDK Manager*
- Un dispositivo virtual (AVD) o un teléfono con **depuración USB** activada
</div>
<div>

### 🍎 iOS
- **Xcode 26** o superior (macOS)
- *Command Line Tools* de Xcode
- Cuenta de Apple para firmar en un dispositivo físico (gratuita para pruebas, USD 99/año para publicar)

<br>

- ⚠️ Las versiones mínimas suben con cada versión mayor de Capacitor. Verifique siempre la página **Environment Setup** de la documentación oficial
</div>
</split-slide>

---

## Emuladores, dispositivos y depuración

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.85rem;">
<div>

### 🔁 Recarga en vivo
```bash
# En el navegador (lo más rápido)
ionic serve

# En el emulador o teléfono, con recarga en vivo
ionic cap run android -l --external
ionic cap run ios -l --external
```

- `-l` = *livereload*: el WebView carga el servidor de desarrollo de la PC
- `--external` expone el servidor en la red local para que el teléfono lo alcance
</div>
<div>

### 🔍 Depuración remota
- **Android**: abrir `chrome://inspect` en Chrome → *Inspect* sobre el WebView de la app → DevTools completas
- **iOS**: Safari → menú *Desarrollo* → el simulador o el iPhone → Web Inspector
- **Código nativo**: *Logcat* en Android Studio, consola de Xcode

<br>

- 💡 El 90 % del desarrollo se hace en `ionic serve`. El dispositivo se usa para **hardware**, rendimiento y gestos
</div>
</split-slide>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00055_.png);
}
</style>
# Frameworks para desarrollo móvil
## Contenidos
- Panorama de frameworks
- Ionic Framework 9
- Capacitor 8
- Crear y estructurar un proyecto

---

## Panorama de frameworks

| Framework | Empresa | Lenguaje | Cómo renderiza | Destaca en |
|:--|:--|:--|:--|:--|
| **Flutter** | Google | Dart | Motor gráfico propio (Impeller) | UI personalizada, animaciones |
| **React Native** | Meta | JS / TS | Componentes **nativos** (Nueva Arquitectura, JSI) | Equipos React; ecosistema Expo |
| **Ionic + Capacitor** | Ionic (OutSystems) | HTML / CSS / TS | **WebView** con componentes web | Reutilizar la web; apps de datos |
| **.NET MAUI** | Microsoft | C# / XAML | Controles nativos | Equipos .NET; escritorio también |
| **Kotlin Multiplatform** | JetBrains | Kotlin | Nativo (+ Compose Multiplatform) | Compartir lógica, UI nativa |
| **NativeScript** | OpenJS | JS / TS | Componentes nativos | Acceso directo a APIs nativas desde JS |

- 📊 Flutter y React Native son los más usados en las encuestas de desarrolladores (Stack Overflow); Ionic sigue fuerte en apps empresariales y de gobierno

---

## Ionic Framework

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.9rem;">
<div>

- **Kit de componentes de UI** para móvil: más de 100 componentes (`<ion-button>`, `<ion-list>`, `<ion-modal>`…)
- Construidos como **componentes web** con *Stencil*: funcionan en **Angular, React, Vue** o JavaScript puro
- Estilo adaptativo `ios` / `md`, gestos, animaciones y temas con **variables CSS**
- **No** accede al hardware: de eso se encarga Capacitor

<br>

- 🔗 ¿Recuerdan el tema de *Web Components*? `<ion-button>` es exactamente eso: un *custom element* con *shadow DOM*
</div>
<div>

### Ionic 9 (agosto 2026)
- Angular **18–22**, React **18+** (React Router 6), Vue **3.5+** (Vue Router 5)
- Requiere **Capacitor 7+**
- iOS **16+**, Chrome/Edge **89+**, Safari **16+**
- En Vue: paquetes `@ionic/vue` y `@ionic/vue-router`
- Se eliminó el *picker* legado; `ion-img` queda obsoleto (usar `<img loading="lazy">`)
- Migración asistida:
```bash
npx @ionic/migrate
```
</div>
</split-slide>

---

## Capacitor

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.9rem;">
<div>

- **Runtime nativo multiplataforma**: envuelve una app web en un proyecto **Android** y otro **iOS** reales
- Expone APIs del dispositivo a JavaScript mediante **plugins**
- Los proyectos nativos son **código fuente** que se versiona y se puede editar en Android Studio / Xcode
- Funciona con **cualquier** app web, no solo con Ionic
- Sucesor moderno de **Apache Cordova** (compatible con la mayoría de sus plugins)
</div>
<div>

### Capacitor 8
- **Swift Package Manager** por defecto para iOS en proyectos nuevos (CocoaPods sigue soportado)
- Soporte de **edge-to-edge** en Android con el plugin *SystemBars*
- 8.5: soporte de **UIScene** en iOS
- Última estable: **8.5.x** (octubre 2026); Capacitor 9 en *alpha*
</div>
</split-slide>

- 🧩 Ionic pone la **cara**, Capacitor pone el **cuerpo**: juntos dan una app instalable con acceso al dispositivo

---

## Arquitectura de una app Ionic + Capacitor

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.88rem;">
<div>

```text
┌────────────────────────────────────────┐
│  App web (Vue + Ionic)                 │
│  views · components · composables      │
│                                        │
│  import { Camera } from                │
│         '@capacitor/camera';           │
├────────────────────────────────────────┤
│  WebView nativo                        │
│  (Android System WebView / WKWebView)  │
├────────────────────────────────────────┤
│  Puente de Capacitor  (JS ⇄ nativo)    │
├──────────────────┬─────────────────────┤
│ Plugin Kotlin    │ Plugin Swift        │
├──────────────────┼─────────────────────┤
│ Android SDK      │ iOS SDK             │
└──────────────────┴─────────────────────┘
```
</div>
<div>

- La app web corre **local** dentro del WebView, servida desde `https://localhost` (Android) o `capacitor://localhost` (iOS)
- Cada llamada a un plugin cruza el puente y devuelve una **Promesa**
- En el navegador, los plugins usan una **implementación web** cuando existe (p. ej. la cámara usa `<input type="file">`)

<br>

- ⚠️ Esos orígenes importan para **CORS** en la API Laravel
</div>
</split-slide>

---

## Crear un proyecto

<steps>
<step>

```bash
# 1. Crear la app: plantilla "tabs", Vue + Vite, con Capacitor
ionic start pedidos-app tabs --type=vue --capacitor

cd pedidos-app
ionic serve                       # http://localhost:8100
```

- Plantillas: `blank`, `tabs`, `sidemenu`, `list` (ver todas con `ionic start --list`)
- `--type` acepta `angular`, `angular-standalone`, `react`, `vue`

</step>
<step>

```bash
# 2. Agregar plataformas nativas
npm i @capacitor/android @capacitor/ios
npx cap add android
npx cap add ios                    # solo en macOS

# 3. Compilar la web y copiarla a los proyectos nativos
ionic build
npx cap sync

# 4. Abrir el IDE nativo o correr directo
npx cap open android
npx cap run android
```

</step>
</steps>

---

## El ciclo de trabajo con Capacitor

<steps>
<step>

```text
  código web (src/)
        │  ionic build
        ▼
  dist/  (bundle de Vite)      ← webDir en capacitor.config.ts
        │  npx cap sync  =  copy + update
        ▼
  android/app/src/main/assets/public     ios/App/App/public
        │  npx cap run / Android Studio / Xcode
        ▼
  APK / AAB                              IPA
```

</step>
<step>

<div class="grid">
<div>

### `npx cap copy`
Solo copia el *bundle* web a los proyectos nativos
</div>
<div>

### `npx cap update`
Actualiza dependencias nativas de los plugins
</div>
<div>

### `npx cap sync`
`copy` + `update`. Ejecútelo después de **instalar un plugin** o compilar
</div>
<div>

### 🚩 Error típico
"Hice cambios y no se ven en el teléfono" → olvidó `build` + `sync` (o no está usando `-l`)
</div>
</div>

</step>
</steps>

---

## Estructura del proyecto

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.82rem;">
<div>

```text
pedidos-app/
├── android/             ← proyecto Android Studio
├── ios/                 ← proyecto Xcode
├── public/
├── src/
│   ├── views/
│   │   ├── TabsPage.vue
│   │   ├── PedidosPage.vue
│   │   └── PedidoDetallePage.vue
│   ├── components/
│   ├── composables/     ← useAuth, usePedidos
│   ├── services/        ← api.ts (fetch)
│   ├── router/index.ts
│   ├── theme/variables.css
│   ├── App.vue
│   └── main.ts
├── .env                 ← VITE_API_URL
├── capacitor.config.ts
├── ionic.config.json
├── vite.config.ts
└── package.json
```
</div>
<div>

### `capacitor.config.ts`
```ts
import type { CapacitorConfig } from '@capacitor/cli';

const config: CapacitorConfig = {
  appId: 'cr.ac.ucr.pedidos',
  appName: 'Pedidos',
  webDir: 'dist',
  plugins: {
    SplashScreen: { launchShowDuration: 1500 },
  },
};

export default config;
```

- `appId` **no debe cambiar** después de publicar: identifica la app en las tiendas
- `android/` e `ios/` **sí** van al repositorio
</div>
</split-slide>

---

## Arranque de la app — `main.ts` y `App.vue`

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.85rem;">
<div>

```ts
// src/main.ts
import { createApp } from 'vue';
import { IonicVue } from '@ionic/vue';
import App from './App.vue';
import router from './router';

/* CSS obligatorio y utilidades de Ionic */
import '@ionic/vue/css/core.css';
import '@ionic/vue/css/normalize.css';
import '@ionic/vue/css/structure.css';
import '@ionic/vue/css/typography.css';
import '@ionic/vue/css/padding.css';
import '@ionic/vue/css/palettes/dark.system.css';
import './theme/variables.css';

const app = createApp(App).use(IonicVue).use(router);

router.isReady().then(() => app.mount('#app'));
```
</div>
<div>

```vue
<!-- src/App.vue -->
<template>
  <ion-app>
    <ion-router-outlet />
  </ion-app>
</template>

<script setup lang="ts">
import { IonApp, IonRouterOutlet } from '@ionic/vue';
</script>
```

- `IonicVue` es un **plugin de Vue**: registra la configuración global (modo, animaciones…)
- Se monta **cuando el router está listo** para que la primera página se pinte sin parpadeo
- Cada componente de Ionic se **importa explícitamente**: solo se empaqueta lo que se usa
</div>
</split-slide>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00056_.png);
}
</style>
# Desarrollo de interfaz de usuario
## Contenidos
- Anatomía de una página
- Componentes principales
- Formularios y *overlays*
- Temas, modo oscuro y áreas seguras

---

## Anatomía de una página Ionic

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.8rem;">
<div>

```html
<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Pedidos</ion-title>
        <ion-buttons slot="end">
          <ion-button @click="refrescar">
            <ion-icon slot="icon-only" :icon="refresh" />
          </ion-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Pedidos</ion-title>
        </ion-toolbar>
      </ion-header>
      <!-- contenido con scroll -->
    </ion-content>
  </ion-page>
</template>
```
</div>
<div>

```html
<script setup lang="ts">
import { IonPage, IonHeader, IonToolbar, IonTitle,
  IonButtons, IonButton, IonIcon, IonContent } from '@ionic/vue';
import { refresh } from 'ionicons/icons';
</script>
```

- Toda vista enrutada se envuelve en **`<ion-page>`**: sin él no hay transiciones ni ciclo de vida
- `ion-header` / `ion-footer` quedan **fijos**; solo `ion-content` hace scroll
- `slot="start"` / `slot="end"` ubican botones en la barra
- `collapse="condense"` produce el **título grande** de iOS que se encoge al hacer scroll
- `ion-content` respeta las **áreas seguras**
</div>
</split-slide>

---

## Componentes principales

<div class="grid">
<div>

### 📋 Listas
`ion-list`, `ion-item`, `ion-label`, `ion-avatar`, `ion-item-sliding` (deslizar para acciones)
</div>
<div>

### 🔘 Acciones
`ion-button`, `ion-fab`, `ion-chip`, `ion-segment` (filtros tipo pestaña)
</div>
<div>

### ✏️ Entradas
`ion-input`, `ion-textarea`, `ion-select`, `ion-toggle`, `ion-checkbox`, `ion-datetime`, `ion-searchbar`
</div>
<div>

### 🪟 Overlays
`ion-modal`, `ion-alert`, `ion-toast`, `ion-action-sheet`, `ion-loading`, `ion-popover`
</div>
<div>

### 🧱 Disposición
`ion-grid` / `ion-row` / `ion-col`, `ion-card`, `ion-accordion`
</div>
<div>

### ♾️ Datos
`ion-refresher` (tirar para actualizar), `ion-infinite-scroll`, `ion-skeleton-text`
</div>
</div>

- 📚 Cada componente documenta sus **propiedades**, **eventos** (`ionChange`, `ionInput`…), **slots** y **partes CSS** en ionicframework.com/docs/components

---

## Una lista real: pedidos de la API

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.76rem;">
<div>

```html
<ion-content>
  <ion-refresher slot="fixed" @ionRefresh="refrescar">
    <ion-refresher-content />
  </ion-refresher>

  <ion-list v-if="pedidos.length">
    <ion-item-sliding v-for="p in pedidos" :key="p.id">
      <ion-item :router-link="`/tabs/pedidos/${p.id}`" detail>
        <ion-label>
          <h2>Pedido #{{ p.id }}</h2>
          <p>{{ p.cliente }} · {{ colones.format(p.total) }}</p>
        </ion-label>
        <ion-badge slot="end">{{ p.estado }}</ion-badge>
      </ion-item>
      <ion-item-options side="end">
        <ion-item-option color="danger" @click="cancelar(p)">
          Cancelar
        </ion-item-option>
      </ion-item-options>
    </ion-item-sliding>
  </ion-list>

  <p v-else class="ion-padding">No hay pedidos todavía</p>
</ion-content>
```
</div>
<div>

```html
<script setup lang="ts">
import { ref } from 'vue';
import { onIonViewWillEnter,
  type RefresherCustomEvent } from '@ionic/vue';
import { pedidosApi, type Pedido } from '@/services/pedidos';

const pedidos = ref<Pedido[]>([]);
const colones = new Intl.NumberFormat('es-CR',
  { style: 'currency', currency: 'CRC' });

async function cargar() {
  pedidos.value = await pedidosApi.listar();
}

onIonViewWillEnter(cargar);

async function refrescar(ev: RefresherCustomEvent) {
  await cargar();
  ev.target.complete();
}
</script>
```

- `detail` agrega la flecha **›** en iOS
- Deslizar el ítem revela acciones **sin ocupar espacio**
</div>
</split-slide>

---

## Formularios

<split-slide style="--left: 58%; --right: 42%; --font-size: 0.7rem;">
<div>

```html
<template>
  <form @submit.prevent="guardar">
    <ion-list>
      <ion-item>
        <ion-input label="Cliente" label-placement="floating"
          v-model="form.cliente"
          :class="{ 'ion-invalid ion-touched': errores.cliente }"
          :error-text="errores.cliente?.[0]" />
      </ion-item>
      <ion-item>
        <ion-input label="Teléfono" type="tel" inputmode="tel"
          label-placement="floating" v-model="form.telefono" />
      </ion-item>
      <ion-item>
        <ion-select label="Estado" v-model="form.estado">
          <ion-select-option value="pendiente">Pendiente</ion-select-option>
          <ion-select-option value="pagado">Pagado</ion-select-option>
        </ion-select>
      </ion-item>
    </ion-list>
    <ion-button expand="block" type="submit">Guardar</ion-button>
  </form>
</template>

<script setup lang="ts">
const form = reactive({ cliente: '', telefono: '', estado: 'pendiente' });
const errores = ref<Record<string, string[]>>({});

async function guardar() {
  try {
    await pedidosApi.crear(form);
  } catch (e) {
    if (e instanceof ErrorApi && e.status === 422) errores.value = e.cuerpo.errors;
  }
}
</script>
```
</div>
<div>

- `v-model` funciona directamente sobre `ion-input`, `ion-select`, `ion-toggle`…
- `ion-input` integra **etiqueta**, mensaje de **error** y ayuda
- `type` e `inputmode` deciden **qué teclado** abre el teléfono
- Los errores **422** de Laravel (`errors.campo[]`) se pintan bajo cada campo: la API sigue siendo la **autoridad**

<br>

- ⚠️ Ionic 9: las etiquetas ya **no flotan solas** con contenido en *slots*; y en `ion-select`, `ionChange` solo se dispara si el valor **cambia**
</div>
</split-slide>

---

## Overlays: alert, toast, modal, loading

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.78rem;">
<div>

```ts
import { alertController, toastController,
  loadingController } from '@ionic/vue';

async function cancelar(p: Pedido) {
  const alert = await alertController.create({
    header: '¿Cancelar pedido?',
    message: `El pedido #${p.id} no se podrá recuperar`,
    buttons: [
      { text: 'No', role: 'cancel' },
      { text: 'Sí, cancelar', role: 'destructive' },
    ],
  });
  await alert.present();
  const { role } = await alert.onDidDismiss();
  if (role !== 'destructive') return;

  const spinner = await loadingController.create({ message: 'Cancelando…' });
  await spinner.present();
  try {
    await pedidosApi.cancelar(p.id);
    const toast = await toastController.create({
      message: 'Pedido cancelado', duration: 2000 });
    await toast.present();
  } finally {
    spinner.dismiss();
  }
}
```
</div>
<div>

- Los *controllers* crean overlays **desde código**; también existen como etiquetas (`<ion-modal :is-open="abierto">`)
- `role` permite saber **cómo** se cerró el diálogo
- **Alert** → decisión que bloquea
- **Toast** → aviso que no bloquea
- **Modal** → tarea completa
- **Action sheet** → elegir entre varias acciones

<br>

- ✅ Confirmar siempre las acciones **destructivas**
</div>
</split-slide>

---

## Temas, modo oscuro y áreas seguras

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.82rem;">
<div>

### 🎨 Colores con variables CSS
```css
/* src/theme/variables.css */
:root {
  --ion-color-primary: #00c0f3;          /* celeste UCR */
  --ion-color-primary-contrast: #ffffff;
  --ion-font-family: 'Inter', sans-serif;
}
```
```html
<ion-button color="primary">Guardar</ion-button>
<ion-badge color="warning">Pendiente</ion-badge>
```

- Colores predefinidos: `primary`, `secondary`, `tertiary`, `success`, `warning`, `danger`, `light`, `medium`, `dark`
</div>
<div>

### 🌙 Modo oscuro
```ts
// main.ts — sigue la preferencia del sistema
import '@ionic/vue/css/palettes/dark.system.css';
```

### 📏 Áreas seguras
```css
.barra-propia {
  padding-top: var(--ion-safe-area-top);
  padding-bottom: var(--ion-safe-area-bottom);
}
```

- Con Capacitor 8 la app dibuja **de borde a borde** en Android: los componentes de Ionic ya lo resuelven, el CSS propio debe usar las variables de área segura
</div>
</split-slide>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00057_.png);
}
</style>
# Navegación
## Contenidos
- Rutas y `ion-router-outlet`
- Pestañas y pilas
- Parámetros y ciclo de vida de páginas
- Guardas y botón atrás

---

## Rutas en Ionic + Vue

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.8rem;">
<div>

```ts
// src/router/index.ts
import { createRouter, createWebHistory } from '@ionic/vue-router';
import type { RouteRecordRaw } from 'vue-router';
import TabsPage from '@/views/TabsPage.vue';

const routes: RouteRecordRaw[] = [
  { path: '/', redirect: '/tabs/pedidos' },
  { path: '/login',
    component: () => import('@/views/LoginPage.vue') },
  { path: '/tabs/', component: TabsPage,
    meta: { requiereSesion: true },
    children: [ /* siguiente diapositiva */ ] },
];

export default createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes,
});
```
</div>
<div>

- Ionic usa **Vue Router**, pero `createRouter` se importa de **`@ionic/vue-router`**: así Ionic conoce la dirección de cada navegación
- `ion-router-outlet` reemplaza a `<router-view>` y agrega:
  - **animaciones** de transición por plataforma
  - una **pila** de páginas que se conservan en memoria
  - **deslizar para volver** (modo `ios`; propiedad `swipeGesture` desde Ionic 9)
- `() => import(…)` carga cada vista **bajo demanda**
- Ionic 9 requiere **Vue 3.5+** y **Vue Router 5**
</div>
</split-slide>

---

## Pestañas con pilas independientes

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.78rem;">
<div>

```ts
// hijos de '/tabs/' en router/index.ts
children: [
  { path: '', redirect: '/tabs/pedidos' },
  { path: 'pedidos', component: () => import('@/views/PedidosPage.vue') },
  { path: 'pedidos/:id', props: true,
    component: () => import('@/views/PedidoDetallePage.vue') },
  { path: 'perfil', component: () => import('@/views/PerfilPage.vue') },
],
```

```html
<!-- src/views/TabsPage.vue -->
<template>
  <ion-page>
    <ion-tabs>
      <ion-router-outlet />
      <ion-tab-bar slot="bottom">
        <ion-tab-button tab="pedidos" href="/tabs/pedidos">
          <ion-icon :icon="receipt" /><ion-label>Pedidos</ion-label>
        </ion-tab-button>
        <ion-tab-button tab="perfil" href="/tabs/perfil">
          <ion-icon :icon="person" /><ion-label>Perfil</ion-label>
        </ion-tab-button>
      </ion-tab-bar>
    </ion-tabs>
  </ion-page>
</template>
```
</div>
<div>

- Cada pestaña mantiene **su propia pila**: si el usuario entra a un detalle en *Pedidos*, cambia a *Perfil* y regresa, sigue en el detalle
- Las rutas de una pestaña comparten **prefijo** (`pedidos`, `pedidos/:id`) para que Ionic sepa a qué pila pertenecen
- 🚩 No navegue **entre pestañas** con `router-link` desde el contenido: use la barra de pestañas, o se mezclan las pilas
</div>
</split-slide>

---

## Navegar, volver y pasar parámetros

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.8rem;">
<div>

### Desde la plantilla
```html
<ion-item :router-link="`/tabs/pedidos/${p.id}`">
<ion-button router-link="/login" router-direction="root">
```

### Desde código
```ts
import { useIonRouter } from '@ionic/vue';
const ionRouter = useIonRouter();

ionRouter.push(`/tabs/pedidos/${id}`);         // avanzar
ionRouter.back();                              // volver
ionRouter.navigate('/login', 'root', 'replace'); // nueva raíz
```

### Botón atrás
```html
<ion-buttons slot="start">
  <ion-back-button default-href="/tabs/pedidos" />
</ion-buttons>
```
</div>
<div>

### Leer el parámetro
```html
<script setup lang="ts">
// la ruta tiene props: true → :id llega como prop
const props = defineProps<{ id: string }>();
const pedido = ref<Pedido | null>(null);

onIonViewWillEnter(async () => {
  pedido.value = await pedidosApi.obtener(Number(props.id));
});
</script>
```

- `router-direction` y `useIonRouter` le dicen a Ionic **qué animación** usar: avanzar, volver o reemplazar la raíz
- `default-href` sirve cuando la app se abre **directo** en el detalle (enlace profundo) y no hay pila
</div>
</split-slide>

---

## Ciclo de vida de las páginas

<split-slide style="--left: 45%; --right: 55%; --font-size: 0.85rem;">
<div>

```text
  navegar hacia la página
         │
   onMounted             ← solo la 1.ª vez
   onIonViewWillEnter    ← cada vez, antes de animar
   onIonViewDidEnter     ← cada vez, al terminar
         │
  navegar a otra página (se queda en la pila)
         │
   onIonViewWillLeave
   onIonViewDidLeave
         │
  sale de la pila (atrás)
         │
   onUnmounted
```
</div>
<div>

- Como `ion-router-outlet` **conserva** las páginas, `onMounted` **no** se vuelve a ejecutar al regresar
- 🚩 Error frecuente: cargar datos en `onMounted` y que la lista **no se actualice** al volver de editar
- ✅ Recargar datos en `onIonViewWillEnter`
- ✅ Iniciar animaciones pesadas o mapas en `onIonViewDidEnter`
- ✅ Pausar video, GPS o *timers* en `onIonViewWillLeave`
- ⚠️ Solo funcionan en vistas envueltas en `<ion-page>`
</div>
</split-slide>

---

## Guardas y botón atrás de Android

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.8rem;">
<div>

### 🛡️ Proteger rutas
```ts
// src/router/index.ts
router.beforeEach(async (to) => {
  const { cargarToken, estaAutenticado } = useAuth();
  await cargarToken();
  if (to.meta.requiereSesion && !estaAutenticado.value) {
    return '/login';
  }
});
```

- El guard **no es seguridad**: solo evita mostrar pantallas inútiles. La API decide con **401/403**
</div>
<div>

### ◀️ Botón / gesto atrás del sistema
- Ionic lo integra con el router automáticamente
- Para personalizarlo (p. ej. confirmar antes de salir de un formulario):

```ts
import { useBackButton, useIonRouter } from '@ionic/vue';
import { App } from '@capacitor/app';

const ionRouter = useIonRouter();

// prioridad alta: se ejecuta primero
useBackButton(10, (siguiente) => {
  if (hayCambios.value) confirmarSalida();
  else siguiente();
});

// prioridad -1: último recurso, en la raíz cierra la app
useBackButton(-1, () => {
  if (!ionRouter.canGoBack()) App.exitApp();
});
```
</div>
</split-slide>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00058_.png);
}
</style>
# Gestión de datos
## Contenidos
- Dónde viven los datos en una app móvil
- Consumo de la API Laravel
- Autenticación con tokens
- Almacenamiento local y modo sin conexión

---

## Dónde viven los datos

| Tipo de dato | Ejemplo | Dónde guardarlo |
|:--|:--|:--|
| **Estado de pantalla** | Filtro activo, formulario a medias | `ref` / `reactive` del componente |
| **Estado compartido** | Usuario actual, carrito | **Composable** con estado de módulo o **Pinia** |
| **Preferencias pequeñas** | Tema, idioma, último usuario | `@capacitor/preferences` |
| **Secretos** | Token de acceso | Almacenamiento **seguro** (Keychain / Keystore) |
| **Datos estructurados locales** | Catálogo sin conexión, cola de envíos | **SQLite** (`@capacitor-community/sqlite`) |
| **Archivos** | Fotos, PDFs descargados | `@capacitor/filesystem` |
| **Fuente de verdad** | Pedidos, clientes | **La API Laravel** en el servidor |

- ⚠️ No use `localStorage` para datos importantes: el sistema operativo **puede borrar** el almacenamiento del WebView cuando falta espacio

---

## Consumir la API Laravel

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.78rem;">
<div>

```ts
// src/services/pedidos.ts
import { api } from './api';

export interface Pedido {
  id: number; cliente: string; total: number; estado: string;
}
export type NuevoPedido = Omit<Pedido, 'id' | 'total'>;

export const pedidosApi = {
  listar: (pagina = 1) =>
    api<{ data: Pedido[] }>(`/pedidos?page=${pagina}`)
      .then(r => r.data),

  obtener: (id: number) =>
    api<{ data: Pedido }>(`/pedidos/${id}`).then(r => r.data),

  crear: (pedido: NuevoPedido) =>
    api<{ data: Pedido }>('/pedidos', {
      method: 'POST', body: JSON.stringify(pedido),
    }).then(r => r.data),

  cancelar: (id: number) =>
    api<void>(`/pedidos/${id}`, { method: 'DELETE' }),
};
```
</div>
<div>

- La app **no conoce** la base de datos: solo el **contrato** de la API (el documento OpenAPI del tema anterior)
- `.data` viene de los **API Resources** de Laravel
- La vista llama al módulo de servicio; el servicio habla HTTP. Misma **separación por capas** que en el back-end

```bash
# .env  (Vite expone solo las variables VITE_*)
VITE_API_URL=http://10.0.2.2:8000/api
```

- `10.0.2.2` es el `localhost` de la PC **visto desde el emulador** de Android
</div>
</split-slide>

---

## Autenticación con tokens de Sanctum

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.75rem;">
<div>

```ts
// src/composables/useAuth.ts
const token = ref<string | null>(null);   // compartido: fuera de la función

export function useAuth() {
  async function login(email: string, password: string) {
    const r = await api<{ token: string }>('/login', {
      method: 'POST',
      body: JSON.stringify({ email, password, device_name: 'app-movil' }),
    });
    token.value = r.token;
    await almacenSeguro.set('token', r.token);
  }
  async function cargarToken() {
    token.value ??= await almacenSeguro.get('token');
  }
  async function cerrarSesion() {
    token.value = null;
    await almacenSeguro.remove('token');
  }
  const estaAutenticado = computed(() => token.value !== null);
  return { token, login, cargarToken, cerrarSesion, estaAutenticado };
}
```
</div>
<div>

- Un **composable** con el `ref` **fuera** de la función: todas las vistas comparten el mismo token (sin necesidad de Pinia)
- `device_name` permite que Sanctum emita **un token por dispositivo** y revocarlo
- `cargarToken()` lo recupera al abrir la app: lo usa la guarda del router

### 🔐 `almacenSeguro`
- `@capacitor/preferences` **no está cifrado**: no sirve para tokens
- Use un plugin respaldado por **Keychain** (iOS) y **Keystore** (Android): plugins comunitarios de *secure storage* o **Identity Vault** (Ionic Enterprise)
- Encapsúlelo en un módulo para poder cambiar de plugin sin tocar el resto
</div>
</split-slide>

---

## Un único cliente HTTP: `api()`

<split-slide style="--left: 60%; --right: 40%; --font-size: 0.8rem;">
<div>

```ts
// src/services/api.ts
export async function api<T>(ruta: string, op: RequestInit = {}): Promise<T> {
  const { token, cerrarSesion } = useAuth();
  const res = await fetch(`${import.meta.env.VITE_API_URL}${ruta}`, { ...op,
    headers: { Accept: 'application/json',
      ...(typeof op.body === 'string' && { 'Content-Type': 'application/json' }),
      ...(token.value && { Authorization: `Bearer ${token.value}` }) } });
  if (res.status === 401) { await cerrarSesion(); router.replace('/login'); }
  if (!res.ok) throw new ErrorApi(res.status, await res.json());
  return res.status === 204 ? (undefined as T) : res.json();
}
```
</div>
<div>

```ts
// src/services/errores.ts
export class ErrorApi extends Error {
  constructor(public status: number, public cuerpo: any) {
    super(`HTTP ${status}`);
  }
}
```
</div>
<div>

- `api()` es el **único lugar** que conoce la URL base, los encabezados y el token: agrega `Authorization: Bearer …` a **todas** las peticiones
- Hace el papel de un *interceptor*: sin dependencias, solo `fetch`
- Un **401** de la API → borrar el token y volver al login
- `ErrorApi` lleva el código y el cuerpo (p. ej. los errores **422**) hasta la vista
- `Accept: application/json` hace que Laravel responda errores en **JSON** y no en HTML
</div>
</split-slide>

---

## CORS, HTTP y la red del dispositivo

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.82rem;">
<div>

### 🌐 Orígenes de la app
La API Laravel debe aceptar los orígenes del WebView:

```php
// config/cors.php
'allowed_origins' => [
    'http://localhost:8100',  // ionic serve
    'https://localhost',      // Capacitor Android
    'capacitor://localhost',  // Capacitor iOS
],
```

### 🔁 Alternativa: `CapacitorHttp`
```ts
// capacitor.config.ts
plugins: { CapacitorHttp: { enabled: true } }
```
- Envía `fetch`/`XMLHttpRequest` por la **pila HTTP nativa**: sin CORS
</div>
<div>

### 🔒 Tráfico en claro
- Android y iOS **bloquean HTTP** sin TLS por defecto
- En desarrollo: permitir *cleartext* solo para el host local
- En producción: **siempre HTTPS**

### 📶 Estado de la red
```ts
import { Network } from '@capacitor/network';

const { connected } = await Network.getStatus();

Network.addListener('networkStatusChange', s => {
  if (s.connected) sincronizarPendientes();
});
```
</div>
</split-slide>

---

## Preferencias y almacenamiento local

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.82rem;">
<div>

### ⚙️ `@capacitor/preferences`
```bash
npm i @capacitor/preferences
npx cap sync
```
```ts
import { Preferences } from '@capacitor/preferences';

await Preferences.set({
  key: 'ajustes',
  value: JSON.stringify({ tema: 'oscuro', idioma: 'es' }),
});

const { value } = await Preferences.get({ key: 'ajustes' });
const ajustes = value ? JSON.parse(value) : null;

await Preferences.remove({ key: 'ajustes' });
```
- `SharedPreferences` en Android, `UserDefaults` en iOS
- Solo **cadenas** y cantidades **pequeñas** de datos
</div>
<div>

### 🗄️ SQLite para datos estructurados
- `@capacitor-community/sqlite`: base de datos **real** en el dispositivo, con consultas SQL y transacciones
- Útil para catálogos grandes y apps que deben funcionar **sin conexión**

### 📁 Archivos
```ts
import { Filesystem, Directory } from '@capacitor/filesystem';

await Filesystem.writeFile({
  path: 'recibos/123.json',
  data: JSON.stringify(recibo),
  directory: Directory.Data,
  encoding: 'utf8',
  recursive: true,
});
```
</div>
</split-slide>

---

## Estrategia sin conexión (offline-first)

<steps>
<step>

```text
  Usuario crea un pedido
        │
        ▼
  ¿Hay red? ──── sí ───▶ POST /api/pedidos ──▶ 201 ✅
        │
        no
        ▼
  Guardar en la cola local (SQLite / Preferences)  ── estado "pendiente de envío"
        │
        ▼
  networkStatusChange → connected  ──▶  reenviar la cola en orden
        │
        ▼
  409 / 422 del servidor → marcar conflicto y avisar al usuario
```

</step>
<step>

<div class="grid">
<div>

### 🔑 Idempotencia
Enviar un identificador generado en el cliente para que un **reintento** no duplique el pedido
</div>
<div>

### 🕒 Caché de lectura
Mostrar la última copia local **de inmediato** y refrescar desde la API en segundo plano
</div>
<div>

### ⚔️ Conflictos
El servidor es la **fuente de verdad**: la app informa, no sobrescribe a ciegas
</div>
<div>

### 👀 Visibilidad
El usuario debe **ver** qué está sincronizado y qué no
</div>
</div>

</step>
</steps>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00059_.png);
}
</style>
# Componentes de hardware
## Contenidos
- Plugins de Capacitor
- Permisos
- Cámara y geolocalización
- Otros sensores y notificaciones

---

## Plugins de Capacitor

<div class="grid">
<div>

### 📷 Multimedia
`@capacitor/camera`, `@capacitor/barcode-scanner`, `@capacitor/screen-reader`
</div>
<div>

### 📍 Ubicación y movimiento
`@capacitor/geolocation`, `@capacitor/motion` (acelerómetro, giroscopio)
</div>
<div>

### 🔔 Notificaciones
`@capacitor/push-notifications` (FCM / APNs), `@capacitor/local-notifications`
</div>
<div>

### 📱 Dispositivo
`@capacitor/device`, `@capacitor/haptics`, `@capacitor/network`, `@capacitor/screen-orientation`
</div>
<div>

### 🧰 Sistema
`@capacitor/app`, `@capacitor/share`, `@capacitor/clipboard`, `@capacitor/keyboard`, `@capacitor/status-bar`
</div>
<div>

### 🌍 Comunidad
Capacitor Community y Capawesome: biometría, SQLite, NFC, Bluetooth LE, Google Maps…
</div>
</div>

- 🔁 El patrón es siempre el mismo: `npm i @capacitor/<plugin>` → `npx cap sync` → configurar permisos → `import` y `await`

---

## Permisos

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.8rem;">
<div>

### En tiempo de ejecución
```ts
import { Geolocation } from '@capacitor/geolocation';

async function asegurarPermiso() {
  let estado = await Geolocation.checkPermissions();

  if (estado.location === 'prompt') {
    estado = await Geolocation.requestPermissions();
  }
  if (estado.location === 'denied') {
    // explicar por qué se necesita y cómo
    // activarlo en Ajustes
    return false;
  }
  return true;
}
```

- Estados: `granted`, `denied`, `prompt`, `prompt-with-rationale`
</div>
<div>

### Declarados en el proyecto nativo
**iOS** — `ios/App/App/Info.plist`
```xml
<key>NSCameraUsageDescription</key>
<string>Para fotografiar el comprobante</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Para calcular el envío</string>
```

**Android** — `AndroidManifest.xml`
```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

- ⚠️ Sin la descripción en `Info.plist`, iOS **cierra la app** al pedir el permiso
- ✅ Pida el permiso **en contexto**, cuando el usuario toca la función, no al abrir la app
</div>
</split-slide>

---

## Cámara

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.8rem;">
<div>

```ts
<script setup lang="ts">
import { ref } from 'vue';
import { Camera } from '@capacitor/camera';

const props = defineProps<{ id: string }>();
const foto = ref<string | null>(null);

async function tomarFoto() {
  const r = await Camera.takePhoto({ quality: 80 });
  foto.value = r.webPath;            // usable en <img src>
}

async function elegirDeGaleria() {
  const { results } = await Camera.chooseFromGallery({ quality: 80 });
  foto.value = results[0]?.webPath ?? null;
}

async function subir() {
  const blob = await fetch(foto.value!).then(r => r.blob());
  const datos = new FormData();
  datos.append('comprobante', blob, 'comprobante.jpg');
  await api(`/pedidos/${props.id}/comprobante`,
    { method: 'POST', body: datos });
}
</script>

<template>
  <img v-if="foto" :src="foto" alt="Comprobante" />
</template>
```
</div>
<div>

- **Desde `@capacitor/camera` 8.1**: `takePhoto()` y `chooseFromGallery()`
- `getPhoto()` y `pickImages()` quedan **obsoletos**; el diálogo "Cámara o Galería" ya no lo pone el plugin: use un `ion-action-sheet`
- El resultado trae `webPath` (para mostrar), `uri` (ruta nativa) y `thumbnail`
- En el navegador funciona con `<input type="file">` como respaldo
- La subida al servidor es un `multipart/form-data` normal que Laravel recibe con `$request->file('comprobante')` (por eso `api()` no fija `Content-Type` cuando el cuerpo es `FormData`)
</div>
</split-slide>

---

## Geolocalización

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.8rem;">
<div>

```ts
import { Geolocation } from '@capacitor/geolocation';

// Una lectura
const pos = await Geolocation.getCurrentPosition({
  enableHighAccuracy: true,
  timeout: 10_000,
});
const { latitude, longitude, accuracy } = pos.coords;

// Seguimiento continuo mientras la vista está activa
let id: string | undefined;

onIonViewDidEnter(async () => {
  id = await Geolocation.watchPosition(
    { enableHighAccuracy: true },
    (pos, err) => { if (pos) repartidor.value = pos.coords; },
  );
});

// ¡Siempre detenerlo!
onIonViewWillLeave(() => {
  if (id) Geolocation.clearWatch({ id });
});
```
</div>
<div>

- `enableHighAccuracy` usa el **GPS**: más preciso, más batería
- Sin él se usa Wi-Fi y antenas: más rápido y barato
- `accuracy` indica el **margen de error en metros**
- `watchPosition` sin `clearWatch` = batería drenada y usuarios molestos
- La ubicación **en segundo plano** requiere permisos y plugins adicionales, y las tiendas la revisan con lupa
</div>
</split-slide>

---

## Otros recursos del dispositivo

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.8rem;">
<div>

### 📳 Háptica
```ts
import { Haptics, ImpactStyle } from '@capacitor/haptics';
await Haptics.impact({ style: ImpactStyle.Medium });
```

### ℹ️ Dispositivo
```ts
import { Device } from '@capacitor/device';
const info = await Device.getInfo();   // platform, model, osVersion…
const bateria = await Device.getBatteryInfo();
```

### 📤 Compartir
```ts
import { Share } from '@capacitor/share';
await Share.share({ title: 'Mi pedido', url: 'https://…/pedidos/123' });
```
</div>
<div>

### 🧭 Movimiento
```ts
import { Motion } from '@capacitor/motion';
Motion.addListener('accel', e => {
  const { x, y, z } = e.acceleration;
});
```

### 🔔 Notificaciones push
- `@capacitor/push-notifications` registra el dispositivo y entrega un **token** de FCM (Android) / APNs (iOS)
- La app envía ese token a **Laravel**, que lo usa para notificar: "Tu pedido está listo"
- Requiere configurar **Firebase** y la cuenta de Apple Developer
</div>
</split-slide>

---

## Web vs. nativo en el mismo código

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.85rem;">
<div>

```ts
import { Capacitor } from '@capacitor/core';

Capacitor.getPlatform();        // 'web' | 'android' | 'ios'
Capacitor.isNativePlatform();   // true en Android / iOS

if (!Capacitor.isPluginAvailable('Camera')) {
  // ofrecer subir un archivo en su lugar
}
```

```ts
// Ionic también expone la plataforma
import { isPlatform } from '@ionic/vue';

isPlatform('android');
isPlatform('hybrid');     // corre dentro de Capacitor
isPlatform('mobileweb');  // navegador de un teléfono
```
</div>
<div>

- La **misma** app corre en el navegador, Android e iOS: decida en tiempo de ejecución qué ofrecer
- Diseñe una **degradación elegante**: si no hay cámara, un selector de archivo; si no hay GPS, un campo de dirección
- ¿No existe el plugin? Capacitor permite **escribir uno propio** en Kotlin y Swift con `npm init @capacitor/plugin`
</div>
</split-slide>

---

## 📋 Laboratorio — App móvil del proyecto

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.9rem;">
<div>

### Qué se entrega
- Proyecto **Ionic 9 + Capacitor 8** con plataforma **Android** (iOS opcional)
- **Login** contra la API Laravel con token de Sanctum guardado de forma **segura**
- Navegación con **pestañas** y al menos una pila **lista → detalle → formulario**
- **CRUD** de una entidad del proyecto consumiendo la API
- Manejo visible de **errores** (401, 422, sin conexión)
</div>
<div>

### Hardware (al menos uno)
- 📷 Cámara: adjuntar una foto a un registro
- 📍 Geolocalización: guardar o mostrar una ubicación
- 📶 Red: aviso y cola de envíos sin conexión
- 🔔 Notificación local al completar una acción

### Evidencia
- APK de depuración instalable
- Video corto en un **dispositivo físico o emulador**
- README con la versión de Node, Ionic y Capacitor usadas
</div>
</split-slide>

---

## Referencias

- Ionic. [Documentación de Ionic Framework](https://ionicframework.com/docs) · [Componentes](https://ionicframework.com/docs/components) · [Migración a Ionic 9](https://ionicframework.com/docs/updating/9-0) · [Navegación en Vue](https://ionicframework.com/docs/vue/navigation) · [Ciclo de vida de páginas en Vue](https://ionicframework.com/docs/vue/lifecycle) · [Botón atrás del hardware](https://ionicframework.com/docs/developing/hardware-back-button) · [Temas y colores](https://ionicframework.com/docs/theming/basics) · [`ionic start`](https://ionicframework.com/docs/cli/commands/start)
- Ionic. [Versiones de Ionic Framework (GitHub)](https://github.com/ionic-team/ionic-framework/releases)
- Capacitor. [Documentación](https://capacitorjs.com/docs) · [Configuración del entorno](https://capacitorjs.com/docs/getting-started/environment-setup) · [Anuncio de Capacitor 8](https://ionic.io/blog/announcing-capacitor-8) · [Versiones (GitHub)](https://github.com/ionic-team/capacitor/releases)
- Capacitor. APIs: [Camera](https://capacitorjs.com/docs/apis/camera) · [Geolocation](https://capacitorjs.com/docs/apis/geolocation) · [Preferences](https://capacitorjs.com/docs/apis/preferences) · [Network](https://capacitorjs.com/docs/apis/network) · [Filesystem](https://capacitorjs.com/docs/apis/filesystem) · [Push Notifications](https://capacitorjs.com/docs/apis/push-notifications) · [CapacitorHttp](https://capacitorjs.com/docs/apis/http) · [Almacenamiento](https://capacitorjs.com/docs/guides/storage)
- Capacitor Community. [SQLite](https://github.com/capacitor-community/sqlite)
- Google. [Material Design 3](https://m3.material.io/) · [Android Developers](https://developer.android.com/) · Apple. [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/) · [Swift](https://developer.apple.com/swift/)
- StatCounter. [Cuota de mercado de sistemas operativos móviles](https://gs.statcounter.com/os-market-share/mobile/worldwide) · Stack Overflow. [Developer Survey](https://survey.stackoverflow.co/)
- Presentaciones del curso: *Fundamentos de la Web* (web vs. móvil, PWA), *Web Components* y *Desarrollo Backend* (API REST y Sanctum)

<script src="../assets/steps.js"></script>
<script src="../assets/image-modal.js"></script>
