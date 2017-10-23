<b>VM Name:</b>
RickdiculouslyEasy:1

<b>Objective:</b>
Get root and find all the flags (total of 130 points)

It's been a bit since I checked Vulnhub for new VMs to mess with, and I was happy to find a bunch were added. One of which was a Rick and Morty-themed one, which I couldn't resist trying out.

After loading up the VM in VirtualBox, step one was to find out the target's IP address with an arp-scan.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/arpscan.png">

With the target IP, it's time to run nmap to determine open ports and services, and I decided to run it over all ports 1-65535 to see if anything interesting popped up.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/nmapresults.png">

There are a bunch of ports to explore, and we can already see a flag, but we can check that port out later. 
I started out with port 80 just in case there was information there I could use for the other ports. Accessing it through a browser, I was greeted by Morty.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/landingpage.png">

There was nothing interesting in the source, so the next thing I check is /robots.txt.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/robotstxt.png">

And, cool, there is some stuff here to check out. Naturally, I should have known that root_shell.cgi would be a troll, but I tried anyway with no good results other than Rick laughing at me. The tracertool.cgi on the otherhand...

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/tracertool.png">

After playing around with this for a bit, I found that you could get away with command injection by escaping the tool with a semi-colon. I also found that it wasn't possible to use "cat" and instead utilized "more." Going straight to what most would go for first, I used this with /etc/passwd.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/command_passwd.png">

Based on the output, we see there are a couple of users, and we'll keep this on the backburner for now. Before moving on from port 80, I booted up Sparta to see if it could detect anything else of interest. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/spartaresults.png">

Nice, a /passwords/ directory. Checking this out we find two files. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/passwords.png">

Boom. A flag! 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/flag2.png">

Going back to the html file, there is a message from Rick, but if we inspect the source we find a hidden password that I tucked away for later.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/passwordhtml.png">
<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/hiddenpasswordsource.png">

Time to move on to the next port--FTP! Anonymous with no password and we are in. Low and behold there is a flag.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/ftpopen.png">
<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/FTP_flag.png">

I already know there is a flag in port 13337, so might as well netcat into it.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/13337_flag.png"> 

Easy enough! Next, I checked out port 9090 by accessing it through https in a browser. Another flag! 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/consoleflag3.png">

Before we move on to our SSH ports, anything interesting with port 60000? Based on the Nmap output, Rick seems to have set up some kind of reverse shell. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/60000_flag.png">

Pickle Rick! OK, SSH time. Port 22 led nowhere, but port 22222 is definitely running SSH. Time to bring out all the info we stored away on the back burner. After trying each user that was listed in the /etc/passwd file, the Summer user combined with the password "winter" gave me access. Makes sense...

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/ssh_on_22222.png">

Taking a look at what files are available, there is a flag in her directory! 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/summerfiles.png">

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/summer_flag.png">

Backing out of her directory, we have a Morty directory and a RickSanchez directory. Morty seems like the easier target, so I scoped out what he had on tap. An image file and a zip file... Very suspect. I copied them to Summer's folder and then transferred them to my box with SCP for analysis.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/scptransfer.png">

The zip was password protected, but using strings on the image there was a password hidden in the image file.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/safepassword_pic_analysus.png">

Caaaaaan do! Using this password, the zip file opened right up. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/journalFLAG.png">

Morty, you shouldn't write down stuff like that in your journal! Looks like we have a bit of info we can tuck away for later. With Morty's folder exhausted of resources, I moved onto Rick's folder. Examining the files inside, it's the safe that was mentioned in Morty's journal. Looks like its an executable file as well, so I transferred it to check it out.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/ricksfolder_and_safe.png">

Strangely I didn't have the library that was needed to run the program on my Kali VM, so I launched it with Ubuntu. Executing without any arguments gave me the hint that I needed to use an argument, so I tried the flag password that Morty revealed in his journal.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/safe_flag.png">

A flag and a clue! We have quite a bit of information to play with to create a password. First things first, even though I am a fan of the show, I can't recall the band name, so time to Google it. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/fleshcurtains.png">

Pairing up all the information that we have on hand, it's crunch time! And by crunch, I mean it's time to use Crunch to generate a wordlist based on what we know. It's gotta be one of the two words (other than 'the'...), so I made two different wordlists that follow the hints given.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/crunchTIME.png">

Now with my handy wordlists, I launched Hydra for SSH access with Rick's username. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/hydraSuccess.png">

And we have a hit on a password! Time to SSH under RickSanchez using our newly found password. Also, there was the hint about sudo, so I guessed that it was going to be easy to become root with this account.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/rootGET.png">

I am root! And we find the final flag (even though I forgot I couldn't use 'cat'...).

Here are all the flags found put together into one spot:
<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/RickdiculouslyEasy-1/walkthrough_images/allflags.png">

Overall this was a pretty simple VM, but it was fun with the Rick and Morty theme and it is a good example of enumeration and other helpful tools for hacking boxes.
