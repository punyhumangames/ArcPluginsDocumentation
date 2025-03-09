# Arc Inventory Tutorial

How to create a simple inventory with an item held in your hand

## Part 0: Setup Arc Inventory

This Tutorial assumes you've setup the inventory plugin and have it working.  It also assumes you have some form of Ability for a weapon.  You can use the FPS shoot ability from the example.

This Tutorial assumes you have a Player Pawn and the Ability System is setup for it.  Refer to the [GASDocumentation](https://github.com/tranek/GASDocumentation) by tranek for tips and strategies to set that up.

This Tutorial assumes you are familiar with Gameplay Tags and Gameplay Tag Queries.  Please refer to the [Unreal Engine Gameplay Tag documentation](https://docs.unrealengine.com/en-US/ProgrammingAndScripting/Tags/index.html) for more information.

For the UI Section of the tutorial, it assumes you are very familiar with UMG.  

This Tutorial uses a mix of C++ and Blueprint.  C++ code will be denoted by a C++ code block like so:

``` cpp
Code()
```

No C++ code is guaranteed to compile or work.  Examples are for illustration of the point.

Blueprint will be described (may add images later).

!!! note
    Ensure that in the Arc Inventory Settings in the Project Settings, appropriate tags are set for the Bag Slot, Equipment Slot, and Active slot. 




## Part 1: Setup the Inventory 

In your player pawn, Implement the `IArcInventoryInterface` from "Interfaces/ArcInventoryInterface.h".  Do so like this:

YourCharacter.h
``` cpp

UCLASS()
class YOUR_API AYourCharacter : public ACharacter, public IArcInventoryInterface /* ASC Interfaces here too */
{
	GENERATED_BODY()
	//Your character class stuff...
 
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Camera, meta = (AllowPrivateAccess = "true"))
	class UArcInventoryComponent_Modular* InventoryComponent;
public:
	static FName InventoryComponentName;
public:
	AYourCharacter(const FObjectInitializer& ObjectInitializer);
	class UArcInventoryComponent_Modular* GetInventoryComponent() const override { return InventoryComponent; }
};
```

YourCharacter.cpp
``` cpp
    FName AYourCharacter::InventoryComponentName(TEXT("InventoryComponent"));

    AYourCharacter::AYourCharacter(const FObjectInitializer& ObjectInitializer)
        : Super(ObjectInitializer)
    {
	///Your Character stuff
        InventoryComponent = CreateDefaultSubobject<UArcInventoryComponent_Modular>(InventoryComponentName); 
    }
```


Next, create a new Blueprint Class that is a child of `AYourCharacter`, named BP_YourCharacter.  Verify that you have an Inventory Component on your character.


## Part 2: Creating Item Slots

Arc Inventory makes a very clear distinction between underlying data and the visual representation of that data.  This allows for separation of work for larger teams, but also for quick iteration of doing temp UI to get design down.  

We'll start with adding item slots.  Our goal will be to create an inventory layout similar to the screenshot on the marketplace for Arc Inventory.  For that, we have the following:

* Primary Weapon
* Secondary Weapon
* Head Armor
* Body Armor
* Passive Slot 1
* Passive Slot 2
* 12 Bag slots

The Primary Weapon and Secondary Weapon slots can hold Active Items (IE: weapons that can be held in the character's hand), and for the sake of this tutorial, we'll say that only Pistols can go in the Secondary Weapon slot and any active items can go into primary.  Head and Body armor require items that fit directly in that slot, but Passives can hold any passive item except Head and Body armor.  N Bag Slots means that you have any number (lets say 12) slots that can hold any item, but if the item is in that bag slot it does nothing. 

To Achieve these different behaviors, we need to add `Inventory Processors` to the inventory.



Open BP_YourCharacter and navigate to the Inventory Component on it.  There, you will see an array of Inventory Processors.  We want to add 3 processors, Bag Processor, Equipment Processor, and Active Processor.  We'll use the default tags for each for this tutorial.

!!! tip
    The Bag, Active, and Equipment processors allow for you to set the slot tags they will look for.  We're going to use the default values for this tutorial. 


For the 12 bag slots, we need to configure the Bag Settings.  Set the property to to 12.  This will give us our Bag Slots.

!!! tip
    There is a Bag Processor that allows for binding to a Gameplay Attribute.  While we wont cover it in this tutorial, if you want a resizable bag, this is a good way to do that!

From there, in the Inventory Layout section, Create 6 array elements.  These will be our custom slots.

In slot 0, we want to have a primary weapon.  So, in the Tags, Give the slot the Active item tag `Inventory.Slot.Active` and the Equipment tag `Inventory.Slot.Equipment`.  This will denote this slot as both an Active and Equipment slot, and those behaviors will activate when an item is placed into this slot.  We also want to indicate that this is the 'Primary Weapon' slot, so create a new tag `Inventory.Slot.PrimaryWeapon` and give it to this slot.  

In this slot's filter, we want to create Filter Query that accepts `ANY(Inventory.ItemType.Weapon)`, and check 'Force Single Stack'.  This will cause the Inventory to check if any item attempting to be placed into this slot contains the `Inventory.ItemType.Weapon` tag, and it will only accept items with 1 stack.  We'll set the items up later.  

Secondary Weapon is similar to Primary weapon.  Give it the Active and Equipment tags, and create a tag named `Inventory.Slot.SecondaryWeapon` and grant it.  In the filter, create a query that checks for the `Inventory.ItemType.Weapon.Pistol` tag to ensure that only pistols can be placed in this slot.  

For Head and Body Armor, we want to set the Tags to be have the Equipment Tag, and a tag for it's name (ie `Inventory.Slot.HeadArmor`).  In the Filter, Ensure that the query is looking for `ANY(Inventory.ItemType.Passive.Head)` and Body respectively.  We also want to force single stack.

The passive slots are similar, however they do not need unique tags to identify them.  Give them the Equipment Tag, and give them both the `Inventory.Slot.Passive` tag, and ensure that their filters only allow passive items (perhaps `ANY(Inventory.ItemType.Passive) AND NOT(Inventory.ItemType.Passive.Head OR Inventory.ItemType.Passive.Body)`)  Slots do not need unique tags, and since any valid item can be placed in either slot.  If we wish to find specific items later in code, we can query for different information.  

With this, the data is set up for this inventory layout.  Now lets create a few items to put into these slots

## Part 3: Create the items

Since we already created the slots, we have an idea what kinds of items could possibly exist.  From here, lets create some weapons.  

### Weapons

First we need to create a new Item Definition for our first item, a Rifle.  Right click in the content browser and go to New -> Miscellaneous -> Data Asset.  Select `ArcInventoryModularItemDef`.  Lets name this BP_Rifle_ItemDef.  

!!! note
    Item Definitions are a collection of static item data that can applied to items.  They allow us to define 'classes' of items, and share bits of data between many item instances.  


In the Definition Tags variable, set the container to have `Inventory.ItemType.Weapon.Rifle`, and any other tags you wish to describe this weapon.  I have seen setups with 10+ tags to describe each weapon, and there is very little cost to having many tags on an item.  When this Item Definition is associated with an Item Stack, the Item Stack will return these tags.

Next, we need to give this definition some Fragments.  There are a few fragments we want to add, but you can add as much data here as you need, or even create your own custom fragments.  For this tutorial, we're just going to add some basic behavior for the item to act as a weapon, a model, a name, and a description.  

Add the following Item Fragments to the definition's Fragments Array:

* `ArcItemFragmentAbilityInfo` 
    - This fragment will give the item behaviors.  Without an AbilityInfo, no GAS elements are placed on the weapon.   We want to mark this specific Ability Info Fragment as the one used for the Active Processor, so we want to put the `Inventory.Slot.Active` tag on it.  Here we also want to give it our Shoot Ability.  There is a few example abilities in the Arc Inventory Example Project.

!!! tip
    The Ability Info fragment is extremely flexible and allows for creating many different gameplay behaviors using GAS.  Many games will place Weapon-specific attribute sets in the Active Ability Info to apply attributes to the player to manage things like rate of fire, recoil, cone of fire, ammo, and much more!

* `Arc Item Fragment Skeletal Mesh`
    - This fragment will give us a visual mesh to hold and in the world.  Select a weapon mesh, and add `Item.Fragment.Mesh.Held` and `Item.Fragment.Mesh.World` to the fragment tags.

!!! tip
    Arc Inventory provides a Static Mesh fragment if you want to use static meshes.  You can also add a second mesh fragment and give it one of the Held or World tags to make the world item appear different than the held item.

* `Arc Item Fragment Text`
    - We want to give the item a basic name, so we'll name this "Rifle" and give it the tag `Item.Fragment.Text.Name`.  

!!! tip
    Arc Inventory also ships with a Description field by default.  It is optional.

!!! tip
    While this tutorial will not cover Dynamic Fragments, you can override the name (or any of these fragments) on a per-item stack basis by adding a Dynamic Fragment to that item stack with the same tag as a fragment on an item definition.

* `Arc Item Fragment Item Card`
    - We want to add two of these fragments.  This will let us define the Large Item Card (or tooltip) and the Small Item Card (the icon in the UI). The first one we'll set the ItemCardWidget to `BP_SimpleSmallItemCard` and the tag `Item.Fragment.UI.ItemCard.Small`. For the second, we'll set the ItemCardWidget to `BP_SimpleLargeItemCard` and the tag to `Item.Fragment.UI.ItemCard.Tooltip`.  These two widgets will look up data from the item stack and display it.  

!!! tip
    You can design your own widgets for displaying tooltips or small item icons.  Just replace the widgets in the Item Card fragments!

* `Arc Item Fragment Texture`
    - We want to give this item an Inventory Icon.  Set the Texture to something that looks like a rifle (The example project has a texture from GameArtIcons).  Set the tags to `Item.Fragment.UI.Icon`.


And that's it.  The Weapon is now composed.  

Once you are happy with this item, Duplicate this item, name it `BP_Pistol_Def` and change it's Definition Tag to have `Inventory.ItemType.Weapon.Pistol`.  This will go in the secondary slot.  

### Equipment

Similar to weapons we want to create some new blueprint classes here.  For this, we'll want to create new item definitions the same way as we created the active items above.  In fact, most of the properties are the same, so we can just duplicate them and change properties.  

Unlike weapons, where we wanted the ability info to be applied to the player when the item is active, with equipment we want the ability info to be applied when an item is placed into an equipment slot.  To do so, we simply need to change the Ability Info fragment tag above to `Inventory.Slot.Equipment`.  This will allow the Equipment Processor to place these GAS elements onto the player when the item is placed into an equipment slot.

In the Gameplay Effects part of the Ability info, go ahead and give them a Gameplay Effect that changes some properties.  A simple example that changes a single property can be found in the Example Project.

!!! tip
    An item can have Fragments for both Active and Equipment behaviors!  Just create two fragments, giving one of them the Active tag and the other the Equipment tag.

In our Definition Tags, we want to give our equipment some tags to describe the item.  We created a few tags when we created the item slots, so make sure you use those tags to describe head and body armor.


## Part 4: UI

This will be very light, as UI is a very complex topic and this is a getting started tutorial.  

Create a new blueprint widget for your inventory.  Split the widget in half using a Vertical Box, with the top half being a Canvas Panel and the bottom half a `BP_InventoryBagWidget` (Note: this is in the ArcInventory plugin.  You should duplicate it into your project if you wish to edit it)

In the top half, place a number of BP_ItemSlotWidgets into the Canvas Panel equal to the number of unique item slots you have.  In the details panel for each of those ItemSlotWidgets, you will see an 'Item Slot Query' property.  In the Slot Type Query field, fill out th Gameplay Tag Query to query the specific slot for each widget (for example, for one of your slots you may want to query `ANY(Inventory.Slot.PrimaryWeapon)` to make that ItemSlotWidget show what is in the primary weapon slot).

From there, consult the Example Project for how to set up responding to Drag and Drop (it's automatically set up with BP_ItemSlotWidget) and how to represent your item in the UI (also, automatically set up).  



--8<-- "includes/abbreviations.md"
