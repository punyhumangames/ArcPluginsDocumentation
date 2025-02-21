The second concept is the Item Stack, or Item Instance.  Item Instancing can get very heavy with a lot of data, and Arc Inventory takes care to make each item as light as possible, while still being extendable.  To achieve this, Arc Inventory splits item data into `Item Fragments`, which can be applied to an Item Stack or an `Item Definition`.  The Item Stack can query for any Fragment, searching for that fragment in either the Item Stack, Item Definition, or an item's Sub Items (details below).  

The Item Definition is a Data Asset that holds a list of Fragments that do not change.  This reduces replication load massively.  While the Item Definition is optional, effective use of Item Definitions allow for efficient networking and memory usage.  


## Item Stacks

Item Stacks are derived directly from UObject, and are replicated inline with the inventory (or other replicated actor who owns the stack).  Item Stacks are as thin as possible, containing the Item Definition, any Dynamic Item Fragments, and the list of Stat Tags.  

!!! warning
    Internally, Item Stacks are duplicated or moved around, so a pointer to an item stack may expire when transferring items across inventories.  This is to help better support replication, but does mean that it's required to discard pointers to items when transferring item stacks or merging them.

Item Stacks have what is known as a Stat Tag array.  This is a list of Tag => Integer mappings that can be used to measure stack size or any other integer property on an item.  

### Fragments

Item Fragments are the instanced properties for each item.  They are composable, overridable, and optionally replicated.  To acquire a Fragment, the Item Stack provides query functions named `FindFirstFragment` and `FindAllFragments`.  

Fragments originating from an Item Definition are known as "Static Fragments", and do not replicate (the item definition pointer itself is replicated).  Fragments originating from the Item Stack's fragment array are known as "Dynamic Fragments", and can be replicated.  Fragments originating from a Sub Item are known as "Sub Item Fragments", and may or may not replicate depending on if the sub item fragment is static or dynamic. 

`FindFirstFragment` returns the first fragment found that matches the query from either the Sub Item Stacks, The Dynamic Fragments, or the Item Definition (in that order).  This behavior can be used to create overrides for certain Fragments, using Sub Items or Dynamic Fragments to override base properties.

`FindAllFragments` returns every fragment that matches the query.  This includes Dynamic Fragments and Sub Item Fragments.  It is up to the user to determine where the fragments came from.

!!! tip
    Item Fragments can be created in either C++ or Blueprint.  

!!! tip
    A large amount of item fragments can cause undesirable performance with the garbage collector.  It's best to make larger fragments with lots of properties that all share the same concept.  For example, all the properties needed to show the item in your UI should be placed onto one Fragment
    
!!! tip
    Dynamic Fragments are intensive on networking, so for multiplayer games, it's best to limit the number of dynamic fragments used.

!!! note
    In Arc Inventory 1.0, it was often necessary to access the Item Definition to access properties on an item.  This is no longer needed in 2.0, as the Fragment Queries handle accessing properties.  It is not recommended to access the item definition from an item stack pointer.  

### Sub Item Stacks

Item Stacks can contain a list of Item Stacks as "Sub Items".  These are fully functional items with fragments that are attached to the parent item.  Sub Items can be nested indefinitely.  This can be used to create a component system. 

Sub Items are replicated inline, and every item can have different sub items.  They are ideal for replicating option information with certain item instances.  

Fragments on a Sub Item Stack are returned first when querying the parent item's Fragments.  This allows Sub Items to override parent item fragments.

!!! tip
    Use Sub Item Stacks to implement perks, attachments, or even firing modes on your items!

## Item Definitions

Item Definitions are the static data for items.  They are Data Assets, and are instanced once per game instance and replicated simply as a pointer.  

Dynamic Fragments on an Item Stack are returned before Fragments on an Item Definition when querying for fragments.  This can be used to override a Definition's fragment with a dynamic fragment.  

!!! tip
    Create default values for item names and descriptions in Item Definitions, and override it with a Dynamic Fragment on an item stack!

!!! tip
    Item Definitions are entirely optional on an item.  While there is quite a bit of network overhead with a fully dynamic item, it's entirely possible.  It works great in single player scenarios!

## World Stacks

Given that Item Stacks are not actors, Items cannot be placed in the game world.  Since this is a common need for inventory systems, Arc Inventory contains a class, `AArcItemStackWorldObject`, which implements basic replication and visuals for an item. 

It is recommended that you set up a subclass of this actor, and override the setting in the Project Settings to use it.  You can use `UArcItemBPFunctionLibrary::SpawnWorldItem` or the `Spawn World Item` function in blueprint to spawn World Items easily.  

!!! tip
    If you want different items to behave differently when spawned into the world, you will need to spawn the World Stack yourself, and call `SetInventoryStack` on it with the item stack you wish to assign.


--8<-- "includes/abbreviations.md"