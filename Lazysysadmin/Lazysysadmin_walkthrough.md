<b>VM name:</b>
Lazysysadmin

<b>Objective:</b>
Get root

Another boot to root! Arp-scan to grab our target IP followed with an Nmap scan to determine open ports/services to see what kind of attack vectors we have available. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Lazysysadmin/images/lazyadmin_arp.png">
<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Lazysysadmin/images/lazyadmin_nmap.png">

Lotsa stuff going on here, but let's start with the http first. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Lazysysadmin/images/lazyadmin_homepage.png">

I checked around the site, the source, and robots.txt, but nothing interesting really stood out.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Lazysysadmin/images/lazyadmin_robotstxt.png">

Time to boot up Dirb and check if there is anything to be found.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Lazysysadmin/images/lazyadmin_dirb.png">

Okay, neat. We have a php admin page and some wordpress goodness going on here. Checking out the php admin page first, I couldn't really figure out too much, so it was a dead end for the moment.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Lazysysadmin/images/lazyadmin_phplogin.png">

Next, I checked out Wordpress. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Lazysysadmin/images/lazyadmin_wp.png">

Togie? Seems like it could be a username that could come in use, so let's store it on the backburner for now, and in the meantime run a Wpscan.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Lazysysadmin/images/lazyadmin_wpscan.png">

Again, not too much here. There is a vulnerability applicable to this WP version, but nothing that is really too useful for our environment, so it's time to move on and recon some more. Next stop, SMB!

Loading up enum4linux, I checked out what was available from SMB.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Lazysysadmin/images/lazyadmin_enum4linux.png">

Sumshare on share$ looks like a good prospect to check out, so off we go!

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Lazysysadmin/images/lazyadmin_smbconnected.png">

Checking around these files, there is one that straight up contains a password.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Lazysysadmin/images/lazyadmin_pwhint.png">

Tucking this away for the moment, we continue onwards to the wordpress config file. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Lazysysadmin/images/lazyadmin_wp_creds.png">

Mmm...more creds. This prompted me to head back to the php admin site and log in; however, the databases were all locked down due to configuration settings, and it seemed like this was not the path to take. Okay, then, how to proceed? Well, perhaps we can just try togie as a username and '12345' as the password for SSH?

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Lazysysadmin/images/lazyadmin_ssh.png">

Whenever gaining access to a shell via SSH it's always fun to at least try to "sudo -i" yourself into root, even though 9/10 it's not going to work. BUUUUUT.... This time we got super lucky AND the password was the same as togie's! (And my luggage!)

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/Lazysysadmin/images/lazyadmin_root_flag.png">

We are root and got the flag! 

While this was pretty easy, it was still interesting to explore various services until we found the proper vector to take in order to gain access to the box.




