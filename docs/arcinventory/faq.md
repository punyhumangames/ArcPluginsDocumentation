

#### **Crash/Disconnect in Multiplayer when swapping items, switching items, or using any of the built in Inventory abilities.** 
This crash often accompanies the error in your log.
```
LogAbilitySystem: Error: Could not find GameplayAbilityTargetData_ItemSwitch in ScriptStructCache
LogAbilitySystem: Error: Could not script struct at idx 2
```

This is caused by failure to init the Gameplay Ability System Globals.  You need to call `UAbilitySystemGlobals::InitGlobalData()`  The Arc Inventory Example Project contains a useful Engine Subsystem for this.  You can grab the Engine Subsystem from a [github gist](https://gist.github.com/RoyAwesome/d13f1c1b13d56b71e8179d879b5f65e0) as well.

You can find out more information in the community GAS documentation about [Initing the Ability System](https://github.com/tranek/GASDocumentation#concepts-asg-initglobaldata).

--8<-- "includes/abbreviations.md"