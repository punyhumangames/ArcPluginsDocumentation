## What Is MoveIt?

!!! quote "MoveIt's Marketplace Description"
    MoveIt! is a semi-procedural locomotion system for Unreal Engine 4 featuring full network replication and easy integration. MoveIt! is aimed at all game types, and works especially well with shooters or melee games that are camera-facing.

    MoveIt! was spawned from a need for high quality, replicated, and performance focused locomotion that simply did not exist. Very clean, easy to use, well documented and works flawlessly in multiplayer while being built on top of a solid foundation allowing for on-going updates and improvements.

    MoveIt! also utilizes multi-threaded animation which is very rare with marketplace assets, and will allow you to have more characters with better performance!

You can acquire it here on the [Unreal Marketplace](https://www.unrealengine.com/marketplace/en-US/product/moveit-locomotion-system)

## Project Setup

To begin integration, ensure that MoveIt is setup according to their documentation

=== "MoveIt 1.x"
    [MoveIt Project Setup](https://github.com/DrowningDragons/MoveIt-Wiki/wiki/Project-Setup)

=== "MoveIt 2.x"
    Documentation is not released yet

!!! warning
    Ensure that `"MoveIt"` is part of your PublicDependencyModuleNames in your build.cs!



## Subclassing ItemDef

Once we've set up MoveIt, it's time to integrate arc inventory.

First, we are going to create a new Subclass of `UArcItemDefinition_Active`, and add MoveIt's `FMIWeapon` structure to it

``` cpp
UCLASS()
class MYGAME_API UMyItemDefinition_Active : public UArcItemDefinition_Active
{
    GENERATED_BODY()
public:
   
    UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Attachment")
    FMIWeapon WeaponAttachData;
};
```

Compile that, and then launch your editor.  

From there, Create a new Blueprint based on `UMyItemDefinition_Active`, name it `BP_MyMoveItItem`.  Save this item

!!! tip
    If you already have an item, you can simply reparent already existing items to this new class

From there, configure the `Weapon Attach Data` field, but leave `Weapon Mesh` as None.

!!! note
    MoveIt ships with a Rifle and Pistol animation pose and offset.  Your own custom animations can be placed here, but that is beyond the scope of this tutorial.

    For more information, refer to the MoveIt [Weapon Documentation](https://github.com/DrowningDragons/MoveIt-Wiki/wiki/Weapons)

## Setting Up the Player

In your Character blueprint, create a new Variable named `WeaponInfo` of type `MI Weapon`.  This will be where we store the Weapon Info struct from the item.

Also, in your character blueprint, Create a new Skeletal Mesh component as a child of Mesh.  Name it `Held Item Mesh`.  

From there, Select your Inventory Component, and scroll to the Events section.  Press the big green button for "On Item Active" and "On Item Inactive" to bind a blueprint function to these events.

In your OnItemActive handler, cast the `Item Stack` to `MyItemDefinition_Active`.  Call `Get Class Defaults` on the resulting struct to grab the Weapon Attach Data.  Set your Character's `Weapon Info` variable to be that attach data.  

![image](../img/miint/onitemactive.png)

Also, in this handler, grab the Held Item Mesh from the class (you may have to select the Get Class Defaults pin and delect it in the details editor) and assign it to your HeldItemMesh component's mesh.

in the OnItemInactive handler, simply set MI Weapon to be an empty struct, and set your HeldItemMesh's mesh to empty.

Finally, in Functions, hit Override and override the function `Get Weapon Anim Info`.  In there, drag in a getter for your `WeaponInfo` struct and right click on it, select 'Split Struct Pin'. Drag off from `Weapon Info Weapon Pose` an Is Valid check.  If it is not valid, simply return an empty struct.  If it is valid, create a new Return node, and right click on that and Split Struct Pin.  Connect all the pins from your class's weapon struct to the return node's pins, except the `Return Value Weapon Mesh` which should be linked to your `Held Item Mesh` component.

![image](../img/miint/getweaponaniminfo.png)


## Conclusion

That is it!  MoveIt will now properly animate your character to hold the weapon.


--8<-- "includes/abbreviations.md"