Inventory is the primary container for items, and the primary controller for what items do to players.  

Inventory is implemented as an Actor Component, and has many children classess to cover different gameplay functions.  The base class, `UArcInventoryComponent` features the core slot structure, placing items into slots, and allowing developers to understand the layout of their inventory through queries.  

## Base Inventory

The Primary mechanism for the base inventory is to manage and replicate Slots.  It also watches if items move in those slots, and handles the insertion and removal of items

The underlying data layout is hidden from implementors, and the public API handles all edge cases around Replication, Ownership, and reports back failure states.

!!! warning
    If a function returns a bool, that means that it can fail.  It is up to you to determine how to handle that failure.  Not catching failure states can lead to Items being lost!




### Slots

Slots are the primary container for items.  The underlying replication type, `FArcItemSlot` is not exposed to the public API, but it covers the details of replication and ensuring that slots are synchronized between client and server.  The public API exposes a `FArcItemSlotReference`, which contains a all the data needed to reference a slot in an inventory.  All functions that refer to inventory slots take a `FArcItemSlotReference`, and for all purposes, that is the item slot.  

!!! note
    Always check if the slotref is valid before attempting to access it or pass it into an inventory.  

!!! warning
    `FArcItemSlotReference` contains a weak pointer to the owning inventory.  If that inventory is destroyed, the slotref will be invalid.  It is not recommend that you store a slotref across multiple frames.  It is recommend that you either strongly control the data lifetime, or query for the slot when you need it.  

### Slot Queries

Slot Queries are the concept for access data inside of an inventory.  They are represented by `FArcInventoryQuery`.  They have two properties, `ItemTypeQuery` and `SlotTypeQuery`.  Item Query functions are generally prefixed with `Query_`, and the different Query functions allow accessing different types of slots. 

ItemType and SlotType can be used interchanably.  ItemType looks for the item in the slot and matches tags on the item's tags.  ItemType queries automatically fail on slots that are empty.  SlotType Query searches the tags on each slot.    

### Slot Filters

Slot Filters allow designers to indicate what items can be placed into the slot.  Slot Filters contain a GameplayTagQuery to search the tags on a given item, and whether or not to force that slot to contain a single stack.  If there is no TagQuery, the slot accepts all items.


### Notable Functions/Properties

* `CustomInventorySlots`
:   An EditDefaultsOnly Array of Item Slot definitions.  This is where you design out all your slots that have custom functionality.  It is very common to have a dozen or more slots defined here.  

* `CreateInventorySlot` / `RemoveInventorySlot`
: Protected runtime creation of inventory slots.  It's protected so you can access these by subclassing your inventory, but allows you to create and remove item slots.  Everything replicates fine when creating and removing slots.  

* `PlaceItemIntoSlot`
:   Places an item into a slot.  Will fail if the slot already has an item. Always check the return value of this function, and handle failure accordingly.  It is common to call LootItem if PlaceItemIntoSlot fails. 

* `LootItem`
:   Places an item into the first slot it can.  The order it checks slots is not garuanteed, so it could end up in slots you don't expect.  If it fails, that means that item cannot possibly fit into the inventory, and failure should be handled.

!!! caution
    If placing an item into a slot succeeds, it is recommend to discard the pointer to the item you currently have.  Internally, the Inventory will remap the owner of the items, sometimes Duplicating the object (this is to deal with problems with Unreal Replication).  


## Bag 

Bag is the first subclass of the base inventory.  Bag inventory creates a number of "Bag Slots", which are slots without item filters.  The number of bag slots can be 0.  You can also bind the number of slots to a Gameplay Attribute to change the number of bag slots at runtime.  

!!! tip
    If you bind the bag slot gameplay attribute, you can change that attribute with an item!  

## Equipment

Equipment slots are slots with the Equipment tag as defined project settings.  When an item is placed into this slot, a callback will fire indicating that the item is equipped.  If your item has an item definition derived from `UArcItemDefinition_Equipment`, then it will activate the equipped item information and placed the GAS primitives on the player.

!!! tip
    Use Equipment slots to hold things like armor in an RPG game, skills in a Overwatch-style hero shooter or MOBA, or stat-stick weapons in a Diablo or Path of Exile style Action RPG!

## Active

Active Inventories are inventories with a set of slots defined with the Active Slot tag.  In an active inventory, only one slot can be active at a time, and when that slot is active a callback is issued.  If an item is in the slot with a definition that derives from `UArcItemDefinition_Active`,  then the GAS Primitives in the Active Item Info property will be applied to the character.

!!! tip
    Use Active Inventories for FPS or RPG games where your character can only hold one item at a time!


!!! info
    If these subclasses do not support your game type, it is easy to extend an inventory component and add your own functionality.  Most games create their own Inventory Component with their own functionality when items are placed into certain slots!

--8<-- "includes/abbreviations.md"