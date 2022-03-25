# transport-protocol
CS3700 Project 4: Reliable Transport Protocol

Sean Hoerl and Jessica van de Ven

## High-Level Approach
SENDER:
- Updates the current congestion window size and waiting status as a result of the sender's message
- Checks for message corruption 
- Uses a Message Cache to track info of messages that have not been acknowledged yet
RECEIVER:

## Challenges 


## Features 
- Sender message has a checksum for error detection
- Uses additive increase and decrease on congestion window based on whether ack was received or message was sent out. This is preferred to multiplicative increase --->>>> ??
- 

Receiver: Out of order handling
- is a performance enhancement!!!

        

## Testing
We used the given test suite to test overall success throughout development.
