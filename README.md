# docker
#https://www.rosehosting.com/blog/how-to-open-ports-in-ubuntu-and-centos-using-iptables/

Having a properly configured firewall is very important for the overall security on your server. In this tutorial, we are going to show you how to set up your firewall and open the ports you need on your Linux VPS.


While there are few powerful tools to manage the firewall on a Linux server, we are going to use IPtables in this tutorial. In case you are using UFW on Ubuntu or Firewalld on CentOS, you can check our corresponding guides about how to set up a firewall with UFW on Ubuntu or how to configure a firewall with FirewallD on CentOS.

First of all, connect to your Linux VPS via SSH and list the current IPtables rules using the following command:

sudo iptables -L
If you recently set up your server there will be no IPtables rules and the output should be similar to the one bellow:

Chain INPUT (policy ACCEPT)
target     prot opt source               destination

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
If there are already some rules that you don’t want to be there you can flush the rules by using the following command:

sudo iptables -F
Be careful with this command, especially if the default policy on your INPUT and OUTPUT chains is set to something other than ACCEPT because that could lock you out of your server.

The first firewall rule you need to add is the following one:

sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
This basically tells your firewall to accept your current SSH connection. The next step is to allow traffic on your loopback interface and to open some basic ports like 22 for SSH and 80 for HTTP.

sudo iptables -A INPUT -i lo -j ACCEPT
sudo iptables -A OUTPUT -o lo -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
You are now ready to open the other ports you want to allow traffic to. Use the same command as you used to open the port 22 and 80 in the previous example.

List the current rules using:

sudo iptables -L
The output should be similar to the one below:

Chain INPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     all  --  anywhere             anywhere             ctstate RELATED,ESTABLISHED
ACCEPT     all  --  anywhere             anywhere
ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:ssh
ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:http

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     all  --  anywhere             anywhere
For more detailed output you can use:

sudo iptables -nvL
And the output should be similar to this one:

Chain INPUT (policy ACCEPT 4 packets, 255 bytes)
 pkts bytes target     prot opt in     out     source               destination
    0     0 ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0            ctstate RELATED,ESTABLISHED
    0     0 ACCEPT     all  --  lo     *       0.0.0.0/0            0.0.0.0/0
    0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:22
    0     0 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:80

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination

Chain OUTPUT (policy ACCEPT 4 packets, 283 bytes)
 pkts bytes target     prot opt in     out     source               destination
    0     0 ACCEPT     all  --  *      lo      0.0.0.0/0            0.0.0.0/0
You can now implement the DROP rule which will block all incoming packets that don’t match one of the ACCEPT rules we added earlier.

sudo iptables -P INPUT DROP
The final thing you need to do is to save the rules and make them permanent. If you are using Ubuntu VPS you can install iptables-persistent for that purpose.

sudo apt-get install iptables-persistent
On Ubuntu 14.04 use the following commands to save/reload the iptables rules:

sudo /etc/init.d/iptables-persistent save
sudo /etc/init.d/iptables-persistent reload
On Ubuntu 16.04 use the following commands:

sudo netfilter-persistent save
sudo netfilter-persistent reload
If you are using CentOS VPS you can use the following command to save the IPtables rules:

service iptables save
Of course, you don’t have to do any of this if you use one of our Linux VPS Hosting services, in which case you can simply ask our expert Linux admins to help you configure your firewall. They are available 24×7 and will take care of your request immediately.

PS. If you liked this post please share it with your friends on the social networks using the buttons on the left or simply leave a reply below. Thanks.
