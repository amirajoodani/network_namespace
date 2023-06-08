# network_namespace
how to create nework namespace in linux <br>
# what is nework namespace ?
to isolate the network resources of system define as network namespace. <br>
# what kind of resources are isolate in network namespace ? 
1-network device <br>
2-routing table <br>
3-ipv4 $ ipv6 <br>
4-firewall rules <br>
5-port number <br>
6-/proc/net directory <br>

the enviroment of system is a firest namespace <br>
# Architecture
![network namespace](https://github.com/amirajoodani/network_namespace/assets/42912741/ce9e6034-c7b3-4bfe-9f69-6ea8636391d1)
now we implement this structure: <br>
on the vm :
```python
# python3 -m http.server 8080
```
create namespace: <br>
```
# ip netns add apple_ns
```
list of ns (namespace): <br>
```
# ip netns list 
```
delete namespace : <br>
```
# ip netns delete apple_ns 
```
start http server on namespace : <br>
```
# ip netns exec apple_ns python3 -m http.server 8080
```
so as we see , we don't have port conflict in different namespace.<br>
for checking network NIC of ns : <br>
```
# ip netns exec apple_ns ip  a  s
```
now for checking webserver we can use use curl command but as you see with below command , loopback interface is down and we should up the interface <br>
```
# ip netns exec apple_ns curl localhost:8080
# ip netns exec apple_ns ip link set dev lo up
# ip netns exec apple_ns ip a s
```
for connecting two interface , the solution is making virtual interface: <br>
```
# ip link add dev host_veth type veth peer name apple_veth
# ip link list
```
we should transfer apple_veth interface to namespace: <br>
```
# ip link set apple_veth netns aplle_ns
# ip netns exec apple_ns ip link list
```
now up the interface and assign ip : <br>
```
# ip link set dev host_veth up
# ip address add 10.0.0.10/24 dev hsot_veth
# ip netns exec apple_ns ip link set dev apple_veth up
# ip netns exec apple_ns ip address add 10.0.0.11/24 dev apple_veth
# ip netns exec apple_ns ip a s
# ping 10.0.0.10 -c 4
# ping 10.0.0.11 -c 4
```
ping should be ok :heavy_check_mark: <br>
```
# curl 10.0.0.11:8080
```
its ok :heavy_check_mark: <br>
but we don't have google ping form apple namespace :heavy_multiplication_x: <br>
```
# ip netns exec aplle_ns ip route add default via 10.0.0.10
# sysctl -w net.ipv4_ip_forward=1
# sysctl -p
# ip link list
```

now create rule on firewall to transfer traffic form virtual interface to real interface : <br>
```
# iptables --append FORWARD --in-interface host_veth --out-interface enp0s7 --jump ACCEPT
# iptables --append FORWARD --in-interface enp0s7 --out-interface host_veth --jump ACCEPT
# iptables --append POSTROUTING --table nat --out-interface enp0s7 --jump MASQUERADE
```

now google ping from apple namespace is ok :heavy_check_mark: <br>





  
 


