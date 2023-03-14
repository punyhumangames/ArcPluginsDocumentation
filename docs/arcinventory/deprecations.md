# Deprecated Classes and Types

This page notes type and classes that have been deprecated, and will be removed.  If possible, a timeline for removal will be provided


## Upcoming Removals

#### Arc Inventory 1.0 classes
Arc Inventory 2.0 features a vast array of new types and a far better system for creating items and storing data on items.  As such, a number of old '1.0' classes were kept around to assist in porting old data to the new modular system.

The following files and classes are deprecated with the release of Arc Inventory 2.0, and will be deleted in Arc Inventory 2.3 (timeframe: around June 2023).  Please port your data over to the Modular Inventory classes if you rely on 1.0 classes.  


* `Components/ArcInventoryComponent_Bag.h/.cpp`
* `Components/ArcInventoryComponent_Equipment .h/.cpp` 
* `Components/ArcInventoryComponent_Active .h/.cpp`
Replaced by the Modular Inventory Processors of the same names

* `Generators/ArcItemGenerator_SimpleRandom .h/.cpp` 
Will be replaced and added to the Sample Project
* `Generators/ArcItemGenerator_Unique .h/.cpp` 
Replaced by ArcItemGenerator_ModularUnique

* `Item/ArcItemDefinition_New .h/.cpp`
* `Item/Definitions/ArcItemDefinition_Equipment .h/.cpp`
* `Item/Definitions/ArcItemDefinition_Active .h/.cpp`
* `Item/Perks/ArcItemDefinition_Perk .h/.cpp`
* `Item/Perks/ArcItemStack_Perk.h`
Replaced by the Modular Item Definitions and Fragments.  

* `ArcItemStack .h/.cpp`
Replaced by ArcItemStackModular
