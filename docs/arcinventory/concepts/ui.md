User Interface is a common requirement for Inventory systems, but often requires unique and game specific implementations to achive the best look.  To this end, Arc Inventory provides a few building blocks for development inventories, but expects that user implement their own stylized views.  

Arc Inventory follows the MVC pattern for UX, with Inventory/Items acting as the Model, UMG Widgets acting as the View, and Game Framework code like Pawns, the Inventory Component, and Player Controllers acting as the Controller.

!!! tip
    While you are welcome to modify all of the UMG widgets inside of the Plugin, it is recommended that you copy them out into your own project.  Plugin Updates may overwrite any changes you make.

## UI Data

Item Specific UI Data is stored in a blueprintable `UIData` instanced object inside of the Item Definition or Item Generator.  Both ItemDef and ItemGen have this object, as it allows you display item generators to the user if your game design requires it.

UI Data objects can be easily extended in blueprint to add new properties.  It is common to create multiple types of UIData structures for different items.  UMG widgets can access this object and cast to whichever child class is needed to display it.

!!! warning
    UI Data is not replicated!  If there are specific properties you wish to replicate to display on the UI, it must be replicated in the Item Stack.

## Item Slot Widget

Item Slot Widgets are the primary method for showing the contents of an item slot.  Item Slot Widgets can query for a given slot based on some query, or can be given a SlotRef directly on construction.  Once bound to an item slot, it will display the contents of that slot.

!!! info 
    Provided is an example Bag widget, which fills the widget with Item Slot Widgets equal to the number of slots in a bag inventory, and bound to those slots.  It's a good reference for dynamically creating Slot Widgets!

### Item Cards

Items are displayed using an 'Item Card' widget, or a specific UMG subclass that contains a reference to an item and displays the information within that card.  By default, there are two types of Item Cards, Small and Large.  Small Item Cards are generally a 32x32 image displaying the item on top of the item slot widget or while dragging and dropping.  Large Item Cards are used to display the item when you mouse over the Item Card, and often contain all of the data.  The Item Cards look into the Item's UI Data for textures and names to use. 

It is up to the implementer to create their own displays, but some examples are provided in the plugin.  

### Drag and Drop

Item Slot Widgets implement drag and drop behaviors, allowing for easy swapping of item slots by simply dragging the item to another slot widget.  This is done by way of calling an ability on the player by a tag as defined in the project settings.  

!!! tip
    More information is located in the [Manipulating Items](../tutorials/containers) tutorial!

--8<-- "includes/abbreviations.md"