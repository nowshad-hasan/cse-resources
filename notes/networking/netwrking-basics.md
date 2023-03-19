1. What are SSL/TLS certificates? - [Hussein Nusser](https://www.youtube.com/watch?v=r1nJT63BFQ0&ab_channel=HusseinNasser), [ByteByteGo](https://www.youtube.com/watch?v=j9QmMEWmcfo&ab_channel=ByteByteGo)
2. What is CDN? [ByteByteGo](https://www.youtube.com/watch?v=RI9np1LWzqw&ab_channel=ByteByteGo)
3. Symmetric vs Assymetric key [Hussein Nusser](https://www.youtube.com/watch?v=Z3FwixsBE94&ab_channel=HusseinNasser)
4. TLS 1.2, 1.3 [Hussein Nusser](https://youtu.be/AlE5X1NlHgg)
5. ARP Table - [Hussein Nusser](https://youtu.be/mqWEWye-8m8)
6. Port forwarding - [Hussein Nasser](https://youtu.be/92b-jjBURkw)
7. TCP vs UDP [Hussein Nusser](https://youtu.be/qqRYkcta6IE), [Freeecodecamp](https://www.freecodecamp.org/news/tcp-vs-udp/)
8. Get Vs Post [Hussein Nusser](https://youtu.be/K8HJ6DN23zI)
9. JWT vs Session [Ben Awad](https://youtu.be/o9hT7v0OLJc)
10. Cookie crash course [Hussein Nasser](https://youtu.be/sovAIX4doOE)


#### TCP vs UDP

TCP (Transmission control protocol):

PROS - 

- Acknowledgement
- Guaranteed delivery
- Connection based
- Congestion control (kind of traffic which control the pathway)
- Ordered packets

CONS - 

- Larger packets
- More bandwidth
- Slower than UDP
- Stateful (TCP is stateful)
- Serve memory (DOS  - denial of service attack)

Applications - 

- Messaging app

UDP (User diagram protocol)

PROS

- Smaller packets
- Less bandwidth
- Faster than TCP
- Stateless (Yeah, obviously as client just keep sending data)
- Scalable


CONS (kind of opposite of TCP's pros)

- No acknowledgement
- No guaranteed delivery
- Connectionless
- NO congestion control
- No ordered packets
- Security

Applications

- Some game
- video streaming
- DNS