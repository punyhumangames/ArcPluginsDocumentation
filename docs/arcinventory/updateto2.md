# Understanding and Upgrading to Arc Inventory 2.0

So you're working on a project that is upgrading from Arc Inventory 1.0 to 2.0.  2.0 involves a very significant data layout change for Item properties and data, and is a bit of a change in how things operate.  

Arc Inventory 2.0 makes use of a Modular Inventory system.  This document will go over the changes, the rational, how to approach development using these modular pieces, and some tips and tricks to update 1.0 code to 2.0 code.

## Modular Inventory 101 - Fragments and Processors

The core change from Arc Inventory 1.0 is a transition from a polymorphic inheritance based system for item data to a modular, data oriented system based around Fragments and Processors.

In Arc Inventory 2.0, **Item Fragments** are given to Items to track data, and **Inventory Processors** are given to an inventory component to make the items do things.  

!!! note
    Almost every inventory function now returns a `UArcItemStackBase*` instead of `UArcItemStack*` (which is now just for 1.0 item stacks).  This is for compatibility between 1.0 systems and 2.0 systems.  It is best to cast it to a `UArcItemStackModular*` and use the item stack that way.  `UArcItemStackBase*` does not have Item Definitions or Stack sizes.  

!!! warning
    The change to `UArcItemStackBase*` may prevent your code from compiling or inventory functions from working as before, and you may have to update your code to `ArcItemStackBase`.  Arc Inventory 2.0 is NOT compatible with ArcInventory 1.0 data.  Classes and data remain _only_ to help port data from 1.0 to 2.0.  

### Modular Inventory

You will have to migrate all Inventory components to `UArcInventory_Modular`.  This is the primary inventory component for ArcInventory 2.0, and where all functionality derives.  

If your actors use the `UArcInventory` base class for holding the inventory component as outlined in the SimpleFPS tutorial for ArcInventory 1.0, in C++ you can use `ObjectInitializer.SetDefaultSubobjectClass<UArcInventoryComponent_Modular>(InventoryComponentName)` in your constructor to change the subclass to the Modular Inventory.  

!!! warning
    Unreal Engine 5.0 has a bug where simply changing the component from the 1.0 inventory subclass to the Modular Inventory causes data corruption in blueprint.  It is recommended you back up any blueprint when transitioning to the new class.  We've found that setting the inventory component property to BlueprintReadWrite and then setting the component to None, and then back to Modular Inventory allows the blueprint to work.  However, this causes data loss.  It is recommended that you copy, screenshot, or otherwise store your custom item slots and active inventory settings before migrating to Modular Inventory.  

    The bug has been reported to Epic.   

### Item Fragments

Item Fragments are a UObject based object that are instanced either on an Item Stack or an Item Definition.  There is an API (`FindFragment` or `FindAllFragments`) on the ItemStack to access a given Item Fragment, and that function can pull from either the Item Definition or the Item Stack itself.

For example:

A gun would have an Item Definition with a number of fragments that define the behaviors of the gun, such as what abilities are granted to the player, what it looks like, what sounds it makes, damage, etc.  

When that gun is generated, a Rarity Fragment and Name fragment are created and placed onto the Item Stack.

When some code that cares about the item queries for data, it uses `FindFragment` to search up that information and use it.  

Fragments of the same type and tags can exist on both the Item Stack and the Item Definition, and the Item Stack's version will be returned instead of the Definition's version.  This lets us override elements of an item on a per-item basis.

### Item Processors

Item Processors listen to events on the inventory and do things when those happen.  For example, the Active processor will listen for when items are placed into active item slots, and it manages which items are active.

Item Processors can be considered as components of an inventory.  

### Other changes

Bits of data that can be represented as an Integer are now stored in a tagged data stack.  For example, Stack Size is now stored there.  One could also implement something like Durability this way as well.

### Item Generators

There is a new Item Generator, the Modular Unique generator.  This functions nearly identically to the Unique item generator (producing a single item with specific settings), however it uses the modular item definitions and can place Fragments directly onto an item stack.  

Otherwise, the Item Generator system hasn't changed much.

!!! note
    There is no 2.0 version of the Simple Random Item Generator yet.  It will be added in a future update. 


## Updating 1.0 Items to 2.0 Items

Arc Inventory 2.0 comes with a helpful script that converts 1.0 items to 2.0 items.  Simply right click on an Arc Inventory 1.0 item definition, select "Scripted Actions" and choose "Convert Old Item Definition to Modular".  This will create a `Convert/` folder in the directory of that item and port all the data in that old item definition into a new item definition.

!!! note
    The Conversion script is located in the [Arc Inventory Sample Project](https://drive.google.com/file/d/1p3AgfIiSpDTTwt0kQ_2z4odG4h_QSYuN/view?usp=share_link).  Copy the script to your own project, and make modifications to support your own data.  

    If you change the way it ports data, simply re-run it over items that have already been converted and it will update already created modular item definitions with new fragments.  



## Creating New Items

When creating a new type of item, care should be taken to consider what the item needs to be functional.  A simple item needs a Static Mesh fragment with the `Item.Fragment.World` tag, it needs a Text fragment with the `Item.Fragment.Name` tag, and probably a UI icon in the form of a Texture fragment with the `Item.Fragment.UI.Icon` tag.  Beyond that, consider the functionality of the item and what fragments already exist to provide it.  Don't be afraid to add as many fragments as you need!  

If you need to create a fragment, try to break the fragment definition down into as many reusable parts as possible.  For example, Scopes can have one or two attachments, so creating a fragment for "Attachment" and distinguishing them using tags is ideal. 

Fragments can also be added to an item dynamically at runtime.  This is very useful if you need to store complex data on an item, such as an item that tracks a PlayerState or the applied abilities for an Ability Info fragment.  For many items, you may want to add dynamic fragments at item generation time, or in other cases, you may want to add things to an item when it's placed in a slot in the inventory (by way of an Item Processor). 

An item does not have to have an Item Definition.  This should be a rare case, but creating a fully dynamic item with fragments is entirely possible!  It is very hefty on replication load, but if you have cool ideas in this space, don't hesitate to explore them (Please report any performance issues on Discord).  



