# transport-protocol
CS3700 Project 4: Reliable Transport Protocol

Sean Hoerl and Jessica van de Ven

## High-Level Approach
SENDER:
- Updates the current congestion window size and waiting status as a result of the sender's message
- Checks for message corruption 
- Uses a Message Cache to track info of messages that have not been acknowledged yet
RECEIVER:
- Uses a Message Buffer to track accurate order of messages, holding place for messages to be identified as ordered correctly or not
- Uses a list of Sequence Numbers to compare against order of messages received
- Reorders messages when not received in correct order
- 

## Challenges 


## Features 
- Sender message has a checksum for error detection
- Uses additive increase and decrease on congestion window based on whether ack was received or message was sent out. This is preferred to multiplicative increase --->>>> ??
- 

Receiver: Out of order handling
- A performance enhancement in our message buffer - removing unnecessary messages once they have been dealt with (re-ordered).

        

## Testing
We used the given test suite to test overall success throughout development.
