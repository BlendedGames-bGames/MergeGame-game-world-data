# World Configuration Guide — MergeGame

This guide describes how to prepare a `worldN.json` file for a MergeGame world. Its structure is based on the world configuration file provided as an example in the project. Field names and values must remain compatible with the version of the game that reads them.

> **Important:** `world.json` registers available worlds, while `worldN.json` defines the content of **a single world**. They are not interchangeable.

## 1. Create the directory and register the world

Create a world directory and place its JSON configuration and referenced graphical assets inside it:

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

Then add this entry to the array in `world.json` (without removing existing worlds):

```json
{
  "dir": "World/World3",
  "file": "world3.json"
}
```

## 2. World file structure

The file consists of a single **JSON object** with these properties:

| Property | Purpose |
| --- | --- |
| `id_world` | Numeric identifier of the world. |
| `name_world` | World name. |
| `story_id` | Identifier of the story associated with the world. |
| `texture_world` | Image used to present the world. |
| `texture_slot` | Image for main board cells. |
| `texture_slot_inv` | Image for inventory cells. |
| `bloqueo_parcial` | Image used for partial blocking. |
| `bloqueo_completo` | Image used for complete blocking. |
| `texture_main_world` | Main scene or background image of the world. |
| `texture_board_world` | Board background image. |
| `items` | World item and generator catalog. |
| `characters` | Characters and their portraits. |
| `grid_main_layout` | Initial item placement on the main board. |
| `grid_inventory_layout` | Initial inventory layout. |
| `store` | Items offered through the LSG-connected store. |
| `fusion_config` | Allowed combinations and resulting items. |
| `reward_config` | Narrative scene requirements and rewards. |

Property names must be written exactly as shown, including case and underscores.

## 3. Define items

Each element of `items` describes an in-game object. For example:

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

- `item_id`: unique item identifier within the world; referenced by grids, store entries, merge rules, and rewards.
- `nombre`, `descripcion`: text displayed to the player.
- `tipo`: item family or progression chain (e.g., `te`).
- `nivel`: level of the item within its family.
- `icono`: path to its image relative to the world directory.
- `is_fusion`: flag present on items configured for merging; the original example omits it on final-level items.
- `precio`: item's selling price, where applicable.
- `item_tipo`: distinguishes special categories, including `generator` and the power-ups present in the project configuration.

### Generators

Generator items use `item_tipo: "generator"` and, when capable of producing items, a `generator_data` property:

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

- `category_chain`: item families the generator may produce.
- `uses_max`: configured maximum number of uses.
- `cooldown_max`: cooldown duration, using the unit expected by the game.
- `generator_data.items`: possible output items and their probabilities (`prob`); each `id` must exist in `items`. Probabilities should add up to `1`.

### Available power-ups

**Power-ups** are defined inside `items`, just like other objects, but their special behavior is determined by `item_tipo`. MergeGame's example configuration uses **four power-up types**:

| `item_tipo` | Example `tipo` | Effect | Restrictions |
| --- | --- | --- | --- |
| `duplicator` | `duplicator` | Duplicates the target item. | Cannot be used on generators, energy, or other power-ups. |
| `divider` | `divider` | Splits the target into three lower-level items. | Cannot be used on generators, energy, or other power-ups. |
| `evolver` | `evolver` | Increases an item's level, if a higher level exists. | Cannot be used on generators, energy, or other power-ups. |
| `cooldown_reducer` | `cooldown` | Reduces a generator's cooldown. | Used with generators; the example also describes merging it with itself to increase its time-based effect. |

Example definitions for the four types inside the `items` array:

```json
[
  {
    "item_id": "duplicator1",
    "nombre": "Espejo",
    "tipo": "duplicator",
    "nivel": 1,
    "descripcion": "Duplicates another item. Cannot be used on generators, energy, or other power-ups.",
    "icono": "powerup/duplicator1.png",
    "item_tipo": "duplicator"
  },
  {
    "item_id": "divider1",
    "nombre": "Martillo",
    "tipo": "divider",
    "nivel": 1,
    "descripcion": "Splits another item into three lower-level items. Cannot be used on generators, energy, or other power-ups.",
    "icono": "powerup/divider1.png",
    "item_tipo": "divider"
  },
  {
    "item_id": "evolver1",
    "nombre": "Flecha ascendente",
    "tipo": "evolver",
    "nivel": 1,
    "descripcion": "Levels up another item if a higher level exists. Cannot be used on generators, energy, or other power-ups.",
    "icono": "powerup/evolver1.png",
    "item_tipo": "evolver"
  },
  {
    "item_id": "cooldown1",
    "nombre": "Reloj de arena",
    "tipo": "cooldown",
    "nivel": 1,
    "descripcion": "Reduces generator cooldown. Can be merged with itself to increase its time-based effect.",
    "icono": "powerup/cooldown1.png",
    "item_tipo": "cooldown_reducer"
  }
]
```

> This array is an **illustrative excerpt from `items`**, not a complete world file. Each `icono` path must refer to an existing image inside the world directory.

**`tipo` vs. `item_tipo`:** `tipo` identifies the item's family, whereas `item_tipo` controls its special behavior. For example, the hourglass uses **`"tipo": "cooldown"`** together with **`"item_tipo": "cooldown_reducer"`**.

**Energy capsules** are configured separately using **`"tipo": "energy"`** (such as `energy_25`, `energy_50`, or `energy_100`), without any of the four power-up `item_tipo` values. For example:

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

The store may offer energy through mechanic `34`, while these power-ups use mechanics `68` through `71` as explained in the [store section](#store-store).

## 4. Define characters and grids

Characters are defined in `characters`:

```json
{
  "character_id": "mia",
  "name": "Mia",
  "rarity": 3,
  "portrait": "mia_normal.png"
}
```

The main board uses `grid_main_layout.grid_config`, while the inventory uses `grid_inventory_layout.grid_config`. Each entry places an item in a cell:

```json
{
  "grid_config": [
    { "slot": 0, "id": "generator_te1" },
    { "slot": 1, "id": "te1", "bloqueado": true },
    { "slot": 2, "id": "te2", "bloqueado": true, "bloqueado_completo": true }
  ]
}
```

`slot` identifies the cell position, and `id` references an `item_id`. In the provided configuration, `bloqueado: true` represents partial blocking; `bloqueado_completo: true` together with `bloqueado: true` represents complete blocking. Omitted positions have no initially configured item. The inventory can start empty using `"grid_config": []`.

## 5. Configure store entries, merges, and rewards

### Store (`store`)

Each `store` entry defines an item that players can redeem using LifeSync-Games (LSG) points. For example, to offer an energy refill:

```json
{
  "id": "energy_100",
  "dimension_id": 2,
  "cost": 5,
  "modifiable_mechanic_videogame_id": 34
}
```

- `id`: the `item_id` of the item awarded; it must exist in `items`.
- `dimension_id`: the LSG points dimension used for redemption. **The value `2` represents the Physical dimension.**
- `cost`: number of LSG points charged from the specified dimension.
- `modifiable_mechanic_videogame_id`: identifier of the redemption mechanic registered in LSG; it must match the offered item's function.

The following mappings are used in MergeGame, according to **Table A.6 in the project's appendix**:

| `modifiable_mechanic_videogame_id` | Redemption mechanic | Example item | `cost` (LSG points) |
| --- | --- | --- | ---: |
| `34` | Refill energy | `energy_100` | 5 |
| `68` | Duplicate an item | `duplicator1` | 8 |
| `69` | Split an item | `divider1` | 6 |
| `70` | Evolve or level up an item | `evolver1` | 8 |
| `71` | Reduce a generator's cooldown | `cooldown1` | 5 |

**Item–mechanic compatibility:** mechanic `34` applies **only to items whose `tipo` is `"energy"`**. For the other mechanics, the example items use `item_tipo` values `"duplicator"`, `"divider"`, `"evolver"`, and `"cooldown_reducer"`, respectively. Do not associate a mechanic with an item from a different category merely by changing its identifier in `store`.

The following is the complete `store` array from the provided world file, showing all five redemption types associated with the Physical dimension:

```json
"store": [
  { "id": "energy_100", "dimension_id": 2, "cost": 5, "modifiable_mechanic_videogame_id": 34 },
  { "id": "duplicator1", "dimension_id": 2, "cost": 8, "modifiable_mechanic_videogame_id": 68 },
  { "id": "divider1", "dimension_id": 2, "cost": 6, "modifiable_mechanic_videogame_id": 69 },
  { "id": "evolver1", "dimension_id": 2, "cost": 8, "modifiable_mechanic_videogame_id": 70 },
  { "id": "cooldown1", "dimension_id": 2, "cost": 5, "modifiable_mechanic_videogame_id": 71 }
]
```

> This is a **fragment of the world object**, not a standalone JSON document. All `id` values must refer to items declared in `items`.

LSG-related identifiers and mechanics must exist and match the service configuration. If a world does not offer items through this store, use `"store": []`.

### Merges (`fusion_config.fusion_items`)

```json
{
  "combination": "te1+te1",
  "result": "te2"
}
```

`combination` specifies two `item_id` values separated by `+`. `result` specifies the produced item. Every referenced identifier must exist in `items`.

### Rewards (`reward_config.rewards`)

```json
{
  "chapter": 1,
  "scene": 1,
  "rewards_id": ["te1"],
  "exp": 10,
  "coins_required": 0
}
```

`chapter` and `scene` identify the narrative scene; `rewards_id` lists awarded items; `exp` sets the earned experience; and `coins_required` defines the coins needed to unlock the scene. Chapters and scenes should match the story referenced by `story_id`.

## 6. Complete, simplified `world3.json` example

This example includes two items, one generator, one character, one merge rule, and one reward. World and story identifiers are **illustrative** and must be adjusted to match the game's actual data.

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

## 7. Checks before publishing

1. Verify JSON syntax, for example with `python -m json.tool World/World3/world3.json`.
2. Confirm that `world.json` points to the correct directory and file.
3. Avoid duplicate `item_id` values; keep identifiers consistent in grids, generators, store entries, merge rules, and rewards.
4. Confirm that every image path matches a file within the world directory.
5. Avoid duplicate `slot` positions within a grid, and ensure they are valid board positions.
6. Check that each generator's output probabilities add up to `1`.
7. Verify referenced stories and scenes. For store entries, confirm that `dimension_id`, `cost`, and `modifiable_mechanic_videogame_id` match LSG and that each mechanic suits the item type.
8. Check that each power-up uses the expected `item_tipo`; mechanic `34` can only be used with items whose `tipo` is `"energy"`.
9. Load the world in MergeGame to test it: valid JSON syntax **does not guarantee** valid references or working gameplay behavior.
