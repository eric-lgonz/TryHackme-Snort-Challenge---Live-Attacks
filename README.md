# TryHackme-Snort-Challenge---Live-Attacks

This is a writeup for TryHackme's "Snort Challenge - Live Attacks" room. This room uses Snort version 2.9.7.

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

There is a local rules file in the snort directory, so we can edit that with <code>sudo gedit /etc/snort/rules/local.rules</code> and add the following rule:

_insert image_
_insert image_

Once you have added the rule, save and exit the file. Then enter the following command to run Snort again, which should filter out the ssh traffic: <code>sudo snort -c /etc/snort/snort.conf -q -Q --daq afpacket -i eth0:eth1 -A full</code>. This command runs snort in IPS mode, allowing it to take action on our rule by dropping the packets that match the rule criteria. After running this command for 20 or so seconds, the flag will appear on the desktop:

_insert image_

Now for the questions:

We were tasked to "Stop the attack and get the flag (which will appear on your Desktop)". The flag we found can be copy and pasted into this box and is the answer to this question.

What is the name of the service under attack? We found that the attack was using ssh, which is our answer for this question.

What is the used protocol/port in the attack? For this question, we need to consider the protocol and port that ssh uses. Looking at our rule, we know that ssh uses TCP on port 22, so TCP/22 is the answer.

<h1>Task 3 - Scenario 2 | Reverse-Shell</h1>

Let's start by powering on our VM in the top right corner (make sure you close out of the other VM first).

_insert image_

<h2>Context Behind Scenario 1</h2>

[+] THE NARRATOR

Good Job! Glad to have you in the team!


[+] J.A.V.A.

Congratulations sir. It is inspiring watching you work.


[+] You

Thanks team. J.A.V.A. can you do a quick scan for me? We haven't investigated the outbound traffic yet. 


[+] J.A.V.A.

Yes, sir. Outbound traffic investigation has begun. 


[+] THE NARRATOR

The outbound traffic? Why?


[+] YOU

We have stopped some inbound access attempts, so we didn't let the bad guys get in. How about the bad guys who are already inside? Also, no need to mention the insider risks, huh? The dwell time is still around 1-3 months, and I am quite new here, so it is worth checking the outgoing traffic as well.


[+] J.A.V.A.

Sir, persistent outbound traffic is detected. Possibly a reverse shell...


[+] YOU

You got it!


[+] J.A.V.A.

Sir, you need to observe the traffic with Snort and identify the anomaly first. Then you can create a rule to stop the reverse shell. GOOD LUCK!

Answer the questions below
First of all, start Snort in sniffer mode and try to figure out the attack source, service and port.

Then, write an IPS rule and run Snort in IPS mode to stop the brute-force attack. Once you stop the attack properly, you will have the flag on the desktop!

Here are a few points to remember:

- Create the rule and test it with "-A console" mode. 
- Use "-A full" mode and the default log path to stop the attack.
- Write the correct rule and run the Snort in IPS "-A full" mode.
- Block the traffic at least for a minute and then the flag file will appear on your desktop.

<h2>Scenario 2 Walkthrough</h2>

On our VM, open a terminal by pressing on the terminal icon:

_insert image_

Similar to the previous task, let's first start by trying to identify anything suspicious about the network traffic. Let's start Snort in packet sniffer mode with the <code>sudo snort -v -l .</code>. This will display the TCP/IP output in the console and will also log the packets to a file in our current directory:

_insert image_

Let this run for about 10 seconds, then stop Snort by hitting <code>Ctrl+C</code>.

Once again, if we do <code>ls</code>, we can see that a snort log file was created in our current directory:

_insert image_

Let's now read this log file by using <code>sudo snort -r snort.log.1743841351 -X</code>.

After this runs, we can scroll up and view the packets:

_insert image_

We see that there is a lot of traffic using ports 80 and 4444. We know that port 80 is normally used for http traffic, but what is port 4444 normally used for?

We can do a quick google search to find out:

_insert image_

It seems that this port is associated with a few different kinds of exploits, and another site says that the Metasploit framework commonly uses port 4444. Let's see if this is the source of our attack by making a rule to filter out this traffic.

Enter the command <code> sudo gedit /etc/snort/rules/local.rules</code> and enter the following rule to drop traffic on port 4444:

_insert image_
_insert image_

Save and exit the file, and test the rule by running the following command in the terminal: <code>sudo snort -c /etc/snort/snort.conf -q -Q --daq afpacket -i eth0:eth1 -A full</code>. This command runs snort in IPS mode, allowing it to take action on our rule by dropping the packets that match the rule criteria. After running this command for 20 or so seconds, the flag will appear on the desktop, in a similar manner to the last task:

_insert image_

Now for the questions:

We were tasked to "Stop the attack and get the flag (which will appear on your Desktop)". The flag we found can be copy and pasted into this box and is the answer to this question.

What is the used protocol/port in the attack? We found that the attack was using tcp over port 4444, making our answer TCP/4444.

Which tool is highly associated with this specific port number? For this question, we can refer back to our google search, which told us that Metasploit is associated with port 4444. This means that our answer is Metasploit.

<h1>Conclusion</h1>

And that's it!

In this room, you used Snort to recognize and defend against different kinds of live attacks using custom written rules.

For more information about Snort, I encourage you to check out my NIDS/NIPS Configuration where I demonstrate how to configure and use Snort on your home network.
