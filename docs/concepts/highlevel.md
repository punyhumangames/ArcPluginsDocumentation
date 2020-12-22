At the highest level, Arc Inventory is an inventory that allows you to power your game code with GAS, and to give you a lot of power over the design and development of your game.

The Inventory System's primary purpose is to give your character and pawns a set of Gameplay Effects, Abilities, and Attributes, and control how and when those are added to and removed from your character's ASC.

## Project Goals

Arc Inventory has a few goals, and they are reflected in every part of the inventory system.

1. Don't Assume Game Design
:   Arc Inventory will not assume your design or make design choices for you.  The core structure can be reused across many genres of game, from RPGs to FPSes to Survival games to Action RPGs.  
    Inventory accomplishes this by breaking up different inventory concepts across the system and classes. 

2. Let the user subclass
:   Arc Inventory attempts to be a general base for GAS powered inventory, but because of #1, it does not make a lot of game design choices like Input or complex UI.  Because of this, every part of the system is intended to be subclasses to fill out features.  

3. The Character Does the Work
:   Arc Inventory makes the character do the work, and is intended to be a component on your character.  Items, ItemDefs, and all pieces are simply bits of information for your character to react to.  Things like how your character actually holds the item or what happens when you activate an item is easily scripted, but must be done on the character or inside of an Gameplay Ability.

## Inventories

Inventories are the primary container for Items.  Inventory is an actor component that handles the placement of items into Slots.  The Inventory has many subclasses to enable different types of functionality, and care should be taken to decide which class is right for your game.

At it's core, Inventories are an array of `Inventory Slots`.  Slots are defined with a set of Gameplay Tags, and can have an Item Filter to indicate which items can be placed into the slot.  

Inventory Slots are split into 3 different concepts, `Bag`, `Equipment`, and `Active`.  

`Bag Slot`
:   Bag Slots are slots that can generally hold any item, but do not do anything when an item is placed into them.  

`Equipment Slot`
:   Equipment Slots are slots that generally hold specific items, and place GAS primitives onto the component owner when the item is put into the slot.  

`Active Slot`
:   Active slots are slots that hold specific items, and only one item can be active at a time of all items in active slots.  These can be thought of as held items for a first person shooter or a sword that the player is currently wielding.

!!! tip
    A slot can be any combination of `Bag`, `Equipment`, or `Active`.  It's very common for Active Slots to also be Equipment Slots!

!!! caution
    `Equipment` and `Active` slots only have meaning when using their corrisponding subclass of UArcInventoryComponent.  See the Inventory page for more details!


## Items

Items are split into two different concepts, the `Item Stack` and the `Item Defininition`.  

`Item Stack`
: A Replicated UObject that contains instance-specific data for a given item.  It's very light and thin to support many items being replicated

`Item Definition`
: A default-object only blueprint class that contains all of the static data for each item.

These concepts are split to allow Item Stack to be as light as possible with replication.  

!!! info
    It is intended that you subclass both `UArcItemStack` and `UArcItemDefinition` for your custom implementations

## Item Generators

Item Generators are factories for creating items.  An item generator can be as simple as just tying together an Item Definition or as complex as procedurally generating an item with multiple prefixes, suffixes, and random stats.  





--8<-- "includes/abbreviations.md"