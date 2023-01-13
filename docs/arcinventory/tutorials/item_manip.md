# Manipulating Items inside and outside of your Inventory



## Swapping Slots

The Primary method to swap items is to activate the built in Swap Item Slots Ability.  This is stored in the ArcInventoryContent folder in your content browser.  

!!! note
    If you do not see it, you will need to click the little eyeball and enable "Show Plugin Content" and "Show Engine Content"

Swapping Items is done by setting, on the client, the `SwapToSlot` and `SwapFromSlot` on the Inventory component, and then activating the ability.  The Ability will then synchronize the FromSlot and ToSlot to the server, and execute the `SwapItemSlots` function.  This is predicted on the local client, so feedback is instant.

!!! note
    The default Item Slot Widget will try to activate abilities by the Swap Item Slot tag in the Project settings.  If you cannot get item swapping working, check that your character has that ability, and that tag is set in the project files, and that tag is part of the Ability Tags in the Swap Item Slot ability.  

    If you are writing your own UI, it is recommended that you set the `SwapToSlot` and `SwapFromSlot` properties and then call the ability.  

!!! tip
    It is recommended that you copy the Swap Item Slots ability out of the plugin and into your project.  Games may need more rules around swapping item slots, and modifying an asset in the plugins folder can be dangerous.

    You can also create your own SwapItemSlots ability if you want to use your own replication method


## Creating the World Item

In order for Arc Inventory to represent items in the world, we need to create a Item Stack World Object.  This actor is not an item stack itself, but it holds item stacks for you.   Arc Inventory provides a default implementation that handles replication for you, so we're going to create a blueprint subclass of that and make use of it.

Right click in the content browser and create a new blueprint class.  Search for `ArcItemStackWorldObject` and create a new blueprint of that named `BP_WorldItem`.  We're going to add some blueprint script to this later, but for now just save and compile the blueprint.

Once that is created, open your Project settings, and go to the `Arc Inventory Developer Settings`.  Find the `Item Stack World Object Class` setting and set it to be your `BP_WorldItem`.  Now the plugin will use your blueprinted item whenever a World Item is needed.


## Dropping Items

Included in the plugin is an ability that will drop an item from an item slot.  This Ability handles all dropping of items by replicating the slot to the server, removing the item from the inventory in that slot, and then spawning a "World Item" with that stack as part of it (that we set up above).

The removal of items from your inventory is not predicted.  In high latency environments, you will have a delay between the user attempting to drop the item, and the item being removed from the inventory and spawning in the world.   

!!! note
    The Default Item Slot Widget will attempt to drop an item into the world if the drag and drop operation ends without an underlying Item Slot Widget.  This is done by activating any ability on the owning character with the `Item Slot Drop Ability Tags`.  As with Swapping, ensure that your character has this ability, that tag is set in the project settings, and the ability has it in it's Ability Tags.

    If you are writing your own UI, it's recommended that you set the `PendingItemDrop` property in Inventory and call the ability by tag.  

!!! tip
    As with the Swap Item Slots ability, it is recommended that you copy this ability out of the plugin and into your project.

    Also as with the Swap Item Slots ability, you can create your own if you need to.


## Creating a simple interaction ability
!!! note
    This part assumes that your character is set up similarly to the character in the Arc Inventory Example Project.  This part of the tutorial attempts to convey the concepts needed, but your game may want to do this in it's own way.

In order to pick up items or interact with other inventories, we need to create a very simple interaction system.  First, we're going to create a simple blueprint interface for interaction.  Right click in your content browser, go to the Blueprints category, and select Blueprint Interface.  We'll name it `BPI_Interact`.  Rename the first function it creates to "On Interact" and add a input parameter of type Actor named "Interactor".

Next, we're going to create a simple Interact ability.  Right Click in the content browser, go to the Gameplay category, and create a new Gameplay Ability.  Inherity from the base Gameplay Ability, and name it `BP_Interact_GPA`.  

Open the newly created ability, and lets create a simple interaction.  From the Activate Ability node, create a Sequence node.  From the Then 0 pin, create a Commit Ability, and then branch off it's return value.  If True, do a Sphere Trace For Objects.  Create a Get Avatar from Actor Info node, and from that do a Get Actor Eyes View Point.  Wire the Out Location pin to the Start input in the Sphere trace.  Wire Out Rotation to Get Forward Vector, then multiply that by 400.  Add the Out Location vector to the multiplied forward, and then connect that to the End pin in the Sphere trace.  Make an array of Object Types and add set the 0th element to WorldDynamic.  Make an array for Actors To Ignore, and wire the Avatar Actor to the 0th element of that array.  

After the Sphere Trace, Wire the Return Value bool to a Branch, and if true, Break the hit result and cast the Hit Actor to BPI_Interact.  Call On Interact on that interface pin, and pass the Avatar Actor to the Interactor parameter.

Go back to the Sequence node at the start, and connect "End Ability" to the Then 1 Pin.

[Blueprint Graph](https://blueprintue.com/render/cb7keqr4/)
<div class="embeddedbp">
    <iframe src="https://blueprintue.com/render/cb7keqr4/" seamless scrolling="no" height="400" allowfullscreen></iframe>
</div>


Finally, once this ability is created, we want to bind it to input.  While your game may do this differently, The Inventory Example project has a input triggered starting abilities map that we'll add this ability to, and bind it to the "Interact" Enhanced Input Action

## Pick up an item

For picking up items, we're going to use the Interact ability and interface that we created, and the World Item we also created.  

Open up `BP_WorldItem`, and go to Class Settings (you may need to open the full blueprint editor).  In the `Interfaces` Category, we want to click the "Add" drop down in the Inherited Interfaces section and type in and select `BPI_Interact`.  Now, we have a On Interact function we can implement, so lets do that.  Right click on that function and create the implementation.

From that On Interact node, we want to create a Switch Has Authority node, and from the Authority pin we want to Get Component By Class using the Arc Inventory Component Modular class.  Check if it's valid, and if so, call Loot Item on that component, getting the Inventory Stack from the world item for the Item pin.  We'll leave Event Tag empty.  From the Return Value of Loot Item, we want to branch, and if True, Destroy the actor.  

[Blueprint Graph](https://blueprintue.com/blueprint/3i82um_q/)
<div class="embeddedbp">
    <iframe src="https://blueprintue.com/render/3i82um_q/" scrolling="no" height="400" allowfullscreen></iframe>
</div>

Now when we interact with the world item, the item will be placed into our inventory!

## Create A Container

Any Actor can hold an Inventory Component.  Item Stashes or Chests are a good example of an actor that can hold many items.

### Create a Container Actor


To Start, Create a new Actor, which we will call `BP_ExampleChest`.  Give that Actor some visuals (A cube will suffice, but I use the fantastic KennyNL assets) and a Modular inventory component.  Give the Inventory a Bag Processor and 12 slots.

!!! warning
    For this to work in Multiplayer, ensure that both the Actor and it's Inventory are set to replicate!

Open up the Class Settings, and much like the World Item, add the `BPI_Interact` interface to this chest.  

Next, we want to create a property on the chest named `StartingItems`, and set it to be the `Arc Starting Item` struct type, and right click on it so that it's an Array.  Set it to Instance Visible (the eye icon).

From there, we want to add some code to Begin Play so we spawn these starting items into the chest.  From Begin Play, add a Switch Has Authority node, and from Authority we want to do a For Each Loop.  The Array will be our `Starting Items` property.  From Array Element, Break the starting item entry struct, and from Item Generator call Generate Item Stack.  From the Context pin, make a ArcItemGeneratorContext struct but don't worry about setting any of the properties.  From the Return Value of the Generate Item Stack function, we want to call Loot Item on our Inventory, and feed the results of Generate Item Stack into it's Item field.  

[Blueprint Graph](https://blueprintue.com/blueprint/clqm9ysx/)
<div class="embeddedbp">
    <iframe src="https://blueprintue.com/render/clqm9ysx/" scrolling="no" height="400" allowfullscreen></iframe>
</div>


Place this actor in the world.  This is our stash that will hold our items.  You can add items to the starting items in the actor properties in the world.  


### Create a View Widget
Now, we need to be able to inspect it.  We must create a UMG widget to view this container.  Create a new widget that we'll call `WBP_SampleChestView`.  Lay out the widget however you want, but I went with a simple black border.  

In the content of the widget, Add in a `BP_InventoryBagWidget` to your widget.  This widget will display the contents of any inventory with slots that match the bag tag.  

Tab over to the Graph for the widget, and create a new blueprint event.  We'll name this `Bind` and make sure it has a Arc Inventory Component input parameter.

From that event, we want to grab the Inventory Bag Widget property and call Set Target Inventory on it.  Give it the value of the Bind event parameter.  Then, we want to call Refresh on it.  

[Blueprint Graph](https://blueprintue.com/blueprint/hzws5rf4/)
<div class="embeddedbp">
    <iframe src="https://blueprintue.com/render/hzws5rf4/" scrolling="no" height="400" allowfullscreen></iframe>
</div>


!!! info
    I also added a "Close" button to my widget, that removes the widget from parent.  You may want to control interaction differently.

### Interaction

Next, we need to interact with a chest to view it's contents.  Go back to the `BP_ExampleChest` blueprint.

Since we implemented the BPI_Interact interface, we simply need to, on interaction, display this UI widget.  Right click on the On Interact interface function and implement it.  From that, lets Create a widget, passing in our `WBP_SampleChestView` widget.  Owning Player is going to be Player Controller 0.  From that widget, we want to call the Bind function we created, and then Add it to viewport.  

[Blueprint Graph](https://blueprintue.com/blueprint/qou0es5p/)
<div class="embeddedbp">
    <iframe src="https://blueprintue.com/render/qou0es5p/" scrolling="no" height="400" allowfullscreen></iframe>
</div>

From here, you can set the Input Mode and show the mouse cursor, but your game may handle UI input it's own way 

!!! tip
    Lyra has a Common UI Implementation that makes this part very easy!

### Transfer Items between Two Inventories

Simply dragging the item between your inventory and the target inventory should work via Drag and Drop.

The Inventory takes any two inventory slots to transfer items to/from.  So long as one of the slotrefs reference a slot in the player's inventory, it will swap between the two inventories.  

!!! caution
    The default implementation does not check if a player should be transferring items between two inventories.  It is up to you to implement these checks

--8<-- "includes/abbreviations.md"