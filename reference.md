# Reference

## General

[Default Data][data-url]

[Default Image][img-url]

### List of all unit names (in alphabetical order)

```
A.P.C.
Disruptor
Drone Swarm
Grenadier
Hammerhead
Col. Jackson
Juggernaut
Jump Jet Troopers
Kodiak
Dr. Liang
Mammoth Tank
MG Squad
Missile Squad
Cdr. McNeil
M.L.R.S.
Mohawk Gunship
M.S.V.
Orca
Orca Bomber
Pitbull
Predator Tank
Razorback
Rhino
Riflemen
Sandstorm
Shatterer
Shockwave Troopers
Slingshot
Sniper Team
Gen. Solomon
Lt. Strongarm
Talon
Titan
War Dogs
Wolverine
Zone Trooper
Artillery
Attack Bikes
Avatar
Banshee
Basilisk
Buggy
Catalyst Gunship
Centurion
Chemical Warriors
Chem Buggy
Confessor
Cyberwheel
Cyborg
Fanatic
Flame Tank
Flame Troopers
Giga-Cannon
Inferno
Jade
Kane
Laser Drone
Laser Squad
Militant
Mutant Marauder
Oxanna
Phantom
Rockworm
Scarabs
Scavenger
Scorpion Tank
Seth
Shade
Stealth Tank
Tick Tank
Venom
Widowmaker
```

## Data structure definition
```typescript
interface Deck {
  faction: Faction; // "GDI" | "Nod"
  commander: Unit;
  units: Unit[];
}

// Unit may contain other fields, see "Default Data" above to see actual unit data
interface Unit {
  name: string;
  faction: Faction; // "GDI" | "Nod"
  building: Building; // "Infantry" | "Vehicle" | "Air" | "Tech" | "Commander"
  cost: number;
  unitType: UnitType; // "Infantry" | "Vehicle" | "Air"
  rarity: Rarity; // "Common" | "Rare" | "Epic"
  speed: Speed; // null | "Slowest" |"Slow" | "Average" | "Fast" | "Faster" | "Fastest"
  squadSize: number;
  antiInfantry: number;
  antiVehicle: number;
  antiAir: number;
  [key: string]: unknown; // other fields
}
```

## Deck functions
### general deck building functions

```typescript
hasCommander(name: string): boolean

containsUnitsByName(names: string[]): boolean

containsUnitByName(name: string): boolean
```

### deck/unit predicates
```typescript
allUnitsThat(fn: (u: Unit) => boolean): boolean

someUnitsThat(fn: (u: Unit) => boolean): boolean

// n between 0 to 6 inclusive
nUnitsThat(n: number, fn: (u: Unit) => boolean): boolean

// range are 2 numbers, from 0 to 6 inclusive
rangeUnitsThat(range: [number, number], fn: (u: Unit) => boolean): boolean

atLeastNUnitsThat(n: number, fn: (u: Unit) => boolean): boolean

atMostNUnitsThat(n: number, fn: (u: Unit) => boolean): boolean
```

### whole deck predicate helpers
```typescript
totalUnitsCost(n: number): number
```

## Unit predicates

- `isInfantry()`
- `isVehicle()`
- `isAir()`
- `isTech()`
- `canAttackInfantry()`
- `canAttackVehicle()`
- `canAttackAir()`
- `strongAgainstInfantry()`
- `strongAgainstVehicle()`
- `strongAgainstAir()`
- `isSquad()`

[data-url]: https://rivalsfan.github.io/data/units.json
[img-url]: https://rivalsfan.github.io/data/imgs.json
