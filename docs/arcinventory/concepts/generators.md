
Item Generators are the factory that produces items.  Because of the need to create Item Stacks and associate them with Item Definitions, It becomes fairly involved to create new items on demand.  Item Generators seek to bridge that gap and allow control over the item production process.

Most games will not need to use Item Generators.  It is very common for a game to create a single Item Generator, set the Item Definition for it to spawn, and then spawn that item.  This is perfectly fine, and it is recommended for most games starting out using the system.  The Example Project uses this method to generate items.  

However, if you need more control over the process of creating items, Arc Inventory gives you a lot of it.

## Basic Setup

Item Generators require subclassing UArcItemGenerator and overriding the `GenerateItemStack_Implementation` function.  This can be done in Blueprint.   

Inside that function, you simply need to return a `UArcItemStack` pointer.  Most item generators will call `CreateNewItemStack` with the Defintion and an optional rarity, do some work on the item stack, and then return it.  

!!! tip
    You can create as many item stacks as you need in a single generator, which is useful for generating SubItems to attach to your item!

Arc Inventory contains a few built in item generators, including a Static Item Generator class for embedding into property editors, and a Unique Item Generator for customizing all of the components of item geration in the property editor.  

## Procedural Generation

Arc Inventory also supports the procedural generation of items through Item Generators.  Included in the plguin is a Simple Random generator that produces 'Perks' and attaches Sub Items to items, based on item rarity.  

The Simple Random generator uses a weighted random algorithm to determine how common certain elements are, and uses child item generators to produce sub items that are attached to the parent item, with a number based on the rarity.  

Simple Random is intended to be an example.  While projects may use it, Procedural Generation of items is generally a game specific mechanic, and it's advised that you create your own generator for procedural generation.


--8<-- "includes/abbreviations.md"