# Networking
each machine on the network can communicate with eachother

## Internet Protocol
* IP Address (everything has an IP Address)
  * IPv4 - 192.198.x.x or 127.0.x.x (4.294B IP address combinations)
  * IPv6 - 
* Port Number (kind of like an apartment number or suite number)
  * for running multiple connections
  * 65535 available ports
  * <1000 is reserved for low level communications( very important stuff only)
  * 3000-9000 is used for dev purposes
  * 80 = http
  * 22 = ssh
  * 443 https
  * 5432 postgres

## Communication
* client and server (2 parts)
* servers have information (ex: youtube server, website hosts)
* clients want information (web browser)

## Transport Layer Protocol
* all communications are broken up into Packets (packets make things more organized, so the communications are more efficient)
* Each packet is like an envelop
* On the outside of the element are Headers:
  * where is the destination
  * who is the sender
  * packet number (1/500, 2/500, etc.)

## Transmission Control Protocol (TCP)
* larger header size
* uses 3-way handshake (initiate connection > connection confirmed < Connection established/confirmation >)
* lost packets are re-sent (which means it is checking integrity of transmission)


## User Datagram Protocol (UDP)
* smaller header size
* connectionless (broadcast only, client isn't listened to by server)
* lost packets are not resent
* can arrive in any order

## Event-Driven Programming
* programming against/when an event occurs
* form submission, button clicked, connection established, etc.
* events used to trigger callback functions

