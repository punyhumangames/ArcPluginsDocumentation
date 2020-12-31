
## Game Mode

The Game Mode Component, `UArcTeamComponent_Gamemode`, is the main controller for Arc Teams.  It must be applied to your GameMode, and two functions must be overriden to provide it functionality.  

!!! info
    See the [Initial Setup page](../initialsetup.md) for more information on how to set up your GameMode to use Arc Teams!

The GameMode Component is the authority in creating teams and allowing players to join or leave those teams.  The GameMode Component also creates SubTeams and handles allowing players to spawn on team associated start points.  

### Notable Functions and Properties

* `CreateTeam`/`CreateSubTeam`
:   These functions create teams for the system.  They are automatically called on game init for teams defined in `DefaultTeams`, but you can call them directly to create teams dynamically.

* `PlayerRequestJoinTeam`
:   This function is the only way for a player to join a team.  This request may fail (so check the return value!) if the JoinRequirements fail the player.  



## Game State

The GameState component is a helper component for replicating teams to the client.  It contains a number of accessors for getting the teams that exist that can be called from the client, as well as callbacks on both the client and server for when teams are added and removed.  

!!! note
    The Game State Component is automatically added to your game state if it doesn't exist.  It is recommended that you add a GameState Component to your GameState manually, but it is not required. 


## Player State

The PlayerState Component is the component that holds the association between Player and Team.  It contains accessors for PrimaryTeam and all SubTeams that a player is part of.  It is also the component that contains the events for when Players join and Leave teams, including SubTeams.  

!!! note
    Player State Components, like the GameState Component, is automatically added to players when they join the game.  It is highly recommened that you manually add this component to your Player State class manually, but it is not required. 


--8<-- "includes/abbreviations.md"