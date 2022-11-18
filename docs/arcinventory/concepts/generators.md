
Item Generators are the factory that produces items.  Because of the need to create Item Stacks and associate them with Item Definitions or Fragments, It becomes fairly involved to create new items on demand.  Item Generators seek to bridge that gap and allow control over the item production process.

Most games will not need to use Item Generators.  It is very common for a game to create a single Item Generator, set the Item Definition for it to spawn, and then spawn that item.  This is perfectly fine, and it is recommended for most games starting out using the system.  The Example Project uses this method to generate items.  

However, if you need more control over the process of creating items, Arc Inventory gives you a lot of it.

## Basic Setup

Item Generators require subclassing UArcItemGenerator and overriding the `GenerateItemStack_Implementation` function.  This can be done in Blueprint.   

Inside that function, you simply need to return a `UArcItemStackBase` pointer.  Most item generators will call `CreateModularItemStack`, assign an Item Definition and optional Dynamic Fragments, and return it.  

!!! tip
    You can create as many item stacks as you need in a single generator, which is useful for generating SubItems to attach to your item!  However, you can only return one

Arc Inventory contains a few built in item generators, including a Static Item Generator class for embedding into property editors, and a Unique Modular Item Generator for customizing all of the components of item generation in the property editor.  Arc Inventory also provides some item generators designed to select an item generator from a weighted list of possible item generators, which is useful for creating a drop table.  

## Procedural Generation

!!! note
    The built in Random Generator is not updated for 2.0.  Expect examples of procedural generation of Modular Items in coming updates!

Arc Inventory also supports the procedural generation of items through Item Generators.  Given that Item Generators can place any item definition, dynamic fragment, or sub item stack onto an item stack before returning it, an entire procedural system can be created.



--8<-- "includes/abbreviations.md"