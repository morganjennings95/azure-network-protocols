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

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

- Create a Windows VM and a Linux VM running in the same virtual network
- Open up wireshark and observe the network
- Create some firewall rules
- Observe traffic with multiple powershell commands and wireshark filters

<h2>Actions and Observations</h2>

![image](https://github.com/user-attachments/assets/a72b5e45-3e51-4a77-a3be-c09262cb0be9)

<p>
Open up your windows vm via remote desktop and go to wireshark.org -> Download -> Windows x64 Installer
</p>
<br />

![image](https://github.com/user-attachments/assets/c085eca5-131e-4b3a-a659-0d210438e589) ![image](https://github.com/user-attachments/assets/4b382590-2e73-41d9-b3e5-a8923abbb33a)

<p>
Open the file once download completes and scroll through the installer until you hit the "Install" page and select install. Simply agree and finsih when prompted
</p>
<br />

![image](https://github.com/user-attachments/assets/774e1c23-0a69-451e-a4aa-0f2a79d8094e) ![image](https://github.com/user-attachments/assets/cfaf7f0c-4d89-4244-9746-25c6ca476d57)

<p>
Open wireshark, click ethernet to highlight it and select the sharkfin icon under "File" here you will see all the packets traversing the network inside your virtual machine.
</p>
<br />

![image](https://github.com/user-attachments/assets/4781d294-bf04-43f4-9c30-0bcd12c59374) ![image](https://github.com/user-attachments/assets/ea860db6-91f4-4739-bf0f-5711914f0ed3)

<p>
Filter for "icmp" traffic only, type icmp in the bar at the top of the window. Now open up power shell and we will ping the private IP Address of the linux vm, and thus wireshark will show us the network traffic of the windows vm requesting the ping and the linux vm replying to the ping. If you click on any of the private IPs in the list it will bring up additional information in the lower quadrants such as the MAC Address of both machines & IP addresses
</p>
<br />

![image](https://github.com/user-attachments/assets/6bd6c06a-053e-4ae3-ba96-2bfd99ae4df0)

<p>
In the last step we performed a sing ping, now we will perform a perpetual ping using the linux private IP and observe the network traffic in wireshark continues to roll as opposed to the previous step which had a finite number of pings, this will go on forever until you kill the command
</p>
<br />

![image](https://github.com/user-attachments/assets/e353fda6-7d23-4f8e-a559-f9f08f289a5f) ![image](https://github.com/user-attachments/assets/70759976-8b62-42db-996a-7791cb21770d)


<p>
Now we can set up a firewall inside the linux VM, selet linux vm -> Networking -> Network Settings -> Network security group -> Inbound security rules -> Add, now adjust the settings to match what I have in the capture above, priority level lower than the '300' showing will put our firewall rule at the highest level
</p>
<br />

![image](https://github.com/user-attachments/assets/e78f4c34-d2a0-46dc-a3fc-66602b3cfa01)

<p>
Once you add the rule, move back over to wireshark, wait for the firewall rule to take effect, and you will find the ping command will fail and the request will time out, meaning the request never gets a reply thus the ping command will fail every time. Once you are finished observing, return to your firewall inbound security rules and remove the rule we added and you will find that once the rule takes effect the icmp traffic is no longer blocked and the ping command will start functioning again.
</p>
<br />

![image](https://github.com/user-attachments/assets/2947add3-3855-4e15-94d3-87052f2de951)

<p>
Now we will filter traffic in wireshark for ssh, in powershell type (ssh labuser@your private ip address for your linux vm) type yes -> your labuser password and you can see the prompt switched over to our linux vm and every keystroke you enter in powershell produces traffic in wireshark, you can additionally attempt prompts sure as 'id' or 'hostname' which will output more info about your linux vm in powershell. Type exit to leave the linux vm and return to the windows vm in powershell
</p>
<br />

![image](https://github.com/user-attachments/assets/1fe5fd6b-a736-43ce-ae9c-ae645038bc43)

<p>
Observing DHCP traffic we can type into powershell "ipconfig /renew" and "ipconfig /release" now you will see the extent to what is happening with renew as it is broadcasting your ip information, now the release command will break connection so be aware that you will need to run those commands back to back in a script file to observe the difference in commands, furthermore you may copy the information from wireshark and get a more in depth understanding of what is going on behind the scenes by pasting it into chat GPT.
</p>
<br />

![image](https://github.com/user-attachments/assets/61fb83f4-bab1-441e-b6bc-d2722f8ee4d2)

<p>
Observing DNS traffic, type dns into the filter on wireshark and "nslookup" any website you wish to find an ip address for, you can see there are several things going on on the back end, now if you wish, you can copy the ip address and paste it into your browser to see if you are able to access the website via the ip address alone. Most sites will unfortunately produce a "404" error page but sometimes the website will throw in a few clues as to what site you are trying to visit.
</p>
<br />

![image](https://github.com/user-attachments/assets/790daea7-1fcc-4694-a7d1-6998dcfbd6c0)

<p>
Lastly we will observe RDP traffic accross our connection. Filter wireshark for tcp.port==3389 and select enter, this will show you every packet and every detail going on behind the scene to deliver you a remote desktop protocol connection. This filter will spam every event and not just keystrokes or commands that we were able to see earlier in DHCP. This will conclude network traffic observations, there are other filters and commands that you can observe in wireshark, I suggest some additional diving to really see what it is capable of, but other wise we will end here.
</p>
<br />
