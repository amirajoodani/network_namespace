# network_namespace
how to create nework namespace in linux <br>
# what is nework namespace ?
to isolate the network resources of system define as network namespace. <br>
# what kind of resources are isolate in network namespace ? 
1- network device <br>
2-routing table <br>
3-ipv4 $ ipv6 <br>
4-firewall rules <br>
5-port number <br>
6- /proc/net directory <br>

the enviroment of system is a firest namespace <br>
# Architecture
![network namespace](https://github.com/amirajoodani/network_namespace/assets/42912741/ce9e6034-c7b3-4bfe-9f69-6ea8636391d1)
now we implement this structure: <br>
on the vm :
```python
#python3 -m http.server 8080
```
create namespace: <br>
```
#ip netns add apple_ns
```
list of ns (namespace): <br>
``` # ip netns list ```

