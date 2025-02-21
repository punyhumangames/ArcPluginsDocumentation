Inventory is the primary container for items, and the primary controller for what items do to players.  

Inventory is implemented as an Actor Component, and should be added to your actors with the `UArcInventoryComponent_Modular`.  The base class, `UArcInventoryComponent` features the core slot structure, placing items into slots, and allowing developers to understand the layout of their inventory through queries.  The Modular Inventory provides the framework for gameplay integration for your inventory.

## Base Inventory

The Primary mechanism for the base inventory is to manage and replicate Slots.  It also watches if items move in those slots, and handles the insertion and removal of items

The underlying data layout is hidden from implementors, and the public API handles all edge cases around Replication, Ownership, and reports back failure states.

The Modular Inventory also provides a bindable "Inventory Event" event that observers can bind to.  Whenever the Inventory has a change in data or a Processor has an event that can be responded to, this event is raised with a Gameplay Tag and payload, allowing for gameplay code to respond to changes in the inventory.

!!! warning
    If a function returns a bool, that means that it can fail.  It is up to you to determine how to handle that failure.  Not catching failure states can lead to Items being lost!


### Slots

Slots are the primary container for items.  The underlying replication type, `FArcItemSlot` is not exposed to the public API, but it covers the details of replication and ensuring that slots are synchronized between client and server.  The public API exposes a `FArcItemSlotReference`, which contains all the data needed to reference a slot in an inventory.  All functions that refer to inventory slots take a `FArcItemSlotReference`, and for all purposes, that is the item slot.  

!!! note
    Always check if the slotref is valid before attempting to access it or pass it into an inventory.  

!!! warning
    `FArcItemSlotReference` contains a weak pointer to the owning inventory.  If that inventory is destroyed, the slotref will be invalid.  It is not recommend that you store a slotref across multiple frames.  It is recommend that you either strongly control the data lifetime, or query for the slot when you need it.  

### Slot Queries

Slot Queries are the concept for accessing data inside of an inventory.  They are represented by `FArcInventoryQuery`.   They have a number of properties to help find items in your inventory.  You can construct queries with the static functions on `FArcInventoryQuery`

The types of queries can be used together to produce a query that targets exact items.  ItemType looks for the item in the slot and matches tags on the item's tags.  ItemType queries automatically fail on slots that are empty.  SlotType Query searches the tags on each slot.  Item Definition queries look for items with the matching item definition.  Each search is mutually exclusive, so you can have a query that is looking for specific items in specific slots.     

### Slot Filters

Slot Filters allow designers to indicate what items can be placed into the slot.  Slot Filters contain a GameplayTagQuery to search the tags on a given item, and whether or not to force that slot to contain a single stack.  If there is no TagQuery, the slot accepts all items.

### Presets

Inventory settings can be defined in a data asset, `UArcInventoryComponentPreset`.  These objects allow you to define custom item slots and inventory processors in the asset, and then share that asset across multiple inventory components.  You can also add additional presets to a preset, allowing you to share a set of inventory settings across multiple presets.


### Notable Functions/Properties

* `CustomInventorySlots`
:   An EditDefaultsOnly Array of Item Slot definitions.  This is where you design out all your slots that have custom functionality.  It is very common to have a dozen or more slots defined here.  

* `CreateInventorySlot` / `RemoveInventorySlot`
: Protected runtime creation of inventory slots.  It's protected so you can access these by subclassing your inventory, but allows you to create and remove item slots.  Everything replicates fine when creating and removing slots.  

* `PlaceItemIntoSlot`
:   Places an item into a slot.  Will fail if the slot already has an item. Always check the return value of this function, and handle failure accordingly.  It is common to call LootItem if PlaceItemIntoSlot fails. 

* `LootItem`
:   Places an item into the first slot it can.  The order it checks slots is not guaranteed, so it could end up in slots you don't expect.  If it fails, that means that item cannot possibly fit into the inventory, and failure should be handled.

!!! caution
    If placing an item into a slot succeeds, it is recommend to discard the pointer to the item you currently have.  Internally, the Inventory will remap the owner of the items, sometimes duplicating the object (this is to deal with problems with Unreal Replication).  

## Inventory Processors

The Modular Inventory provides a number of Inventory Processors to add functionality to your inventory.  Processors are all derived from `UArcInventoryProcessor` and have a number of overridable functions and events to watch for changes to the inventory, and respond to them.

You can have any number of Processors on an inventory.  You can have multiple copies of the same processor.

Arc Inventory has 3 built in processors, although you are encouraged to make your own for your own gameplay needs.  

### Bag Processor

The Bag Processor creates a number of "Bag Slots", which are slots with a shared item filter.  The number of bag slots can be 0.  The Bag Processor can also set the tags of all the slots it creates.

There is a second Bag Processor class that uses a Gameplay Attribute to change the number of bag slots to the value of that attribute when it changes.    

!!! tip
    If you bind the bag slot gameplay attribute, you can change that attribute with an item!  

!!! tip
    You can have multiple bag processors with different tags applied to the slots!

### Equipment Processor

The Equipment Processor contains a query for item slots that it uses to respond to item insert and removal events on the inventory.  If an item is placed into a slot that it is watching for, and that item contains an Ability Info fragment that matches a tag set in the Equipment Processor, then the Equipment Processor places the Gameplay Abilities, Attribute Sets, and Gameplay Effects listed onto the owning actor.  If the item is removed, it removes those GAS elements.  

!!! tip
    Use the Equipment Processor  to hold things like armor in an RPG game, skills in a Overwatch-style hero shooter or MOBA, or stat-stick weapons in a Diablo or Path of Exile style Action RPG!

### Active Processor

The Active Processor contains a query for item slots that it uses to create a list of slots where it can make one item "Active" at a time.  When an item is made "Active", the Active Processor looks up the Ability Info fragment that matches the tag set in the Active Processor, and then applies the Gameplay Abilities, Attribute Sets, and Gameplay Effects to the owner actor.  If the Active Item changes, then it removes the previously active item's GAS elements.  The Active Processor also sends an Active Item Changed event to the inventory, allowing the owner to respond to that event.  

!!! tip
    Use Active Inventories for FPS or RPG games where your character can only hold one item at a time!


--8<-- "includes/abbreviations.md"