# TryHackme-Snort-Challenge---Live-Attacks

This is a writeup for TryHackme's "Snort Challenge - Live Attacks" room. This room uses Snort 2.9.7.

<h1>Task 1 - Introduction</h1>

The room invites you to a challenge where you will investigate a series of traffic data and stop malicious activity under two different scenarios. Let's start working with Snort to analyse live and captured traffic.

Before joining this room, we suggest completing the Snort room. 

Note: There are two VMs attached to this challenge. Each task has dedicated VMs. You don't need SSH or RDP, the room provides a "Screen Split" feature.

Now that we understand the context of the room, let's complete this task and move on to Task 2.

_insert image_

<h1>Task 2 - Scenario 1 | Brute Force</h1>

Let's start by powering on our VM in the top right corner.

_insert image_

<h2>Context Behind Scenario 1</h2>

[+] THE NARRATOR


J&Y Enterprise is one of the top coffee retails in the world. They are known as tech-coffee shops and serve millions of coffee lover tech geeks and IT specialists every day. 


They are famous for specific coffee recipes for the IT community and unique names for these products. Their top five recipe names are;

WannaWhite, ZeroSleep, MacDown, BerryKeep and CryptoY.

J&Y's latest recipe, "Shot4J", attracted great attention at the global coffee festival. J&Y officials promised that the product will hit the stores in the coming months. 

The super-secret of this recipe is hidden in a digital safe. Attackers are after this recipe, and J&Y enterprises are having difficulties protecting their digital assets.

Last week, they received multiple attacks and decided to work with you to help them improve their security level and protect their recipe secrets.  

This is your assistant J.A.V.A. (Just Another Virtual Assistant). She is an AI-driven virtual assistant and will help you notice possible anomalies. Hey, wait, something is happening...



[+] J.A.V.A.

Welcome, sir. I am sorry for the interruption. It is an emergency. Somebody is knocking on the door!


[+] YOU

Knocking on the door? What do you mean by "knocking on the door"?


[+] J.A.V.A.

We have a brute-force attack, sir.


[+] THE NARRATOR

This is not a comic book! Would you mind going and checking what's going on! Please... 


[+] J.A.V.A.

Sir, you need to observe the traffic with Snort and identify the anomaly first. Then you can create a rule to stop the brute-force attack. GOOD LUCK!

Answer the questions below
First of all, start Snort in sniffer mode and try to figure out the attack source, service and port.

Then, write an IPS rule and run Snort in IPS mode to stop the brute-force attack. Once you stop the attack properly, you will have the flag on the desktop!

Here are a few points to remember:

- Create the rule and test it with "-A console" mode. 
- Use "-A full" mode and the default log path to stop the attack.
- Write the correct rule and run the Snort in IPS "-A full" mode.
- Block the traffic at least for a minute and then the flag file will appear on your desktop.

<h2>Scenario 1 Walkthrough</h2>

On our VM, open a terminal by pressing on the terminal icon:

_insert image_

Before we can stop the attack with Snort rules, we first have to know what kind of attack we are being hit with. Let's start Snort in packet sniffer mode with the <code>sudo snort -v -l .</code>. This will display the TCP/IP output in the console and will also log the packets to a file in our current directory:

_insert image_

Let this run for about 10 seconds, then stop Snort by hitting <code>Ctrl+C</code>.

If we do <code>ls</code>, we can see that a snort log file was created in our current directory:

_insert image_

Let's now read this log file by using <code>sudo snort -r snort.log.1743829415 -X</code>.

After this runs, we can scroll up and view the packets:

_insert image_

Something I noticed in the packets is that there was a lot of traffic on ports 80 and 22. Port 80 is used for http traffic, and port 22 is used for ssh, which is a tool used to remotely login to systems. Let's look to see if this port 22 traffic is indeed ssh by searching for it with grep: <code>sudo snort -r snort.log.1743829415 -X | grep "ssh"</code>:

_insert image_

We can see that ssh pops up many times, and this could very well be the source of the attack, as someone is repeatedly trying to login to another machine. Let's write a general rule that stops ssh traffic from traversing the network.










