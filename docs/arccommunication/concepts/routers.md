!!! caution "Unfinished Plugin"
    Arc Communication is not finished.  Some features may be missing or unfinished.  Pardon the dust

Routers are the primary object for sending message data to clients.  All messages, text or voice, are sent along a Route, and Routers must be set up to determine which clients will recieve anything pushed over that route.

Routes are denoted by a `FGameplayTag`, and Routers determine which messages are handled through a `FGameplayTagQuery`.  Many Routers can apply to a single route, and messages are only sent over the Route if all Routers agree that a reciever can recieve a given message from a sender.

Messages can be sent by any Actor in the scene.  When a message is sent by a Player, the Sender is that player's Player State.  This can be used to create a notification system or voice broadcasting system, in addition to player-sent chat.

## Basic Setup

To add new routes for messages, you must add them to the Arc Communication Gamemode Component.  You can have any number of routes in the component, and can have multiple Tag Queries match the same route.  

If multiple Route Queries match with a given message or VOIP broadcast, all Routers must agree that a given reciever can recieve that message.  If any Router denies the message to be sent to that client, the message is not sent to that client.  

!!! note
    If a client is not eligible to recieve a message due to the router declining the message, the server will not send anything across the network to that client regarding that message.  A client may not recieve a message they sent themselves, if the route declines sending it back ot them.  Do not assume you will recieve a message.

For example, if you wanted to create a set of routes [`Chat.All`, `Chat.Team`, `Chat.Announcements`], and have All and Team only be able to be sent messages by players and Announcement can only be sent to by the server, and have All and Announcements go to everyone, you would set up 4 routers:

- Router 1: ANY(Chat.All, Chat.Announcement), Router Class: ArcComRouter_All
- Router 2: ANY(Chat.All, Chat.Team), Router Class: ArcCommRouter_RequiresValidSender
- Router 3: ANY(Chat.Announcement), Router Class: ArcCommRouter_RequiresNoSender
- Router 4: ANY(Chat.Team) Router Class: YourCommRouter_Team

The first router will route any messages sent to All and Announcements to everyone.  The second router will restrict the sender, requiring a valid player state to send to those routes.  Router 3 will prevent senders with a valid player state from sending to that route (thus requiring gameplay code to send it), and Router 4 is some hypothetical router that determiens if the sender and reciever are on the same team.  

Such a router setup will also work for Voice channels.  

## Built In Routers

Arc Communication comes built in with a few routers to help get chat and voip working quickly.  You can create your own routers in C++ or Blueprint.  All built in routers work with Text or Voice.

### ArcCommRouter_All

The All router sends to every player on the server

### ArcCommRouter_RequiresValidSender

The Requires Valid Sender router checks if the sender has a valid player state.  This is to validate that only players can send to this route.

### ArcCommRouter_RequiresNoSender

The Requires No Sender router checks if the sender does not have a valid player state.  This is to ensure that players cannot send messages to this route, even if they want to.  Only gameplay code can send to this route, using an actor that is not a player.  See Sending Messages for more information.

### ArcCommRouter_Proximity

The Proximity router determines the distance between the sender and reciever, and if they are within a certain distance will send the message to that reciever.  The Proxmity Router determines the locations between any sender actor and the reciever.  

--8<-- "includes/abbreviations.md"