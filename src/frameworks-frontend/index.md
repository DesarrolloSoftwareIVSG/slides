---
marp: true
theme: alo
paginate: true
---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00070_.png);
}
</style>
# Desarrollo en un framework front-end
## Contenidos
- Frameworks vs librerías · Historia
- Componentes, módulos y su estructura
- Enrutamiento · Pipes y directivas
- Decoradores, observables y servicios web
- Manejo del estado · Pruebas

> **React 19**, **Vue 3** y **Angular 22** de forma comparativa <br>Curso *Desarrollo de Software IV* · II Ciclo 2026

---

## ¿Qué construimos en este tema?

- El **front-end** es la aplicación que corre en el navegador: dibuja la interfaz, reacciona a lo que hace la persona usuaria y conversa con la API del back-end

<div class="grid">
<div>

### 🧩 Componentes
Cómo partir una interfaz en piezas reutilizables con vista, lógica y estilo
</div>
<div>

### 🧭 Navegación
Cómo pasar de "una página" a una **SPA** con rutas, parámetros y guardas
</div>
<div>

### 🔌 Datos
Cómo consumir la **API REST** del tema de back-end y mantener el estado coherente
</div>
<div>

### ✅ Calidad
Cómo probar componentes, servicios y flujos completos
</div>
</div>

- 🎯 Al final del tema cada equipo tendrá un **cliente web** que consume su API: listado, detalle, formulario y pruebas

---

## Los tres protagonistas

<split-slide style="--left: 50%; --right: 50%;">
<div>

### ⚛️ React — la librería
- Creada por **Meta** (2013)
- **JSX** + funciones + *hooks*
- Solo resuelve la **vista**: el resto lo elige usted (router, estado, HTTP)

### 💚 Vue — el progresivo
- Creado por **Evan You** (2014), comunidad independiente
- Plantillas HTML + **Composition API**
- Núcleo pequeño + piezas **oficiales** (Vue Router, Pinia)

</div>
<div>

### 🅰️ Angular — el framework completo
- Mantenido por **Google** (2016, reescritura de AngularJS)
- **TypeScript** obligatorio, decoradores, inyección de dependencias
- Trae **todo**: router, HTTP, formularios, pruebas, CLI

<br>

- 💡 Los tres resuelven el mismo problema: **mantener la interfaz sincronizada con el estado**. Cambia el *cómo* y cuánto deciden por usted

</div>
</split-slide>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00057_.png);
}
</style>
# Frameworks vs librerías
## Contenidos
- Inversión de control
- El espectro librería ↔ framework
- Metaframeworks
- Cómo elegir

---

## Librería vs framework: ¿quién llama a quién?

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.85rem;">
<div>

### 📚 Librería
- **Usted** llama al código de la librería cuando lo necesita
- Usted decide la arquitectura, la estructura de carpetas y el resto de piezas
- Ej.: `lodash`, `axios`, **React**

```js
import { format } from 'date-fns';

// Yo decido cuándo y dónde
const texto = format(new Date(), 'dd/MM/yyyy');
```

</div>
<div>

### 🏗️ Framework
- El **framework** llama a su código: usted rellena huecos que él define
- Impone estructura, convenciones y ciclo de vida
- Ej.: **Angular**, Laravel, NestJS

```ts
@Component({ selector: 'app-hola', template: '...' })
export class Hola implements OnInit {
  // Angular decide cuándo ejecutarlo
  ngOnInit() { /* ... */ }
}
```

</div>
</split-slide>

- 🎬 *Principio de Hollywood*: "no nos llame, nosotros le llamamos" — eso es la **inversión de control**

---

## El espectro: no es blanco o negro

```text
 Librería ◄───────────────────────────────────────────────────► Framework completo
   │                         │                                            │
 React                      Vue                                        Angular
 "solo la vista"            "progresivo"                               "baterías incluidas"
 Usted elige router,        Núcleo + piezas oficiales                  Todo viene en la caja
 estado, HTTP, forms        que se adoptan por partes                  y con una forma de hacerlo
```

<div class="grid">
<div>

### ⚛️ Libertad
Máxima flexibilidad… y máxima cantidad de decisiones. Dos proyectos React pueden no parecerse en nada
</div>
<div>

### 💚 Adopción gradual
Se puede usar en **un** `<div>` de una página existente o como SPA completa
</div>
<div>

### 🅰️ Consistencia
Dos proyectos Angular se parecen mucho entre sí: ideal para equipos grandes y proyectos largos
</div>
</div>

---

## Comparación general

| | ⚛️ React | 💚 Vue | 🅰️ Angular |
|:--|:--|:--|:--|
| Tipo | Librería de UI | Framework progresivo | Framework completo |
| Lenguaje | JS / TS + **JSX** | JS / TS + plantillas | **TypeScript** + plantillas |
| Reactividad | Re-render + Virtual DOM | **Proxies** + Virtual DOM (Vapor en camino) | **Signals** (sin `zone.js`) |
| Router | React Router / TanStack Router | **Vue Router** (oficial) | **@angular/router** |
| Estado global | Context, Zustand, Redux | **Pinia** (oficial) | Servicios + signals, NgRx |
| HTTP | `fetch`, TanStack Query | `fetch`, VueUse | **HttpClient** |
| Pruebas | Vitest + Testing Library | Vitest + Vue Test Utils | **Vitest** + TestBed |
| Herramienta de inicio | Vite | `create-vue` (Vite) | **Angular CLI** |

---

## Metaframeworks: el siguiente escalón

- Un **metaframework** se monta sobre uno de estos y agrega **renderizado en servidor (SSR)**, generación estática, enrutamiento por archivos y *endpoints* de API

<div class="grid">
<div>

### ⚛️ React
**Next.js**, **React Router** en "modo framework" (antes Remix)
</div>
<div>

### 💚 Vue
**Nuxt**
</div>
<div>

### 🅰️ Angular
**Angular SSR** (integrado en el CLI), **Analog**
</div>
<div>

### 🚀 Agnóstico
**Astro** — islas con cualquiera de los tres (ver tema *Astro*)
</div>
</div>

- 🔗 Recuerden la imagen del tema *WebComponents*: tecnología base → librerías → frameworks → metaframeworks
- En este curso trabajamos en modo **SPA** (CSR) porque la lógica y los datos viven en **nuestra API**

---

## ¿Cómo elegir?

<div class="grid">
<div>

### Elija ⚛️ React si…
- El equipo ya domina JavaScript y quiere libertad
- Necesita el ecosistema más grande (y React Native)
- El mercado laboral local lo pide
</div>
<div>

### Elija 💚 Vue si…
- Quiere la curva de aprendizaje más suave
- Va a "enriquecer" un sitio existente poco a poco
- Prefiere plantillas HTML y piezas oficiales
</div>
<div>

### Elija 🅰️ Angular si…
- El equipo es grande o rota con frecuencia
- Necesita convenciones fuertes y todo integrado
- Viene de Java / C# (DI, decoradores, tipos)
</div>
</div>

- 🚩 **Mala razón** para elegir: "es el más rápido". En aplicaciones reales la diferencia la hace el **código del equipo**, no el framework

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00067_.png);
}
</style>
# Historia de los frameworks front-end
## Contenidos
- Del DOM a mano a los componentes
- Las cuatro eras
- Versiones actuales

---

## Línea de tiempo

<steps>
<step>

```text
2006  jQuery ................ manipular el DOM sin pelear con cada navegador
2010  Backbone, Knockout .... primeros MVC/MVVM en el cliente
2010  AngularJS (Google) .... data binding bidireccional + inyección de dependencias
2011  Ember ................. convenciones fuertes, router
2013  React (Facebook) ...... componentes + Virtual DOM + "la UI es una función del estado"
2014  Vue (Evan You) ........ lo mejor de AngularJS, más simple
2016  Angular 2 ............. reescritura total en TypeScript (rompe con AngularJS)
2016  Svelte ................ el framework como compilador
```

</step>
<step>

```text
2019  React Hooks (16.8) .... adiós a las clases
2020  Vue 3 ................. Composition API + reactividad con Proxies
2021  AngularJS fin de vida . soporte terminado en diciembre
2022  Angular 14 ............ componentes standalone (sin NgModule)
2023  Angular 16-17 ......... signals + nuevo control de flujo (@if, @for)
2024  React 19 .............. Actions, use(), Server Components estables
2025  React Compiler 1.0 .... memorización automática
2025  Angular 21 ............ zoneless por defecto, Vitest, Signal Forms experimental
2026  Angular 22 ............ Signal Forms estable, OnPush por defecto
2026  React 19.3 · Vue 3.6 RC (Vapor Mode: sin Virtual DOM)
```

</step>
</steps>

---

## Las cuatro eras

<div class="grid">
<div>

### 1️⃣ DOM imperativo (2006–2010)
`$('#total').text(x)`: cada cambio de datos se refleja **a mano** en el DOM. Funciona… hasta que la app crece
</div>
<div>

### 2️⃣ MVC en el cliente (2010–2013)
Modelos, vistas y *data binding*. AngularJS sincroniza solo, pero con **dirty checking** costoso
</div>
<div>

### 3️⃣ Componentes + Virtual DOM (2013–2020)
React: la UI es `f(estado)`. Se re-renderiza y se compara un árbol virtual (*diffing*)
</div>
<div>

### 4️⃣ Compiladores y signals (2020–hoy)
Reactividad **fina**: solo se actualiza el nodo que depende del dato. Angular signals, Vue Vapor, Svelte, Solid
</div>
</div>

- 💡 La tendencia es clara: **menos JavaScript en el navegador** y **actualizaciones más precisas**

---

## Versiones actuales (octubre 2026)

| Framework | Versión estable | Publicación | Requisitos / notas |
|:--|:--|:--|:--|
| ⚛️ React | **19.3** | set 2026 | `<ViewTransition>`, *refs* en `Fragment`; React 20 aún no anunciado |
| 💚 Vue | **3.5** | 2024 (parches 2026) | **3.6** en RC con Vapor Mode y nueva reactividad (*alien-signals*) |
| 💚 Vue Router | **5** | 2026 | Rutas por archivos integradas en el núcleo |
| 🅰️ Angular | **22** | jun 2026 | **Node 22+**, **TypeScript 6**; versión mayor cada 6 meses |

- Angular publica **dos versiones mayores al año** (~mayo y ~noviembre) con 18 meses de soporte
- React y Vue no tienen calendario fijo: versiones mayores cada varios años, menores frecuentes
- ⚠️ Lo que encuentren en tutoriales de 2022 probablemente use `NgModule`, `*ngIf`, `class components` u Options API: **funciona, pero ya no es la forma recomendada**

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00055_.png);
}
</style>
# Modularización del proyecto
## Contenidos
- ¿Qué es un componente?
- Árbol de componentes y flujo de datos
- Módulos y estructura de carpetas
- Crear el proyecto

---

## ¿Qué es un componente?

<split-slide style="--left: 45%; --right: 55%; --font-size: 0.85rem;">
<div>

```text
┌───────────────── App ─────────────────┐
│ ┌────────────── Encabezado ─────────┐ │
│ │  Logo   Menu        Carrito (3)   │ │
│ └───────────────────────────────────┘ │
│ ┌──── Filtros ──┐ ┌── ListaProductos ┐│
│ │               │ │ ┌TarjetaProducto┐││
│ │               │ │ └───────────────┘││
│ │               │ │ ┌TarjetaProducto┐││
│ │               │ │ └───────────────┘││
│ └───────────────┘ └──────────────────┘│
└───────────────────────────────────────┘
```

</div>
<div>

- Una **pieza reutilizable** de interfaz que encapsula:
  - **Vista** — qué se dibuja
  - **Lógica** — estado y comportamiento
  - **Estilo** — cómo se ve
- Recibe datos de **entrada** (*props* / *inputs*)
- Emite **eventos** de salida hacia su padre
- Se compone con otros: la aplicación es un **árbol**

<br>

- 🔗 Es la misma idea de los *custom elements* del tema *WebComponents*, con más ayuda del framework

</div>
</split-slide>

---

## Flujo de datos: props hacia abajo, eventos hacia arriba

```text
                  App  (estado: carrito)
                 ╱                        ╲
   props: cantidad                          props: productos
             ▼                                     ▼
        Encabezado                          ListaProductos
                                                   │ props: producto
                                                   ▼
                                           TarjetaProducto
                                                   │
                                  evento: agregar(producto)  ▲ sube hasta App
```

- El **dueño** del dato es quien lo modifica; los hijos solo lo **leen** y **avisan**
- Este *flujo unidireccional* es lo que hace predecible la aplicación
- 🚩 Si un hijo modifica directamente un *prop*, el framework le avisará (o fallará en silencio)

---

## Entradas y salidas comparadas

<steps>
<step>

### ⚛️ React — props y *callbacks*

```jsx
// TarjetaProducto.jsx
export function TarjetaProducto({ producto, onAgregar }) {
  return (
    <article>
      <h3>{producto.nombre}</h3>
      <button onClick={() => onAgregar(producto)}>Agregar</button>
    </article>
  );
}

// Uso en el padre
<TarjetaProducto producto={p} onAgregar={agregarAlCarrito} />
```

- No hay "eventos": se pasa una **función** como *prop*

</step>
<step>

### 💚 Vue — `defineProps` y `defineEmits`

```ts
<!-- TarjetaProducto.vue -->
<script setup>
const props = defineProps({ producto: { type: Object, required: true } });
const emit = defineEmits(['agregar']);
</script>

<template>
  <article>
    <h3>{{ producto.nombre }}</h3>
    <button @click="emit('agregar', producto)">Agregar</button>
  </article>
</template>

<!-- Uso en el padre -->
<TarjetaProducto :producto="p" @agregar="agregarAlCarrito" />
```

</step>
<step>

### 🅰️ Angular — `input()` y `output()`

```ts
// tarjeta-producto.ts
@Component({
  selector: 'app-tarjeta-producto',
  template: `
    <article>
      <h3>{{ producto().nombre }}</h3>
      <button (click)="agregar.emit(producto())">Agregar</button>
    </article>`,
})
export class TarjetaProducto {
  producto = input.required<Producto>();
  agregar = output<Producto>();
}
```

```html
<!-- Uso en el padre -->
<app-tarjeta-producto [producto]="p" (agregar)="agregarAlCarrito($event)" />
```

</step>
</steps>

---

## Contenido proyectado: componentes "envoltorio"

- A veces el padre quiere pasar **marcado**, no datos: una tarjeta, un modal, un *layout*

| | ⚛️ React | 💚 Vue | 🅰️ Angular |
|:--|:--|:--|:--|
| Mecanismo | `children` | **slots** | **content projection** |
| Componente | `<div className="card">{children}</div>` | `<div class="card"><slot /></div>` | `<div class="card"><ng-content /></div>` |
| Con nombre | *props* con JSX: `header={<h2/>}` | `<slot name="header" />` | `<ng-content select="[header]" />` |
| Uso | `<Card><p>Hola</p></Card>` | `<Card><p>Hola</p></Card>` | `<app-card><p>Hola</p></app-card>` |

- 🔗 Idéntico al `<slot>` de *Shadow DOM* visto en *WebComponents*

---

## Módulos: de NgModule a standalone

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.8rem;">
<div>

- Los tres usan **módulos ES** (`import` / `export`) para organizar el código (ver tema *Módulos ESM*)
- ⚛️ y 💚: **un archivo = un componente**, se importa y se usa
- 🅰️ Angular tenía además **NgModule**: una capa extra para declarar qué componentes existen

```ts
// ❌ Antes (Angular ≤ 16): NgModule
@NgModule({
  declarations: [TarjetaProducto],
  imports: [CommonModule],
  exports: [TarjetaProducto],
})
export class ProductosModule {}
```

</div>
<div>

```ts
// ✅ Hoy: componentes standalone (por defecto desde v19)
@Component({
  selector: 'app-lista-productos',
  imports: [TarjetaProducto, CurrencyPipe],
  templateUrl: './lista-productos.html',
})
export class ListaProductos {}
```

- Cada componente declara **sus propias dependencias** en `imports`
- La "modularización" ahora se hace por **carpetas de funcionalidad** y **carga diferida** de rutas
- ⚠️ En código legado aún verá `NgModule`; existe la migración `ng generate @angular/core:standalone`

</div>
</split-slide>

---

## Estructura de carpetas por funcionalidad

<split-slide style="--left: 45%; --right: 55%; --font-size: 0.85rem;">
<div>

```text
src/
├── app/               (o src/ directo en React/Vue)
│   ├── core/          ← una sola vez en toda la app
│   │   ├── auth/          interceptor, guard, sesión
│   │   └── layout/        encabezado, menú
│   ├── shared/        ← reutilizable y "tonto"
│   │   ├── ui/            botón, modal, tarjeta
│   │   └── pipes/         formatos
│   └── features/      ← una carpeta por caso de uso
│       ├── productos/
│       │   ├── lista-productos/
│       │   ├── detalle-producto/
│       │   ├── productos.service.ts
│       │   └── productos.routes.ts
│       └── pedidos/
└── environments/
```

</div>
<div>

- Agrupe por **funcionalidad**, no por tipo de archivo (`components/`, `services/`, `utils/`… no escala)
- Cada *feature* se puede cargar de forma **diferida** cuando se visita su ruta
- **Componentes presentacionales** (`shared/ui`): reciben *props*, emiten eventos, no saben de la API
- **Componentes contenedores** (`features/*`): obtienen datos y coordinan

<br>

- 💡 Si el proyecto final tiene entidades `Producto`, `Pedido`, `Cliente`… esas son sus carpetas de `features`

</div>
</split-slide>

---

## Crear el proyecto

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.85rem;">
<div>

```bash
# ⚛️ React con Vite (plantilla TypeScript)
npm create vite@latest tienda-react -- --template react-ts
cd tienda-react && npm install
npm run dev            # http://localhost:5173

# 💚 Vue con create-vue (pregunta por Router, Pinia, Vitest…)
npm create vue@latest tienda-vue
cd tienda-vue && npm install
npm run dev            # http://localhost:5173
```

</div>
<div>

```bash
# 🅰️ Angular CLI (requiere Node 22+)
npm install -g @angular/cli
ng new tienda-angular --style=css --ssr=false
cd tienda-angular
ng serve               # http://localhost:4200

# Generadores
ng generate component features/productos/lista-productos
ng generate service  features/productos/productos
ng generate pipe     shared/pipes/colones
```

</div>
</split-slide>

- Puertos del curso: front-end **5173** (Vite) / **4200** (Angular) · API Laravel **8000** — los mismos del tema de back-end

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00056_.png);
}
</style>
# Estructura de un componente
## Contenidos
- Vista, lógica y estilo
- El mismo componente en los tres
- Plantillas: condicionales y listas
- Ciclo de vida

---

## Vista, lógica y estilo: ¿dónde vive cada cosa?

<div class="grid">
<div>

### ⚛️ React — todo es JavaScript
- **Vista**: JSX devuelto por la función
- **Lógica**: *hooks* (`useState`, `useEffect`…)
- **Estilo**: CSS Modules, Tailwind, CSS-in-JS
- Un archivo `.jsx` / `.tsx`
</div>
<div>

### 💚 Vue — Single File Component
- **Vista**: `<template>`
- **Lógica**: `<script setup>`
- **Estilo**: `<style scoped>`
- Un archivo `.vue` con tres bloques
</div>
<div>

### 🅰️ Angular — archivos separados
- **Vista**: `contador.html`
- **Lógica**: clase en `contador.ts`
- **Estilo**: `contador.css` (encapsulado)
- Tres archivos unidos por `@Component`
</div>
</div>

- La **separación de responsabilidades** existe en los tres: React separa por **componente**, Angular por **tipo de archivo**, Vue en **bloques** dentro del mismo archivo

---

## El mismo contador en los tres

<steps>
<step>

### ⚛️ React

```jsx
import { useState } from 'react';
import styles from './Contador.module.css';

export function Contador({ inicial = 0 }) {
  const [cuenta, setCuenta] = useState(inicial);           // estado
  const doble = cuenta * 2;                                // derivado: se recalcula en cada render

  return (
    <div className={styles.caja}>
      <p>{cuenta} (doble: {doble})</p>
      <button onClick={() => setCuenta(c => c + 1)}>+1</button>
    </div>
  );
}
```

- Cada `setCuenta` **vuelve a ejecutar** la función completa y React compara el resultado

</step>
<step>

### 💚 Vue

```ts
<script setup>
import { ref, computed } from 'vue';
const props = defineProps({ inicial: { type: Number, default: 0 } });

const cuenta = ref(props.inicial);                 // estado reactivo
const doble = computed(() => cuenta.value * 2);    // derivado y cacheado
</script>

<template>
  <div class="caja">
    <p>{{ cuenta }} (doble: {{ doble }})</p>
    <button @click="cuenta++">+1</button>
  </div>
</template>

<style scoped>
.caja { padding: 1rem; border: 1px solid #ccc; }
</style>
```

</step>
<step>

### 🅰️ Angular

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.8rem;">
<div>

```ts
// contador.ts
@Component({
  selector: 'app-contador',
  templateUrl: './contador.html',
  styleUrl: './contador.css',
})
export class Contador {
  inicial = input(0);
  cuenta = linkedSignal(() => this.inicial());  // estado
  doble = computed(() => this.cuenta() * 2);    // derivado

  incrementar() {
    this.cuenta.update(c => c + 1);
  }
}
```

</div>
<div>

```html
<!-- contador.html -->
<div class="caja">
  <p>{{ cuenta() }} (doble: {{ doble() }})</p>
  <button (click)="incrementar()">+1</button>
</div>
```

```css
/* contador.css — solo afecta a este componente */
.caja { padding: 1rem; border: 1px solid #ccc; }
```

</div>
</split-slide>

</step>
</steps>

---

## Estilos encapsulados

- El problema: en CSS todo es **global**. Un `.caja` en un componente afecta a todos los `.caja` de la app

| | ⚛️ React | 💚 Vue | 🅰️ Angular |
|:--|:--|:--|:--|
| Por defecto | **Global** (`import './x.css'`) | Global | **Encapsulado** |
| Encapsular | `x.module.css` → `styles.caja` | `<style scoped>` | `ViewEncapsulation.Emulated` (defecto) |
| Cómo lo hace | Renombra clases: `caja_a1b2` | Atributo `data-v-a1b2` | Atributo `_ngcontent-a1b2` |
| Shadow DOM real | — | — | `ViewEncapsulation.ShadowDom` |

- Los tres conviven bien con **Tailwind** y con **variables CSS** para temas (ver *CSS avanzado*)

---

## Plantillas: interpolación, eventos y *binding*

| Necesidad | ⚛️ React (JSX) | 💚 Vue | 🅰️ Angular |
|:--|:--|:--|:--|
| Mostrar valor | `{nombre}` | `{{ nombre }}` | `{{ nombre() }}` |
| Atributo dinámico | `src={url}` | `:src="url"` | `[src]="url"` |
| Evento | `onClick={fn}` | `@click="fn"` | `(click)="fn()"` |
| Clase condicional | `` className={activo ? 'on' : ''} `` | `:class="{ on: activo }"` | `[class.on]="activo()"` |
| Doble vía (input) | `value` + `onChange` | `v-model="nombre"` | `[(ngModel)]` o Signal Forms (`form()`) |

- ⚛️ JSX **es JavaScript**: no hay sintaxis especial que aprender, pero tampoco atajos
- 💚 y 🅰️ usan una **plantilla HTML extendida** que el compilador transforma en JavaScript

---

## Condicionales y listas

<steps>
<step>

### ⚛️ React — JavaScript puro

```jsx
{cargando ? <Spinner /> : null}
{error && <p className="error">{error}</p>}

<ul>
  {productos.map(p => (
    <li key={p.id}>{p.nombre}</li>
  ))}
</ul>
{productos.length === 0 && <p>Sin productos</p>}
```

</step>
<step>

### 💚 Vue — directivas `v-if` / `v-for`

```html
<Spinner v-if="cargando" />
<p v-else-if="error" class="error">{{ error }}</p>

<ul v-else>
  <li v-for="p in productos" :key="p.id">{{ p.nombre }}</li>
</ul>
<p v-if="!productos.length">Sin productos</p>
```

</step>
<step>

### 🅰️ Angular — control de flujo integrado (v17+)

```html
@if (cargando()) {
  <app-spinner />
} @else if (error()) {
  <p class="error">{{ error() }}</p>
} @else {
  <ul>
    @for (p of productos(); track p.id) {
      <li>{{ p.nombre }}</li>
    } @empty {
      <li>Sin productos</li>
    }
  </ul>
}
```

- `track` es **obligatorio**: equivale a `key` en React y Vue. Reemplaza a `*ngIf` / `*ngFor`

</step>
</steps>

---

## Ciclo de vida comparado

| Momento | ⚛️ React | 💚 Vue | 🅰️ Angular |
|:--|:--|:--|:--|
| Al crearse | cuerpo de la función | `<script setup>` | `constructor` / `inject()` |
| Montado en el DOM | `useEffect(fn, [])` | `onMounted` | `ngOnInit` / `afterNextRender` |
| Cuando cambia un dato | `useEffect(fn, [dato])` | `watch(dato, fn)` | `effect()` / `ngOnChanges` |
| Antes de destruirse | `return () => {…}` en `useEffect` | `onUnmounted` | `ngOnDestroy` / `DestroyRef` |

- 🚩 El error clásico en React: olvidar el arreglo de dependencias de `useEffect` → **bucle infinito** de peticiones
- 💡 En los tres, prefiera **valores derivados** (`computed`, cálculo en render) antes que efectos que copian datos

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00058_.png);
}
</style>
# Enrutamiento
## Contenidos
- SPA y la History API
- Definir rutas en los tres
- Parámetros y navegación
- Guardas y carga diferida

---

## ¿Qué es el enrutamiento en una SPA?

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.85rem;">
<div>

```text
Sitio tradicional (MPA)
  /productos   → petición → HTML nuevo completo
  /productos/7 → petición → HTML nuevo completo

SPA (Single Page Application)
  index.html se descarga UNA vez
  /productos   → el router muestra <ListaProductos>
  /productos/7 → el router muestra <DetalleProducto id=7>
                 (solo se piden datos JSON a la API)
```

</div>
<div>

- El *router* escucha la URL (**History API**: `pushState`, `popstate`) y decide **qué componente** dibujar
- La URL sigue siendo la **fuente de verdad**: se puede compartir, recargar y usar "atrás"
- ⚠️ En producción el servidor debe responder `index.html` para **cualquier** ruta (si no, `/productos/7` da **404** al recargar)

</div>
</split-slide>

| | ⚛️ React | 💚 Vue | 🅰️ Angular |
|:--|:--|:--|:--|
| Paquete | `react-router` (v7) | `vue-router` (v5) | `@angular/router` |
| Dónde se dibuja | `<Outlet />` | `<RouterView />` | `<router-outlet />` |
| Enlace | `<Link to="/x">` | `<RouterLink to="/x">` | `<a routerLink="/x">` |

---

## Definir rutas

<steps>
<step>

### ⚛️ React Router 7

```jsx
import { createBrowserRouter, RouterProvider } from 'react-router';

const router = createBrowserRouter([
  {
    path: '/', element: <Layout />,              // Layout contiene <Outlet />
    children: [
      { index: true, element: <Inicio /> },
      { path: 'productos', element: <ListaProductos /> },
      { path: 'productos/:id', element: <DetalleProducto /> },
      { path: '*', element: <NoEncontrado /> },
    ],
  },
]);

createRoot(document.getElementById('root')).render(<RouterProvider router={router} />);
```

</step>
<step>

### 💚 Vue Router 5

```js
import { createRouter, createWebHistory } from 'vue-router';

export const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', component: Inicio },
    { path: '/productos', component: ListaProductos },
    { path: '/productos/:id', component: DetalleProducto, props: true },
    { path: '/:pathMatch(.*)*', component: NoEncontrado },
  ],
});

createApp(App).use(router).mount('#app');      // App.vue contiene <RouterView />
```

- Novedad de la v5: rutas **por archivos** (`src/pages/productos/[id].vue`) integradas vía `vue-router/vite`

</step>
<step>

### 🅰️ Angular Router

```ts
// app.routes.ts
export const routes: Routes = [
  { path: '', component: Inicio },
  { path: 'productos', component: ListaProductos },
  { path: 'productos/:id', component: DetalleProducto },
  { path: '**', component: NoEncontrado },
];

// app.config.ts
export const appConfig: ApplicationConfig = {
  providers: [provideRouter(routes, withComponentInputBinding())],
};
```

- `withComponentInputBinding()` pasa `:id` directamente a un `input()` del componente

</step>
</steps>

---

## Parámetros y navegación programática

<steps>
<step>

### Leer `:id` de la URL

| ⚛️ React | 💚 Vue | 🅰️ Angular |
|:--|:--|:--|
| `const { id } = useParams();` | `const route = useRoute();` <br> `route.params.id` <br> (o `props: true` → `defineProps`) | `id = input.required<string>();` <br> (con `withComponentInputBinding`) |
| `useSearchParams()` para `?q=` | `route.query.q` | `queryParams` como `input()` |

</step>
<step>

### Navegar desde código (después de guardar un formulario)

```js
// ⚛️ React
const navigate = useNavigate();
navigate(`/productos/${nuevo.id}`);

// 💚 Vue
const router = useRouter();
router.push({ path: `/productos/${nuevo.id}` });

// 🅰️ Angular
private router = inject(Router);
this.router.navigate(['/productos', nuevo.id]);
```

</step>
</steps>

---

## Guardas de ruta: ¿puede entrar?

<steps>
<step>

### 🅰️ Angular — guardas funcionales

```ts
export const authGuard: CanActivateFn = () => {
  const auth = inject(AuthService);
  return auth.autenticado() ? true : inject(Router).createUrlTree(['/login']);
};

{ path: 'pedidos', component: Pedidos, canActivate: [authGuard] }
```

</step>
<step>

### 💚 Vue — guarda global con `meta`

```js
{ path: '/pedidos', component: Pedidos, meta: { requiereAuth: true } }

router.beforeEach((to) => {
  const auth = useAuthStore();
  if (to.meta.requiereAuth && !auth.autenticado) return { path: '/login' };
});
```

</step>
<step>

### ⚛️ React — componente protector (o `loader` con `redirect`)

```jsx
function RutaProtegida() {
  const { usuario } = useAuth();
  return usuario ? <Outlet /> : <Navigate to="/login" replace />;
}

{ element: <RutaProtegida />, children: [{ path: 'pedidos', element: <Pedidos /> }] }
```

- 🚩 Una guarda **no es seguridad**: solo mejora la experiencia. Quien protege los datos es la **API** (401 / 403)

</step>
</steps>

---

## Carga diferida (*lazy loading*)

- Sin carga diferida, el usuario descarga **toda** la app aunque solo visite el inicio

```js
// ⚛️ React
{ path: 'admin', lazy: () => import('./admin/Admin') }

// 💚 Vue
{ path: '/admin', component: () => import('./admin/Admin.vue') }

// 🅰️ Angular — un componente o un archivo de rutas completo
{ path: 'admin', loadComponent: () => import('./admin/admin').then(m => m.Admin) }
{ path: 'productos', loadChildren: () => import('./features/productos/productos.routes') }
```

- El empaquetador (Vite / esbuild) crea un **chunk** separado por cada `import()` dinámico
- 🔗 Medible con el análisis de bundle del tema *Rendimiento web*

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00059_.png);
}
</style>
# Pipes y directivas
## Contenidos
- Directivas: estructurales y de atributo
- Directivas personalizadas
- Pipes: transformar datos en la vista
- Equivalentes en Vue y React

---

## Directivas: extender el HTML

- Una **directiva** es una instrucción en la plantilla que agrega comportamiento a un elemento

<div class="grid">
<div>

### 🧱 Estructurales
Agregan o quitan elementos del DOM
- 🅰️ `@if`, `@for`, `@switch` (antes `*ngIf`, `*ngFor`)
- 💚 `v-if`, `v-for`, `v-show`
</div>
<div>

### 🎨 De atributo
Cambian apariencia o comportamiento de un elemento existente
- 🅰️ `[class.x]`, `[style.x]`, `ngModel`, `routerLink`
- 💚 `v-bind`, `v-model`, `v-on`
</div>
<div>

### 🧩 Componentes
En Angular, un componente **es** una directiva con plantilla
</div>
<div>

### ⚛️ ¿Y React?
**No tiene directivas**: usa JavaScript (`&&`, `map`) y *hooks* personalizados para reutilizar comportamiento
</div>
</div>

---

## Directiva personalizada

<steps>
<step>

### 🅰️ Angular — `@Directive`

```ts
@Directive({
  selector: '[appResaltar]',
  host: {
    '(mouseenter)': 'activo.set(true)',
    '(mouseleave)': 'activo.set(false)',
    '[style.backgroundColor]': 'activo() ? color() : null',
  },
})
export class Resaltar {
  color = input('gold', { alias: 'appResaltar' });
  activo = signal(false);
}
```

```html
<p appResaltar="lightblue">Pase el mouse por aquí</p>
```

</step>
<step>

### 💚 Vue — objeto con *hooks* de directiva

```ts
<script setup>
// Cualquier variable vNombre se registra como directiva v-nombre
const vFoco = { mounted: (el) => el.focus() };
const vResaltar = {
  mounted(el, { value = 'gold' }) {
    el.addEventListener('mouseenter', () => (el.style.background = value));
    el.addEventListener('mouseleave', () => (el.style.background = ''));
  },
};
</script>

<template>
  <input v-foco placeholder="Buscar…" />
  <p v-resaltar="'lightblue'">Pase el mouse por aquí</p>
</template>
```

</step>
<step>

### ⚛️ React — un *hook* personalizado

```jsx
function useResaltar(color = 'gold') {
  const [activo, setActivo] = useState(false);
  return {
    onMouseEnter: () => setActivo(true),
    onMouseLeave: () => setActivo(false),
    style: { background: activo ? color : undefined },
  };
}

function Parrafo() {
  const resaltar = useResaltar('lightblue');
  return <p {...resaltar}>Pase el mouse por aquí</p>;
}
```

- Mismo resultado: la reutilización se hace con **funciones**, no con atributos mágicos

</step>
</steps>

---

## Pipes: transformar datos en la vista

- Un **pipe** toma un valor y devuelve otro **solo para mostrarlo**, sin cambiar el dato original

```html
<!-- 🅰️ Angular: pipes integrados -->
<p>{{ producto().precio | currency:'CRC':'symbol-narrow':'1.0-0' }}</p>   <!-- ₡12 500 -->
<p>{{ pedido().fecha | date:'dd/MM/yyyy' }}</p>                          <!-- 21/10/2026 -->
<p>{{ cliente().nombre | uppercase }}</p>
<p>{{ 0.135 | percent:'1.1-1' }}</p>                                     <!-- 13,5 % -->
<pre>{{ respuesta() | json }}</pre>
```

| Pipe | Uso |
|:--|:--|
| `date`, `currency`, `number`, `percent` | Formatos con la configuración regional (`LOCALE_ID`) |
| `uppercase`, `lowercase`, `titlecase`, `slice` | Texto y arreglos |
| `async` | Se suscribe a un **Observable / Promise** y muestra el último valor |
| `json`, `keyvalue` | Depuración y recorrer objetos |

---

## Pipe personalizado

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.85rem;">
<div>

```ts
// shared/pipes/colones.ts
@Pipe({ name: 'colones' })
export class ColonesPipe implements PipeTransform {
  private fmt = new Intl.NumberFormat('es-CR', {
    style: 'currency',
    currency: 'CRC',
    maximumFractionDigits: 0,
  });

  transform(valor: number | null): string {
    return valor == null ? '—' : this.fmt.format(valor);
  }
}
```

```html
<!-- Importar en el componente: imports: [ColonesPipe] -->
<td>{{ item.subtotal | colones }}</td>
```

</div>
<div>

- Los pipes son **puros** por defecto: solo se recalculan si cambia la **entrada**, no en cada ciclo
- ⚠️ No ponga lógica de negocio en un pipe: solo **presentación**

<br>

### 💚 Vue y ⚛️ React
- Vue 2 tenía *filters* (`{{ x | moneda }}`); **Vue 3 los eliminó**
- En ambos se usa una **función** o un valor **computado**:

```js
const colones = (v) => fmt.format(v);
// Vue:   {{ colones(item.subtotal) }}
// React: {colones(item.subtotal)}
```

</div>
</split-slide>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00064_.png);
}
</style>
# Decoradores y objetos observables
## Contenidos
- Decoradores en TypeScript y Angular
- Inyección de dependencias
- El patrón observador y RxJS
- Observables, promesas y signals

---

## Decoradores

- Un **decorador** (`@nombre(...)`) es una función que se aplica a una clase, método o propiedad para **agregarle metadatos o comportamiento** sin cambiar su código. Existe en TypeScript y se estandariza en JavaScript (TC39)

<div class="grid">
<div>

### 🅰️ Angular vive de ellos
`@Component`, `@Directive`, `@Pipe`, `@Injectable`, `@Service` (v22): le dicen al compilador **qué es** cada clase
</div>
<div>

### 💚 Vue
No los usa. El viejo `vue-class-component` está **obsoleto**; hoy todo son funciones (`defineProps`, `ref`)
</div>
<div>

### ⚛️ React
No los usa. Los componentes son **funciones**; los "decoradores" equivalen a *higher-order components* o *hooks*
</div>
<div>

### 🔗 Back-end
Los mismos que vieron en **NestJS** (`@Controller`, `@Get`) y los **atributos** de Laravel 13 / C#
</div>
</div>

- 💡 Angular está moviendo los decoradores de propiedades a **funciones**: `@Input()` → `input()`, `@Output()` → `output()`, `@ViewChild()` → `viewChild()`

---

## Decoradores de clase + inyección de dependencias

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.85rem;">
<div>

```ts
// Angular ≤ 21
@Injectable({ providedIn: 'root' })
export class ProductosService { /* ... */ }

// Angular 22: atajo equivalente
@Service()
export class ProductosService {
  private http = inject(HttpClient);

  listar() {
    return this.http.get<Producto[]>('/api/productos');
  }
}

// En cualquier componente
export class ListaProductos {
  private productos = inject(ProductosService);
}
```

</div>
<div>

- `providedIn: 'root'` → **una sola instancia** (singleton) para toda la app
- `inject()` pide la dependencia al **inyector**: el componente no hace `new`
- Ventaja: en pruebas se **reemplaza** el servicio por un falso sin tocar el componente

<br>

| ⚛️ React | 💚 Vue |
|:--|:--|
| `createContext` + `useContext` | `provide` / `inject` |

- Misma idea, sin decoradores

</div>
</split-slide>

---

## El patrón observador

```text
        Productor (Observable)                      Consumidores (Observers)
   ──●─────●─────●──────────●───────|──►           subscribe({
     "a"   "ab"  "abc"     "abcd"   fin              next:  v => ...,   ← cada valor
                                                     error: e => ...,   ← si falla
   tiempo ──────────────────────────────►            complete: () => ...← al terminar
                                                   })
```

- Un **Observable** representa un **flujo de valores en el tiempo**: teclas, clics, respuestas HTTP, mensajes de un WebSocket
- Es **perezoso**: no hace nada hasta que alguien se **suscribe**
- Se puede **cancelar** (`unsubscribe`) — una promesa no
- Angular usa la librería **RxJS** para esto (`HttpClient`, eventos del router, formularios reactivos)

---

## RxJS: operadores

- Los operadores se encadenan con `pipe()` y transforman el flujo — como `map` / `filter` de los arreglos, pero **en el tiempo**

```ts
// Buscador que no satura la API
termino$ = new Subject<string>();

resultados$ = this.termino$.pipe(
  map(t => t.trim()),
  filter(t => t.length >= 3),          // ignora búsquedas cortas
  debounceTime(300),                    // espera a que deje de teclear
  distinctUntilChanged(),               // no repite la misma búsqueda
  switchMap(t => this.api.buscar(t)),   // cancela la petición anterior si llega otra
  catchError(() => of([])),
);
```

```html
<input (input)="termino$.next($any($event.target).value)" />
@for (p of resultados$ | async; track p.id) { <li>{{ p.nombre }}</li> }
```

- `| async` se suscribe y **se desuscribe solo** al destruir el componente

---

## Observables, promesas y signals

| | Promise | Observable (RxJS) | Signal |
|:--|:--|:--|:--|
| Valores | **Uno** | **Muchos** en el tiempo | **Uno actual** (siempre tiene valor) |
| Ejecución | Inmediata | Perezosa (al suscribirse) | Síncrona al leer |
| Cancelable | No (`AbortController` aparte) | Sí (`unsubscribe`) | No aplica |
| Ideal para | Una petición | Eventos, *streams*, WebSockets | **Estado** de la interfaz |
| Lectura | `await p` | `subscribe()` / `\| async` | `contador()` |

- 🅰️ Angular moderno: **signals para el estado**, RxJS para **eventos asíncronos complejos**. Se convierten con `toSignal()` y `toObservable()`
- 💚 Vue: `ref` y `computed` **son** signals desde siempre; el `watch` cumple el papel del observador
- ⚛️ React: no tiene signals; `useState` provoca un **re-render** del componente

---

## Signals en los tres: la misma idea

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.85rem;">
<div>

```ts
// 🅰️ Angular
const precio = signal(1000);
const cantidad = signal(2);
const total = computed(() => precio() * cantidad());
effect(() => console.log('Total', total()));

cantidad.set(3);          // total → 3000, el effect se ejecuta
```

```js
// 💚 Vue
const precio = ref(1000);
const cantidad = ref(2);
const total = computed(() => precio.value * cantidad.value);
watchEffect(() => console.log('Total', total.value));

cantidad.value = 3;
```

</div>
<div>

```jsx
// ⚛️ React
const [precio] = useState(1000);
const [cantidad, setCantidad] = useState(2);
const total = precio * cantidad;   // se recalcula en cada render
useEffect(() => console.log('Total', total), [total]);

setCantidad(3);   // vuelve a ejecutar TODO el componente
```

- **Reactividad fina** (Angular, Vue): el sistema sabe exactamente **quién depende de quién**
- **Re-render** (React): se recalcula todo y se compara; el **React Compiler** memoriza automáticamente para evitar trabajo extra

</div>
</split-slide>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00066_.png);
}
</style>
# Consumo de servicios web
## Contenidos
- Los tres estados de una petición
- HTTP en Angular, Vue y React
- Interceptores y autenticación
- CORS, proxies y variables de entorno

---

## Toda petición tiene tres estados

<split-slide style="--left: 50%; --right: 50%;">
<div>

```text
            ┌──────────────┐
  inicio ─► │  cargando…   │
            └──────┬───────┘
          ┌────────┴────────┐
          ▼                 ▼
   ┌────────────┐    ┌────────────┐
   │   datos    │    │   error    │
   │ (200/201)  │    │ (4xx/5xx)  │
   └────────────┘    └────────────┘
```

</div>
<div>

- La interfaz **debe** mostrar los tres: un *spinner*, el resultado o un mensaje útil
- 🚩 El error más común: solo programar el "camino feliz"
- Errores esperables de la API del curso:
  - **401** → redirigir a *login*
  - **403** → "no tiene permiso"
  - **422** → mostrar errores **por campo** del formulario
  - **409** → mensaje de regla de negocio

</div>
</split-slide>

- 🔗 Repase `fetch`, `async/await` y `AbortController` en el tema *Asincronía*

---

## 🅰️ Angular: `HttpClient` y `httpResource`

<split-slide style="--left: 52%; --right: 48%; --font-size: 0.8rem;">
<div>

```ts
// app.config.ts
providers: [provideHttpClient(withInterceptors([authInterceptor]))]

// productos.service.ts — Observables
@Service()
export class ProductosService {
  private http = inject(HttpClient);
  private url = `${environment.apiUrl}/productos`;

  listar()          { return this.http.get<Producto[]>(this.url); }
  obtener(id: number) { return this.http.get<Producto>(`${this.url}/${id}`); }
  crear(p: NuevoProducto) { return this.http.post<Producto>(this.url, p); }
}
```

- Desde v22 `HttpClient` usa **`fetch`** por debajo (`withFetch()` queda obsoleto)

</div>
<div>

```ts
// Componente — httpResource (estable en v22)
export class DetalleProducto {
  id = input.required<number>();

  producto = httpResource<Producto>(
    () => `${environment.apiUrl}/productos/${this.id()}`
  );
}
```

```html
@if (producto.isLoading()) { <app-spinner /> }
@else if (producto.error()) { <p>No se pudo cargar</p> }
@else if (producto.hasValue()) {
  <h2>{{ producto.value().nombre }}</h2>
}
```

- Si cambia `id()`, **vuelve a pedir** y cancela la anterior

</div>
</split-slide>

---

## 💚 Vue: un *composable* con `fetch`

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.8rem;">
<div>

```js
// composables/useFetch.js
import { ref, toValue, watchEffect } from 'vue';

export function useFetch(url) {
  const datos = ref(null);
  const error = ref(null);
  const cargando = ref(false);

  watchEffect(async (onCleanup) => {
    const ctrl = new AbortController();
    onCleanup(() => ctrl.abort());        // cancela si cambia la URL
    cargando.value = true; error.value = null;
    try {
      const r = await fetch(toValue(url), { signal: ctrl.signal });
      if (!r.ok) throw new Error(`HTTP ${r.status}`);
      datos.value = await r.json();
    } catch (e) {
      if (e.name !== 'AbortError') error.value = e;
    } finally { cargando.value = false; }
  });

  return { datos, error, cargando };
}
```

</div>
<div>

```vue
<script setup>
const props = defineProps(['id']);
const { datos, error, cargando } =
  useFetch(() => `${API}/productos/${props.id}`);
</script>

<template>
  <Spinner v-if="cargando" />
  <p v-else-if="error">No se pudo cargar</p>
  <h2 v-else-if="datos">{{ datos.nombre }}</h2>
</template>
```

- Un **composable** es la forma de reutilizar lógica con estado en Vue
- Hay versiones listas: `useFetch` de **VueUse**, **TanStack Query** para Vue

</div>
</split-slide>

---

## ⚛️ React: `useEffect`… o mejor TanStack Query

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.78rem;">
<div>

```jsx
// A mano: hay que cuidar carreras y limpieza
function DetalleProducto({ id }) {
  const [datos, setDatos] = useState(null);
  const [error, setError] = useState(null);

  useEffect(() => {
    const ctrl = new AbortController();
    fetch(`${API}/productos/${id}`, { signal: ctrl.signal })
      .then(r => r.ok ? r.json() : Promise.reject(r.status))
      .then(setDatos)
      .catch(e => e.name !== 'AbortError' && setError(e));
    return () => ctrl.abort();            // limpieza
  }, [id]);                               // ⚠️ dependencias

  if (error) return <p>No se pudo cargar</p>;
  if (!datos) return <Spinner />;
  return <h2>{datos.nombre}</h2>;
}
```

</div>
<div>

```jsx
// Con TanStack Query: caché, reintentos, recarga
function DetalleProducto({ id }) {
  const { data, isPending, error } = useQuery({
    queryKey: ['producto', id],
    queryFn: () =>
      fetch(`${API}/productos/${id}`).then(r => r.json()),
  });

  if (isPending) return <Spinner />;
  if (error) return <p>No se pudo cargar</p>;
  return <h2>{data.nombre}</h2>;
}
```

- La documentación de React recomienda **no** hacer *fetching* a mano con `useEffect` en apps reales: use una librería o un *framework*
- React 19 agrega `use(promesa)` + `<Suspense>` para leer datos asíncronos

</div>
</split-slide>

---

## Interceptores: el token en cada petición

- La API del tema de back-end usa **Sanctum** con `Authorization: Bearer <token>`. No lo repita en cada llamada: **intercéptelo**

<steps>
<step>

### 🅰️ Angular — `HttpInterceptorFn`

```ts
export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const token = inject(AuthService).token();
  const conToken = token ? req.clone({ setHeaders: { Authorization: `Bearer ${token}` } }) : req;

  return next(conToken).pipe(
    catchError((err: HttpErrorResponse) => {
      if (err.status === 401) inject(Router).navigate(['/login']);
      return throwError(() => err);
    }),
  );
};
```

</step>
<step>

### 💚 ⚛️ Vue y React — un cliente HTTP propio

```js
// api/cliente.js — se usa en lugar de fetch directo
export async function api(ruta, opciones = {}) {
  const token = localStorage.getItem('token');
  const r = await fetch(`${import.meta.env.VITE_API_URL}${ruta}`, {
    ...opciones,
    headers: {
      'Content-Type': 'application/json',
      Accept: 'application/json',
      ...(token && { Authorization: `Bearer ${token}` }),
      ...opciones.headers,
    },
  });
  if (r.status === 401) window.location.assign('/login');
  if (!r.ok) throw await r.json();          // { message, errors } de Laravel
  return r.status === 204 ? null : r.json();
}
// Con axios: axios.interceptors.request.use(...)
```

</step>
</steps>

---

## CORS, proxy y variables de entorno

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.8rem;">
<div>

### 🌐 CORS en desarrollo
- `localhost:5173` → `localhost:8000` son **orígenes distintos**: el navegador bloquea si la API no responde con `Access-Control-Allow-Origin`
- Solución 1: configurar CORS en Laravel (`config/cors.php`)
- Solución 2: un **proxy** del servidor de desarrollo

```js
// vite.config.js (React y Vue)
server: {
  proxy: { '/api': 'http://localhost:8000' },
}
```

```json
// proxy.conf.json (Angular) → ng serve --proxy-config proxy.conf.json
{ "/api": { "target": "http://localhost:8000", "secure": false } }
```

</div>
<div>

### 🔧 Variables de entorno

```bash
# .env.development  (Vite: solo se exponen las que empiezan con VITE_)
VITE_API_URL=http://localhost:8000/api
```

```js
const API = import.meta.env.VITE_API_URL;
```

```ts
// Angular: ng generate environments
// src/environments/environment.development.ts
export const environment = {
  apiUrl: 'http://localhost:8000/api',
};
```

- ⚠️ Todo lo que llega al navegador es **público**: nunca ponga secretos ni claves privadas en el front-end

</div>
</split-slide>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00069_.png);
}
</style>
# Manejo del estado
## Contenidos
- Tipos de estado
- El problema del *prop drilling*
- Estado global en los tres
- Estado del servidor

---

## No todo el estado es igual

<div class="grid">
<div>

### 🏠 Local
Un *input*, si un menú está abierto. Vive **en el componente**
</div>
<div>

### 👪 Compartido
Lo usan unos pocos componentes cercanos. Se **sube al padre** común
</div>
<div>

### 🌍 Global
Usuario autenticado, carrito, tema. Lo usa **media aplicación** → *store*
</div>
<div>

### ☁️ Del servidor
Productos, pedidos. Es una **copia** de la API → caché (TanStack Query, `httpResource`)
</div>
<div>

### 🔗 De la URL
Filtros, página, búsqueda. Va en la **ruta** (`?q=…&page=2`) para poder compartirse
</div>
<div>

### 📝 De formulario
Valores, errores, *touched*. Lo maneja la librería de formularios
</div>
</div>

- 💡 Regla de oro: ponga cada dato **lo más cerca posible** de donde se usa. Global solo lo que realmente es global

---

## El problema: *prop drilling*

<split-slide style="--left: 50%; --right: 50%;">
<div>

```text
App            (usuario) ─┐
 └ Layout      (usuario)  │  solo lo
    └ Encabezado(usuario) │  pasan…
       └ Menu  (usuario)  │
          └ Avatar ◄──────┘  …el único
                (usa usuario)  que lo usa
```

</div>
<div>

- Pasar un dato por **cinco niveles** de componentes que no lo necesitan
- Cada nivel intermedio queda **acoplado** a algo que no le importa
- Soluciones, de menor a mayor:
  1. **Contexto / inyección** (`useContext`, `provide/inject`, servicios)
  2. Un **store** global con acciones y valores derivados

</div>
</split-slide>

| | ⚛️ React | 💚 Vue | 🅰️ Angular |
|:--|:--|:--|:--|
| Sin librería | `useState`, `useReducer`, **Context** | `ref`, `reactive`, `provide/inject` | **Servicio + signals** |
| Store recomendado | **Zustand**, Redux Toolkit | **Pinia** (oficial) | **NgRx SignalStore**, NgRx Store |
| Estado del servidor | TanStack Query | TanStack Query, Pinia Colada | `httpResource`, TanStack Query |

---

## El mismo carrito en los tres

<steps>
<step>

### ⚛️ React — Zustand

```jsx
import { create } from 'zustand';

export const useCarrito = create((set) => ({
  items: [],
  agregar: (p) => set((s) => ({ items: [...s.items, p] })),      // inmutable
  quitar: (id) => set((s) => ({ items: s.items.filter(i => i.id !== id) })),
  vaciar: () => set({ items: [] }),
}));

// En cualquier componente, sin Provider
const total = useCarrito((s) => s.items.reduce((t, i) => t + i.precio, 0));
const agregar = useCarrito((s) => s.agregar);
```

- En React el estado se **reemplaza**, no se muta: `[...items, p]`, nunca `items.push(p)`

</step>
<step>

### 💚 Vue — Pinia (*setup store*)

```js
import { defineStore } from 'pinia';
import { ref, computed } from 'vue';

export const useCarritoStore = defineStore('carrito', () => {
  const items = ref([]);
  const total = computed(() => items.value.reduce((t, i) => t + i.precio, 0));

  function agregar(p) { items.value.push(p); }                     // mutación reactiva
  function quitar(id) { items.value = items.value.filter(i => i.id !== id); }

  return { items, total, agregar, quitar };
});

// En el componente
const carrito = useCarritoStore();   // carrito.total, carrito.agregar(p)
```

- Pinia se integra con las **Vue DevTools**: historial de cambios y "viaje en el tiempo"

</step>
<step>

### 🅰️ Angular — servicio con signals

```ts
@Service()
export class CarritoStore {
  private readonly _items = signal<Producto[]>([]);

  readonly items = this._items.asReadonly();                       // solo lectura afuera
  readonly total = computed(() => this._items().reduce((t, i) => t + i.precio, 0));

  agregar(p: Producto) { this._items.update(items => [...items, p]); }
  quitar(id: number)   { this._items.update(items => items.filter(i => i.id !== id)); }
}

// En el componente
carrito = inject(CarritoStore);      // {{ carrito.total() | colones }}
```

- Para apps grandes, **NgRx SignalStore** formaliza este patrón (`signalStore`, `withState`, `withMethods`)

</step>
</steps>

---

## Persistir el estado

- El estado en memoria **se pierde al recargar**. Para carrito o preferencias:

```js
// ⚛️ Zustand
create(persist((set) => ({ /* ... */ }), { name: 'carrito' }));

// 💚 Pinia + pinia-plugin-persistedstate
defineStore('carrito', () => { /* ... */ }, { persist: true });

// 🅰️ Angular: un effect que sincroniza
effect(() => localStorage.setItem('carrito', JSON.stringify(this._items())));
```

<div class="grid">
<div>

### ✅ Sí persistir
Carrito, tema claro/oscuro, idioma, filtros recientes
</div>
<div>

### 🚩 No persistir
Datos de la API (se vuelven **viejos**), información sensible, contraseñas
</div>
</div>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00071_.png);
}
</style>
# Pruebas de software
## Contenidos
- Qué probar en el front-end
- Herramientas: Vitest, Testing Library, TestBed
- Pruebas de componentes comparadas
- Simular la API y pruebas E2E

---

## La pirámide en el front-end

<split-slide style="--left: 45%; --right: 55%;">
<div>

```text
                ▲
               ╱ ╲        E2E (Playwright)
              ╱   ╲       pocos · lentos · reales
             ╱─────╲
            ╱       ╲     Componentes / integración
           ╱         ╲    Testing Library, TestBed
          ╱───────────╲
         ╱             ╲  Unitarias
        ╱               ╲ pipes, funciones, stores
       ╱─────────────────╲
```

</div>
<div>

- **Unitarias**: funciones puras, pipes, *stores*, validadores. Rápidas y muchas
- **De componente**: se monta el componente y se interactúa como **una persona usuaria** (clic, escribir, leer texto)
- **E2E**: un navegador real recorre un flujo completo contra la app levantada

<br>

- 💡 Pruebe **comportamiento**, no implementación: "al hacer clic en *Agregar* el contador muestra 1", no "se llamó a `setCuenta`"

</div>
</split-slide>

---

## Herramientas

| | ⚛️ React | 💚 Vue | 🅰️ Angular |
|:--|:--|:--|:--|
| Ejecutor | **Vitest** | **Vitest** | **Vitest** (por defecto desde v21; antes Karma + Jasmine) |
| Montar componentes | **React Testing Library** | **Vue Test Utils** o Testing Library | **TestBed** (+ Testing Library opcional) |
| Entorno DOM | `jsdom` / `happy-dom` | `jsdom` / `happy-dom` | `jsdom` o navegador real |
| Simular HTTP | **MSW** | **MSW** | `HttpTestingController` o MSW |
| E2E | Playwright / Cypress | Playwright / Cypress | Playwright / Cypress |
| Comando | `npx vitest` | `npm run test:unit` | `ng test` |

- 🔗 Vitest ya lo usaron en el tema de JavaScript: `describe`, `it`, `expect` funcionan igual en los tres
- Migrar un proyecto Angular viejo: `ng g @schematics/angular:refactor-jasmine-vitest`

---

## Probar el contador

<steps>
<step>

### ⚛️ React Testing Library

```jsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { Contador } from './Contador';

it('incrementa al hacer clic', async () => {
  render(<Contador inicial={5} />);

  await userEvent.click(screen.getByRole('button', { name: '+1' }));

  expect(screen.getByText(/6 \(doble: 12\)/)).toBeInTheDocument();
});
```

- Busca por **rol y texto accesible**, como lo haría una persona (o un lector de pantalla)

</step>
<step>

### 💚 Vue Test Utils

```js
import { mount } from '@vue/test-utils';
import Contador from './Contador.vue';

it('incrementa al hacer clic', async () => {
  const wrapper = mount(Contador, { props: { inicial: 5 } });

  await wrapper.get('button').trigger('click');   // await: espera el re-render

  expect(wrapper.text()).toContain('6 (doble: 12)');
});
```

</step>
<step>

### 🅰️ Angular TestBed + Vitest

```ts
import { TestBed } from '@angular/core/testing';
import { Contador } from './contador';

it('incrementa al hacer clic', async () => {
  const fixture = TestBed.createComponent(Contador);
  fixture.componentRef.setInput('inicial', 5);
  await fixture.whenStable();

  fixture.nativeElement.querySelector('button').click();
  await fixture.whenStable();                      // zoneless: esperar la actualización

  expect(fixture.nativeElement.textContent).toContain('6 (doble: 12)');
});
```

</step>
</steps>

---

## Probar código que llama a la API

<steps>
<step>

### 🅰️ Angular — `HttpTestingController`

```ts
beforeEach(() => {
  TestBed.configureTestingModule({
    providers: [provideHttpClient(), provideHttpClientTesting()],
  });
});

it('lista productos', () => {
  const service = TestBed.inject(ProductosService);
  const http = TestBed.inject(HttpTestingController);

  service.listar().subscribe(lista => expect(lista).toHaveLength(2));

  http.expectOne('/api/productos').flush([{ id: 1 }, { id: 2 }]);   // respuesta falsa
  http.verify();                                                     // no quedaron pendientes
});
```

</step>
<step>

### ⚛️ 💚 MSW — Mock Service Worker (sirve en los tres)

```js
import { http, HttpResponse } from 'msw';
import { setupServer } from 'msw/node';

const server = setupServer(
  http.get('*/api/productos', () => HttpResponse.json([{ id: 1, nombre: 'Café' }])),
  http.post('*/api/productos', () =>
    HttpResponse.json({ errors: { nombre: ['Requerido'] } }, { status: 422 })),
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());
```

- Intercepta a nivel de **red**: el componente usa su `fetch` real. Ideal para probar los estados **422**, **401**, **500**

</step>
</steps>

---

## Pruebas E2E con Playwright

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.85rem;">
<div>

```js
// e2e/carrito.spec.ts
import { test, expect } from '@playwright/test';

test('agrega un producto al carrito', async ({ page }) => {
  await page.goto('/productos');

  await page.getByRole('button', { name: 'Agregar' }).first().click();

  await expect(page.getByTestId('contador-carrito')).toHaveText('1');
});
```

```bash
npm init playwright@latest
npx playwright test --ui
```

</div>
<div>

- Abre un navegador **real** (Chromium, Firefox, WebKit) y ejecuta el flujo de punta a punta
- Es **independiente del framework**: la misma prueba sirve si migran de Vue a React
- Más lentas y frágiles: reserve E2E para los **flujos críticos** (login, compra, registro)

<br>

### ¿Qué probar como mínimo?
- Un *store* / servicio (unitaria)
- Un formulario con error **422** (componente + MSW)
- El flujo principal (E2E)

</div>
</split-slide>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00072_.png);
}
</style>
# Cierre
## Contenidos
- Resumen comparativo
- Antipatrones frecuentes
- Actividad de cierre

---

## Resumen: el mismo concepto, tres sintaxis

| Tema | ⚛️ React | 💚 Vue | 🅰️ Angular |
|:--|:--|:--|:--|
| Componente | Función + JSX | SFC `.vue` | Clase + `@Component` |
| Entradas / salidas | *props* / *callbacks* | `defineProps` / `defineEmits` | `input()` / `output()` |
| Estado reactivo | `useState` | `ref` / `computed` | `signal` / `computed` |
| Efectos | `useEffect` | `watch` / `watchEffect` | `effect` |
| Reutilizar lógica | *Custom hooks* | *Composables* | Servicios + DI |
| Directivas / pipes | — (JavaScript) | `v-*` / funciones | `@Directive` / `@Pipe` |
| Router | React Router | Vue Router | Angular Router |
| HTTP | `fetch` / TanStack Query | `fetch` / composable | `HttpClient` / `httpResource` |
| Estado global | Zustand / Redux | Pinia | Servicio + signals / NgRx |
| Pruebas | Vitest + RTL | Vitest + VTU | Vitest + TestBed |

---

## Antipatrones frecuentes

<div class="grid">
<div>

### 🚩 Componente "Dios"
Un `App` de 800 líneas que pide datos, valida, dibuja y navega
</div>
<div>

### 🚩 Todo es global
Meter en el *store* el valor de cada *input* "por si acaso"
</div>
<div>

### 🚩 `fetch` en cada componente
URLs duplicadas, sin token, sin manejo de errores: use **un** cliente / servicio
</div>
<div>

### 🚩 Solo el camino feliz
Sin *spinner*, sin mensaje de error, sin estado vacío
</div>
<div>

### 🚩 Confiar en el front-end
Validar o "proteger" solo en el cliente. La API **siempre** valida y autoriza
</div>
<div>

### 🚩 Mutar *props* o estado de React
`props.items.push(x)` o `estado.push(x)`: la vista no se entera
</div>
</div>

---

## Referencias

- React. [Documentación](https://react.dev/) · [Pensar en React](https://es.react.dev/learn/thinking-in-react) · [Quizás no necesites un efecto](https://es.react.dev/learn/you-might-not-need-an-effect) · [Notas de versión](https://github.com/facebook/react/releases) · [React Compiler](https://react.dev/learn/react-compiler)
- Vue. [Documentación](https://vuejs.org/guide/introduction) · [Composition API](https://vuejs.org/guide/extras/composition-api-faq) · [Composables](https://vuejs.org/guide/reusability/composables) · [Directivas personalizadas](https://vuejs.org/guide/reusability/custom-directives) · [Vue Router](https://router.vuejs.org/) · [Migración v4 → v5](https://router.vuejs.org/guide/migration/v4-to-v5) · [Pinia](https://pinia.vuejs.org/)
- Angular. [angular.dev](https://angular.dev/) · [Signals](https://angular.dev/guide/signals) · [Router](https://angular.dev/guide/routing) · [Pipes](https://angular.dev/guide/templates/pipes) · [Directivas](https://angular.dev/guide/directives) · [HttpClient](https://angular.dev/guide/http) · [httpResource](https://angular.dev/guide/http/http-resource) · [Pruebas](https://angular.dev/guide/testing) · [Calendario de versiones](https://angular.dev/reference/releases)
- Ninja Squad. [What's new in Angular 22.0](https://blog.ninja-squad.com/2026/06/03/what-is-new-angular-22.0) · InfoQ. [Angular v22 Released](https://www.infoq.com/news/2026/08/angular-v22-released/) · [Vue Router 5](https://infoq.com/news/2026/03/vue-router-5)
- React Router. [Documentación](https://reactrouter.com/) · TanStack. [Query](https://tanstack.com/query) · Zustand. [Documentación](https://zustand.docs.pmnd.rs/) · NgRx. [SignalStore](https://ngrx.io/guide/signals) · RxJS. [Documentación](https://rxjs.dev/)
- Pruebas. [Vitest](https://vitest.dev/) · [Testing Library](https://testing-library.com/) · [Vue Test Utils](https://test-utils.vuejs.org/) · [MSW](https://mswjs.io/) · [Playwright](https://playwright.dev/)
- MDN. [Frameworks de JavaScript del lado del cliente](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Frameworks_libraries) · [History API](https://developer.mozilla.org/es/docs/Web/API/History_API) · [CORS](https://developer.mozilla.org/es/docs/Web/HTTP/CORS)
- Manz.dev. [lenguajejs.com](https://lenguajejs.com/) · [bootcamp.manz.dev](https://bootcamp.manz.dev/)

<script src="../assets/steps.js"></script>
<script src="../assets/image-modal.js"></script>
