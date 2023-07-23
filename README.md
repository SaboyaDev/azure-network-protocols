<p align="center">
  <img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>

In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 Pro (22H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

- Create Resources
- Observe ICMP Traffic
- Observe SSH Traffic
- Observe DHCP Traffic
- Observe DNS Traffic
- Observe RDP Traffic

<h2>Create a Resource Group</h2>
<p>
  <img src="./images/resource-group.png" height="75%" width="75%" alt="Resource Group"/>
  <img src="./images/creating-resource-group.png" height="75%" width="75%" alt="Resource Group"/>
  <img src="./images/create-resource-group.png" height="75%" width="75%" alt="Resource Group"/>
</p>

<h2>How to create a Virtual Machine</h2>
<p>
  <img src="./images/virtual-machine.png" height="75%" width="75%" alt="Windows VM"/>
  <img src="./images/create-virtual-machine.png" height="75%" width="75%" alt="Windows VM"/>
</p>

<h2>Create a Windows 10 Pro Virtual Machine</h2>

While creating the VM, select the previously created Resource Group and allow it to create a new <strong>Virtual Network (Vnet) and Subnet</strong>. Make sure to use the password option under the <strong>Administrator Account</strong> section (not seen in image):

<p>
  <img src="./images/windows-vm.png" height="75%" width="75%" alt="Windows VM"/>
</p>

<strong>\*\*\*(You can also create your own custom Virtual Network)</strong>

<p>
  <img src="./images/create-virtual-network.png" height="75%" width="75%" alt="Windows VM"/>
</p>

<h2>Create a Ubuntu Server 20.0.4 Virtual Machine</h2>

While creating the VM, select the previously created <strong>Resource Group</strong> and <strong>Virtual Network (Vnet) and Subnet</strong>. Make sure to use the password option under the <strong>Administrator Account</strong> section (not seen in image)

<p>
  <img src="https://i.imgur.com/N5zwQUH.png" height="75%" width="75%" alt="Ubuntu VM"/>
</p>

<h2>Observe Your Virtual Network Infrastructure with Network Watcher</h2>
<p>
  <img src="./images/network-topology.png" height="75%" width="75%" alt="Network Watcher"/>
</p>

<h2>Observing ICMP Traffic</h2>
<p>
  Remote into your Windows 10 Virtual Machine, install <strong><a href="https://www.Wireshark.org/">Wireshark</a></strong>, open it and filter for ICMP traffic only. If you are using a Mac like me, you'll have to download <strong><a href="https://apps.apple.com/us/app/microsoft-remote-desktop/id1295203466?mt=12">Microsoft Remote Desktop</a></strong> from the app store or install the cask via <strong><a href="https://formulae.brew.sh/cask/microsoft-remote-desktop">Homebrew</a></strong>:
</p>
<p>
  <img src="./images/remote-desktop.png" height="60%" width="60%" alt="windows remote desktop on a mac"/>
</p>

<h3>- Private IP Address</h3>

Retrieve the <strong>private IP address</strong> of the Ubuntu VM

<p>
  <img src="./image/../images/ubuntu-networking-overview.png" height="75%" width="75%" alt="Ubuntu private IP"/>
</p>

<h3>- Ping the Ubuntu VM</h3>

Attempt to ping it from within the Windows 10 VM. Observe ping requests and replies within Wireshark:

<p>
  <img src="./images/icmp-traffic.png" height="75%" width="75%" alt="ICMP traffic - private IP"/>
</p>

<h3>- Ping a Random Website</h3>

Attempt to ping a public website such as <strong>www.SaboyaDev.com</strong> and observe the traffic in Wireshark:

<p>
  <img src="./images/ping-website.png" height="75%" width="75%" alt="ICMP traffic - public IP"/>
</p>

<h3>- Non-Stop Ping</h3>

Initiate a perpetual/non-stop ping from your Windows 10 VM to your Ubuntu VM:

<p>
  <img src="./images/non-stop-ping.png" height="75%" width="75%" alt="ICMP traffic - perpetual ping"/>
</p>

<h3>- Network Security Group</h3>

Open the Network Security Group your Ubuntu VM is using and disable incoming <strong>(inbound) ICMP traffic</strong>, while back in the Windows 10 VM, observe the ICMP traffic in Wireshark and the command line Ping activity. Afterwards re-enable ICMP traffic for the Network Security Group in your Ubuntu VM and back in the Windows 10 VM, observe the ICMP traffic in Wireshark and the command line ping activity (should start working again).Finally, stop the ping activity.

<p>
  <img src="./images/deny-icmp-traffic.png" height="75%" width="75%" alt="ICMP traffic - perpetual ping"/>
</p>

<p>
  <img src="./images/non-stop-ping-with-rule.png" height="75%" width="75%" alt="ICMP traffic - ICMP denied"/>
</p>

<h2>Observing SSH Traffic</h2>

Back in Wireshark, filter for SSH traffic only and from your Windows 10 VM, “SSH into” your Ubuntu virtual machine (via its private IP address). Type commands (ls, pwd, etc) into the linux SSH connection and observe SSH traffic spam in Wireshark.

Exit the SSH connection by typing ‘exit’ and pressing [return]:

<p>
  <img src="./images/ssh-traffic.png" height="75%" width="75%" alt="SSH traffic"/>
</p>

<h2>Observing DHCP Traffic</h2>

Back in Wireshark, filter for DHCP traffic only. From your Windows 10 VM, attempt to issue your VM a new IP address from the command line (ipconfig /renew)

Observe the DHCP traffic appearing in Wireshark:

<p>
  <img src="./images/dhcp-traffic.png" height="75%" width="75%" alt="DHCP traffic"/>
</p>

<h2>Observing DNS Traffic</h2>

Back in Wireshark, filter for DNS traffic only.

From your Windows 10 VM within a command line, use nslookup to see what <strong><a href="www.github.com">Github</a></strong> and <strong><a href="www.nasa.gov">Nasa's</a></strong> IP addresses are and observe the DNS traffic being shown in Wireshark:

<p>
  <img src="./images/dns-traffic.png" height="75%" width="75%" alt="DNS traffic"/>
</p>

<h2>Observing RDP Traffic</h2>

Back in Wireshark, filter for RDP traffic only (tcp.port == 3389).

Observe the immediate non-stop spam of traffic? Why is it non-stop spamming vs only showing traffic when a command is inputted?

The answer is because the RDP (protocol) is constantly showing you a live stream from one computer to another, therefor traffic is always being transmitted:

<p>
  <img src="./images/rdp-traffic.png" height="75%" width="75%" alt="RDP traffic"/>
</p>

<h2>Final Thoughts</h2>
This tutorial intention is to help people learn a little bit about network security protocols and observe traffic between virtual machines. I ran this on a my Mac Mini, this can be easily done on a PC without having to download a remote desktop app since Windows provides that with it's software.

<strong>DON'T FORGET TO CLEAN UP YOUR AZURE ENVIRONMENT</strong> so that you don't incur unnecessary charges.

Close your Remote Desktop connection, delete the Resource Group(s) created at the beginning of this tutorial, and verify Resource Group deletion.
