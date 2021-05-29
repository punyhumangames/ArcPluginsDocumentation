# Initial Setup

Thanks for purchasing Arc Teams!  Once you've downloaded the plugin and enabled it in your project, this is how you start to use it!


### Configuring the Plugin

Once the plugin is installed and enabled, the next step is to add the Gamemode Component to your gamemode.  This component is the driver for the entire system, and is what manages everything.  Let's add one to our Gamemode to enable configuration and start making teams!

=== "C++"

    In your Gamemode class, add the the component to your gamemode and implement the `IArcTeamInterface_Gamemode`

    `MyGameMode.h`    
    ```cpp 
    #include "Interfaces/ArcTeamInterface_Gamemode.h"

    class UArcTeamComponent_Gamemode;

    class MYGAME_API AMyGameMode: public AGameMode, public IArcTeamInterface_Gamemode
    {
        GENERATED_BODY()

        private:
            UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Camera, meta = (AllowPrivateAccess = "true"))
            UArcTeamComponent_Gamemode* TeamComponent;

        public:
            AMyGameMode(const FObjectInitializer& ObjectInitializer);

            virtual UArcTeamComponent_Gamemode* GetTeamComponent() const override
            {
                return TeamComponent;
            }

            virtual AActor* ChoosePlayerStart_Implementation(AController* Player) override;

	        virtual bool ShouldSpawnAtStartSpot(AController* Player) override;
    };
    ```

    `MyGameMode.cpp`
    ```cpp
    AMyGameMode::AMyGameMode()
	: Super()
    {
        TeamComponent = CreateDefaultSubobject<UArcTeamComponent_Gamemode>(TEXT("Team Component"));
    }

    AActor* AMyGameMode::ChoosePlayerStart_Implementation(AController* Player)
    {
        return ChooseTeamPlayerStart(Player);
    }

    bool AMyGameMode::ShouldSpawnAtStartSpot(AController* Player)
    {
        return Super::ShouldSpawnAtStartSpot(Player) && GetTeamComponent()->CanUsePlayerStartForPlayer(Player, Player->StartSpot.Get());
    }

    ```

    Once this is made, create a new Blueprint for this team class.

    !!! warning 
        Remember to add `ArcTeams` to your build.cs!

=== "Blueprint"

    For Blueprint, Arc Teams contains a base class `ArcTeamGamemodeBase`.  Simply create a new blueprint from that class or reparent your gamemode to it

    !!! note
        It is currently not possible to add a team gamemode component in blueprint.  This is because some functions need to be overriden in AGameMode and you cannot override them in blueprint.

        It is highly recommended that you follow the C++ path for this section to set up your gamemode.  

Once the component is added, you need to configure the teams in the component.

Open your Gamemode blueprint and inspect the Team Component that is part of it.  Within it contains a number of configuration properties for setting up teams.  

For now, we are going to modify the `Default Teams` array to add two teams, a Red Team and a Blue Team.  Add two array elements and se their Instance Tags to `Team.Slot.Red` and `Team.Slot.Blue` respectively.  We will return to this to set their TeamDefinitions, but Team Class remain `none` as we are not changing the underlying Team Instance in this tutorial.

!!! info
    The Instance Tags property makes use of Unreal Engine's Gameplay tag system.  More information can be found here: [Unreal Engine Documentation](https://docs.unrealengine.com/en-US/ProgrammingAndScripting/Tags/index.html)

### Creating Team definitions

In the editor, we need to create a pair of team definitions for Red Team and Blue Team.  Create two blueprints that inherit from `ArcTeamDefinition`, `BP_TeamDef_Red` and `BP_TeamDef_Blue`.  

In the Red team blueprint, we will set the Owned Tags property to contain the `Team.Side.Red` tag (Create it if it doesn't exist) and in the UI Data property, set it's type to `Arc Team UIObject Team Definition`.  Set it's name to `Red` and the color to a reddish color.  

For Blue Team, do the same, but change the names to Blue and the color to Blue.  

Head back to our gamemode, and within the Default Teams array, set the first element's TeamDefinition to Red and the second's to Blue.  

!!! info 
    Team Definitions are a data-only object that provide all the static data for a given team.  For more information, check the [Team Definition Concepts](concepts/team.md) page!

### Putting Players Into Teams

Putting players into teams is fairly straightforward, and Arc Teams provides an automatic way to assign players to teams if your game wants to do that.  If, in the Gamemode Component `Auto Assign New Players` is checked, then when a player joins the game they will be assigned automatically to any team that has `Auto Joinable` checked in it's team definition.

However, if you want players to be manually assigned to a team, you can do so by calling `PlayerRequestJoinTeam` on the GameMode component with that player's PlayerState component.

!!! tip
    PlayerState Components are automatically placed on characters.  You can get a player's PlayerState Component easily by calling `UArcTeamsBlueprintLibrary::GetPlayerStateComponentFromPlayer` in C++ or that same function in Blueprint.  It takes the PlayerController.  

    It is recommended, for performance reasons, that you manually add the PlayerState Component to your player state object, but ArcTeams will automatically do it for you if you forget or want to get the plugin up in running fast.

### Conclusion

That's it!  Now you have a working team system!



--8<-- "includes/abbreviations.md"