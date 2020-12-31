


## Teams

Teams are the primary object segment players, represented by the UArcTeam object.  Teams are a Replicated UObject that are sent to all players through the Game State Component. 

Teams are extremely thin, small objects, and players have one Primary Team and many SubTeams.  Teams can only be created by the Game Mode Component. 

By default, each player is assigned to a Primary Team upon joining a server.  A player may only be in one Primary Team, and must leave their Primary Team before joining a new one.  Players may also not have a Primary Team, and are considered Unassigned.  A Spectator Team is also created by default, and any player placed into Spectator by AGameMode is placed into that spectator team. 

!!! info
    The auto assignment behavior can be modified easily by unchecking 'Auto Assign New Players' in the Game Mode Component.

### Join Requirements

By Default, Arc Teams does not implement any mechanism to ensure that players should join teams.  To that end, a class, UArcTeamJoinRequirement, exists with an implementable method in C++ or Blueprint to implement rules around joining any team.  


### Sub Teams
All Teams (including SubTeams) may have any number of Sub Teams, which are simply UArcTeam objects added to a parent team.  A player may join any SubTeam, so long as the join is approved by that team's Join Requirements.  

Subteams are useful for further segmenting players, and are used for features such as Squads, Parties, and similar segmentation within a team.

!!! note
    By Default, Arc Teams does not require a player be part of of a SubTeam's parent team to join the SubTeam.  Arc Teams does provide a default implementation for a Join Requirement that enforces this state.

Subteams can be queried from nearly any accessor for teams.  ArcTeams provides a few methods to do so, including a query by TeamDefinition class, matching GameplayTagQuery, or just collecting all of them.

## Team Definitions

Team Definitions are a class, UArcTeamDefinition, that describes static data and UI data for the team.  The Team Definition may be reused across multiple teams, and assists in reducing replication load for the system.  

!!! note
    Team Definitions cannot be dynamically created, but multiple teams can use the same definition.  


## Team States

To assist with replicating large amounts of team data, Arc Teams provides a pair of actors that are spawned by teams to assist with replication.  

Public Team States, `AArcPublicTeamState` is replicated to every player, and is easily accessed on clients.  Similar to the AGameState class, this is a useful construct for describing team data that everyone in the game can see, such as score, objective progression, and the like.

Private Team States, `AArcPrivateTeamState`, is only replicated to players within that team.  This is a useful construct for replicating private team data like objectives, storing a team's private game objects, or progress toward victory.  

Team States are created when a team is created, and destroyed when the team is destroyed.  If no TeamState class is provided in the TeamDefinition, no TeamState is created.  

!!! tip
    Team States are full actors! Try putting components on them for additional functionality!

--8<-- "includes/abbreviations.md"