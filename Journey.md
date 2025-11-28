# Journey

I've always thought to myself that if you want to do something impactful, you might as well do it big. With this, somewhat naive mindset, I attempted to build my own personal homelab as my first cybersecurity project and decided to create one with all the bells and whistles.

## The Big Plan

I wanted to use:
- **Arch Linux** with KDE Plasma because I like the look of it
- **Kali Linux**
- **Ubuntu Server**
- **Windows 11**
- **Windows Server**

I felt like these five VMs would be a good amount and they would allow me to test multiple different environments. I also always wanted to try the famous Arch Linux for myself, considering I always heard about it online and had only used Ubuntu.

## Reality Check

But alas, I ran into physics.

As much as I would love to get this big homelab plan of mine up and running, I couldn't do it with my laptop. There wasn't enough RAM, there wasn't enough storage, and there wasn't enough airflow to keep the whole system within acceptable temperatures. And to make matters worse, due to the rolling release aspect of Arch Linux, keeping my homelab "stable" could be way too complicated, and KDE Plasma is apparently too resource-hungry.

So to my disappointment, I was forced to scale down my homelab, at least for the time being, to only three VMs:
- **Ubuntu Desktop**
- **Kali Linux**
- **Ubuntu Server**

## Building the Foundations

So I was ready to start building it.

When I was creating the VM template, I noticed I couldn't use Q35 since it wasn't available, so I switched to PIIX3. Then I found that whenever I turned on UEFI my VMs wouldn't start, so I turned it off.

After that was done, I finished setting up my Virtual Machines and they were working. I created a snapshot of all of them and started updating them.

Everything was going well until I noticed that my Ubuntu Server was black-screening after the update. I scrambled for a bit trying to figure out what was going on, but after I gave it one more core (it had only 1 core initially) it went back to working. Then I made another snapshot of them.

Now it was time for setting up the display resolution, getting dark mode turned on, and adjusting the UI just the way I like it for all my VMs (except the server, which doesn't have a GUI). After all of that was done, I created a third snapshot of all of them.

## The Dreaded Networking

Now here comes the fun part: the dreaded **NETWORKING**.

Before I tried any of this, I had never actually touched networking and had only used IP addresses when setting up online gaming matches (apologies to all my future employers for my lack of experience).

So I set out to do some reading on the subject. I also watched some YouTube videos and used ChatGPT to clear up some questions I had.

Eventually, I learned I needed to use these three different networks:
- **NAT Network** for connecting my VMs to the internet
- **Host-Only Network** to connect my VMs to the host
- **Internal Network** to connect my VMs to one another and use Kali to attack them

## Realizations

Initially, I planned to connect them all to all my VMs, but this is where I learned something extremely important: **NETWORK SAFETY**. If I'm going to use Kali to attack the Server, I shouldn't leave the Server connected to the internet because malware might escape the lab environment.

Using caution, I created two states or "modes" for my homelab:
- **Management mode**: Both Ubuntu Desktop and the Server would be connected to the internet through the NAT Network but wouldn't be connected to the Internal Network
- **Attacking mode**: They would be connected to the Internal Network but not to the NAT Network

## Some Troubleshooting

I created the three networks and connected them all at the same time (for testing).

I expected the Internal Network not to be working since I had to manually assign each VM a static IP, but when I went to test the VMs, the situation was worse than I thought:
- Ubuntu Desktop was working, so that was okay
- In Kali Linux, not a single network was working
- On the Server, only the NAT Network was working

It was time for some troubleshooting.

I first gave Ubuntu Desktop its static IP for the Internal Network.

I then went to Kali and noticed the problem was that I had to manually enable each Ethernet connection and assign them to the respective networks. This was easier than I expected. I also gave Kali its static IP for the Internal Network.

The Server one (as expected) annoyed me because I tried a few ways to get the Host-Only network to work, and none seemed to stick. The configurations worked temporarily, but when I restarted the server, they reverted to not working. So I did some digging and found I could add the IP address using the same method I used for the Internal Network, but through Netplan configuration.

Everything was finally working, and the IPs were properly assigned.

I then did some ping testing and ran into another issue: my host firewall was preventing my VMs from communicating over the Host-Only Network. I fixed that, and finally everything was working, so I created another snapshot.

## Documentation

The final task was creating a README and a diagram that I was proud of. Luckily, I had been writing and taking notes about everything I was doing, so in terms of data, I was fine. I researched how to use markup language and started writing. I had an original draft that I was happy with, so I refined the language (easier said than done) and checked the formatting.

It was now time to do the diagram. While I had dabbled with some diagramming in the past, I had no idea where to start. I checked some pictures online to see how other people did their diagrams, only to find out that they were all different from one another. So I had to get creative.

As I was trying multiple different setups, from having two different diagrams (one for attacking mode and one for management mode) to just having a simple boxes-and-arrows diagram where nothing made sense and the arrows were a mess, I was also learning some very important lessons about it. The first one is clarity, because it doesn't matter how much detail I put in the diagram, if it's messy and hard to understand, I already failed. The second thing is it needs to make some physical sense. So I connected the Host-Only Network to the host machine and the NAT Network to the internet. This is where it clicked.

The diagram is a representation and an abstraction of the "physical" system for someone else to see and understand, so I need to "draw" exactly how the logical relationships work, but I should abstract some of the more complicated parts. After setting up the system, I decided to go check online for a color system to help visualize the diagram better. I added it, labeled it, and it was complete.

## Reflections

Well, that was a wonderful ride. I honestly had a lot of fun setting this up, and I hope I get to build more labs in the future. I learned many things and actually felt like I was doing proper work, even though this is a relatively simple homelab.

I guess this is a good start to my cybersecurity journey. Now all I have to do is start testing on it and work on other projects. My next step will be hardening my VMs.
