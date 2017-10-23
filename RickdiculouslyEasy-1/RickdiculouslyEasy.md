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

Will write the rest up tomorrow!

