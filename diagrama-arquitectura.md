# Diagrama de Arquitectura Docker - Práctica 3

## Redes Docker Creadas

### 1. Red net-wp (Bridge Network)
- **Contenedor mysql**: MySQL 8.4.8 
  - Variables de entorno:
    - MYSQL_ROOT_PASSWORD=root
    - MYSQL_DATABASE=wordpress
    - MYSQL_USER=wpuser
    - MYSQL_PASSWORD=wp123
  - Volumen: 5871fd2ae5727928bb53d77de92b636d3b939c646369fe7c150b7be33454e270
  - Puerto: 3306 (interno)

- **Contenedor wordpress**: WordPress 6.9.4
  - Variables de entorno:
    - WORDPRESS_DB_HOST=mysql
    - WORDPRESS_DB_USER=wpuser
    - WORDPRESS_DB_PASSWORD=wp123
    - WORDPRESS_DB_NAME=wordpress
  - Puerto: 9400 (externo) → 80 (interno)
  - Volumen: 418238dfca1f740e59f2e3d442816b44c1afb3378db2ce9fcd6603ac4f3f48a5

### 2. Red net-drupal (Bridge Network)
- **Contenedor server-postgres**: PostgreSQL
  - Variables de entorno:
    - POSTGRES_DB=db_drupal
    - POSTGRES_PASSWORD=12345
    - POSTGRES_USER=user_drupal
  - Volumen: vol-postgres
  - Puerto: 5432 (interno)

- **Contenedor client-postgres**: pgAdmin 4
  - Variables de entorno:
    - PGADMIN_DEFAULT_PASSWORD=54321
    - PGADMIN_DEFAULT_EMAIL=admin@epn.edu.ec
  - Puerto: 9500 (externo) → 80 (interno)

- **Contenedor server-drupal**: Drupal
  - Volúmenes nombrados:
    - drupal_files → /var/www/html/sites/default/files
    - drupal_modules → /var/www/html/modules
    - drupal_themes → /var/www/html/themes
    - drupal_profiles → /var/www/html/profiles
  - Puerto: 9700 (externo) → 80 (interno)

## Volúmenes Nombrados Creados

### Volúmenes Drupal:
- drupal_files (archivos de Drupal)
- drupal_modules (módulos de Drupal)
- drupal_themes (temas de Drupal)
- drupal_profiles (perfiles de Drupal)

### Volúmenes WordPress:
- Volumen anónimo para WordPress (418238dfca1f740e59f2e3d442816b44c1afb3378db2ce9fcd6603ac4f3f48a5)

### Volúmenes MySQL:
- Volumen anónimo para MySQL (5871fd2ae5727928bb53d77de92b636d3b939c646369fe7c150b7be33454e270)

### Volúmenes PostgreSQL:
- vol-postgres (datos de PostgreSQL)

## Diagrama Visual

```
┌─────────────────────────────────────────────────────────────────────────────────────────┐
│                           DOCKER HOST                                  │
├─────────────────────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐   │
│  │   RED NET-WP   │    │ RED NET-DRUPAL│    │   OTRAS REDES  │   │
│  │                 │    │                 │    │                 │   │
│  │ ┌─────────────┐ │    │ ┌─────────────┐ │    │ ┌─────────────┐ │   │
│  │ │    MYSQL    │ │    │ │ SERVER-POSTGRES│ │    │ │   ANDY      │ │   │
│  │ │ 3306 (int) │ │    │ │   5432 (int) │ │    │ │             │ │   │
│  │ └─────────────┘ │    │ └─────────────┘ │    │ └─────────────┘ │   │
│  │       │           │    │       │           │    │                 │   │
│  │ ┌─────────────┐ │    │ ┌─────────────┐ │    │ ┌─────────────┐ │   │
│  │ │ WORDPRESS  │ │    │ │ CLIENT-POSTGRES│ │    │ │ NET-CURSO01 │ │   │
│  │ │ 9400→80   │ │    │ │ 9500→80     │ │    │ │             │ │   │
│  │ └─────────────┘ │    │ └─────────────┘ │    │ └─────────────┘ │   │
│  └─────────────────┘    └─────────────────┘    └─────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │                    VOLÚMENES DOCKER                        │   │
│  │                                                             │   │
│  │ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐          │   │
│  │ │vol-postgres │ │drupal_files │ │wordpress_vol│          │   │
│  │ └─────────────┘ └─────────────┘ └─────────────┘          │   │
│  │       │               │               │                     │   │
│  │ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐          │   │
│  │ │drupal_themes│ │drupal_modules│ │drupal_profiles│          │   │
│  │ └─────────────┘ └─────────────┘ └─────────────┘          │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────────────────────┘
```

## Resumen de Puertos Externos

| Servicio | Puerto Externo | Contenedor | Red |
|----------|----------------|-------------|-------|
| WordPress | 9400 | wordpress | net-wp |
| pgAdmin | 9500 | client-postgres | net-drupal |
| Drupal | 9700 | server-drupal | net-drupal |

## Conexiones de Datos

### WordPress + MySQL
- WordPress se conecta a MySQL a través del nombre "mysql" en la red net-wp
- Datos persistentes en volúmenes Docker anónimos

### Drupal + PostgreSQL
- Drupal se conecta a PostgreSQL a través del nombre "server-postgres" en la red net-drupal
- pgAdmin se conecta a PostgreSQL para administración web
- Datos persistentes en volúmenes nombrados específicos


