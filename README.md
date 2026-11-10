🌐 Available in: [Español](#-español) | [English](#-english)

# 🇪🇸 Español

# MergeGame — Contenido estático

Este repositorio almacena **exclusivamente archivos estáticos** utilizados por **MergeGame**, un videojuego de tipo *Merge & Story* desarrollado en el contexto de LifeSync-Games. Contiene las configuraciones de los mundos y los recursos gráficos asociados: características de cada mundo, ítems, personajes, disposición inicial de las grillas, tienda, reglas de fusión y recompensas, entre otros.

El repositorio **no contiene el código fuente del videojuego** ni los datos de progreso de los jugadores.

## Estructura del repositorio

```text
.
├── world.json
├── World/
│   ├── World1/
│   │   ├── world1.json
│   │   └── ... recursos gráficos del mundo 1
│   └── World2/
│       ├── world2.json
│       └── ... recursos gráficos del mundo 2
└── docs/
    ├── CREAR_MUNDO.md
    └── CREATE_WORLD.md
```

La estructura interna de cada directorio puede incluir subcarpetas de imágenes para distintos tipos de ítems.

## Registro de mundos (`world.json`)

El archivo `world.json` contiene una lista de los mundos disponibles y la ubicación de sus archivos de configuración:

```json
[
  {
    "dir": "World/World1",
    "file": "world1.json"
  },
  {
    "dir": "World/World2",
    "file": "world2.json"
  }
]
```

- **`dir`**: directorio que contiene la configuración y los recursos gráficos del mundo.
- **`file`**: nombre del archivo JSON de configuración ubicado dentro de ese directorio.

Cada `worldN.json` define el contenido y la configuración particular de un mundo. Las rutas de imágenes utilizadas en ese archivo deben corresponder con los recursos disponibles en su directorio.

## Incorporar un nuevo mundo

Para añadir un mundo, se debe crear su directorio dentro de `World/`, incorporar su archivo de configuración y recursos gráficos, y agregar una nueva entrada en `world.json` con los valores correspondientes de `dir` y `file`.

La estructura y las propiedades de un archivo de mundo se explican en la **[guía para crear un mundo](docs/CREAR_MUNDO.md)**.

# 🇬🇧 English

# MergeGame — Static Content

This repository stores **only static files** used by **MergeGame**, a *Merge & Story* video game developed in the context of LifeSync-Games. It contains world configuration files and their graphical assets: world properties, items, characters, initial grid layouts, the store, merge rules, rewards, and other content.

This repository **does not contain the game's source code** or player progress data.

## Repository structure

```text
.
├── world.json
├── World/
│   ├── World1/
│   │   ├── world1.json
│   │   └── ... world 1 graphical assets
│   └── World2/
│       ├── world2.json
│       └── ... world 2 graphical assets
└── docs/
    ├── CREATE_WORLD.md
    └── CREAR_MUNDO.md
```

Each world directory may contain image subdirectories for different item types.

## World registry (`world.json`)

The `world.json` file lists the available worlds and the locations of their configuration files:

```json
[
  {
    "dir": "World/World1",
    "file": "world1.json"
  },
  {
    "dir": "World/World2",
    "file": "world2.json"
  }
]
```

- **`dir`**: directory containing the world's configuration and graphical assets.
- **`file`**: name of the world's JSON configuration file inside that directory.

Each `worldN.json` defines the content and configuration of an individual world. Image paths used in that file must match the assets available in its directory.

## Adding a new world

To add a world, create its directory inside `World/`, include its configuration file and graphical assets, then add a new entry to `world.json` with the corresponding `dir` and `file` values.

See the **[world creation guide](docs/CREATE_WORLD.md)** for the structure and properties of individual world configuration files.

