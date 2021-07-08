!!! caution "Unfinished Plugin"
    Arc Communication is not finished.  Some features may be missing or unfinished.  Pardon the dust

Localized Messages are Arc Communication's method for sending text and stylized messages to players.  All messages, from chat to a killfeed to player join/leave notifications to achievement toasts can be represented as a Localized Message.

Localized Messages are a UObject that is created on the client per-message.  When a Client recieves a localized message, the Server sends it a Subclass of the localized message class and a struct of relevant information for that message.  The Client then constructs the localized message object and forwards it to UI for rendering. 

Localized Messages are intended to be subclassed in BP or C++ for a given message type.  Included in the Localized Message class is a UMG Widget subclass for rendering the Message (See User Interface for more information).  While Localized Messages can be rendered without a custom widget, creating a widget is what allows for styling and making Localized Messages look different from each other.

### Built in Localized Messages

Most messages will need to be defined for your implementation, but Arc Communication provides a few pre-made message types.  

#### UArcCommLocalMessage_TextMessage

Text Message is the main local message for player chat.  A valid Text Message subclass must be provided to the Game Mode Component so it can properly send text to clients in a chat context.

#### UArcCommLocalMessage_JoinLeaveNotification

!!! caution "Not Implemented Yet"

The JoinLeave Notification message is sent whenever a player joins or leaves the server.  If the server doesn't have a valid JoinLeave notification message, it wont send the notifications to everyone.  



--8<-- "includes/abbreviations.md"