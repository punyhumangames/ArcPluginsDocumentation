
High level, Arc Teams is a player segmentation system, and a way to tag players into groups and teams.  


## Project Goals

Arc Teams has a few goals, and they are reflected within the Teams System

1. Don't assume Game Design
:   Arc Teams does not assume your game design.  It's core structure is a player segmentation system that allows players to be put into different teams, and then allow those teams to operate among themselves and among the players.

2. Let the user Subclass
:   Arc Teams does not implement game design, so design must allow users to subclass as much as possible to add functionality to teams and players within them.  Every part of Teams can be subclassed, functionality extended, and game design can be implemented with ease.

3. The Components do the work
:   Arc Teams introduces a number of components that do the work for the system.  By isolating the implementation details into components, team interaction can grow, be tested, and improve without negative code patterns forming.

## Team Components

The primary driver of Teams is a collection of components that are placed onto certain Gameplay Framework objects, such as the GameMode, Game State, and Player States.  Each component has a primary function that drives certain parts of the team and segmentation logic.  

The GameMode component is the authoirty of the system.  It handles the creation and destruction of all teams, the segmentation of players, and executing the rulesets for players joining and leaving those teams.  

The GameState Component is the replication owner for all ArcTeams primitives.  It handles the transfer of teams to all players and provides a common starting point for UI to display information about the teams.

The Player State Component handles the association of Player to Team, and the per-player state for the system.

## Teams

Teams are a tree-like UObject that is replicated to all players.  Players are associatied with Teams, forming the backbone of the segmentation system.

Teams can have any number dependent Teams, known as SubTeams.  Those SubTeams can have further SubTeams, allowing for segmenting players within a given Team.  Teams without a parent Team are known as Primary Teams.

!!! tip
    Use Subteams to create features such as Squads or Fireteams!

Teams have an associated default-object only object known as a Team Definition, that contains static data for that Team.  This data is not replicated, and can be used to store UI information that does not need to be sent to the clients for a Team.  

## Team States

Some data requires a full actor channel to replicate effeciently, so Teams may create Team State actors to handle the replication to clients.  Team States come in two flavors, the Public Team State, and the Private Team State.  Public Team States are replicated to all players in a server, and Private Team States are replicated to only members of that team. 

!!! tip
    Use Public Team States for replicating data such as the Score, Respawns, or other game state that all players must know.  You can use the Private Team State to replicate information only available to a Team, such as Resources, Team Orders, or even collections of team associated objects like buildings.



--8<-- "includes/abbreviations.md"