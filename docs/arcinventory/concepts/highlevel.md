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

Inventories are the primary container for Items.  Inventory is an actor component that handles the placement of items into Slots.  

!!! note
    Inventories components are split into two classes: `UArcInventoryComponent` and `UArcInventoryComponent_Modular` (which inherits from `UArcInventoryComponent`).  The Base Inventory Component handles the management of Inventory Slots and basic inventory operations (such as adding and removing items), while the Modular Inventory Component provides the framework for gameplay integration with inventory data.  It is intended that you use `UArcInventoryComponent_Modular` for all your gameplay classes.    

At it's core, Inventories are an array of `Inventory Slots`.  Slots are defined with a set of Gameplay Tags, and can have an Item Filter to indicate which items can be placed into the slot.  

The Modular Inventory Component is the primary class for the system.  It contains a number of `Inventory Processors` that provide functionality for the inventory when items are placed into a slot, when items in a slot update, or various other events.

Arc Inventory ships with 3 default processors: `Bag`, `Equipment`, and `Active`. 


`Bag Processor`
:   The Bag Processor creates a number of slots in the inventory with a shared tag, and sets filters for that tag.  This can be used to create many slots to store items in. 

`Equipment Processor`
:   Equipment Processors listen to for an event when items are placed into specifically tagged slots.  Once an item is placed into that slot, the Processor looks up it's AbilityInfo fragment matching the Equipment Tag and applies the Abilities, Attribute Sets, and Gameplay Effects to the owning actor.  

`Active Processor`
:   The Active Processor collects a list of slots that match the Active Processor's slot query.  It then manages which item slot of that list is "Active", as only one item in that list can be active at a time.  When an item is made active, the Processor sends a "Item Active" event to the owner, and then looks up the AbilityInfo fragment matching the Active Slot on the item in that slot and applies the Abilities, Attribute Sets, and Gameplay Effects to the owning actor.  This can be thought of as the item currently held by the character in a first person shooter.

Item Slots can hold any number of tags, allowing for multiple processors to match the same slot if behaviors are desired there.  

!!! note
    Wile ArcInventory provides a number of built in processors, you may need to create your own for your own gameplay needs.  Simply subclass `UArcItemProcessor` in C++ or Blueprint and override any of it's functions to get started.  

## Items

Every item instance is represented by a `Item Stack` UObject.  In a multiplayer scenario, the ItemStack handles replication as a replicated subobject of the actor that owns the inventory that the item is in.  Items not owned by an actor are garbage, and will be garbage collected.

!!! note
    Items are split into the classes `UArcItemStackBase` and `UArcItemStackModular`, which inherits from UArcItemStackBase. `UArcItemStackBase` contains all of the replication and management code, where UArcItemStackModular contains the properties, fragments, and other higher level functionality bits.  It is recommended that you use `UArcItemStackModular` in all game code and blueprint.

Items contain a list of Fragments, which provide per-item properties to the item.  Fragments can be replicated.  

Items may hold a `Item Definition`, which is a list of fragments shared between all items that share the same definition.  Fragments within an Item Definition cannot be modified and are not individually replicated.

Fragments placed onto an Item Stack directly are known as `Dynamic Fragments`.  They may override fragments placed on the Item Definition.

Items may have `Sub Items`, which are items attached to other items.  SubItems are not a special class.  Any Item can be attached to any other item.  Fragments on sub items may override fragments on parent items, making them useful as attachments, perks, or other gameplay design elements.

!!! note
    While Arc Inventory provides a number of built in item fragments, it is intended that you create your own for your own gameplay needs, and makes creating new fragments as easy as possible.  

## Item Generators

Item Generators are factories for creating items.  An item generator can be as simple as just tying together an Item Definition or as complex as procedurally generating an item with multiple prefixes, suffixes, and random stats.  





--8<-- "includes/abbreviations.md"