<b>VM Name:</b>
Zico2

<b>Objective:</b>
Get root

Loading up the VM in VirtualBox, it's time to do another round of arp-scan and Nmap to find out our target IP address and enumerate on what ports/services are open to us.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Zico2/images/zico_arp_nmap.png">

Jumping right in to port 80, we load up the website in a browser. There's a bunch of stuff on this site.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Zico2/images/homepage.png">

Quite a bit to look at, but clicking on the tools section, the URL tickled the part of me that wants to test out directory traversal. Sure enough, it works, and we can see the /etc/passwd file. I looked around a bit more but nothing else really stood out.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Zico2/images/pathtraversal.png">

Next, I booted up Dirb using a common wordlist, and tried to see if there were any directories hidden from us. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Zico2/images/dirbresults.png">

The /dbadmin directory looks like it could be tasty.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Zico2/images/dbadmin.png">

Nice--a login screen. Does 'admin' work for the password?

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Zico2/images/dbadminlogin.png">

Lol... yep. Bad, bad, bad, Mr. Admin, sir. You should always change the default password! So we have a phpLiteAdmin v1.9.3 running on this system. Searching on Exploitdb, I found just what we needed, which fit this version perfectly:

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Zico2/images/exploitforphpadmin.png">

I tested this out by following the PoC steps, and it worked like a charm.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Zico2/images/testing2.png">

Now we can get creative and think of a way to get a reverse shell going. I initially tried using the PHP reverse shell code from Pentest Monkey, but it was having a bit of trouble going through, and while I try to avoid Msfvenom/metasploit when possible, I decided to just go for it this time. I generated a reverse shell and then hosted it on my box using Python's simpleHTTPserver.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Zico2/images/msfvenom.png">

After my server was running, I created a new table in the database with the following php code as the default text:

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Zico2/images/phpcode.png">

Then I setup a listening port and got ready to deploy this shell.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Zico2/images/listening.png">
<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Zico2/images/tablecreate.png">

Executing it via our directory traversal bug we found earlier, the system downloads our reverse shell and then executes it, granting us access!

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Zico2/images/breakoutshell.png">

Now it times to explore and find interesting files. Heading into the zico directory there is a to_do.txt file which looks promising. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Zico2/images/checkingoutfiles.png">

This immediately sets off the alarm that we should check out the wordpress folder, and more specificaly the config file.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Zico2/images/wpconfig.png">
<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Zico2/images/wpusers.png">

Nice, we have a username and a password. Perhaps this will work with SSH?

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Zico2/images/zicoed.png">

And yep! We are in on the zico user. Taking a look at what can be executed as superuser/root there are two things, but I couldn't find an interesting way to use them. After researching this when I was done, it seems there is a neat trick to use zip, which I will tuck away in my bag of tricks for another day. Instead, I opted to do some more Exploitdb research and came across a good exploit that could be used with this version of Ubuntu. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Zico2/images/exploitforroot.png">

Copying the source code, we can bring up vim and paste it in, and then compile and run according to the source notes. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Zico2/images/rooted!.png">
<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Zico2/images/zicoflag.png">

Awesome--we are root now and we got the flag! GG!













