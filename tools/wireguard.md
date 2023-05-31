**wireguard** is a modern VPN protocol that utilizes cryptography through the use of public and private keys to secure your traffic between hosts. **Wireguard** is fast, simple, and easy to configure. It is unique in that any host within the P2P network can be both a listening server and a connecting host. 

[Wireguard Project](https://www.wireguard.com/)  
[Unofficial Documentation](https://github.com/pirate/wireguard-docs)
_____

# Installation

Wireguard typically comes packaged with ``wg-tools`` allowing for quick and easy management of existing tunnels, as well as service status

#### Debian
```bash
sudo apt install wireguard
```

#### Redhat
```bash
sudo dnf install wireguard-tools
```


# Configuration
[quick guide I wrote](https://github.com/barkwoofdog/howtowithdog/wiki/Hosting-Your-Own-Server-&-Bypassing-Your-ISP-with--Wireguard)

### Before You Start (on Linux)

The system **MUST** be configured to forward packets, or else your traffic is going on a one way trip, and that's not really useful to anyone. You must make a change in the ``/etc/sysctl.conf`` file to allow for forwarding. The following is a snippet from that file

```bash
# Uncomment the next line to enable TCP/IP SYN cookies
# See http://lwn.net/Articles/277146/
# Note: This may impact IPv6 TCP sessions too
#net.ipv4.tcp_syncookies=1

# Uncomment the next line to enable packet forwarding for IPv4
net.ipv4.ip_forward=1

# Uncomment the next line to enable packet forwarding for IPv6
#  Enabling this option disables Stateless Address Autoconfiguration
#  based on Router Advertisements for this host
#net.ipv6.conf.all.forwarding=1
```

As you can see, the ``net.ipv4.ip_forward`` value has been set to 1, and is also uncommented unlike the ``net.ipv6.conf.all.forwarding`` value.  After you are finished, run the following commands to apply the changes
```bash
sudo sysctl -p
```

```bash
sudo sysctl --system
```

You will also need to allow the the wireguard port on your firewall. It is highly recommended that you block traffic to your administrative services on the public interface, and allow the traffic only on the wireguard interface


### Creating an Interface and Configuring it

Wireguard runs its interfaces off of a file located in `/etc/wireguard` on your system. your file can be named anything from `wg0.conf` to `fuckfacetunnel.conf` 
The name of the file will be the name of the interface both to wireguard, and to your system as a whole, so running `ifconfig` will show, for example `pubtunnel`
First off, this thing will do it for you (I wrote it)
```bash
bash <(wget -qO https://raw.githubusercontent.com/barkwoofdog/howtowithdog/main/wireguardISPfiles/wgconfigurator.sh)
```

**HOWEVER** this would be the way to do it step by step

##### Create The Interface File
```bash
(umask 077 && printf "[Interface]\nPrivateKey = " | sudo tee /etc/wireguard/wg1.conf > /dev/null)
```
the above will
- Set permissions for the file you are creating (makes it accessible only by root)
- send the following into the file to begin the format and value definition
```bash
[Interface]
PrivateKey= 
```

##### Generate Your Keys

Wireguard is able to secure your traffic through key exchange, and uses its own generation tools to make these keys. ([[wg-tools]])

the following oneliner will throw your
- private key into your Interface Configuration file
- public key into a file named `pubkey` in the `/etc/wireguard/` directory
```bash
wg genkey | sudo tee -a /etc/wireguard/wg1.conf | wg pubkey | sudo tee /etc/wireguard/pubkey
```

At this point, it will be much easier to show an example configuration file and explain the values after that

```bash
[Interface]
PrivateKey = 
ListenPort = 55564
Address= 10.0.0.1/24
DNS = 1.1.1.1

PostUp = <iptables rule add here>

PostDown = <iptables rule del here>

[Peer]
PublicKey=
AllowedIPs= 10.0.0.10/32
```

##### ListenPort
The UDP Port this Interface listens on. Be sure to allow it on your firewall

##### Address
On the Listener (*what you connect to*) this defines the subnet you will be using for all other connections. So if I use `10.10.200.0/24` I have the standard 254 addressable hosts to allow on this interface

##### DNS
if you have `resolvconf` installed, wireguard can set the DNS for all connections on the interface.

##### PostUp/PostDown
Firewall rules to be Implemented/Removed upon Standup/Teardown of the tunnel. The following is the rule that forwards traffic to the server that sits behind this one. Detailed more in [my guide on bypassing your ISP's crap](https://github.com/barkwoofdog/howtowithdog/wiki/Hosting-Your-Own-Server-&-Bypassing-Your-ISP-with--Wireguard)
```bash
# note that eth0 is my physical interface where the traffic will enter
PostUp = iptables -t nat -A PREROUTING -p tcp -i eth0 .

#the `!` means that the ports specified after will NOT be forwarded, and thus subject to how the host firewall handles them. Recommended you put 22 here so you can SSH into your box
--match multiport '!' --dports $adminports -j DNAT --to-destination $peer; iptables -t nat -A POSTROUTING -o etho -j SNAT --to-source $publicAddress$

PostUp = iptables -t nat -A PREROUTING -p up -i eth0 '!' --dport $wgPort$ -j DNAT --to-destination $peer$;

PostDown = iptables -t nat -D PREROUTING -p tcp -i eth0

--match multiport '!' --ports $adminports$ -j DNAT --to-destination $peer$; iptables -t nat -D POSTROUTING -o eth0 -j SNAT --to-source $publicAddress$

PostDown = iptables -t nat -D PREROUTING -p udp -i eth0 '!' --dport 55420 -j DNAT --to-destination $peerAddress;
```
What Up makes, Down Taketh away

##### [Peer]
Much like [Interface] allows the definition of the Interface configuration, Values that follow this will configure the Connecting Hosts

##### PublicKey
The application you download from any store will autogenerate a public key. Put it here

##### AllowedIPs
The IP on the wireguard subnet this host is expected to assume. note you **MUST** use a `/32` as its subnet mask, as it is the ONLY IP expected. 

##### Success! Your Listener is configured
Configuring a peer is quite simple. Below is an example of a peer configuration

```bash
[Interface]
PrivateKey=
Address= 10.0.02/24

[Peer]
PublicKey= 
AllowedIPs= 0.0.0.0/0
Endpoint= <publicIPforHostServer>
PersistentKeepAlive= 30
```

There are a few unique circumstances to a Peer Configuration. 

##### Endpoint
this is the server you will be connected to. You must designate a port
`192.168.0.5:50069`

##### AllowedIPs
This is set to `0.0.0.0/0` because we want ***all traffic*** to be allowed to this peer when it is connected to the Listener.  
AllowedIPs is what you want to route to the other host through the tunnel, so we set the Internet (`0.0.0.0/0`)

##### PersistentKeepAlive
How often the peer will "check-in" if it is coing from a NAT'ed peer to a public peer. This is required to keep the connection alive in the router's table. Recommended for all peer clients
