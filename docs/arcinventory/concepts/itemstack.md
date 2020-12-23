The second concept is the Item Stack, or Item Instance.  Item Instancing can get very heavy with a lot of data, and Arc Inventory takes care to make each item as light as possible, while still being extendable.  To achieve this, Arc Inventory creates two concepts for storing item data, the Stack and the Definition.  The Item Stack is the per-instance data, and only data that is necissary is included in it.  The Item Definition is the static data that doesn't change across instances, like the abilities an item grants or it's mesh.  

The difference between an Item Stack and an Item Defition is important and a key concept to understand with Arc Inventory

!!! tip
    It is possible and intended to extend both Item Stack and Item Definition.  Extending Item Definition to add your own static data is extremely common.  Extending ItemStack is less common, but if you need to replicate per-instance data, it can be easily done.

## Item Stacks

Item Stacks are derived directly from UObject, and are replicated inline with the inventory (or other replicated actor who owns the stack).  Item Stacks are extremely thin, and only replicate and contain necissary information for the instance of the object.  The contain the full representation of an item, including the item definition

!!! warning
    Internally, Item Stacks are duplicated or moved around, so a pointer to an item stack may expire when transfering items across inventories.  This is to help better support replication, but does mean that it's required to discard pointers to items when transfering item stacks or merging them.

Item Stacks can have a stack size, meaning multiple items of the same definition can be combined into one single object.  They can be merged and split, creating new item stack objects in the process.  The Item Definition must allow items to do this, and two item stacks with different sub-items cannot be merged.

Item Stacks also hold instanced attribute sets for the instanced attribute system.  When the inventory needs to store off attributes or active gameplay effects, it does so by storing them in the Item Stack.

### Sub Item Stacks

Item Stacks can contain a list of Item Stacks as "Sub Items".  These are fully functional items with definitions that are attached to the parent item.  Sub Items can be nested indefinitely.  This can be used to create a component system. 

Sub Items are replicated inline, and every item can have different sub items.  They are ideal for replicating option information with certain item instances.  

!!! tip
    Use Sub Item Stacks to implement perks, attachments, or even firing modes on your items!

## Item Definitions

Item Definitions are the static data for items.  They are used as class definitions and CDOs only, never instanced.  In this regard, they can be very lightly replicated, and are key to high performance in the system.

Item Definitions carry all of the data required to make the item work.  They are often subclassed to add more data depending on the usage of that item, and easily support all kinds of data.  Different subclasses of the Inventory Component do work with the data in an Item Definition to achieve functionality.

!!! note
    It is the Inventory that applies GA primitives to the character.  The Item Definition simply contains that data.  




## World Stacks

Given that Item Stacks and Item Definitions are not actors, Items cannot be placed in the game world.  Since this is a common need for inventory systems, Arc Inventory contains a class, `AArcItemStackWorldObject`, which implements basic replication and visuals for an item. 

It is recommended that you set up a subclass of this actor, and override the setting in the Project Settings to use it.  You can use `UArcItemBPFunctionLibrary::SpawnWorldItem` or the `Spawn World Item` function in blueprint to spawn World Items easily.  

!!! tip
    If you want different items to behave differently when spawned into the world, you will need to spawn the World Stack yourself, and call `SetInventoryStack` on it with the item stack you wish to assign.


--8<-- "includes/abbreviations.md"