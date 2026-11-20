# Guía de configuración de mundos — MergeGame

Esta guía describe cómo preparar un archivo `worldN.json` para un mundo de MergeGame. El formato se basa en el archivo de mundo utilizado como ejemplo en el proyecto. Los campos y valores deben mantenerse compatibles con la versión del juego que los consume.

> **Importante:** `world.json` registra los mundos disponibles; `worldN.json` define el contenido de **un solo mundo**. No son archivos intercambiables.

## 1. Crear el directorio y registrar el mundo

Crea un directorio para el mundo e incorpora su archivo JSON y los recursos gráficos referenciados por este:

```text
World/
└── World3/
    ├── world3.json
    ├── menu.png
    ├── slot.png
    ├── slot_inventory.png
    ├── bloqueo_parcial.png
    ├── bloqueo_completo.png
    ├── escenario.png
    ├── tablero.png
    ├── mia_normal.png
    ├── te/
    │   ├── te1.png
    │   └── te2.png
    ├── generator_te/
    │   └── generator_te1.png
    ├── powerup/
    │   ├── duplicator1.png
    │   ├── divider1.png
    │   ├── evolver1.png
    │   └── cooldown1.png
    └── energy/
        └── energy_100.png
```

Añade luego esta entrada a la lista de `world.json` (sin eliminar los mundos existentes):

```json
{
  "dir": "World/World3",
  "file": "world3.json"
}
```

## 2. Estructura del archivo de mundo

El archivo contiene un único **objeto JSON** con las siguientes propiedades:

| Propiedad | Contenido |
| --- | --- |
| `id_world` | Identificador numérico del mundo. |
| `name_world` | Nombre que identifica al mundo. |
| `story_id` | Identificador de la historia asociada al mundo. |
| `texture_world` | Imagen de presentación del mundo. |
| `texture_slot` | Imagen de las casillas del tablero principal. |
| `texture_slot_inv` | Imagen de las casillas del inventario. |
| `bloqueo_parcial` | Imagen del bloqueo parcial. |
| `bloqueo_completo` | Imagen del bloqueo completo. |
| `texture_main_world` | Imagen principal o escenario del mundo. |
| `texture_board_world` | Imagen de fondo del tablero. |
| `items` | Catálogo de ítems y generadores del mundo. |
| `characters` | Personajes y retratos asociados. |
| `grid_main_layout` | Distribución inicial de ítems en el tablero principal. |
| `grid_inventory_layout` | Distribución inicial del inventario. |
| `store` | Ítems disponibles en la tienda vinculada con LSG. |
| `fusion_config` | Combinaciones permitidas y resultados de fusión. |
| `reward_config` | Recompensas y requisitos de las escenas narrativas. |

Los nombres de las propiedades se deben escribir exactamente como aparecen, respetando mayúsculas, minúsculas y guiones bajos.

## 3. Configurar los ítems

Cada elemento de `items` define un objeto del juego. Ejemplo:

```json
{
  "item_id": "te1",
  "nombre": "Hojas de té",
  "tipo": "te",
  "nivel": 1,
  "descripcion": "",
  "icono": "te/te1.png",
  "is_fusion": true,
  "precio": 1
}
```

- `item_id`: identificador único del ítem dentro del mundo; se reutiliza en grillas, tienda, fusiones y recompensas.
- `nombre`, `descripcion`: información que se muestra al jugador.
- `tipo`: familia o cadena a la que pertenece el ítem (por ejemplo, `te`).
- `nivel`: nivel del objeto dentro de su familia.
- `icono`: ruta de su imagen dentro del directorio del mundo.
- `is_fusion`: marca presente en los ítems configurados como fusionables; en el ejemplo original se omite en los niveles finales.
- `precio`: valor utilizado para la venta del ítem, cuando corresponda.
- `item_tipo`: distingue categorías especiales, como `generator` o los *power-ups* presentes en la configuración del proyecto.

### Generadores

Los ítems generadores incorporan `item_tipo: "generator"` y, cuando producen objetos, una propiedad `generator_data`:

```json
{
  "item_id": "generator_te1",
  "nombre": "Tetera",
  "tipo": "generator_te",
  "nivel": 1,
  "descripcion": "Genera hojas de té.",
  "icono": "generator_te/generator_te1.png",
  "item_tipo": "generator",
  "generator_data": {
    "category_chain": ["te"],
    "uses_max": 64,
    "cooldown_max": 3600,
    "items": [
      { "id": "te1", "prob": 1.0 }
    ]
  }
}
```

- `category_chain`: familias de ítems que puede producir.
- `uses_max`: cantidad máxima de usos configurada.
- `cooldown_max`: duración de enfriamiento según la unidad empleada por el juego.
- `generator_data.items`: posibles resultados y su probabilidad (`prob`); sus `id` deben existir en `items`. Las probabilidades deben sumar `1`.

### Power-ups disponibles

Los **power-ups** se declaran dentro de `items`, como cualquier otro objeto, pero se identifican por el valor de `item_tipo`. En la configuración de MergeGame se utilizan **cuatro tipos de power-ups**:

| `item_tipo` | `tipo` del ejemplo | Efecto | Restricciones |
| --- | --- | --- | --- |
| `duplicator` | `duplicator` | Duplica el ítem sobre el que se utiliza. | No se aplica a generadores, energía ni otros power-ups. |
| `divider` | `divider` | Divide el ítem en tres ítems de nivel inferior. | No se aplica a generadores, energía ni otros power-ups. |
| `evolver` | `evolver` | Aumenta el nivel del ítem, si tiene un nivel superior disponible. | No se aplica a generadores, energía ni otros power-ups. |
| `cooldown_reducer` | `cooldown` | Reduce el tiempo de enfriamiento de un generador. | Se utiliza con generadores; según el ejemplo, puede fusionarse consigo mismo para aumentar su efecto temporal. |

Ejemplo de definición de los cuatro tipos dentro del arreglo `items`:

```json
[
  {
    "item_id": "duplicator1",
    "nombre": "Espejo",
    "tipo": "duplicator",
    "nivel": 1,
    "descripcion": "Duplica otro ítem. No funciona en generadores, energía ni otros power-ups.",
    "icono": "powerup/duplicator1.png",
    "item_tipo": "duplicator"
  },
  {
    "item_id": "divider1",
    "nombre": "Martillo",
    "tipo": "divider",
    "nivel": 1,
    "descripcion": "Divide otro ítem en tres ítems de menor nivel. No funciona en generadores, energía ni otros power-ups.",
    "icono": "powerup/divider1.png",
    "item_tipo": "divider"
  },
  {
    "item_id": "evolver1",
    "nombre": "Flecha ascendente",
    "tipo": "evolver",
    "nivel": 1,
    "descripcion": "Sube un nivel a otro ítem, si existe un nivel superior. No funciona en generadores, energía ni otros power-ups.",
    "icono": "powerup/evolver1.png",
    "item_tipo": "evolver"
  },
  {
    "item_id": "cooldown1",
    "nombre": "Reloj de arena",
    "tipo": "cooldown",
    "nivel": 1,
    "descripcion": "Reduce el enfriamiento de generadores. Puede fusionarse consigo mismo para aumentar su efecto temporal.",
    "icono": "powerup/cooldown1.png",
    "item_tipo": "cooldown_reducer"
  }
]
```

> El arreglo mostrado es un **fragmento ilustrativo de `items`**, no un archivo de mundo completo. Cada `icono` debe apuntar a una imagen existente dentro del directorio del mundo.

**Diferencia entre `tipo` e `item_tipo`:** `tipo` identifica la familia del ítem, mientras que `item_tipo` determina su comportamiento especial. Por ejemplo, para el reloj de arena se utiliza **`"tipo": "cooldown"`** junto con **`"item_tipo": "cooldown_reducer"`**.

Las **cápsulas de energía** se configuran por separado con **`"tipo": "energy"`** (como `energy_25`, `energy_50` o `energy_100`), sin ninguno de esos cuatro valores de `item_tipo`. Por ejemplo:

```json
{
  "item_id": "energy_100",
  "nombre": "energia",
  "tipo": "energy",
  "nivel": 3,
  "descripcion": "Una capsula de energia de mayor capacidad.",
  "icono": "energy/energy_100.png",
  "precio": 100
}
```

La tienda puede ofrecerlas mediante la mecánica `34`, mientras que los power-ups usan las mecánicas `68` a `71` indicadas en la [sección de tienda](#tienda-store).

## 4. Configurar personajes y grillas

Los personajes se declaran en `characters`:

```json
{
  "character_id": "mia",
  "name": "Mia",
  "rarity": 3,
  "portrait": "mia_normal.png"
}
```

El tablero principal utiliza `grid_main_layout.grid_config` y el inventario `grid_inventory_layout.grid_config`. Cada entrada asigna un ítem a una posición:

```json
{
  "grid_config": [
    { "slot": 0, "id": "generator_te1" },
    { "slot": 1, "id": "te1", "bloqueado": true },
    { "slot": 2, "id": "te2", "bloqueado": true, "bloqueado_completo": true }
  ]
}
```

`slot` identifica la posición de la casilla; `id` hace referencia a un `item_id`. En la configuración de ejemplo, `bloqueado: true` representa un bloqueo parcial y `bloqueado_completo: true`, junto con `bloqueado: true`, representa un bloqueo completo. Las posiciones omitidas no tienen un ítem inicial configurado. El inventario puede declararse inicialmente vacío mediante `"grid_config": []`.

## 5. Configurar tienda, fusiones y recompensas

### Tienda (`store`)

Cada elemento de `store` define un ítem que el jugador puede canjear utilizando puntos LifeSync-Games (LSG). Por ejemplo, para ofrecer una recarga de energía:

```json
{
  "id": "energy_100",
  "dimension_id": 2,
  "cost": 5,
  "modifiable_mechanic_videogame_id": 34
}
```

- `id`: `item_id` del ítem que se entregará; debe existir en `items`.
- `dimension_id`: dimensión de puntos LSG utilizada en el canje. **El valor `2` corresponde a la dimensión Física.**
- `cost`: cantidad de puntos LSG de la dimensión indicada que cuesta el canje.
- `modifiable_mechanic_videogame_id`: identificador de la mecánica de canje registrada en LSG; debe corresponder a la función del ítem ofrecido.

La siguiente relación es la utilizada en MergeGame, según la **tabla A.6 del anexo del documento del proyecto**:

| `modifiable_mechanic_videogame_id` | Mecánica de canje | Ítem de ejemplo | `cost` (puntos LSG) |
| --- | --- | --- | ---: |
| `34` | Recargar energía | `energy_100` | 5 |
| `68` | Duplicar un ítem | `duplicator1` | 8 |
| `69` | Dividir un ítem | `divider1` | 6 |
| `70` | Evolucionar o subir de nivel un ítem | `evolver1` | 8 |
| `71` | Reducir el tiempo de enfriamiento de un generador | `cooldown1` | 5 |

**Compatibilidad entre ítem y mecánica:** la mecánica `34` es **exclusiva de ítems cuyo `tipo` sea `"energy"`**. Para las demás mecánicas, los ítems del ejemplo utilizan los valores de `item_tipo` `"duplicator"`, `"divider"`, `"evolver"` y `"cooldown_reducer"`, respectivamente. No se debe asignar una mecánica a un ítem de otra categoría solo por cambiar su identificador en `store`.

El siguiente es el arreglo `store` completo del archivo de ejemplo; muestra los cinco tipos de canjes asociados a la dimensión Física:

```json
"store": [
  { "id": "energy_100", "dimension_id": 2, "cost": 5, "modifiable_mechanic_videogame_id": 34 },
  { "id": "duplicator1", "dimension_id": 2, "cost": 8, "modifiable_mechanic_videogame_id": 68 },
  { "id": "divider1", "dimension_id": 2, "cost": 6, "modifiable_mechanic_videogame_id": 69 },
  { "id": "evolver1", "dimension_id": 2, "cost": 8, "modifiable_mechanic_videogame_id": 70 },
  { "id": "cooldown1", "dimension_id": 2, "cost": 5, "modifiable_mechanic_videogame_id": 71 }
]
```

> Este es un **fragmento del objeto del mundo**, por lo que no constituye un documento JSON independiente. Para usarlo, todos los `id` deben estar declarados previamente en `items`.

Los identificadores y las mecánicas asociados a LSG deben existir y coincidir con la configuración del servicio. Si un mundo no ofrece ítems en esta tienda, puede utilizar `"store": []`.

### Fusiones (`fusion_config.fusion_items`)

```json
{
  "combination": "te1+te1",
  "result": "te2"
}
```

`combination` indica los dos `item_id` que se combinan, separados por `+`. `result` es el identificador del ítem obtenido. Todos deben existir en el catálogo de `items`.

### Recompensas (`reward_config.rewards`)

```json
{
  "chapter": 1,
  "scene": 1,
  "rewards_id": ["te1"],
  "exp": 10,
  "coins_required": 0
}
```

`chapter` y `scene` identifican la escena; `rewards_id` contiene los ítems entregados; `exp` establece la experiencia obtenida y `coins_required` las monedas necesarias para desbloquearla. Los capítulos y escenas deben corresponder con la historia asociada mediante `story_id`.

## 6. Ejemplo completo y reducido de `world3.json`

El siguiente ejemplo ilustra la estructura con dos ítems, un generador, un personaje, una fusión y una recompensa. Los identificadores de mundo e historia son **ilustrativos** y deben ajustarse a los datos reales del juego.

```json
{
  "id_world": 3,
  "name_world": "Mundo de ejemplo",
  "story_id": 4,
  "texture_world": "menu.png",
  "texture_slot": "slot.png",
  "texture_slot_inv": "slot_inventory.png",
  "bloqueo_parcial": "bloqueo_parcial.png",
  "bloqueo_completo": "bloqueo_completo.png",
  "texture_main_world": "escenario.png",
  "texture_board_world": "tablero.png",
  "items": [
    {
      "item_id": "te1", "nombre": "Hojas de té", "tipo": "te",
      "nivel": 1, "descripcion": "", "icono": "te/te1.png",
      "is_fusion": true, "precio": 1
    },
    {
      "item_id": "te2", "nombre": "Bolsa de té", "tipo": "te",
      "nivel": 2, "descripcion": "", "icono": "te/te2.png",
      "precio": 2
    },
    {
      "item_id": "generator_te1", "nombre": "Tetera", "tipo": "generator_te",
      "nivel": 1, "descripcion": "Genera hojas de té.",
      "icono": "generator_te/generator_te1.png", "item_tipo": "generator",
      "generator_data": {
        "category_chain": ["te"], "uses_max": 64, "cooldown_max": 3600,
        "items": [{ "id": "te1", "prob": 1.0 }]
      }
    }
  ],
  "characters": [
    { "character_id": "mia", "name": "Mia", "rarity": 3, "portrait": "mia_normal.png" }
  ],
  "grid_main_layout": {
    "grid_config": [
      { "slot": 0, "id": "generator_te1" },
      { "slot": 1, "id": "te1", "bloqueado": true },
      { "slot": 2, "id": "te2", "bloqueado": true, "bloqueado_completo": true }
    ]
  },
  "grid_inventory_layout": { "grid_config": [] },
  "store": [],
  "fusion_config": {
    "fusion_items": [{ "combination": "te1+te1", "result": "te2" }]
  },
  "reward_config": {
    "rewards": [
      { "chapter": 1, "scene": 1, "rewards_id": ["te1"], "exp": 10, "coins_required": 0 }
    ]
  }
}
```

## 7. Comprobaciones antes de publicar

1. Comprueba que el JSON tenga sintaxis válida. Por ejemplo: `python -m json.tool World/World3/world3.json`.
2. Verifica que `world.json` apunte al directorio y archivo correctos.
3. Evita `item_id` duplicados y utiliza los mismos identificadores en grillas, generadores, tienda, fusiones y recompensas.
4. Comprueba que todas las rutas de imágenes correspondan con archivos existentes en el directorio del mundo.
5. Comprueba que no se repitan posiciones `slot` dentro de una misma grilla y que correspondan a posiciones válidas del tablero.
6. Revisa que las probabilidades de salida de cada generador sumen `1`.
7. Verifica la existencia de la historia y las escenas. En la tienda, comprueba que `dimension_id`, `cost` y `modifiable_mechanic_videogame_id` coincidan con LSG y que la mecánica corresponda al tipo del ítem.
8. Comprueba que los power-ups tengan el `item_tipo` esperado y recuerda que la mecánica `34` admite exclusivamente ítems con `tipo: "energy"`.
9. Prueba la carga del mundo en MergeGame: validar la sintaxis JSON **no garantiza** que todas sus referencias o mecánicas funcionen.
