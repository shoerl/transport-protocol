# transport-protocol
CS3700 Project 4: Reliable Transport Protocol

Sean Hoerl and Jessica van de Ven

## High-Level Approach
SENDER:
- Uses sliding window approach for sending packets
- Uses basic sequence numbers (1, 2, 3)
    - Uses a sequence number to ACK cache to identify duplicate ACK's
- Updates the current congestion window size and waiting status everytime we send a message or recieve an ACK
    - We treat our window and congestion window as the same thing
- Checks and handles ACK corruption
    - Using Try/Except to catch packets which are not valid JSON's
    - Using checksum to detect corruption within ACK (CRC32 Checksum using ZLib library)
- Adds checksum to data packets for corruption detection on reciever side
- Uses a Message Cache to track info of messages that have not been acknowledged yet (incase we need to retransmit them)
- Keeps track of current RTT and RTO
    - Calculate RTT using recommended formula from slides
    - RTO is 2 * RTT
- Retransmit packets if it has been longer than the RTO since we sent it and haven't recieved an ACK
- Exit if there is no more data to send and our current window size is 0
- Log helpful messages to STDERR to track what is going on in our program

RECEIVER:
- Uses a Message Buffer to track accurate order of messages, holding place for messages to be identified as ordered correctly or not
- Uses a list of Sequence Numbers to detect data packet duplication and enforce correct ordering of data messages when printing
- Reorders messages when not received in correct order (buffers them until they are in order)
- Sends selective ACKs
- Checks and handles data packet corruption
    - Using Try/Except to catch data packets which are not valid JSON's
    - Using checksum to detect corruption within data packets (CRC32 Checksum using ZLib library)
- Log helpful messages to STDERR to track what is going on in our program


## Challenges 
The first difficulty we ran into was that of figuring out how to represent sequence numbers. We decided to just use our sequence numbers to represent the
segment number as it made the logic significantly easier and also since all of our packets sent have the same number of bytes. We also ran into some confusion
deciding whether or not to ever remove sequence numbers from our list on the reciever side, as doing so would prevent us from detecting whether or not it was a duplicate. Because
of that, we decided to not ever remove them. It was also difficult to figure out under what circumstances we should send ACKs back on the reciever side. We decided to always send them back
unless it was a corrupt packet, as this meant we signaled to the sender that its data was recieved, regardless if it was duplicate or out of order. We had difficulty calculating the checksum
of our JSON packets, but this was made much easier by realizing that we could use the ZLib library to handle that logic for us (we initially tried to do it manually with sums of the byte arrays). Growing and shrinking the window was also particularly challenging at first, as we weren't sure what the difference was between our congestion window and our window in general, as well as confusions with what the value of ssthresh. We ultimately decided to treat our congestion window as our window in general, and veered away from using a ssthresh, rather opting to just do a simple additive increase whenever we recieved a packet, and a multiplicative decrease when we detect that a packet had been dropped. We tried dropping the current window size to 1 whenever we dropped a packet, but this caused significant performance issues with the 8-1 test as packets get dropped occasionally meaning we were constantly having to build our window back up.

## Features
We believe we did a good job putting distinctive logic and steps into functions in an effort to make the code more readable. For detecting ACK / data packet corruption we first did a Try/Except to handle packets that were corrupted in such a way that it was no longer a valid JSON. Then we check to see if the JSON has all of the attributes/fields required to be a valid message, and finally we compute the checksum and compare it to the checksum advertised on the packet. We believe this was a fairly resilient way to handle all of the possible ways in which a packet could be corrupted. When calculating the checksum, we first pop off the advertised checksum from the packet and store it in memory to ensure that we are calculating it correctly. We also keep track of what packets have been retransmitted in our message cache, so we can be sure to not use those packets in our calculations of RTT/RTO when we finally recieve the corresponding ACK. On the sender side we are sure to remove the corresponding packet from our message cache when we do finally recieve a valid ACK. We also print helpful log messages when something distinctive happens, which allowed us to debug easier. We do a additive increase on our max window size whenever we recieve a packet, and a multiplicative decrease on our max window size whenever we drop a packet, which we thought was effective and simple to understand. We also are sure to check if we need to retransmit packets regardless of if we have data to send / recieved ACK's to look at, which we thought was smart. We also thought it was smart to remove entries from our message buffer once they have been dealt with, to keep the size of the buffer to a minimum.
        

## Testing
We used the given test suite to test overall success throughout development. We also were sure to include helpful log messages so that we could keep track of what was going on within our program as we were developing and testing. We also were able to use these log messages to inspect what was happening at each stage in certain functions, to ensure that everything was working exactly as we expected it to. Before attempting to develop any stage of this project, we would discuss how we believed things should work, and then once we had finished that stage we would run it against the tests and analyze the output and ensure that it corresponded to what we believed the proper behavior should be. We found it helpful to reduce the max window size when testing certain aspects of our program (RTT/ corruption), to make it easier to analyze the output and so that we could look at things iteratively. When dealing with functions from external libraries (ZLib checksum), we first played around with the function outside of this project in a different file and analyzed the output in different cases/scenarios, and made sure that it corresponded with what we believed the function should do/wanted it to do. Developing this project iteratively (using steps in project spec.) allowed us to keep things as simple as possible at each stage and prevented us by getting overwhelmed when dealing with multiple aspects of our transport protocol being tested at once.
