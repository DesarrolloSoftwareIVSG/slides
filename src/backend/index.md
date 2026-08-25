---
marp: true
theme: alo
paginate: true
---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00050_.png);
}
</style>
# Desarrollo Backend
## Contenidos
- Capa de datos, de negocios y de controladores
- Manejo de errores
- Autenticación, autorización y sesiones
- Pruebas de unidad

> **Laravel 13** como framework principal <br>**NestJS** y **ASP.NET Core** a modo comparativo <br>Curso *Desarrollo de Software IV* · II Ciclo 2026

---

## ¿Qué construimos en este tema?

- Un **back-end** es el software que vive en el servidor: recibe peticiones HTTP, aplica las reglas del negocio, persiste datos y devuelve respuestas

<div class="grid">
<div>

### 🗄️ Capa de datos
Cómo hablamos con la base de datos sin escribir SQL a mano
</div>
<div>

### 🧠 Capa de negocios
Dónde viven las reglas del dominio y las validaciones
</div>
<div>

### 🚦 Capa de controladores
Cómo exponemos todo eso como una **API REST**
</div>
<div>

### 🛡️ Transversal
Errores, autenticación, sesiones y pruebas
</div>
</div>

- 🎯 Al final del tema cada equipo tendrá una **API REST funcional, documentada, autenticada y probada**

---

## Los tres frameworks del curso

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.95rem;">
<div>

### 🔴 Laravel 13 — el principal
- Lenguaje **PHP 8.3+**
- Se enseña **en clase** y sostiene el producto completo del proyecto


### 🟢 NestJS — comparativo
- **TypeScript** sobre Node.js
- Porción vertical acotada (**Hito 1**)
</div>
<div>

### 🟣 ASP.NET Core — comparativo
- **C#** sobre .NET
- Porción vertical acotada (**Hito 2**)

<br>


</div>
</split-slide>


---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00068_.png);
}
</style>
# Arquitectura por capas
## Contenidos
- Por qué separar responsabilidades
- El recorrido de una petición
- Laravel 13: versiones y estructura
- Artisan

---

## Arquitectura por capas

- Cada capa tiene **una** responsabilidad y solo conoce a la que está inmediatamente debajo

<split-slide style="--left: 45%; --right: 55%; --font-size: 0.9rem;">
<div>

```text
   Cliente (web / móvil)
            │  HTTP
            ▼
  ┌─────────────────────┐
  │  Controladores      │ ← HTTP
  ├─────────────────────┤
  │  Servicios (negocio)│ ← reglas
  ├─────────────────────┤
  │  Modelos / ORM      │ ← datos
  └─────────────────────┘
            │  SQL
            ▼
      Base de datos
```
</div>
<div>

- **Controlador** — traduce HTTP a llamadas de negocio. No decide nada
- **Servicio** — dueño de las reglas del dominio. No sabe qué es HTTP
- **Modelo / repositorio** — persistencia. No conoce reglas de negocio

<br>

- 🚩 El error más frecuente del curso: **meter la lógica de negocio en el controlador**
</div>
</split-slide>

---

## El recorrido de una petición

<steps>
<step>

```text
1. Ruta            routes/api.php         →  ¿existe este endpoint?
2. Middleware      auth, throttle, cors   →  ¿puede pasar?
3. FormRequest     validación declarativa →  ¿los datos son válidos?  → 422
4. Controlador     traduce y delega       →  llama al servicio
5. Servicio        reglas de negocio      →  ¿la operación es legal?  → 409
6. Modelo / ORM    persistencia           →  SQL con parámetros
7. Recurso (API)   transforma la salida   →  JSON estable
8. Respuesta       código + encabezados   →  200 / 201 / 204
```

</step>
<step>

- Cada paso es **un punto de falla distinto** y devuelve **un código de estado distinto**
- Si algo se rompe, saber en qué paso ocurrió es la mitad de la depuración

<div class="grid">
<div>

### 401
Falló el paso 2: no hay identidad
</div>
<div>

### 422
Falló el paso 3: datos mal formados
</div>
<div>

### 403
Falló el paso 4 o 5: identidad sin permiso
</div>
<div>

### 409
Falló el paso 5: regla de negocio violada
</div>
</div>

</step>
</steps>

---

## Laravel 13 — versiones y soporte

| Versión | PHP soportado | Publicación | Correcciones hasta | Seguridad hasta |
|:--|:--|:--|:--|:--|
| 11 | 8.2 – 8.4 | 12 mar 2024 | 3 set 2025 | 12 mar 2026 |
| 12 | 8.2 – 8.5 | 24 feb 2025 | 13 ago 2026 | 24 feb 2027 |
| **13** | **8.3 – 8.5** | **17 mar 2026** | Q3 2027 | 17 mar 2028 |

- Laravel publica una **versión mayor al año** (~Q1) y sigue *Semantic Versioning*
- Correcciones por **18 meses**, parches de seguridad por **2 años**

- ⚠️ **Ojo con PHP:** la documentación exige **8.3** como mínimo, pero desde **13.3** el framework arrastra `symfony/console` y `symfony/error-handler` en su versión **8**, que **requieren PHP 8.4**. En la práctica: instale **PHP 8.4 o superior**

---

## Novedades de Laravel 13

<div class="grid">
<div>

### 🤖 Laravel AI SDK
API unificada para generación de texto, agentes con herramientas, *embeddings*, audio e imágenes
</div>
<div>

### 📄 Recursos JSON:API
Respuestas conformes a la especificación **JSON:API** de primera mano
</div>
<div>

### 🏷️ Atributos PHP
`#[Middleware]`, `#[Authorize]`, `#[Tries]`, `#[Timeout]` declarados junto a la clase
</div>
<div>

### 🛡️ PreventRequestForgery
Protección contra falsificación de solicitudes con verificación por **origen**
</div>
<div>

### 🔍 Búsqueda vectorial
`whereVectorSimilarTo()` sobre PostgreSQL + `pgvector`
</div>
<div>

### 📬 Queue::route()
Enrutamiento centralizado de trabajos a colas y conexiones
</div>
</div>

- ✅ **Pocos cambios rompientes**: la mayoría de aplicaciones sube de 12 a 13 sin tocar código

---

## Instalación y verificación

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.85rem;">
<div>

```bash
# 1. Verificar versiones (los tres entornos)
php -v          # 8.4 o superior
composer -V
laravel --version   # 13.x

node -v && npm -v
nest --version  # npm i -g @nestjs/cli
dotnet --info   # SDK .NET
```

```bash
# 2. Proyecto principal del curso
laravel new proyecto-if0009
cd proyecto-if0009
php artisan serve   # http://localhost:8000
```
</div>
<div>

```bash
# 3. Habilitar la API (Sanctum + routes/api.php)
php artisan install:api
```

```bash
# 4. Puertos del curso — fíjelos desde el inicio
# Laravel        8000
# Front-end      5173 / 4200
# NestJS         3000
# ASP.NET Core   5000 / 5001
```

- 🚩 El `.env` **nunca** se confirma en el repositorio: se versiona solo `.env.example`
</div>
</split-slide>

---

## Estructura de un proyecto Laravel 13

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.85rem;">
<div>

```text
app/
 ├── Http/
 │    ├── Controllers/   ← capa de controladores
 │    ├── Requests/      ← validaciones
 │    └── Resources/     ← transformación de salida
 ├── Models/             ← Eloquent
 ├── Services/           ← capa de negocio (la creamos)
 ├── Policies/           ← autorización
 └── Exceptions/         ← excepciones propias
bootstrap/app.php        ← rutas, middleware, errores
config/
database/
 ├── migrations/  factories/  seeders/
routes/  api.php  web.php
tests/   Feature/  Unit/
```
</div>
<div>

- Desde **Laravel 11** desaparecieron `app/Http/Kernel.php` y `app/Exceptions/Handler.php`
- Todo eso se configura ahora en un único **`bootstrap/app.php`**

<br>

- `app/Services/` **no** viene por defecto: es una convención que adoptamos en el curso para que la capa de negocio tenga un lugar propio
</div>
</split-slide>

---

## Artisan — la consola de Laravel

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.85rem;">
<div>

```bash
php artisan list             # todos los comandos
php artisan about            # entorno y paquetes

# Generadores que usaremos en el tema
php artisan make:model Pedido -mfs
#   -m migración  -f fábrica  -s poblador
php artisan make:controller PedidoController --api
php artisan make:request GuardarPedidoRequest
php artisan make:resource PedidoResource
php artisan make:policy PedidoPolicy --model=Pedido
php artisan make:test PedidoServiceTest --unit

# Base de datos
php artisan migrate
php artisan migrate:rollback
php artisan migrate:fresh --seed
php artisan db:seed
```
</div>
<div>

- Artisan **no** es un atajo cosmético: genera los archivos **en el lugar correcto** y con el **espacio de nombres correcto**

<br>

- `php artisan tinker` abre una consola interactiva contra la aplicación — invaluable para probar consultas Eloquent sin escribir un controlador
</div>
</split-slide>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00029_.png);
}
</style>
# Capa de datos
## Contenidos
- Frameworks de persistencia (ORM)
- Migraciones, modelos y relaciones
- Procedimientos almacenados
- CRUD, paginación y limitación

---

## ¿Qué es un framework de persistencia?

- Un **ORM** (*Object-Relational Mapping*) es una técnica para convertir datos entre el sistema de tipos de un lenguaje **orientado a objetos** y una base de datos **relacional** usada como motor de persistencia

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.9rem;">
<div>

### 😖 Sin ORM
```php
$sql = "SELECT * FROM pedidos
        WHERE cliente_id = " . $id;
$rows = mysqli_query($conn, $sql);
// arreglos asociativos sueltos
// SQL concatenado → inyección
```
</div>
<div>

### 😌 Con ORM
```php
$pedidos = Pedido::where('cliente_id', $id)
                 ->with('cliente')
                 ->get();
// objetos Pedido, tipados
// parámetros vinculados siempre
```
</div>
</split-slide>

- 🧭 **Navego objetos, no tablas**: `$user->roles` recorre la tabla pivote sin que yo escriba el `JOIN`

---

## Los tres ORM del curso

| | 🔴 Laravel 13 | 🟢 NestJS | 🟣 ASP.NET Core |
|:--|:--|:--|:--|
| **ORM** | Eloquent | TypeORM / Prisma | Entity Framework Core |
| **Patrón** | *Active Record* | *Data Mapper* / cliente generado | *Data Mapper* + `DbContext` |
| **Migraciones** | `php artisan make:migration` | `typeorm migration:generate` · `prisma migrate` | `dotnet ef migrations add` |
| **Definición** | Clase PHP + migración | Entidad decorada / `schema.prisma` | Clase C# + `DbContext` |
| **Consulta** | `Pedido::where(...)` | `repo.find({ where })` | `ctx.Pedidos.Where(...)` (LINQ) |

- ⚖️ **Active Record**: el modelo *es* la fila y sabe guardarse. Menos código, más acoplamiento
- ⚖️ **Data Mapper**: la entidad es un objeto plano y un repositorio la persiste. Más ceremonia, más testeable

---

## Migraciones: control de versiones para la base de datos

<steps>
<step>

### El problema
- Versionar la base de datos es **más complejo** que versionar el código:
  - En producción **existen datos**
  - No podemos borrarlos porque cambiamos las tablas
  - Debemos **transformar** los datos

> 📌 Ejemplo: pasar una relación 1:N a N:M. Cambiar el modelo entidad-relación es fácil —separo la tabla en dos—, pero además debo **detectar los estudiantes duplicados y unificarlos**. Sería trivial si no hubiera datos.

</step>
<step>

### La solución
- Las **migraciones** son el control de versiones de tu esquema
- Permiten modificar la base de datos y **compartir esos cambios con el equipo**
- La creación del esquema también se hace con migraciones

- 🔑 Regla del curso: **una migración debe ser reversible**. Si el retroceso falla, el esquema no es reproducible

</step>
</steps>

---

## Migración en Laravel 13

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.8rem;">
<div>

```php
// database/migrations/..._create_pedidos_table.php
public function up(): void
{
    Schema::create('pedidos', function (Blueprint $table) {
        $table->id();
        $table->string('codigo', 20)->unique();
        $table->foreignId('cliente_id')
              ->constrained()
              ->restrictOnDelete();
        $table->decimal('total', 12, 2)->default(0);
        $table->enum('estado', ['borrador','confirmado','anulado'])
              ->index();
        $table->timestamps();
    });
}

public function down(): void
{
    Schema::dropIfExists('pedidos');
}
```
</div>
<div>

- **Tipos y longitudes** explícitos
- **Restricciones de nulidad** e **índices**
- **Claves foráneas** con política de borrado (`restrictOnDelete` o `cascadeOnDelete`)

```bash
php artisan migrate
php artisan migrate:rollback  # ¿funciona?
php artisan migrate           # ¿reaplica?
```

- 🚩 Si el `rollback` falla, se penaliza: es la diferencia entre un esquema **versionado** y uno **improvisado**
</div>
</split-slide>

---

## Migraciones en los tres frameworks

<split-slide style="--left: 34%; --right: 66%; --font-size: 0.78rem;">
<div>

### 🔴 Laravel
```bash
php artisan make:migration create_pedidos_table
php artisan migrate
php artisan migrate:rollback
```
</div>
<div>

<div class="grid">
<div>

### 🟢 NestJS + TypeORM
```bash
npm run typeorm migration:generate -- -n CreatePedidos
npm run typeorm migration:run
npm run typeorm migration:revert
```
</div>
<div>

### 🟣 ASP.NET Core + EF Core
```bash
dotnet ef migrations add CreatePedidos
dotnet ef database update
dotnet ef migrations remove
```
</div>
</div>

- Los tres resuelven el mismo problema con el mismo ciclo: **generar → aplicar → revertir**
- Diferencia práctica: Laravel y EF Core **generan la migración desde el código**; TypeORM la **compara contra el esquema actual**
</div>
</split-slide>

---

## Modelos Eloquent

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.78rem;">
<div>

```php
class Pedido extends Model
{
    // Asignación masiva protegida
    protected $fillable = ['codigo', 'cliente_id', 'estado'];

    // Nunca se serializan
    protected $hidden = ['costo_interno'];

    // Conversores de tipo (Laravel 11+: método, no propiedad)
    protected function casts(): array
    {
        return [
            'total'         => 'decimal:2',
            'confirmado_en' => 'datetime',
        ];
    }

    public function cliente(): BelongsTo
    { 
        return $this->belongsTo(Cliente::class); 
    }

    public function detalles(): HasMany
    { 
        return $this->hasMany(DetallePedido::class); 
    }

    // Alcance reutilizable
    public function scopeConfirmados($q)
    { 
        return $q->where('estado', 'confirmado'); 
    }
}
```
</div>
<div>

### 🛡️ `$fillable`
Sin él, una solicitud podría modificar **campos no previstos** — por ejemplo el **rol** de la persona usuaria. Es una vulnerabilidad real

### 🙈 `$hidden`
Contraseñas y tokens deben ocultarse en la serialización, o **se filtran** en cualquier respuesta que devuelva el modelo completo

### 🔁 `scope`
Encapsula un filtro con nombre de negocio y lo hace reutilizable
</div>
</split-slide>

---

## Relaciones

<steps>
<step>

### 1️⃣ ➡️ N `hasMany`
Un pedido tiene muchos detalles

```php
public function detalles(): HasMany
{
  return $this->hasMany(DetallePedido::class);
}
```
</step>
<step>

### N ➡️ 1️⃣ `belongsTo`
Un pedido pertenece a un cliente

```php
public function cliente(): BelongsTo
{
  return $this->belongsTo(Cliente::class);
}
```
</step>
<step>

### N ↔️ M `belongsToMany`
Con tabla pivote y datos adicionales

```php
public function roles(): BelongsToMany
{
  return $this->belongsToMany(Role::class)
              ->withPivot('asignado_en')
              ->withTimestamps();
}
```
</step>
</steps>


- 📋 El proyecto exige **mínimo seis entidades** con una relación **1:N**, una **N:M** y una **N:M con datos adicionales en el pivote**

---

## El problema N+1

<steps>
<step>

```php
// ❌ 21 consultas para listar 20 pedidos
$pedidos = Pedido::all();          // 1 consulta
foreach ($pedidos as $pedido) {
    echo $pedido->cliente->nombre; // +1 consulta por cada pedido
}
```

- Una consulta para la lista, **más una consulta por cada elemento**
- Con 20 registros son 21 consultas; con 2 000 registros son **2 001**
- No se nota en desarrollo con datos de prueba. **Se nota en producción**

</step>
<step>

```php
// ✅ 2 consultas: carga anticipada (eager loading)
$pedidos = Pedido::with('cliente', 'detalles.producto')
                 ->confirmados()
                 ->paginate(15);
```

```php
// AppServiceProvider::boot() — que el framework falle en desarrollo
Model::preventLazyLoading(! app()->isProduction());
```

- 📊 El laboratorio exige el **dato numérico**: consultas **antes** y **después**. Una afirmación sin conteo no cuenta

</step>
</steps>

---

## Cómo medir las consultas

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.85rem;">
<div>

```php
// Registro de consultas en una ruta de prueba
DB::enableQueryLog();

$pedidos = Pedido::all();
foreach ($pedidos as $p) { $p->cliente->nombre; }

dd(count(DB::getQueryLog()));  // 21
```

```php
// Con carga anticipada
DB::enableQueryLog();

$pedidos = Pedido::with('cliente')->get();
foreach ($pedidos as $p) { $p->cliente->nombre; }

dd(count(DB::getQueryLog()));  // 2
```
</div>
<div>

- 🔭 Alternativas: **Laravel Telescope**, **Laravel Debugbar** o **Pulse** muestran el conteo en cada petición sin tocar el código

<br>

- 🟢 En NestJS: `logging: true` en TypeORM
- 🟣 En EF Core: `optionsBuilder.LogTo(Console.WriteLine)`
</div>
</split-slide>

---

## Fábricas y pobladores

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.8rem;">
<div>

```php
// database/factories/PedidoFactory.php
class PedidoFactory extends Factory
{
    public function definition(): array
    {
        return [
            'codigo'     => fake()->unique()->bothify('PED-####'),
            'cliente_id' => Cliente::factory(),
            'estado'     => fake()->randomElement(
                                ['borrador','confirmado']),
            'total'      => fake()->randomFloat(2, 1000, 500000),
        ];
    }
}
```

```php
// database/seeders/DatabaseSeeder.php
Cliente::factory(10)
       ->has(Pedido::factory(3)->has(DetallePedido::factory(4)))
       ->create();
```
</div>
<div>

- Las fábricas usan **Faker** para generar datos **realistas**, no `aaa`, `test1`, `asdf`

<br>

- 📋 El laboratorio exige **mínimo 20 registros** en la entidad principal y **datos coherentes** en las relacionadas

<br>

- 💡 Los datos de prueba también son el insumo de las **pruebas automatizadas** y del **contrato comparativo**
</div>
</split-slide>

---

## Consumo de procedimientos almacenados

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.8rem;">
<div>

```php
// Ejecutar un procedimiento sin resultado
DB::statement('CALL recalcular_totales(?)', [$pedidoId]);

// Procedimiento que devuelve filas
$filas = DB::select('CALL reporte_ventas_mes(?, ?)',
                    [$anio, $mes]);

// Varios conjuntos de resultados a la vez
[$opciones, $notificaciones] = DB::selectResultSets(
    "CALL get_user_options_and_notifications(?)",
    $request->user()->id
);

// Consultar una vista como si fuera una tabla
$ventas = DB::table('v_ventas_por_cliente')
            ->where('anio', 2026)
            ->get();
```
</div>
<div>

### ✅ Cuándo usarlos
- Cálculos **pesados** que conviene resolver **cerca de los datos**
- Lógica ya existente en sistemas **heredados**
- Operaciones masivas con muchas escrituras

### ⚠️ Cuándo evitarlos
- Reglas de negocio que el equipo tendrá que **mantener y probar**: quedan fuera del control de versiones de la aplicación y fuera de las pruebas

- 🔑 Siempre con **parámetros vinculados** (`?`), nunca concatenando texto
</div>
</split-slide>

---

## Procedimientos almacenados en los tres frameworks

| | 🔴 Laravel / Eloquent | 🟢 NestJS / TypeORM | 🟣 ASP.NET Core / EF Core |
|:--|:--|:--|:--|
| **Sin resultado** | `DB::statement('CALL sp(?)', [$x])` | `dataSource.query('CALL sp(?)', [x])` | `ctx.Database.ExecuteSqlAsync(...)` |
| **Con filas** | `DB::select('CALL sp(?)', [$x])` | `repo.query('CALL sp(?)', [x])` | `ctx.Pedidos.FromSql(...)` |
| **Múltiples resultados** | `DB::selectResultSets(...)` | Manual según el *driver* | `DbCommand` manual |
| **Vistas** | `DB::table('v_ventas')` | `@ViewEntity()` | Entidad con `ToView("v_ventas")` |

- 🧪 En el **Laboratorio 3** se pide implementar **tres consultas**: una con el constructor de consultas y alcances, una con **agregación y agrupación**, y una que **consuma un procedimiento almacenado o una vista**

---

## Operaciones CRUD con Eloquent

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.8rem;">
<div>

### ➕ Create
```php
$pedido = Pedido::create($datosValidados);
```

### 🔍 Read
```php
$pedido  = Pedido::findOrFail($id);   // 404 si no existe
$pedidos = Pedido::with('cliente')->paginate(15);
$uno     = Pedido::where('codigo', $c)->first();
```
</div>
<div>

### ✏️ Update
```php
$pedido->update($datosValidados);   // ✅
// $pedido->update($request->all()); // ❌ vulnerable
```

### 🗑️ Delete
```php
$pedido->delete();          // borrado
$pedido->forceDelete();     // definitivo
Pedido::withTrashed()->find($id);  // SoftDeletes
```
</div>
</split-slide>

- 🚩 `update($request->all())` es **asignación masiva sin control**: acepta cualquier campo que venga en la petición. Siempre `validated()`

---

## CRUD comparado

| Operación | 🔴 Eloquent | 🟢 TypeORM | 🟣 EF Core |
|:--|:--|:--|:--|
| **Crear** | `Pedido::create($d)` | `repo.save(repo.create(d))` | `ctx.Add(p); ctx.SaveChanges()` |
| **Leer uno** | `Pedido::findOrFail($id)` | `repo.findOneByOrFail({id})` | `ctx.Pedidos.FindAsync(id)` |
| **Leer lista** | `Pedido::with('cliente')->get()` | `repo.find({relations:['cliente']})` | `ctx.Pedidos.Include(p=>p.Cliente)` |
| **Actualizar** | `$pedido->update($d)` | `repo.update(id, d)` | `ctx.Update(p); ctx.SaveChanges()` |
| **Eliminar** | `$pedido->delete()` | `repo.remove(pedido)` | `ctx.Remove(p); ctx.SaveChanges()` |
| **Borrado lógico** | `SoftDeletes` (rasgo) | `@DeleteDateColumn()` | Filtro global manual |

- 💡 Note quién **confirma** la escritura: Eloquent y TypeORM guardan al llamar; EF Core acumula cambios y los persiste en `SaveChanges()` (*unit of work*)

---

## Paginación

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.82rem;">
<div>

```php
// Paginación completa: sabe cuántas páginas hay
$pedidos = Pedido::paginate(15);
// → 2 consultas: COUNT(*) + SELECT ... LIMIT/OFFSET

// Paginación simple: solo anterior / siguiente
$pedidos = Pedido::simplePaginate(15);
// → 1 consulta, sin COUNT(*)

// Paginación por cursor: para grandes volúmenes
$pedidos = Pedido::orderBy('id')->cursorPaginate(15);
// → sin OFFSET, ideal para scroll infinito
```
</div>
<div>

| Método | Consultas | Total | Uso típico |
|:--|:--:|:--:|:--|
| `paginate` | 2 | ✅ | Tablas con numeración |
| `simplePaginate` | 1 | ❌ | Listas largas |
| `cursorPaginate` | 1 | ❌ | *Scroll* infinito |

- ⚡ `OFFSET 100000` obliga al motor a **descartar 100 000 filas**. El cursor no: filtra por el valor de la última fila vista
</div>
</split-slide>

---

## Limitación de resultados: el tope de página

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.8rem;">
<div>

```php
// ❌ El cliente decide cuántos registros trae
$porPagina = $request->input('per_page', 15);

// ✅ El servidor impone un techo
$porPagina = min((int) $request->input('per_page', 15), 100);

// Listado con filtros condicionales, orden y tope
Pedido::query()
    ->when($request->q, fn ($q, $t) =>
        $q->where('codigo', 'like', "%{$t}%"))
    ->when($request->estado, fn ($q, $e) =>
        $q->where('estado', $e))
    ->orderBy($request->input('sort', 'created_at'),
              $request->input('dir', 'desc'))
    ->paginate($porPagina);
```
</div>
<div>

### 🛡️ Por qué es un control de seguridad
- Sin tope, una sola petición con `?per_page=999999` puede **agotar la memoria** del servidor
- Es un vector de **denegación de servicio** trivial de explotar

<br>

- 🚩 Los filtros se construyen **condicionalmente** sobre el constructor de consultas y con **parámetros vinculados**. Nunca concatenando texto
</div>
</split-slide>

---

## Paginación comparada

| | 🔴 Laravel | 🟢 NestJS | 🟣 ASP.NET Core |
|:--|:--|:--|:--|
| **API** | `paginate()`, `simplePaginate()`, `cursorPaginate()` | `repo.findAndCount({skip, take})` o `nestjs-paginate` | `.Skip(n).Take(m)` con LINQ |
| **Metadatos** | Automáticos (`meta`, `links`) | Se arman a mano | Se arman a mano |
| **Tope** | Manual (`min(...)`) | `@Max()` en el DTO de consulta | Validación en el modelo |

- 🎯 Este es exactamente el tipo de diferencia que el **estudio comparativo** debe medir: *¿cuántas líneas cuesta devolver un listado paginado con metadatos consistentes en cada framework?*

---

## 📋 Laboratorio 3 — Capa de datos

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.92rem;">
<div>

### Qué se entrega
- Modelo entidad-relación con **6+ entidades**: 1:N, N:M y pivote con datos adicionales
- Migraciones con tipos, índices, claves foráneas y política de borrado — **reversibles**
- Modelos con relaciones, `$fillable`, `casts()` y `$hidden`
- Fábricas y pobladores: **20+ registros**
- Tres consultas: alcances, agregación y **procedimiento almacenado o vista**
- **Conteo de consultas** antes y después de corregir N+1
</div>
<div>

### 🎯 Hito 0 — Contrato comparativo
Antes de cerrar la semana se **congela** `docs/contrato-comparativo.md`:

- Una entidad con **máximo 8 campos** y **una relación**
- Los **seis endpoints** del contrato común
- Los **datos de prueba** idénticos para las tres implementaciones

> ⚠️ No puede modificarse después del Hito 1: es lo que hace **comparables** las tres implementaciones
</div>
</split-slide>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00035_.png);
}
</style>
# Capa de negocios
## Contenidos
- Separación de responsabilidades
- Manejo de validaciones
- Reglas de dominio
- Transacciones

---

## ¿Qué es la capa de negocios?

- Es donde viven las **reglas del dominio**: lo que el sistema puede y no puede hacer, expresado en el lenguaje de la organización

<div class="grid">
<div>

### 🚦 Controlador
*"Llegó un POST con este cuerpo"*

Traduce HTTP. **No decide**
</div>
<div>

### 🧠 Servicio
*"No se puede confirmar un pedido sin detalle"*

**Decide**. No sabe qué es HTTP
</div>
<div>

### 🗄️ Modelo
*"Guarda esta fila"*

Persiste. No conoce reglas
</div>
</div>

- 🔑 Prueba de fuego: **¿podría llamar a mi lógica de negocio desde un comando de consola, sin HTTP?** Si no, está en la capa equivocada
- 📏 Regla del curso: **ningún método de controlador supera 15 líneas**

---

## Manejo de validaciones

- Validar es **rechazar datos mal formados antes de que toquen el negocio**. No es lo mismo que una regla de negocio

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.9rem;">
<div>

### ✅ Validación (declarativa)
- El campo `codigo` es obligatorio
- Tiene máximo 20 caracteres
- `cliente_id` existe en la tabla clientes
- `cantidad` es un entero entre 1 y 999

→ Responde **422 Unprocessable Content**
</div>
<div>

### 🧠 Regla de negocio (imperativa)
- No se puede confirmar un pedido **sin detalle**
- No se elimina un cliente **con pedidos activos**
- El descuento se aplica **sobre cierto umbral**

→ Responde **409 Conflict**
</div>
</split-slide>

- 💡 Si puede expresarse como una lista de condiciones sobre un campo, es **validación**. Si necesita consultar el estado del sistema, es **regla de negocio**

---

## Validación en Laravel: Form Request

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.75rem;">
<div>

```php
class GuardarPedidoRequest extends FormRequest
{
    public function rules(): array
    {
        return [
            'codigo' => ['required','string','max:20',
                Rule::unique('pedidos')->ignore($this->pedido)],
            'cliente_id' => ['required','exists:clientes,id'],
            'fecha'      => ['required','date','after_or_equal:today'],
            'detalles'   => ['required','array','min:1'],
            'detalles.*.producto_id' => ['required','exists:productos,id'],
            'detalles.*.cantidad'    => ['required','integer','min:1','max:999'],
        ];
    }

    public function messages(): array
    {
        return [
            'detalles.min'  => 'El pedido debe incluir al menos una línea de detalle.',
            'codigo.unique' => 'Ya existe un pedido con ese código.',
        ];
    }
}
```
</div>
<div>

- Se **inyecta** en el método del controlador: si falla, Laravel devuelve **422** con el detalle **por campo** y el controlador nunca se ejecuta

```json
{
  "message": "The given data was invalid.",
  "errors": {
    "codigo": ["Ya existe un pedido con ese código."],
    "detalles": ["El pedido debe incluir..."]
  }
}
```

- 📋 El laboratorio exige mensajes **en español** y **asociados a cada campo**, no un texto único
</div>
</split-slide>

---

## Validación comparada

<steps>
<step>

### 🔴 Laravel — Form Request
```php
class GuardarPedidoRequest extends FormRequest {
  public function rules(): array {
    return ['codigo' => ['required','max:20']];
  }
}
```
Reglas como **arreglo**. 422 nativo

</step>
<step>

### 🟢 NestJS — DTO + class-validator
```ts
export class CrearPedidoDto {
  @IsString() @MaxLength(20)
  codigo: string;

  @IsInt() @Min(1)
  clienteId: number;
}
```
Reglas como **decoradores**
- ⚠️ En NestJS el DTO debe ser una **clase**, no una interfaz: las interfaces se borran al compilar y no hay prototipo donde leer los decoradores
- ⚠️ NestJS **no valida por defecto**: hay que registrar el `ValidationPipe` global en `main.ts`

</step>
<step>

### 🟣 ASP.NET Core — Data Annotations
```csharp
public class CrearPedidoDto {
  [Required, MaxLength(20)]
  public string Codigo { get; set; }

  [Range(1, int.MaxValue)]
  public int ClienteId { get; set; }
}
```
Atributos, o **FluentValidation**

</step>
</steps>



---

## Servicios y reglas de negocio

<split-slide style="--left: 58%; --right: 42%; --font-size: 0.72rem;">
<div>

```php
class PedidoService
{
    public function __construct(
        private InventarioService $inventario
    ) {}

    public function confirmar(Pedido $pedido): Pedido
    {
        // Regla 1: no se confirma un pedido vacío
        if ($pedido->detalles()->doesntExist()) {
            throw new ReglaNegocioException(
                'No se puede confirmar un pedido sin detalle.');
        }

        // Regla 2: no se reconfirma
        if ($pedido->estado === 'confirmado') {
            throw new ReglaNegocioException(
                'El pedido ya fue confirmado.');
        }

        return DB::transaction(function () use ($pedido) {
            $pedido->update([
                'estado'        => 'confirmado',
                'confirmado_en' => now(),
            ]);
            $this->inventario->descontar($pedido->detalles);
            return $pedido->fresh('detalles');
        });
    }
}
```
</div>
<div>

- El servicio recibe **dependencias por el constructor** — el contenedor de Laravel las resuelve solo

<br>

- Lanza una **excepción propia** (`ReglaNegocioException`), no un `abort(409)`: el servicio **no sabe qué es HTTP**

<br>

- 📋 El laboratorio exige **cuatro o más reglas** que **no puedan expresarse de forma declarativa**
</div>
</split-slide>

---

## Transacciones

<steps>
<step>

- Una **transacción** garantiza que un conjunto de escrituras ocurra **por completo o no ocurra**

```php
DB::transaction(function () use ($pedido) {
    $pedido->update(['estado' => 'confirmado']);
    $this->inventario->descontar($pedido->detalles);  // si esto falla...
    $this->bitacora->registrar($pedido);
});
// ...nada de lo anterior queda escrito
```

- 🔑 Regla del curso: **toda operación que escriba en más de una tabla va dentro de una transacción**

</step>
<step>

### Cómo se verifica (y el laboratorio lo exige)

```php
it('revierte todo si falla el descuento de inventario', function () {
    $pedido = Pedido::factory()->has(DetallePedido::factory(2))->create();

    // Forzamos el fallo en el segundo paso
    $this->mock(InventarioService::class)
         ->shouldReceive('descontar')->andThrow(new RuntimeException());

    expect(fn () => app(PedidoService::class)->confirmar($pedido))
        ->toThrow(RuntimeException::class);

    // Ninguna de las dos tablas conserva el cambio
    expect($pedido->fresh()->estado)->toBe('borrador');
    expect(Movimiento::count())->toBe(0);
});
```

</step>
</steps>

---

## Transacciones comparadas

| | 🔴 Laravel | 🟢 NestJS + TypeORM | 🟣 ASP.NET Core + EF Core |
|:--|:--|:--|:--|
| **Sintaxis** | `DB::transaction(fn () => ...)` | `dataSource.transaction(async m => ...)` | `ctx.Database.BeginTransaction()` |
| **Reversión** | Automática al lanzar excepción | Automática al lanzar excepción | Manual: `Commit()` / `Rollback()` |
| **Reintentos** | `DB::transaction(fn, 3)` | Manual | Estrategia de ejecución configurable |
| **Anidamiento** | *Savepoints* automáticos | *Savepoints* | *Savepoints* |

- 💡 En EF Core, `SaveChanges()` **ya es transaccional** por sí mismo: solo hace falta una transacción explícita si se combinan varios `SaveChanges()` o SQL crudo

---

## 📋 Laboratorio 4 — Capa de negocios

<div class="grid">
<div>

### 📐 Separación de capas
Controladores delgados. Validación, negocio y persistencia cada uno en su capa
</div>
<div>

### ✅ Validaciones
Form Requests para creación y actualización de dos entidades, con mensajes en español por campo
</div>
<div>

### 🧠 Reglas de negocio
Cuatro o más reglas en la capa de servicio, con excepción propia
</div>
<div>

### 🔒 Transacciones
Toda escritura múltiple es transaccional, con reversión **verificada**
</div>
<div>

### 📄 CRUD completo
Con paginación, ordenamiento por dos campos, filtros combinables y **tope de página**
</div>
<div>

### 🧪 Primeras pruebas
Una prueba por cada regla implementada
</div>
</div>

- 🚩 *"Reglas de negocio dentro del controlador se penalizan como falta de separación de responsabilidades: es el error más frecuente de este laboratorio."*

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00033_peticiones.png);
}
</style>
# Capa de controladores
## Contenidos
- Aspectos básicos de HTTP
- Creación de servicios web (API REST)
- Creación de microservicios
- Documentación y librerías esenciales

---

## HTTP — Métodos

- HTTP define métodos de petición que indican **la acción deseada** sobre un recurso. Cada uno tiene una **semántica** distinta

| Método | Acción | Seguro | Idempotente | Cuerpo | Éxito |
|:--|:--|:--:|:--:|:--:|:--|
| **GET** | Solicita un recurso | ✅ | ✅ | ❌ | `200` |
| **POST** | Crea un recurso | ❌ | ❌ | ✅ | `201` + `Location` |
| **PUT** | Reemplaza el recurso completo | ❌ | ✅ | ✅ | `200` / `204` |
| **PATCH** | Modifica parcialmente | ❌ | ❌ | ✅ | `200` |
| **DELETE** | Elimina el recurso | ❌ | ✅ | ❌ | `204` |

- **Seguro** = no modifica estado · **Idempotente** = repetirlo produce el mismo resultado
- 🔑 `DELETE` dos veces debe dar el mismo estado final. `POST` dos veces crea **dos** recursos

---

## HTTP — Códigos de estado

<div class="grid">
<div>

### ✅ 2xx — Éxito
- `200 OK` — consulta o actualización
- `201 Created` — creación, **con `Location`**
- `204 No Content` — eliminación
</div>
<div>

### ↩️ 3xx — Redirección
- `301` permanente · `302` temporal
- `304 Not Modified` — caché válida
</div>
<div>

### ⚠️ 4xx — Error del cliente
- `400` petición mal formada
- `401` **no autenticado** (¿quién eres?)
- `403` **no autorizado** (te conozco, no puedes)
- `404` no existe
- `409` conflicto: **regla de negocio**
- `422` validación fallida
- `429` demasiadas peticiones
</div>
<div>

### 💥 5xx — Error del servidor
- `500` error no controlado
- `502` / `503` / `504` — infraestructura
</div>
</div>

- 🚩 Devolver `200` con `{"error": "..."}` en el cuerpo es un **error de diseño**: el cliente no puede reaccionar sin leer el JSON

---

## 401 vs 403 · 400 vs 422 · 409

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.92rem;">
<div>

### 🔐 401 vs 403
- **401 Unauthorized** — *no sé quién eres*. Falta el token o está vencido
- **403 Forbidden** — *sé quién eres y no puedes*. El token es válido pero el rol o la política lo impide

### 📝 400 vs 422
- **400 Bad Request** — el cuerpo **no se puede interpretar** (JSON malformado)
- **422 Unprocessable Content** — el cuerpo se entiende pero **no pasa las validaciones**
</div>
<div>

### ⚔️ 409 Conflict
- La petición es válida y la persona tiene permiso, pero **el estado del sistema lo impide**
- *"No se puede confirmar un pedido sin detalle"*
- *"No se puede eliminar un cliente con pedidos activos"*

<br>

> 📌 En Laravel la validación fallida devuelve **422 de forma nativa**, no 400. Debe respetarse esa semántica y documentarse
</div>
</split-slide>

---

## HTTP — Encabezados

<div class="grid">
<div>

### 📥 De petición
```http
Authorization: Bearer eyJhbGci...
Content-Type: application/json
Accept: application/json
Accept-Language: es-CR
If-None-Match: "a1b2c3"
```
</div>
<div>

### 📤 De respuesta
```http
Content-Type: application/json
Location: /api/pedidos/42
ETag: "a1b2c3"
Cache-Control: no-store
X-RateLimit-Remaining: 57
Retry-After: 60
```
</div>
<div>

### 🛡️ De seguridad
```http
Content-Security-Policy: default-src 'self'
Strict-Transport-Security: max-age=31536000
X-Content-Type-Options: nosniff
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: geolocation=()
```
</div>
</div>

- 🔑 El token viaja en `Authorization`, **nunca en la URL**: la URL queda registrada en las bitácoras del servidor y en el historial del navegador

---

## Diseño de una API REST

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.88rem;">
<div>

### ✅ Rutas orientadas a recursos
```http
GET    /api/pedidos
GET    /api/pedidos/{pedido}
POST   /api/pedidos
PUT    /api/pedidos/{pedido}
DELETE /api/pedidos/{pedido}
GET    /api/clientes/{cliente}/pedidos
```
- **Sustantivos en plural**
- **Sin verbos** en la ruta
- Recursos **anidados** para las relaciones
</div>
<div>

### ❌ Rutas orientadas a acciones
```http
GET  /api/obtenerPedidos
POST /api/pedido/eliminar/1
GET  /api/getPedidoById?id=5
POST /api/crearNuevoPedido
```
- El **verbo** ya lo dice el método HTTP
- Repetirlo en la ruta es redundante y rompe la uniformidad
</div>
</split-slide>

- 💡 Si el nombre del endpoint necesita un verbo, probablemente sea un **recurso que no ha sido modelado** (`POST /api/pedidos/{id}/confirmacion`)

---

## Rutas y controladores en Laravel 13

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.78rem;">
<div>

```php
// routes/api.php
Route::apiResource('pedidos', PedidoController::class);
Route::apiResource('clientes.pedidos', ClientePedidoController::class)
     ->only(['index']);
```

```text
apiResource genera 5 rutas:
GET    /pedidos            index
POST   /pedidos            store
GET    /pedidos/{pedido}   show
PUT    /pedidos/{pedido}   update
DELETE /pedidos/{pedido}   destroy
```

```php
// Controlador delgado: recibe, delega, responde
public function store(GuardarPedidoRequest $request): JsonResponse
{
    $pedido = $this->service->crear($request->validated());

    return (new PedidoResource($pedido))
        ->response()
        ->setStatusCode(201)
        ->header('Location', route('pedidos.show', $pedido));
}
```
</div>
<div>

- La **validación** ya ocurrió al inyectar el `FormRequest`
- El **negocio** está en `$this->service`
- El controlador solo elige el **código de estado** y el **encabezado**

<br>

- 🆕 En Laravel 13 el middleware y la autorización pueden declararse con **atributos**:

```php
#[Middleware('auth:sanctum')]
class PedidoController { }
```
</div>
</split-slide>

---

## Recursos de API: desacoplar la respuesta del esquema

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.78rem;">
<div>

```php
class PedidoResource extends JsonResource
{
    public function toArray($request): array
    {
        return [
            'id'      => $this->id,
            'codigo'  => $this->codigo,
            'estado'  => $this->estado,
            'total'   => (float) $this->total,
            'cliente' => new ClienteResource(
                             $this->whenLoaded('cliente')),
            'creado'  => $this->created_at->toIso8601String(),
        ];
    }
}
```

```php
// Uso
return new PedidoResource($pedido);
return PedidoResource::collection($pedidos);  // con meta y links
```
</div>
<div>

### 🎯 Por qué importa
- El **contrato** de la API deja de depender del **esquema** de la base de datos
- Si renombro la columna `costo_interno`, la respuesta **no cambia**
- Los campos sensibles simplemente **no aparecen**

<br>

- 🚩 Criterio de evaluación: *"si al renombrar una columna cambia la respuesta de la API, el desacoplamiento no se logró"*
</div>
</split-slide>

---

## Novedad de Laravel 13: recursos JSON:API

<split-slide style="--left: 52%; --right: 48%; --font-size: 0.78rem;">
<div>

```bash
php artisan make:resource PostResource --json-api
```

```json
{
  "data": {
    "type": "posts",
    "id": "1",
    "attributes": { "title": "Hola", "body": "..." },
    "relationships": {
      "author": { "data": { "type":"users", "id":"7" } }
    },
    "links": { "self": "/api/posts/1" }
  },
  "included": [ { "type":"users", "id":"7" } ]
}
```
</div>
<div>

- **JSON:API** es una especificación que estandariza cómo se ven las respuestas: tipos, relaciones, `included`, *sparse fieldsets* y enlaces

- Laravel 13 lo trae **de primera mano**: maneja la serialización, la inclusión de relaciones, los campos dispersos y los encabezados conformes

<br>

- ⚖️ Para el proyecto **no es obligatorio**: lo importante es que la estructura sea **consistente en toda la API**
</div>
</split-slide>

---

## Metadatos de paginación en la respuesta

<split-slide style="--left: 45%; --right: 55%; --font-size: 0.75rem;">
<div>

```php
return PedidoResource::collection(
    Pedido::with('cliente')->paginate($porPagina)
);
```
</div>
<div>

```json
{
  "data": [ { "id": 1, "codigo": "PED-0001" } ],
  "links": {
    "first": "https://api.../pedidos?page=1",
    "last":  "https://api.../pedidos?page=9",
    "prev":  null,
    "next":  "https://api.../pedidos?page=2"
  },
  "meta": {
    "current_page": 1, "from": 1, "last_page": 9,
    "per_page": 15, "to": 15, "total": 128
  }
}
```
</div>
</split-slide>

- 📋 El laboratorio exige **página actual, total de páginas, total de registros y enlaces**, con una estructura **consistente en toda la API**
- 💡 En Laravel esto es automático. En NestJS y ASP.NET Core hay que **construirlo a mano** — otro punto medible del comparativo

---

## Microservicios

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.88rem;">
<div>

### 🏛️ Monolito
- **Un** despliegue, **una** base de datos, **un** repositorio
- Transacciones locales y sencillas
- Refactorizar es barato
- ✅ Es lo correcto para empezar
</div>
<div>

### 🧩 Microservicios
- Servicios pequeños, **desplegables por separado**
- Cada uno **dueño de sus datos**
- Se comunican por HTTP, gRPC o mensajería
- ⚠️ Consistencia eventual, trazabilidad distribuida, más operación
</div>
</split-slide>

- 🚩 **No** se adoptan microservicios por moda. Se adoptan cuando hay un **problema concreto** que el monolito no resuelve: escalado independiente, equipos autónomos, ciclos de despliegue distintos o aislamiento de fallas
- 💡 *"Empiece con un monolito bien modularizado. Si las costuras están bien puestas, extraer un servicio después es mecánico."*

---

## Cuándo extraer un microservicio

<steps>
<step>

### 🎯 Señales legítimas
- La responsabilidad tiene un **perfil de carga distinto** (generación de reportes que satura el servidor web)
- Necesita **escalar por separado** del resto
- Tiene un **ciclo de vida propio**: cambia mucho más o mucho menos que el núcleo
- Es un **punto de falla** que conviene aislar (envío de notificaciones a un proveedor externo)
- Usa una **tecnología distinta** justificadamente

</step>
<step>

### 🧪 El ejercicio del Laboratorio 5

> *"Diseñe y documente el contrato de un microservicio que resuelva una responsabilidad acotada del proyecto (por ejemplo, notificaciones o generación de reportes), justificando técnicamente su separación."*

- No se pide **implementarlo**: se pide **diseñar el contrato** y **justificar** la separación
- El contrato define: endpoints, formato de mensajes, manejo de fallas y **qué pasa si el servicio está caído**

</step>
</steps>

---

## Comunicación entre servicios

<div class="grid">
<div>

### 🔄 Síncrona — HTTP / REST
Simple y universal. El llamador **espera**. Si el destino cae, el origen falla
</div>
<div>

### ⚡ Síncrona — gRPC
Contratos tipados con *protobuf*, binario y rápido. Ideal entre servicios internos
</div>
<div>

### 📬 Asíncrona — Colas
RabbitMQ, Redis, SQS. El origen **no espera**. Tolera caídas del destino
</div>
</div>

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.82rem;">
<div>

```php
// Laravel: trabajo en cola
ProcesarReporte::dispatch($pedido)
    ->onQueue('reportes');

// Laravel 13: enrutamiento centralizado
Queue::route(ProcesarReporte::class,
    connection: 'redis', queue: 'reportes');
```
</div>
<div>

- 🟢 **NestJS** trae microservicios de primera mano: `@nestjs/microservices` con transportes TCP, Redis, NATS, Kafka y gRPC
- 🟣 **ASP.NET Core** se apoya en **.NET Aspire** para orquestar, y en gRPC nativo
</div>
</split-slide>

---

## Documentación efectiva: OpenAPI

- **OpenAPI** (antes *Swagger*) es una especificación para describir una API REST en un archivo legible por máquinas y humanos

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.85rem;">
<div>

### ¿Para qué sirve?
- 📖 Otra persona puede **consumir la API sin leer el código fuente**
- 🧪 Genera un cliente de prueba navegable
- 🤖 Genera **SDK de cliente** automáticamente
- 🤝 Es el **contrato** entre el back-end y los equipos web y móvil
</div>
<div>

### El criterio del curso
> *"La documentación debe permitir que otra persona consuma la API sin leer el código fuente: ese es el criterio de documentación efectiva."*

- No basta con generar el archivo: hay que **enriquecerlo** con descripciones, ejemplos de solicitud y respuesta, y **los códigos de error de cada operación**
</div>
</split-slide>

---

## Herramientas de OpenAPI en Laravel

| Herramienta | Enfoque | Ventaja | Costo |
|:--|:--|:--|:--|
| **Scramble** | **Cero anotaciones**: analiza el código, los Form Requests y los Resources | Documentación al día sin esfuerzo manual | Menos control fino |
| **Scribe** | Analiza el código + anotaciones opcionales | Genera además una página HTML muy completa | Configuración inicial mayor |
| **L5-Swagger** | Anotaciones PHPDoc explícitas | Control total sobre cada detalle | Hay que escribir y mantener las anotaciones |

```bash
composer require dedoc/scramble          # documentación en /docs/api
php artisan scramble:export --path=docs/openapi.yaml
```

- 📋 El laboratorio pide **exportar la especificación** a `docs/openapi.yaml` y **versionarla**

---

## Documentación comparada

<div class="grid">
<div>

### 🔴 Laravel
**Scramble** (cero anotaciones), **Scribe** o **L5-Swagger**.

No viene nada de fábrica: hay que instalar un paquete
</div>
<div>

### 🟢 NestJS
**`@nestjs/swagger`** es **oficial**. Lee los DTO y sus decoradores de `class-validator`

```ts
@ApiProperty({ example: 'PED-0001' })
codigo: string;
```
</div>
<div>

### 🟣 ASP.NET Core
Desde .NET 9 usa **`Microsoft.AspNetCore.OpenApi`** integrado (reemplazó a Swashbuckle por defecto)

```csharp
builder.Services.AddOpenApi();
app.MapOpenApi();
```
</div>
</div>

- 🎯 Criterio comparativo medible: *"esfuerzo para producir la especificación OpenAPI y completitud del resultado"*
- 💡 Aquí NestJS y ASP.NET Core parten con ventaja: la documentación es **de primera mano**

---

## Librerías esenciales para back-end

<div class="grid">
<div>

### 🔴 Laravel
`sanctum` (tokens) · `spatie/laravel-permission` (roles) · `spatie/laravel-query-builder` (filtros) · `dedoc/scramble` (OpenAPI) · `telescope` / `pulse` (observabilidad) · `pint` (formato) · `larastan` (análisis estático)
</div>
<div>

### 🟢 NestJS
`@nestjs/swagger` · `class-validator` + `class-transformer` · `@nestjs/passport` + `passport-jwt` · `@nestjs/throttler` · `helmet` · `nestjs-pino` · `@nestjs/config`
</div>
<div>

### 🟣 ASP.NET Core
`Microsoft.AspNetCore.OpenApi` · `FluentValidation` · `Authentication.JwtBearer` · `Serilog` · `AutoMapper` · `Polly` (reintentos)
</div>
</div>

- 🔍 Antes de agregar una dependencia: **¿está mantenida? ¿cuántas descargas tiene? ¿qué pasa si mañana la abandonan?**
- 🛡️ Y siempre: `composer audit` · `npm audit --omit=dev` · `dotnet list package --vulnerable`

---

## 📋 Laboratorio 5 — API REST y documentación

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.9rem;">
<div>

### Qué se entrega
- Rutas **orientadas a recursos**, con anidamiento
- **Recursos de API**: ningún modelo se serializa directo
- Códigos correctos: `200`, `201` + `Location`, `204`, `400`, `401`, `403`, `404`, `409`, `422`
- **Manejo centralizado** de excepciones, sin trazas de pila
- Metadatos de paginación consistentes
- **OpenAPI** exportado a `docs/openapi.yaml`
- Colección del cliente HTTP con **15+ solicitudes**
- Contrato de un **microservicio** justificado
</div>
<div>

### 🎯 Hito 1 — Porción vertical en NestJS
Con el contrato estable y la referencia terminada en Laravel, se replica en NestJS:

- Los **seis endpoints** con los mismos códigos y estructuras
- Validación, manejo centralizado de errores y autenticación por token
- **Tres pruebas** equivalentes
- **Mediciones**: tiempo de puesta en marcha, archivos y líneas, esfuerzo por aspecto
</div>
</split-slide>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00030_.png);
}
</style>
# Manejo de errores
## Contenidos
- Manejo centralizado de excepciones
- Traducción a códigos HTTP
- Qué nunca debe salir en una respuesta
- Registro y observabilidad

---

## Por qué centralizar el manejo de errores

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.85rem;">
<div>

### ❌ Disperso
```php
public function show($id) {
    try {
        $p = Pedido::find($id);
        if (!$p) {
            return response()->json(
                ['error' => 'no existe'], 404);
        }
        // ...
    } catch (Exception $e) {
        return response()->json(
            ['error' => $e->getMessage()], 500);
    }
}
```
Repetido en **cada** método. Formatos inconsistentes
</div>
<div>

### ✅ Centralizado
```php
public function show(Pedido $pedido) {
    $this->authorize('view', $pedido);
    return new PedidoResource($pedido);
}
```
- El *model binding* lanza `ModelNotFoundException` → **404**
- `authorize` lanza `AuthorizationException` → **403**
- Ambas se traducen **en un solo lugar**
</div>
</split-slide>

---

## bootstrap/app.php — el punto único

<split-slide style="--left: 58%; --right: 42%; --font-size: 0.72rem;">
<div>

```php
// bootstrap/app.php
->withExceptions(function (Exceptions $exceptions): void {

    // Regla de negocio → 409
    $exceptions->render(function (ReglaNegocioException $e) {
        return response()->json([
            'mensaje' => $e->getMessage(),
            'codigo'  => 'REGLA_NEGOCIO',
        ], 409);
    });

    // Recurso inexistente en la API → 404 limpio
    $exceptions->render(function (NotFoundHttpException $e, Request $r) {
        if ($r->is('api/*')) {
            return response()->json(['mensaje' => 'Recurso no encontrado.'], 404);
        }
    });

    // Siempre JSON bajo /api
    $exceptions->shouldRenderJsonWhen(fn (Request $r, Throwable $e) =>
        $r->is('api/*') || $r->expectsJson());

    // No inundar la bitácora
    $exceptions->dontReport([ReglaNegocioException::class]);
});
```
</div>
<div>

- `render()` — cómo se **muestra** al cliente
- `report()` — cómo se **registra**
- `level()` — con qué severidad
- `dontReport()` — qué se ignora
- `context()` — datos comunes a toda bitácora
- `throttle()` — limita el volumen de errores registrados

<br>

- 🆕 Laravel 13: marcar la excepción con la interfaz `ShouldntReport` equivale a `dontReport`
</div>
</split-slide>

---

## Traducción excepción → código de estado

| Excepción | Código | Cuándo ocurre |
|:--|:--:|:--|
| `ValidationException` | **422** | El Form Request rechazó los datos |
| `AuthenticationException` | **401** | No hay token o venció |
| `AuthorizationException` | **403** | El token es válido pero la política lo niega |
| `ModelNotFoundException` | **404** | El *model binding* no encontró el registro |
| `ReglaNegocioException` *(propia)* | **409** | Una regla del dominio lo impide |
| `ThrottleRequestsException` | **429** | Se superó el límite de tasa |
| Cualquier otra | **500** | Error no previsto |

- 🔑 Que exista una **excepción propia por familia de error** es lo que permite traducir sin `if` encadenados

---

## Qué nunca debe salir en una respuesta

<div class="grid">
<div>

### 🚫 Trazas de pila
Revelan rutas del servidor, versiones y estructura interna del código
</div>
<div>

### 🚫 Mensajes de la base de datos
`SQLSTATE[23000]: Duplicate entry...` expone el esquema
</div>
<div>

### 🚫 Datos de otras personas
Un mensaje de error nunca debe filtrar información de otro registro
</div>
<div>

### 🚫 Credenciales o tokens
Ni en la respuesta ni en la bitácora
</div>
</div>

```bash
# .env en producción — no negociable
APP_DEBUG=false
APP_ENV=production
```

- 🚩 `APP_DEBUG=true` en producción expone **valores de configuración sensibles** a cualquier visitante. Es un hallazgo de seguridad, no un descuido menor

---

## Registro de errores y observabilidad

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.85rem;">
<div>

### 📋 Qué registrar
- Marca de tiempo
- Identificador de la persona usuaria o de la sesión
- Dirección de origen
- Operación y resultado
- Identificador de correlación de la petición

### 🙈 Qué enmascarar
- Contraseñas (aunque vengan en el cuerpo)
- Tokens completos
- Datos personales sensibles
</div>
<div>

```php
// Contexto global en toda la bitácora
$exceptions->context(fn () => [
    'usuario' => auth()->id(),
    'ip'      => request()->ip(),
]);
```

- 🚩 Hallazgo típico del Laboratorio 12: *"la bitácora imprime el cuerpo de la solicitud de inicio de sesión"* → hay que **enmascarar** antes de registrar

- 🔭 Herramientas: **Telescope** (desarrollo), **Pulse**, **Nightwatch**, **Sentry** (producción)
</div>
</split-slide>

---

## Manejo de errores comparado

| | 🔴 Laravel | 🟢 NestJS | 🟣 ASP.NET Core |
|:--|:--|:--|:--|
| **Punto central** | `bootstrap/app.php` → `withExceptions` | `ExceptionFilter` global | *Middleware* + `IExceptionHandler` |
| **Excepciones HTTP** | `abort(404)` | `throw new NotFoundException()` | `Results.NotFound()` |
| **Formato estándar** | Propio del equipo | Propio del equipo | **RFC 7807** *Problem Details* nativo |
| **Validación** | 422 automático | 400 por defecto (configurable) | 400 con `ValidationProblemDetails` |
| **Modo depuración** | `APP_DEBUG` | `NODE_ENV` | `ASPNETCORE_ENVIRONMENT` |

- 💡 ASP.NET Core parte con ventaja: **Problem Details** (RFC 7807) es un formato **estandarizado** de error que ya viene implementado
- 🎯 Criterio del **Hito 3**: *"¿qué expone cada framework por omisión ante un error no controlado en modo producción?"*

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00028_.png);
}
</style>
# Autenticación, autorización y sesiones
## Contenidos
- Autenticación por token
- Autorización: roles y políticas
- Defensa en profundidad
- Seguridad en el manejo de sesiones

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

- 🎯 El proyecto exige **al menos tres roles** con permisos diferenciados **y** autorización **a nivel de recurso**
- 💡 No basta con *"solo los administradores pueden ver pedidos"*: hace falta *"cada cliente solo ve **sus** pedidos"*

---

## Sesiones con estado vs. tokens

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.85rem;">
<div>

### 🍪 Sesión con estado (cookie)
- El servidor guarda la sesión y manda un identificador en una **cookie**
- ✅ Revocación inmediata: se borra del servidor
- ✅ La cookie puede ser `HttpOnly` (invisible a JavaScript)
- ❌ El servidor debe **recordar**: complica el escalado horizontal
- ❌ Vulnerable a **CSRF** si no se protege
</div>
<div>

### 🎫 Token (Bearer)
- El servidor emite un token; el cliente lo manda en `Authorization`
- ✅ **Sin estado**: cualquier instancia lo valida
- ✅ Sirve igual para web, móvil y otros servicios
- ❌ Revocar es más difícil (por eso: expiración corta + rotación)
- ❌ Si se guarda en `localStorage`, **XSS lo roba**
</div>
</split-slide>

- 🧭 En este curso usamos **tokens** porque la misma API sirve a la aplicación web **y** a la aplicación móvil Ionic

---

## Sanctum: emisión de tokens

<split-slide style="--left: 58%; --right: 42%; --font-size: 0.72rem;">
<div>

```bash
php artisan install:api   # instala Sanctum y crea routes/api.php
```

```php
// LoginController
public function __invoke(LoginRequest $request): JsonResponse
{
    $usuario = User::where('email', $request->email)->first();

    // Mensaje idéntico exista o no la cuenta
    if (! $usuario || ! Hash::check($request->password, $usuario->password)) {
        throw ValidationException::withMessages([
            'email' => ['Las credenciales proporcionadas son incorrectas.'],
        ]);
    }

    $token = $usuario->createToken(
        name: 'api',
        abilities: ['pedidos:leer', 'pedidos:escribir'],
        expiresAt: now()->addMinutes(30)
    );

    return response()->json(['token' => $token->plainTextToken], 200);
}
```

```php
Route::post('/login', LoginController::class)->middleware('throttle:5,1');
Route::middleware('auth:sanctum')->group(function () {
    Route::apiResource('pedidos', PedidoController::class);
});
```
</div>
<div>

### 🔒 Tres controles en una pantalla
1. **Expiración** del token (30 min)
2. **Capacidades** asociadas (*abilities*)
3. **Limitación de intentos** (`throttle:5,1`)

<br>

- 🚩 El mensaje de error **no distingue** entre cuenta inexistente y contraseña incorrecta: distinguirlas permite **enumerar cuentas**
</div>
</split-slide>

---

## Contraseñas

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.8rem;">
<div>

```php
// Laravel deriva la contraseña automáticamente
protected function casts(): array
{
    return ['password' => 'hashed'];  // bcrypt / argon2
}

// Verificación
Hash::check($request->password, $usuario->password);
```

```php
// Política de complejidad
use Illuminate\Validation\Rules\Password;

'password' => ['required', 'confirmed',
    Password::min(12)
        ->letters()->mixedCase()
        ->numbers()->symbols()
        ->uncompromised(),   // ¿apareció en una filtración?
];
```
</div>
<div>

- 🔑 **Nunca** se guarda la contraseña: se guarda su **derivación** con `bcrypt` o `argon2id`
- 🧂 El algoritmo incluye una **sal** distinta por contraseña: dos personas con la misma clave tienen derivaciones distintas
- ✅ `uncompromised()` consulta la base de *Have I Been Pwned* sin enviar la contraseña (k-anonimato)

- 🚩 `md5()` y `sha1()` **no** son algoritmos de contraseñas: son demasiado rápidos
</div>
</split-slide>

---

## Autorización: roles y políticas

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.75rem;">
<div>

```php
// Política por recurso
class PedidoPolicy
{
    public function view(User $user, Pedido $pedido): bool
    {
        return $user->hasRole('admin')
            || $pedido->cliente->user_id === $user->id;
    }

    public function delete(User $user, Pedido $pedido): bool
    {
        return $user->hasRole('admin')
            && $pedido->estado === 'borrador';
    }
}
```

```php
// Capa 1 — en el controlador
public function show(Pedido $pedido): PedidoResource
{
    $this->authorize('view', $pedido);   // 403 si no procede
    return new PedidoResource($pedido->load('detalles'));
}
```

```php
// Laravel 13 — con atributos
#[Authorize('view', 'pedido')]
public function show(Pedido $pedido) { }
```
</div>
<div>

### 🎭 Roles vs. políticas
- **Rol** — *¿puede esta persona ver pedidos en general?*
- **Política** — *¿puede ver **este** pedido?*

<br>

- 🚩 Solo con roles se produce la vulnerabilidad más común de la OWASP: **control de acceso roto**. `GET /api/pedidos/42` devolvería el pedido de cualquiera con solo cambiar el número
</div>
</split-slide>

---

## Defensa en profundidad: verificar en dos capas

<steps>
<step>

```php
// Capa 1 — ruta o controlador
public function show(Pedido $pedido): PedidoResource
{
    $this->authorize('view', $pedido);
    return new PedidoResource($this->service->obtener($pedido, auth()->user()));
}
```

```php
// Capa 2 — dentro del servicio
public function obtener(Pedido $pedido, User $actor): Pedido
{
    Gate::forUser($actor)->authorize('view', $pedido);  // responde 403
    return $pedido->load('detalles');
}
```

</step>
<step>

### ¿Por qué dos veces?

> *"Un error frecuente es proteger la ruta pero no el servicio: si otro componente invoca el servicio directamente, la restricción se evade."*

- Un comando de consola, un trabajo en cola o **otro controlador** pueden llamar al servicio **sin pasar por la ruta protegida**
- 📋 El laboratorio lo evalúa de forma **explícita**: hay que **demostrar** que la invocación directa al servicio también es rechazada

</step>
</steps>

---

## Seguridad en el manejo de sesiones

<div class="grid">
<div>

### ⏱️ Expiración corta
El token vive minutos, no meses. Un token robado tiene ventana limitada
</div>
<div>

### 🔄 Rotación en la renovación
Al renovar, la credencial anterior **se invalida**
</div>
<div>

### 🗑️ Revocación al cerrar sesión
`currentAccessToken()->delete()`
</div>
<div>

### 🎯 Capacidades mínimas
El token solo puede lo que necesita: `pedidos:leer` ≠ acceso total
</div>
<div>

### 🔐 Almacenamiento seguro
En móvil: almacenamiento **cifrado** del dispositivo, no `localStorage`
</div>
<div>

### 🚫 Nunca en la URL
Queda en bitácoras del servidor, historial y encabezado `Referer`
</div>
</div>

```php
// Cierre de sesión con revocación
public function logout(Request $request): Response
{
    $request->user()->currentAccessToken()->delete();
    return response()->noContent();   // 204
}
```

---

## Controles de la API (Laboratorio 13)

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.7rem;">
<div>

```php
// config/cors.php — orígenes explícitos
'paths'   => ['api/*'],
'allowed_origins' => ['https://proyecto.ucr.ac.cr',
                      'http://localhost:5173'],
'allowed_methods' => ['GET','POST','PUT','DELETE'],
'allowed_headers' => ['Authorization','Content-Type'],
'supports_credentials' => true,   // incompatible con '*'
```

```php
// Limitación de tasa: general y de autenticación
RateLimiter::for('api', fn ($r) =>
    Limit::perMinute(60)->by($r->user()?->id ?: $r->ip()));

RateLimiter::for('login', fn ($r) =>
    Limit::perMinute(5)->by($r->ip()));   // → 429
```

```http
Content-Security-Policy: default-src 'self'; frame-ancestors 'none'
Strict-Transport-Security: max-age=31536000; includeSubDomains
X-Content-Type-Options: nosniff
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: geolocation=(), camera=()
```
</div>
<div>

### 🚩 Malentendido frecuente
> *"La política de origen cruzado **no es un control de autorización**: protege al navegador de la persona usuaria, no a la API. Un cliente que no sea un navegador la ignora."*

<br>

- El **comodín `*` con credenciales** es rechazado por los navegadores y es una configuración insegura

<br>

- 🆕 Laravel 13 formaliza `PreventRequestForgery`, con verificación **por origen** además del token CSRF
</div>
</split-slide>

---

## OWASP: los cinco hallazgos que verificaremos

| Vulnerabilidad | Cómo se manifiesta en el proyecto | Mitigación |
|:--|:--|:--|
| **Control de acceso roto** | `GET /api/pedidos/42` devuelve el pedido de otra persona | Verificar la pertenencia **en el servicio** |
| **Inyección** | Consultas construidas por concatenación | Parámetros vinculados; el ORM ya lo hace |
| **Exposición de datos sensibles** | La respuesta incluye la contraseña derivada o campos internos | **Recursos de API** explícitos, nunca la entidad |
| **Configuración deficiente** | Trazas visibles, CORS abierto, documentación pública sin control | Perfiles diferenciados por entorno |
| **Fallo de autenticación** | Sin límite de intentos, tokens sin expiración | `throttle` + expiración corta con rotación |

- ⚖️ **Advertencia**: las pruebas se realizan **únicamente sobre la aplicación del propio equipo**, en entorno local. Probar sistemas de terceros sin autorización es una falta grave con consecuencias legales

---

## Autenticación comparada

| | 🔴 Laravel 13 | 🟢 NestJS | 🟣 ASP.NET Core |
|:--|:--|:--|:--|
| **Paquete** | **Sanctum** (con `install:api`) | `@nestjs/passport` + `passport-jwt` | `JwtBearer` (integrado) |
| **Tipo de token** | Opaco en base de datos, o JWT con Passport | **JWT** | **JWT** |
| **Revocación** | ✅ Nativa: se borra la fila | ❌ Requiere lista de revocación | ❌ Requiere lista de revocación |
| **Autorización** | *Gates* y *Policies* | *Guards* + `@Roles()` | Políticas + `[Authorize]` |
| **Contraseñas** | `Hash::make` (bcrypt/argon2) | `bcrypt` / `argon2` manual | `PasswordHasher<T>` (PBKDF2) |
| **Límite de intentos** | ✅ `throttle` integrado | `@nestjs/throttler` | *Rate limiting* integrado |

- 💡 Ventaja real de Sanctum: los tokens **viven en la base de datos**, así que **revocarlos es inmediato**. Con JWT puro hay que esperar a que expiren

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00023_.png);
}
</style>
# Pruebas de unidad
## Contenidos
- Qué es una prueba automatizada
- Unidad vs. integración
- Dobles de prueba
- Cobertura y aislamiento

---

## ¿Qué es la automatización de pruebas?

> *"Consiste en el uso de software especial —casi siempre separado del software que se prueba— para controlar la ejecución de pruebas y la comparación entre los resultados obtenidos y los resultados esperados."*

<div class="grid">
<div>

### 🔬 Prueba unitaria
Verifica **una unidad** de código en aislamiento. Rápida y precisa: cuando falla, sabes **exactamente** dónde
</div>
<div>

### 🔗 Prueba de integración
Verifica que **varias piezas** funcionan juntas: servicio + base de datos, controlador + servicio
</div>
<div>

### 🌐 Prueba de extremo a extremo
Recorre el sistema completo desde la interfaz. Lenta, frágil, pero valiosa en los flujos críticos
</div>
</div>

- 🚩 *"Una prueba unitaria de la capa de servicio **no debe tocar la base de datos**: eso la convierte en una prueba de integración, más lenta y menos precisa para localizar el defecto."*

---

## Pest: el ejecutor de pruebas de Laravel

<split-slide style="--left: 52%; --right: 48%; --font-size: 0.75rem;">
<div>

```php
// tests/Feature/PedidoApiTest.php
it('crea un pedido y devuelve 201 con Location', function () {
    $usuario = User::factory()->create();
    $cliente = Cliente::factory()->create();

    actingAs($usuario)
        ->postJson('/api/pedidos', [
            'codigo'     => 'PED-0001',
            'cliente_id' => $cliente->id,
            'detalles'   => [['producto_id' => 1, 'cantidad' => 2]],
        ])
        ->assertCreated()                    // 201
        ->assertHeader('Location')
        ->assertJsonStructure(['data' => ['id','codigo','estado','total']]);
});

it('impide ver el pedido de otra persona usuaria', function () {
    $otro   = User::factory()->create();
    $pedido = Pedido::factory()->create();

    actingAs($otro)
        ->getJson("/api/pedidos/{$pedido->id}")
        ->assertForbidden();                 // 403
});
```
</div>
<div>

- **Pest** es el ejecutor por omisión desde Laravel 11. Se apoya en **PHPUnit** por debajo

```bash
php artisan test
php artisan test --filter=Pedido
php artisan test --coverage
php artisan test --parallel
```

- 🧪 Laravel provee una API que permite hacer **peticiones HTTP y analizar la salida** sin levantar un servidor
</div>
</split-slide>

---

## Dobles de prueba

<split-slide style="--left: 55%; --right: 45%; --font-size: 0.72rem;">
<div>

```php
// tests/Unit/PedidoServiceTest.php
it('no confirma un pedido sin detalle', function () {
    // El inventario es un doble: no queremos su comportamiento real
    $inventario = Mockery::mock(InventarioService::class);
    $inventario->shouldNotReceive('descontar');

    $servicio = new PedidoService($inventario);
    $pedido   = new Pedido(['estado' => 'borrador']);

    expect(fn () => $servicio->confirmar($pedido))
        ->toThrow(ReglaNegocioException::class,
                  'No se puede confirmar un pedido sin detalle.');
});
```

```php
it('descuenta inventario al confirmar', function () {
    $inventario = Mockery::mock(InventarioService::class);
    $inventario->shouldReceive('descontar')->once();  // se espera 1 llamada

    // ...
});
```
</div>
<div>

### 🎭 Tipos de doble
- **Dummy** — se pasa pero no se usa
- **Stub** — devuelve respuestas fijas
- **Mock** — además **verifica** que fue llamado como se esperaba
- **Fake** — implementación simplificada real (base de datos en memoria)

<br>

- 🎯 Los dobles permiten probar el servicio **sin base de datos, sin red y sin el inventario real**
</div>
</split-slide>

---

## Qué probar

<div class="grid">
<div>

### 😊 Camino feliz
La operación funciona con datos válidos y permisos correctos
</div>
<div>

### 🚫 Cada regla de negocio
Una prueba **por cada regla**: la violación produce el error esperado
</div>
<div>

### 🧱 Casos límite
Colección vacía, valor máximo, cero, fecha frontera, decimal con redondeo
</div>
<div>

### 🔐 Acceso denegado
Rol incorrecto, recurso ajeno, token vencido → 401 / 403
</div>
<div>

### 📋 Códigos y estructura
Que la API devuelva el código correcto y la forma de respuesta acordada
</div>
<div>

### 🔄 Reversión
Que un fallo intermedio no deje registros parciales
</div>
</div>

- 📋 El Laboratorio 6 exige **12 pruebas unitarias** de servicio con dobles + **6 pruebas de API** = **18 mínimo**

---

## Cobertura y aislamiento

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.82rem;">
<div>

```xml
<!-- phpunit.xml -->
<php>
  <env name="DB_CONNECTION" value="sqlite"/>
  <env name="DB_DATABASE" value=":memory:"/>
</php>
```

```php
// Base de datos limpia en cada prueba
uses(RefreshDatabase::class);
```

```bash
php artisan test --coverage --min=70
```
</div>
<div>

### 📊 La cobertura es un piso, no una meta
- **70 %** en la capa de servicio es el mínimo del proyecto
- 100 % de cobertura **no** significa 0 defectos: significa que cada línea se ejecutó, no que se verificó su comportamiento

### 🧼 Aislamiento
> *"Una suite que depende de los datos de desarrollo deja de ser confiable en cuanto alguien los modifica."*
</div>
</split-slide>

---

## Pruebas comparadas

| | 🔴 Laravel 13 | 🟢 NestJS | 🟣 ASP.NET Core |
|:--|:--|:--|:--|
| **Ejecutor** | **Pest** (sobre PHPUnit) | **Jest** (o Vitest) | **xUnit** (o NUnit / MSTest) |
| **Dobles** | Mockery, `$this->mock()` | `jest.mock()`, `TestingModule` | Moq, NSubstitute |
| **Pruebas de API** | `getJson()`, `assertCreated()` | `supertest` | `WebApplicationFactory<T>` |
| **Base de datos de prueba** | `RefreshDatabase` + SQLite en memoria | Contenedor o SQLite | EF Core InMemory o *Testcontainers* |
| **Cobertura** | `--coverage` (Xdebug/PCOV) | `--coverage` (Jest) | `coverlet` |
| **Configuración** | Cero: viene listo | Cero: viene listo | Proyecto de pruebas aparte |

- 🎯 Criterio comparativo: *"herramienta utilizada y esfuerzo para escribir tres pruebas equivalentes"*

---

## 📋 Laboratorio 6 — Autenticación, autorización y pruebas

<split-slide style="--left: 50%; --right: 50%; --font-size: 0.88rem;">
<div>

### 🔐 Seguridad
- Registro, inicio y cierre de sesión por token, con **expiración**, **capacidades** y **revocación**
- Contraseñas derivadas con política de complejidad
- **Tres roles** + políticas **por recurso**
- Autorización verificada **en dos capas**, demostrando que la invocación directa al servicio también se rechaza
- Limitación de intentos y respuesta que **no permite enumerar cuentas**
</div>
<div>

### 🧪 Pruebas
- **12 pruebas unitarias** de servicio con dobles: camino feliz, cada regla y **dos casos límite**
- **6 pruebas de API**: códigos de estado, estructura de respuesta y acceso permitido/denegado por rol
- Base de datos de pruebas **aislada**
- Reporte de cobertura **≥ 70 %** en la capa de servicio
</div>
</split-slide>

---

<!-- _class: cover -->
<style scoped>
section {
  --cover: url(../assets/img_00017_.png);
}
</style>
# El estudio comparativo
## Contenidos
- El contrato común
- Criterios de comparación
- Hitos y fechas
- Cómo se evalúa

---

## El contrato común

- Las tres implementaciones deben cumplir **exactamente lo mismo**. Sin eso, la comparación no mide nada

| Operación | Método y ruta | Respuesta esperada |
|:--|:--|:--|
| Inicio de sesión | `POST /api/login` | `200` con token · `422` datos inválidos · `401` credenciales incorrectas |
| Listado paginado | `GET /api/recursos?page=&per_page=&q=` | `200` con datos y metadatos · `401` sin token |
| Detalle | `GET /api/recursos/{id}` | `200` · `404` si no existe · `403` si no le corresponde |
| Creación | `POST /api/recursos` | `201` con encabezado `Location` · `422` datos inválidos |
| Actualización | `PUT /api/recursos/{id}` | `200` · `404` · `422` |
| Eliminación | `DELETE /api/recursos/{id}` | `204` · `404` · `409` si tiene dependencias |

- 📐 Una entidad con **máximo 8 campos y una relación**. **Misma** base de datos y **mismos** datos de prueba en las tres

---

## Los diez criterios de comparación

| Criterio | Cómo se mide |
|:--|:--|
| Tiempo de puesta en marcha | Minutos desde el proyecto vacío hasta el primer endpoint respondiendo |
| Tamaño de la porción | Archivos y líneas de código **propias**, excluyendo dependencias |
| Esfuerzo de validación | Líneas y archivos para validar la entrada y devolver errores por campo |
| Esfuerzo de autenticación | Líneas y dependencias para emitir, validar y revocar el token |
| Documentación automática | Esfuerzo para producir OpenAPI y completitud del resultado |
| Pruebas | Herramienta y esfuerzo para escribir tres pruebas equivalentes |
| Rendimiento | Peticiones por segundo y **latencia p95** bajo carga idéntica |
| Huella de ejecución | Memoria residente en reposo y tamaño del artefacto desplegable |
| *Seguridad por omisión* | Protecciones activas sin configuración adicional |
| *Ecosistema y curva* | Valoración argumentada con evidencia de la experiencia del equipo |

- ⚠️ *"Una medición no reproducible se evalúa como no presentada."* Misma máquina, misma base de datos, mismo escenario

---

## Cómo medir el rendimiento

<split-slide style="--left: 52%; --right: 48%; --font-size: 0.78rem;">
<div>

```bash
# Prueba de carga idéntica en los tres
# k6, autocannon, bombardier o wrk

k6 run --vus 50 --duration 60s carga.js

autocannon -c 50 -d 60 \
  -H "Authorization: Bearer $TOKEN" \
  http://localhost:8000/api/recursos
```

```bash
# Huella de memoria en reposo
ps -o rss= -p $(pgrep -f "php artisan serve")
```

```bash
# Tamaño del artefacto desplegable
du -sh vendor/                  # Laravel
du -sh dist/ node_modules/      # NestJS
dotnet publish -c Release       # ASP.NET Core
```
</div>
<div>

### 📏 Qué reportar
- **Peticiones por segundo**
- **Latencia p95** (no el promedio: el promedio esconde la cola)
- Condiciones: máquina, versión, datos, concurrencia

<br>

- 🚩 Reportar el **procedimiento completo**. Un número sin procedimiento no es una medición: es una opinión con decimales
</div>
</split-slide>

---

## Hitos del estudio comparativo

| Hito | Fecha límite | Entregable |
|:--|:--|:--|
| **Hito 0** — Contrato | Domingo 30 de agosto | `docs/contrato-comparativo.md` con entidad, endpoints y datos |
| **Hito 1** — NestJS | Domingo 4 de octubre | Porción vertical funcional, tres pruebas y mediciones |
| **Hito 2** — ASP.NET Core | Domingo 25 de octubre | Porción vertical funcional, tres pruebas y mediciones |
| **Hito 3** — Seguridad | Domingo 8 de noviembre | Contraste de las protecciones por omisión en los tres |
| **Informe y presentación** | Jueves 12 de noviembre | Informe comparativo y exposición de 15 min (**investigación, 5 %**) |

- ⚖️ El informe y su presentación constituyen la **investigación del curso**: 5 % de la nota final, evaluado de forma independiente
- 📊 Las porciones verticales funcionales se evalúan dentro del **componente D** del proyecto (15 puntos)

---

## Panorama de los tres frameworks

| | 🔴 Laravel 13 | 🟢 NestJS 11 | 🟣 ASP.NET Core |
|:--|:--|:--|:--|
| **Lenguaje** | PHP 8.3+ (8.4 en la práctica) | TypeScript / Node.js | C# / .NET |
| **Versión de referencia** | 17 mar 2026 | NestJS 11 | .NET 10 LTS (nov 2025) |
| **Arquitectura** | MVC + capas | Modular + inyección de dependencias | MVC / *Minimal API* |
| **ORM** | Eloquent (*Active Record*) | TypeORM / Prisma | EF Core (*Data Mapper*) |
| **Validación** | Form Request | DTO + `class-validator` | Data Annotations / FluentValidation |
| **Auth** | Sanctum (incluido) | Passport + JWT | JwtBearer (integrado) |
| **OpenAPI** | Paquete externo | `@nestjs/swagger` oficial | `Microsoft.AspNetCore.OpenApi` |
| **Pruebas** | Pest | Jest | xUnit |
| **Fuerte en** | Velocidad de desarrollo | Tipado, microservicios | Rendimiento, ecosistema empresarial |

---

## Cómo elegir un back-end (más allá del curso)

<steps>
<step>

### 🔍 Criterios técnicos
- ¿El **rendimiento** es un requisito del producto o una preferencia estética?
- ¿Cuánto pesa la **huella de ejecución** en el costo de infraestructura?
- ¿Necesito **tipado estático** por el tamaño del equipo o del dominio?
- ¿Qué trae **por omisión** y qué tendré que construir?

</step>
<step>

### 👥 Criterios de equipo y negocio
- ¿Qué **sabe ya** el equipo? ¿Qué se contrata en el mercado local?
- ¿Cuál es el **calendario de soporte**? ¿Hay versiones LTS?
- ¿Cuál es el **costo de salida** si nos equivocamos?

> 🚩 Mala razón: *"es lo más rápido en un banco de pruebas sintético"*
> ✅ Buena razón: *"resuelve nuestro problema, el equipo lo sostiene y tiene soporte hasta 2028"*

</step>
</steps>

---

## Errores más frecuentes

<div class="grid">
<div>

### 🚩 Lógica en el controlador
El error más frecuente del Lab 4. El controlador **traduce**, no decide
</div>
<div>

### 🚩 Migración no reversible
Si el `rollback` falla, el esquema no es reproducible
</div>
<div>

### 🚩 N+1 sin medir
Se exige el **dato numérico**, no la afirmación
</div>
<div>

### 🚩 Devolver el modelo directo
Acopla la API al esquema y filtra campos sensibles
</div>
<div>

### 🚩 Autorizar solo en la ruta
Si otro componente llama al servicio, la restricción se evade
</div>
<div>

### 🚩 `.env` en el repositorio
La falta más frecuente del Lab 2
</div>
<div>

### 🚩 `update($request->all())`
Asignación masiva sin control
</div>
<div>

### 🚩 Prueba unitaria que toca la base de datos
Deja de ser unitaria: es de integración
</div>
<div>

### 🚩 200 con un error en el cuerpo
El cliente no puede reaccionar sin leer el JSON
</div>
</div>

---

## Actividad de cierre

- En equipo, tomen **una** entidad de su proyecto y respondan por escrito:

<div class="grid">
<div>

### 1️⃣ Recorrido
Dibujen el recorrido de un `POST` desde la ruta hasta la base de datos, nombrando la clase de cada paso
</div>
<div>

### 2️⃣ Códigos
Enumeren **todos** los códigos de estado que ese endpoint puede devolver y qué los provoca
</div>
<div>

### 3️⃣ Reglas
Identifiquen dos **validaciones** y dos **reglas de negocio**. ¿Por qué cada una está donde está?
</div>
<div>

### 4️⃣ Pruebas
Escriban el **nombre** de las cinco pruebas que necesitaría ese endpoint. Solo los nombres
</div>
</div>

- 💡 Si no logran nombrar las cinco pruebas, probablemente el endpoint todavía no está bien definido

---

## Referencias

- Laravel. [Documentación 13.x](https://laravel.com/docs/13.x) · [Notas de versión](https://laravel.com/docs/13.x/releases) · [Eloquent](https://laravel.com/docs/13.x/eloquent) · [Recursos de API](https://laravel.com/docs/13.x/eloquent-resources) · [Validación](https://laravel.com/docs/13.x/validation) · [Manejo de errores](https://laravel.com/docs/13.x/errors) · [Paginación](https://laravel.com/docs/13.x/pagination) · [Sanctum](https://laravel.com/docs/13.x/sanctum) · [Autorización](https://laravel.com/docs/13.x/authorization) · [Pruebas](https://laravel.com/docs/13.x/testing)
- Laravel News. [Laravel 13 Released](https://laravel-news.com/laravel-13-released)
- laravel/framework. [Issue #59564 — Laravel 13.3+ requiere PHP 8.4 por Symfony 8](https://github.com/laravel/framework/issues/59564)
- NestJS. [Documentación oficial](https://docs.nestjs.com/) · [Validación](https://docs.nestjs.com/techniques/validation) · [OpenAPI](https://docs.nestjs.com/openapi/introduction) · [Microservicios](https://docs.nestjs.com/microservices/basics)
- Microsoft. [ASP.NET Core](https://learn.microsoft.com/aspnet/core/) · [Entity Framework Core](https://learn.microsoft.com/ef/core/) · [Anuncio de .NET 10](https://devblogs.microsoft.com/dotnet/announcing-dotnet-10/)
- Dedoc. [Scramble — generador de OpenAPI para Laravel](https://scramble.dedoc.co/)
- MDN. [Métodos HTTP](https://developer.mozilla.org/es/docs/Web/HTTP/Methods) · [Códigos de estado](https://developer.mozilla.org/es/docs/Web/HTTP/Status) · [Encabezados](https://developer.mozilla.org/es/docs/Web/HTTP/Headers)
- OWASP. [Top Ten](https://owasp.org/www-project-top-ten/) · [API Security Top 10](https://owasp.org/API-Security/)
- Documentos del curso: *IF0009 — Laboratorios v4* y *IF0009 — Proyecto Final v3* · Presentación *Intro Laravel* (Prog. Web II, TUPAR-UNICEN)

<script src="../assets/steps.js"></script>
<script src="../assets/image-modal.js"></script>
