# Deprecated Classes and Types

This page notes type and classes that have been deprecated, and will be removed.  If possible, a timeline for removal will be provided


## Upcoming Removals

#### Arc Inventory 1.0 classes
Arc Inventory 2.0 features a vast array of new types and a far better system for creating items and storing data on items.  As such, a number of old '1.0' classes were kept around to assist in porting old data to the new modular system.

The following files and classes are deprecated with the release of Arc Inventory 2.0, and will be deleted in Arc Inventory 2.3 (timeframe: around June 2023).  Please port your data over to the Modular Inventory classes if you rely on 1.0 classes.  


* `Components/ArcInventoryComponent_Bag.h/.cpp`
* `Components/ArcInventoryComponent_Equipment .h/.cpp` 
* `Components/ArcInventoryComponent_Active .h/.cpp`

!!! info 
    Replaced by the Modular Inventory Processors of the same names

* `Generators/ArcItemGenerator_SimpleRandom .h/.cpp` 

!!! info
    Will be replaced and added to the Sample Project

* `Generators/ArcItemGenerator_Unique .h/.cpp` 

!!! info
    Replaced by ArcItemGenerator_ModularUnique

* `Item/ArcItemDefinition_New .h/.cpp`
* `Item/Definitions/ArcItemDefinition_Equipment .h/.cpp`
* `Item/Definitions/ArcItemDefinition_Active .h/.cpp`
* `Item/Perks/ArcItemDefinition_Perk .h/.cpp`
* `Item/Perks/ArcItemStack_Perk.h`

!!! info
    Replaced by the Modular Item Definitions and Fragments.  

* `ArcItemStack .h/.cpp`

!!! info
    Replaced by ArcItemStackModular

#### Arc Inventory plugin blueprints

With the launch of Arc Inventory 2.0, we've begun keeping all supported versions of Arc Inventory up to date.  However, this has lead to maintenance issues with saving these assets with various engine versions.  

In an effort to ease the upgrade path for these blueprints, they are being migrated to the Arc Inventory Example Project, where they can be easily migrated into your project.

As such, the following blueprints are deprecated and will be removed in Arc Inventory 2.3 (timeframe: around June 2023).

* UI/BP_ArcInventoryUIFunctionLibrary
* UI/BP_BaseItemTooltip
* UI/BP_EmptyItemCard
* UI/BP_ItemSlotDragAndDrop
* UI/BP_ItemSlotReference
* UI/BP_ItemSlotWidget
* UI/wooden-crate
* UI/Examples/BP_InventoryBagWidget
* UI/Examples/ItemCards/BP_SimpleItemUIData
* UI/Examples/ItemCards/BP_SimpleLargeItemCard
* UI/Examples/ItemCards/BP_SimplePerkItemCard
* UI/Examples/ItemCards/BP_SimpleSmallItemCard

* Abilities/BP_DropItemFromSlotNew
* Abilities/BP_LootAllTheThings
* Abilities/BP_SwapItemSlots

* Input/ArcInvInputBinder_Raw

All of these files will be located in the Arc Inventory Example project, and the most recent engine versions (starting with 5.1) will be tagged.  