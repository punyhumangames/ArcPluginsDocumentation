# Initial Setup

Thanks for purchasing Arc Inventory!  Once you've downloaded the plugin and enabled it in your project, this is how you start to use it!

!!! info 
    This plugin uses a lot of other Unreal Engine concepts to achieve full functionality.  It's recommended that you have a firm grasp on Unreal Engine, GAS, and Gameplay Tags

    * [GASDocumentation](https://github.com/tranek/GASDocumentation)
    * [Unreal Engine Gameplay Tag documentation](https://docs.unrealengine.com/en-US/ProgrammingAndScripting/Tags/index.html)

Once you have the plugin installed and enabled, we'll start with configuring it!

### Configuring Arc Inventory

Your first step will be to configure the necissary tags for Arc Inventory's internal functions.  These are required, and many features will fail to function if these tags are not configured.  

=== "Editor"
    In the Unreal Editor, go to View -> Project Settings.  Select the **Arc Inventory Developer Settings** category within Game.

    ![Image](img/initialsetup/configurationpanel.png)

    !!! bug
        `Ability Tag Mappings` is deprecated and unused.  It will be removed in future versions of Arc Inventory


=== ".ini"
    in `DefaultGame.ini` the you can configure the following fields with tags:

    ``` ini 
    [/Script/ArcInventory.ArcInventoryDeveloperSettings]
    EquippedSlotTag=(TagName="Inventory.Slot.Equipment")
    ActiveItemSlotTag=(TagName="Inventory.Slot.Active")
    BagItemSlotTag=(TagName="Inventory.Slot.Bag")
    ItemSlotSwapAbilityTags=(GameplayTags=((TagName="Ability.Inventory.SwapItemSlot")))
    ItemSlotDropAbilityTags=(GameplayTags=((TagName="Ability.Inventory.DropItems")))
    ```

    !!! warning
        Ensure that the tags are defined in your `GameplayTags.ini` or however else you've configured gameplay tags!


Once the tags are configured, the Plugin is setup and ready to go!  You are ready to add the Inventory Component to your player!


--8<-- "includes/abbreviations.md"