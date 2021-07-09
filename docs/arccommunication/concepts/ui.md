!!! caution "Unfinished Plugin"
    Arc Communication is not finished.  Some features may be missing or unfinished.  Pardon the dust

To render localized messages and voip information, Arc Communication provides a number of built in UMG widgets to display text. 

Message rendering is split into two different parts, the Message View, and the Message Widget.  The View is a widget that holds a number of messages, while the Message Widget renders a Localized Message.

Voip does Voip things when Voip is done.

### Message Views

A Message View widget is a widget designed to display messages.  It implments the `BPI_LocalizedMessageViewReciever` interface.  The default implementation contains a FGameplayTagQuery used to determine if a View should recieve a message.  When received, the View Widget constructs a Message widget and applies the Localized Message to it.  

You can implement your own "meta view" widget, that contains many children view widgets and implement the `BPI_LocalizedMessageViewReciever` interface on it.  From there, you can decide which view actually recieves the message.  

### Messages

A Message Widget is a widget that implements the `BPI_LocalizedMessageInterface` interface.  A classic chat message interface would display the Channel, the Sender, and the message.  A Message Widget can implement the display of the localized message however it wants, and is hosted in a view.  



### Voip Notifications

!!! caution "Not Implemented Yet"

--8<-- "includes/abbreviations.md"