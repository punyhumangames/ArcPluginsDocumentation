# Arc Inventory Tutorial

How to create a simple Active and Equippable Inventory

## Part 0: Setup Arc Inventory

This Tutorial assumes you've setup the inventory plugin and have it working.  It also assumes you have some form of Ability for a weapon.  You can use the FPS shoot ability from the example.

This Tutorial assumes you have a Player Pawn and the Ability System is setup for it.  Refer to the [GASDocumentation](https://github.com/tranek/GASDocumentation) by tranek for tips and strategies to set that up.

This Tutorial assumes you are familair with Gameplay Tags and Gameplay Tag Queries.  Please refer to the [Unreal Engine Gameplay Tag documentation](https://docs.unrealengine.com/en-US/ProgrammingAndScripting/Tags/index.html) for more information.

For the UI Section of the tutorial, it assumes you are very familiar with UMG.  

This Tutorial uses a mix of C++ and Blueprint.  C++ code will be denoted by a C++ code block like so:

``` cpp
Code()
```

No C++ code is gauranteed to compile or work.  Examples are for illustration of the point.

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
    //Your character class stuff...

private: 
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Camera, meta = (AllowPrivateAccess = "true"))
	class UArcInventoryComponent* InventoryComponent;
public:
	static FName InventoryComponentName;

    class UArcInventoryComponent* GetInventoryComponent() const override { return InventoryComponent; }
};
```

YourCharacter.cpp
``` cpp
    FName AYourCharacter::InventoryComponentName(TEXT("InventoryComponent"));

    AYourCharacter::AYourCharacter(const FObjectInitializer& ObjectInitializer)
        : Super(ObjectInitializer.SetDefaultSubobjectClass<UArcInventoryComponent_Active>(InventoryComponentName))
    {
        InventoryComponent = CreateDefaultSubobject<UArcInventoryComponent>(InventoryComponentName); 

    }
```

!!! note
    This setup, with the `static FName InventoryComponentName` and `SetDefaultSubobjectClass` allows you to have a bit of a heirarchy for your character classes.  For example, you can have a base class that uses a basic inventory component and a 'PlayerCharacter' subclass that has an Active inventory component.  It is up to you if you want to go this route.

Next, create a new Blueprint Class that is a child of `AYourCharacter`, named BP_YourCharacter.  Verify that you have an Inventory Component on your character.


## Part 2: Creating Item Slots

Arc Inventory makes a very clear distinction between underlying data and the visual representation of that data.  This allows for seperation of work for larger teams, but also for quick iteration of doing temp UI to get design down.  

We'll start with adding item slots.  Our goal will be to create an inventory layout similar to the screenshot on the marketplace for Arc Inventory.  For that, we have the following:

* Primary Weapon
* Secondary Weapon
* Head Armor
* Body Armor
* Passive Slot 1
* Passive Slot 2
* N Bag slots

The Primary Weapon and Secondary Weapon slots can hold Active Items (IE: weapons that can be held in the character's hand), and for the sake of this tutorial, we'll say that only Pistols can go in the Secondary Weapon slot and any active items can go into primary.  Head and Body armor require items that fit directly in that slot, but Passives can hold any passive item except Head and Body armor.  N Bag Slots means that you have any number (lets say 12) slots that can hold any item, but if the item is in that bag slot it does nothing. 

Open BP_YourCharacter and navigate to the Inventory Component on it.  There, you will see 'Bag Slot Count'.  Set that to 12 (Note, you can bind it to a GameplayAttribute, and the bag will resize as that attribute changes).  This gives us N Bag Slots.

From there, in the Inventory Layout section, Create 6 array elements.  These will be our custom slots.

In slot 0, we want to have a primary weapon.  So, in the Tags, Give the slot the Active item tag and the Equipment tag (Ensure you set these in the Arc Inventory settings in the project settings).  This will denote this slot as both an Active and Equipment slot, and those behaviors will activate when an item is placed into this slot.  We also want to indicate that this is the 'Primary Weapon' slot, so create a new tag `Inventory.Slot.PrimaryWeapon` and give it to this slot.  

In this slot's filter, we want to create Filter Query that accepts `ANY(Inventory.ItemType.Weapon)`, and check 'Force Single Stack'.  This will cause the Inventory to check if any item attempting to be placed into this slot contains the `Inventory.ItemType.Weapon` tag, and it will only accept items with 1 stack.  We'll set the items up later.  

Secondary Weapon is similar to Primary weapon.  Give it the Active and Equiment tags, and create a tag named `Inventory.Slot.SecondaryWeapon` and grant it.  In the filter, create a query that checks for the `Inventory.ItemType.Weapon.Pistol` tag to ensure that only pistols can be placed in this slot.  

For Head and Body Armor, we want to set the Tags to be have the Equipment Tag, and a tag for it's name (ie `Inventory.Slot.HeadArmor`).  In the Filter, Ensure that the query is looking for `ANY(Inventory.ItemType.Passive.Head)` and Body respectively.  We also want to force single stack.

The passive slots are similar, however they do not need unique tags to identify them.  Give them the Equipment Tag, and give them both the `Inventory.Slot.Passive` tag, and ensure that their filters only allow passive items (perhaps `ANY(Inventory.ItemType.Pasive) AND NOT(Inventory.ItemType.Passive.Head OR Inventory.ItemType.Passive.Body)`)  Slots do not need unique tags, and since any valid item can be placed in either slot.  If we wish to find specific items later in code, we can query for different information.  

With this, the data is set up for this inventory layout.  Now lets create a few items to put into these slots

## Part 3: Create the items

Since we already created the slots, we have an idea what kinds of items could possibly exist.  From here, lets create some weapons.  

### Weapons

Create a new Blueprint Class that inherits from UArcItemDefinition_Active.  Active Item Definitions are item definitions that denote items that can be held in a player's hand.  Active Items can be placed into any slot, but they only can become active if placed in a slot with the Active Item tag, and the player makes that item active (usually through swapping to that item).

In the Owned Tags variable, set the containter to have `Inventory.ItemType.Weapon.Rifle`, and any other tags you wish to describe this weapon.  I have seen setups with 10+ tags to describe each weapon, and there is very little cost to having many tags on a weapon.

In **Active Item Ability Info**, Set up the Active Ability Entry, by giving it a shoot ability and bind it to input.  See GAS Documentation for methods for doing input.  Add any Attribute Sets you want to this item as well, and any tags that are granted to the player.  Anything defined and set up in Active Item Ability Info is only applied to the character when the item is held in the character's hand.  

Once you are happy with this item, Duplicate this item and change it's Owned Tag to have `Inventory.ItemType.Weapon.Pistol`.  This will go in the secondary slot.  

### Equipment

Similar to weapons we want to create some new blueprint classes here.  Unlike weapons, we want to create these based on UArcItemDefinition_Equipment.  While Active items inherit from Equipment (and have many of the same properties), by inheriting from Equipment you cannot make these items active if they are in an active item slot, and thus cannot be held in hand.  

In our Owned Tags, we want to give our equipment some tags to describe the item.  we created a few tags when we created the item slots, so make sure you use those tags to describe head and body armor.

In the **Equipped Item Ability Info**, give these Head and Body armor pieces an 'Added Gameplay Effects' entry and give them a gameplay effect that is applied to the character when these items are placed into a slot with the Equipment tag.  This is done automatically.  


## Part 4: UI

This will be very light, as UI is a very complex topic and this is a getting started tutorial.  

Create a new blueprint widget for your inventory.  Split the widget in half using a Vertical Box, with the top half being a Canvas Panel and the bottom half a `BP_InventoryBagWidget` (Note: this is in the ArcInventory plugin.  You should duplicate it into your project if you wish to edit it)

In the top half, place a number of BP_ItemSlotWidgets into the Canvas Panel equal to the number of unique item slots you have.  In the details panel for each of those ItemSlotWidgets, you will see an 'Item Slot Query' property.  In the Slot Type Query field, fill out th Gameplay Tag Query to query the specific slot for each widget (for example, for one of your slots you may want to query `ANY(Inventory.Slot.PrimaryWeapon)` to make that ItemSlotWidget show what is in the primary weapon slot).

From there, consult the Example Project for how to set up responding to Drag and Drop (it's automatically set up with BP_ItemSlotWidget) and how to represent your item in the UI (also, automatically set up).  



--8<-- "includes/abbreviations.md"
