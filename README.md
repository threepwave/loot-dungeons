Dungeons (for Adventurers). A simple procedural dungeon map generator that developers can use to build out games for the Loot ecosystem.

````
0000000                                        0000000
0003300   Dungeons (for Adventurers)           0003300
0003300     simple procedural map generator    0003300
0004400                                        0004400
```` 

Example:

![Example Dungeons](https://github.com/threepwave/dungeons/raw/names/images/example-dungeons.png)

See live outputs, visit: https://lootdungeons.vercel.app

Follow along with the project: https://twitter.com/lootdungeons/

More info about Loot: https://discord.gg/EQ2835HQ

## Principles

1. This is a primitive. It is designed to play nicely with many other primitives.
2. Let the game developers/dungeon masters make as many important decisions as possible, for example..
  a. Monsters
  b. Visual style
  c. Spawn points
3. Expose everything via Solidity API (vs proprietary formats or focusing on the art)

## Drop Structure

There are 9000 Dungeons. 7777 are gated to whoever owns OG Loot at 0.02ETH. 1000 are available to the public at 0.1ETH (to allow for new entrants). The remaining 233 are reserved for those who worked on the project. Loot holders have 24h to claim their dungeon before the drop opens to the public.

After this, two dungeons will release per week:
1. One to a random wallet that holds OG Loot. The loot holder will need to pay 0.1ETH to mint this new dungeon. If the dungeon goes unclaimed for 24 hours, it becomeos available for mint by anyone for 1ETH.
2. One open to anyone for 1ETH. This will allow new users to have a chance at owning a dungeon.

I hope that this will create a cadence of new, interesting content each week and create incentives for game designers and developers to continue supporting these new dungeons.

You can mint Dungeons via Etherscan: 
Mint()
MintWithLoot()

## NFT Structure
A Dungeon NFT is made up of three parts:
1. ID (Number) - Each dungeon has a unique identifier (integer) numbered 1-10000.
2. Image (svg) - Each dungeon has a unique image representing its layout, rendered in pixel art.
3. Metadata - Each dungeon has metadata representing it which can be accessed from getters on the smart contract:
  a. id (int) - The ID of the dungeon
  c. layout (string) - A string representing walls, walkable floor, interactable objects (e.g. doors), and points of interest.
  d. environment (int) - An integer depicting an environment for the dungeon
  e. name (string) - The name of the dungeon

Developers can choose to ignore any of these. 

## Dungeon Structure

Dungeons are **always square** (e.g. 10x10) and can range from 6x6 -> 25x25.

Dungeons can be 'rooms' (rectangular areas connected by hallways) or 'caverns' (sprawling organic shapes). Most dungeon areas are connected but some dungeons have small areas that are not connected (for secret rooms, etc).

![Rooms vs Caverns](https://github.com/threepwave/dungeons/raw/names/images/rooms-caverns.png)

Dungeons contain a list of 'tiles' which have different charactertics:
* Walls: Represented by 'X'
* Floors: Represented by ' ' (where players can walk)
* Points of interest: Represented by 'i'
* Doors: Represented by 'D'

Dungeons have a name (e.g. 'Den of the Twins') to give each one a sense of permanence. Some names will be repeated (e.g. 'Stony Field') but more rare names will be unique.

Dungeons have an environment that implies a theme. Our hope is that the environment combined with the name and shape can give each dungeon its own character. Environments are as follows:
0 - Desert Plateau
1 - Forest Ruins
2 - Underwter Keep
3 - Stone Temple
4 - Mountain Deep
5 - Ember's Glow

![Environments](https://github.com/threepwave/dungeons/raw/names/images/environments.png)

Developers can choose to ignore any of the above attributes and use whatever is useful.

All other attributes (e.g. monsters, spawn point, etc) to be defined by the developer.

## What could I build with this ?

We used a simple API (string with characters) so that developers who build on top of this can mirror that structure and add metadata on top of the dungeon. Your array could indicate where players can find water, where they can ride mounts, etc.

We kept dungeons incredibly simple so developers, dungeon masters, etc so it plays 
nicely with other components. We hope that people add Encounters, Monsters, Rewards, Traps, Locks, Environmental Art, Flying, Running, Walking... even full 2D and 3D environments!

We hope that incentives are built over time to reward people that hold dungeons (e.g. a proceeds of the rewards could go to the holders).

This is just the beginning.

## Usage

We expect most developers will want to create a 2D array containing to represent the dungeon:
For example:
````
[
  ['X', 'X', 'X', 'X', 'X'],
  ['X', ' ', ' ', 'X', ' '],
  ['X', ' ', 'X', 'D', ' '],
  ['X', 'D', ' ', ' ', 'X'],
  ['X', ' ', 'i', ' ', 'X'],
  ['X', 'X', ' ', 'X', 'X']
]
````

Would generate:

![Example dungeon](https://github.com/threepwave/dungeons/raw/names/images/array-example.png)

You can generate a 2D array from the getDungeon() string by looping through the string.

For example (javascript):
````
// Assume you start with the string in a variable called 'dungeon'
const size = Math.sqrt(dungeon.length)

let dungeonArray = []
let count = 0

for(let y = 0; y < size; y++) {
  let row = []
  for(let x = 0; x < size; x++) {
    row.push(dungeon[count])
    count++
  }
  dungeonArray.push(row)
}
````

For more details, see **API - getDungeon()** below.

## API 

The dungeon contract exposes the following endpoints to allow developers to use and query dungeons in their games and applications.

We'll use this dungeon for all examples below:
````
[
  ['X', 'X', 'X', 'X', 'X', 'X', 'X', 'X'],
  ['X', 'X', 'X', 'X', ' ', ' ', ' ', 'X'],
  ['X', ' ', ' ', 'X', ' ', 'i', ' ', 'X'],
  ['X', ' ', ' ', 'X', ' ', ' ', ' ', 'X'],
  ['X', ' ', ' ', 'X', ' ', ' ', ' ', 'X'],
  ['X', ' ', ' ', ' ', 'D', ' ', 'X', 'X'],
  ['X', ' ', ' ', 'X', 'X', 'X', 'X', 'X'],
  ['X', 'X', 'X', 'X', 'X', 'X', 'X', 'X'],  
  ['X', 'X', 'X', 'X', 'X', 'X', 'X', 'X']
]
````

![API Example](https://github.com/threepwave/dungeons/raw/names/images/api-example.png)


### Dungeon Geometry

**getDungeon()** (string) - Returns a string representing all tiles in the dungeon as ascii characters. The array uses a zero-based index and the top left corner of the dungeon starts at (0, 0).

Using our example dungeon:
"XXXXXXXXXXXX i XX  X   XX  X   XX  X  XX   D XXX  XXXXXXXXXXXXXXXXXXXXX"


Each character represents a 'tile' which have different charactertics:
* Walls: Represented by 'X'
* Floors: Represented by ' ' (where players can walk)
* Points of interest: Represented by 'i'
* Doors: Represented by 'D'

Developers who embrace this format for other purposes could use whatever ascii characters they want to represent new types of tiles or objects.

### Dungeon Attributes

**getName()** (string) - Returns the name of the dungeon. In our example: `Den of the Twins.`

**getEnvironment()** (int) - Returns the environment of the dungeon. There are six total environments and each affects the color of the original SVG. Developers can choose to embrace or ignore these environments. In our example: `0`

**numDoors()** (int) - Returns the number of doors present in the dungeon. In our example: `1`.
*Useful for: Querying for dungeons with a specific number of doors.*

**numPoints()** (int) - Returns the number of points of interest present in the dungeon. In our example: `1`.
*Useful for: Querying for dungeons with a specific number of points.*

**numFloors()** (int) - Returns the number of accessible floor tiles in the dungeon. This includes doors and points of interest. In our example: `26`
*Useful for: Determining probabilities of objects based on the amount of floor space. Making sure a given dungeon is suitable for a given party size.*

**size()** (int) - Returns the overall size of the dungeon. In this case, we have an 8x8 dungeon, so in our example: `64`
*Useful for: Querying for dungeon based on size*

*Note: Dungeons are always square. Developers can also determine the size of the dungeon by taking the sqrt of the length of the string*


# License

Dungeons are licensed under a CC-0 (Free use) license.

Everyone has read access to every dungeon. Therefore, dungeons can be used by anyone, in any app/game/context. However they want. 

I hope that over time, people will build incentives for dungeon holders but that is not baked into the contract or the design.
