User Interface is a common requirement for Inventory systems, but often requires unique and game specific implementations to achieve the best look.  To this end, Arc Inventory provides a few building blocks for development inventories, but expects that user implement their own stylized views.  

Arc Inventory follows the MVC pattern for UX, with Inventory/Items acting as the Model, UMG Widgets acting as the View, and Game Framework code like Pawns, the Inventory Component, and Player Controllers acting as the Controller.

!!! tip
    While you are welcome to modify all of the UMG widgets inside of the Plugin, it is recommended that you copy them out into your own project.  Plugin Updates may overwrite any changes you make.

## Item Slot Widget

Item Slot Widgets are the primary method for showing the contents of an item slot.  Item Slot Widgets can query for a given slot based on some query, or can be given a SlotRef directly on construction.  Once bound to an item slot, it will display the contents of that slot.

!!! info 
    Provided is an example Bag widget, which fills the widget with Item Slot Widgets equal to the number of slots in a bag inventory, and bound to those slots.  It's a good reference for dynamically creating Slot Widgets!

### Item Cards

Items are displayed using an 'Item Card' widget, or a specific UMG subclass that contains a reference to an item and displays the information within that card.  By default, there are two types of Item Cards, Small and Large.  Small Item Cards are generally a 32x32 image displaying the item on top of the item slot widget or while dragging and dropping.  Large Item Cards are used to display the item when you mouse over the Item Card, and often contain all of the data.  The Item Cards look into the Item Fragments for what information to display.

It is up to the implementer to create their own displays, but some examples are provided in the plugin.  

!!! tip
    Arc Inventory ships with a `ItemFragment_ItemCard` that allows an item to define what item card classes to use.  

### Drag and Drop

Item Slot Widgets implement drag and drop behaviors, allowing for easy swapping of item slots by simply dragging the item to another slot widget.  This is done by way of calling an ability on the player by a tag as defined in the project settings.  

!!! tip
    More information is located in the [Manipulating Items](../tutorials/containers) tutorial!

--8<-- "includes/abbreviations.md"