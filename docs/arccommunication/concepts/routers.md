!!! caution "Unfinished Plugin"
    Arc Communication is not finished.  Some features may be missing or unfinished.  Pardon the dust

Routers are the primary object for sending message data to clients.  All messages, text or voice, are sent along a Route, and Routers must be set up to determine which clients will recieve anything pushed over that route.

Routes are denoted by a `FGameplayTag`, and Routers determine which messages are handled through a `FGameplayTagQuery`.  Many Routers can apply to a single route, and messages are only sent over the Route if all Routers agree that a reciever can recieve a given message from a sender.

Messages can be sent by any Actor in the scene.  When a message is sent by a Player, the Sender is that player's Player State.  This can be used to create a notification system or voice broadcasting system, in addition to player-sent chat.

## Basic Setup

## Built In Routers

--8<-- "includes/abbreviations.md"