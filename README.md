# Biblioteca 2

Sistema bibliotecario implementado en Supabase (PostgreSQL) bajo el esquema `biblioteca_2`. Contiene 5 tablas relacionadas y 10 registros de ejemplo en cada una.

## Esquema

```text
biblioteca_2
├── autores
├── categorias
├── libros
├── usuarios
└── prestamos
```

## Tablas

### `autores`

| Columna | Tipo | Restricciones |
|---|---|---|
| id | bigint | PK, identity |
| nombre | text | NOT NULL |
| nacionalidad | text | — |
| fecha_nacimiento | date | — |

### `categorias`

| Columna | Tipo | Restricciones |
|---|---|---|
| id | bigint | PK, identity |
| nombre | text | NOT NULL, UNIQUE |
| descripcion | text | — |

### `libros`

| Columna | Tipo | Restricciones |
|---|---|---|
| id | bigint | PK, identity |
| titulo | text | NOT NULL |
| autor_id | bigint | NOT NULL, FK → autores.id |
| categoria_id | bigint | NOT NULL, FK → categorias.id |
| isbn | text | UNIQUE |
| anio_publicacion | integer | — |
| ejemplares | integer | NOT NULL, default 1 |
| disponible | boolean | NOT NULL, default true |

### `usuarios`

| Columna | Tipo | Restricciones |
|---|---|---|
| id | bigint | PK, identity |
| nombre | text | NOT NULL |
| email | text | NOT NULL, UNIQUE |
| telefono | text | — |
| rol | text | NOT NULL, default 'lector' |
| fecha_registro | date | NOT NULL, default CURRENT_DATE |

### `prestamos`

| Columna | Tipo | Restricciones |
|---|---|---|
| id | bigint | PK, identity |
| libro_id | bigint | NOT NULL, FK → libros.id |
| usuario_id | bigint | NOT NULL, FK → usuarios.id |
| fecha_prestamo | date | NOT NULL, default CURRENT_DATE |
| fecha_devolucion_esperada | date | NOT NULL |
| fecha_devolucion_real | date | — |
| estado | text | NOT NULL, default 'prestado' |

## Relaciones

```text
autores    (1) ──── (N) libros
categorias (1) ──── (N) libros
libros     (1) ──── (N) prestamos
usuarios   (1) ──── (N) prestamos
```

- `libros.autor_id` → `autores.id`
- `libros.categoria_id` → `categorias.id`
- `prestamos.libro_id` → `libros.id`
- `prestamos.usuario_id` → `usuarios.id`

## Datos de ejemplo

| Tabla | Registros |
|---|---|
| autores | 10 |
| categorias | 10 |
| libros | 10 |
| usuarios | 10 |
| prestamos | 10 |

## Consulta de ejemplo

```sql
SELECT l.titulo,
       a.nombre AS autor,
       c.nombre AS categoria,
       u.nombre AS usuario,
       p.estado
FROM biblioteca_2.prestamos p
JOIN biblioteca_2.libros l ON l.id = p.libro_id
JOIN biblioteca_2.autores a ON a.id = l.autor_id
JOIN biblioteca_2.categorias c ON c.id = l.categoria_id
JOIN biblioteca_2.usuarios u ON u.id = p.usuario_id
ORDER BY p.id;
```

## Índices

- `idx_libros_autor` (`libros.autor_id`)
- `idx_libros_categoria` (`libros.categoria_id`)
- `idx_prestamos_libro` (`prestamos.libro_id`)
- `idx_prestamos_usuario` (`prestamos.usuario_id`)

## Creación

El esquema se creó en Supabase mediante las migraciones:

- `create_biblioteca_2_schema`
- `seed_biblioteca_2_data`

> Nota: las tablas tienen RLS deshabilitado por defecto. Habilítalo y define políticas antes de exponerlas a la API.
