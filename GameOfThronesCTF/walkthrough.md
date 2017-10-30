<b>VM name:</b>
Game of Thrones Hacking CTF

<b>Objective:</b>
Collect all the flags

This was a refreshing challenge after doing a bunch of boot-to-root VMs, and I had a lot of fun--especially with the theme being Game of Thrones. There was also a ton to learn from it. So let's get to it...

Arp-scan and Nmap first as usual to find out the target IP and ports/services enumeration. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_arp.png">
<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_nmap.png">

We've got a lot to sort through, but let's hit port 80 first by accessing it with a browser.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_homepage.png">

Taking a look at the source, we get a full scoop on the rules, goals, and some hints. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_source_rules.png">

Next we take a look at robots.txt.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_robots.png">

Let's explore these one by one. First: /the-tree/. We are stopped by Jon Snow, who seems to be telling us that we know nothing, but looking at the source code for this page we get another hint.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_the_tree.png">
<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_useragenthint.png">

Look at those capital letters spelling it out for us. We need to change our user-agent to be the three-eyed raven as indicated by robots.txt.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_agent_edited.png">

Now that we added a user agent and swapped to it, reloading the page yields a different result. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_branhelps.png">

Inspecting the source, we get another hint. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_bran_hints.png">

Let's tuck this info away and get exploring the rest of the directories. Next: /secret-island/. Oh, hi, Littlefinger. I don't really want to be your friend, but I will take your map.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_mappage.png">
<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_map.png">

Just as stated in the goals/rules text, it looks like we have our set path ahead of us and can see what we need to do for each challenge/flag. The final entry in the robots.txt did not have much in terms of clues. But before we move on to Dorne (FTP), let's really scour this port for everything possible to gather as many clues as possible. So, checking out everything we can we find the following:

CSS source code:

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_css.png">

JS source code:

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_js.png">

Sitemap XML:

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_sitemap.png">

Which then leads us to raven.php:

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_ravenphp.png">
<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_mcrypthint.png">

I even downloaded everything possible, which includes the audio files, and I took a look at all of them. It was a good idea, because running exiftool on the mp3 file uncovered a flag! One of the secret flags.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_savageflag.png">

Finally, just to be sure, let's run Dirb with the common.txt wordlist to see if there is anything else hiding.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_dirb.png">

And there is! Heading to this URL we get some more info.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_hidden.png">

Thanks for letting us know, Jorah! Scoping out the code we get some hints.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_hidden_hints.png">

That pretty much does it for port 80. Now we are armed with a bunch of clues, and our scouting has also uncovered the credentials for the FTP server (oberynmartell:A_verySmallManCanCastAVeryLargeShad0w), so it's time to head to Dorne!

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_FTPin.png">

Nice! Our first kingdom flag! And there are two files to check out here. the_wall.txt.nc is encrypted and locked down, but the problems_in_the_north.txt gives us some information to work with.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_northtxt.png">

Alright, so we have a hash and a clue as to how the hash was created. $s is the salt and $p is the password, and checking Hashcat for this type of hash, we come up dry. However, instead of looking at the help text via commandline, we can pull up a cheatsheet from the Hashcat wiki, and at the verrrrry bottom, we find our hash structure under 3610.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_outdatedhash.png">

This means we can't reverse this hash with the <i>current</i> version of Hashcat, but we can use an older verison since this is a legacy hash type. Easy enough--we just download Hashcat 2.0, install it, and run it with our hash using the rockyou.txt wordlist. 
Also, before running it, instead of the "$" that is in our string, we need to change that to a colon.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_hashcat.png">

Voila--our password: stark

Now, looking back on our list of hints, we know that mcrypt is what we want to use to decrypt the the_wall.txt.nc file.
Oh, and it looks like we need a passphrase, which we just happened to find out.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_mcrypt_decrypt.png">

Opening the decrypted file, we get the following:

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_encryptedTEXT.png">

Onward to Winterfell! Let's drop this link in the browser annnnd...

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_winterfelltroll.png">

Oh, right. We are going to have to change our /etc/hosts file first so we can navigate here.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_hostsmod.png">

Cool, now that that is all set, we arrive at a login prompt to which we put in the credentials we got from dear, old Samwell.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_winterfellcreds.png">

Looking at the source to this page we are welcomed to Winterfell and get another kingdom flag! There is also another clue. So, downloading the shield and running strings on it, we get the following:

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_starkshieldstrings.png">

Nice, another hint! Before moving on, let's just check out the CSS file as well.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_winterfellcss.png">

Our next stop is the Iron Islands, which we know from our Nmap scan is port 53--meaning DNS, so combining this knowledge with the hints we've been given, it would seem we need to check out a TXT query for Timef0rconqu3rs, which we can do with the "hosts" command. I initially tried "dig", but it didn't return any descriptive text like hosts did (I'm sure there is a way though...).

All we need to do is add the result of the domain onto what we got in the hint and our IP address and it comes back with descriptive text for the domain.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_DNSTEXT.png">

We got another kingdom flag and a clue for the next segment of our journey! Adding another entry to our host file, we are off to the Stormlands!

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_stormlands.png">

Logging in with the credentials we got, it appears this is a webmin site. It's clear there is a flag.txt file somewhere in here, but nothing seems to turn up. That search box sure does look like it could be fun to play with though! And sure enough, just inserting an apostrophe into the field turns up a list (with only one item) that leads us to a file management page.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_apostropheinput.png">
<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_stormland_filemanager.png"

Navigating with the file manager to /home/aryastark, we find our flag.txt file, which we can save to our machine and read.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_aryastark.png">
<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_stormlands_flag.png">

Awesome--a kingdom flag and credentials for the Mountain and the Vale! Our next stop is postgresql, and it looks like we are going to have to stick to CLI to traverse around that land. This required a bit of research on my part since I was not familiar with all of the commands (I only know some pretty basic SQL commands), but this resource helped: https://www.tutorialspoint.com/postgresql/postgresql_syntax.htm

With that said, let's head into the mountainandthevale database using the credentials we attained.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_psql_login.png">

Looking at the commands that can be used in the database, \dt will list all the tables, and there are 4 that we can check out.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_psql_tables.png">

We'll check these out one by one and gather what we can. 

aryas_kill_list (I have something highlighted here, and you will see why in a moment.):

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_braavos_database.png">

Braavos book:

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_braavosbook.png">

Huh... what's that gobbledygook at the end? Well, it definitely looks like a cipher. Instict makes me want to check right away if it is a rot-13 cipher, but it was not. HOWEVER! It is another kind of shift-cipher, a rot-16. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_rot16_frompsql.png">

A clue! And as you see now, what I highlighted in Arya's kill list is important. We now have a full set of credentials for a different databse. Before we deviate from the current database and over into Braavos, let's finish up what we can here.

Eyrie:

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_eyrie.png">

So we know there is a flag here. I tried to do: select * from flag, but was instantly denied access. However, Littlefinger is giving us a clue to OWN everything. This is where reading up on the various commands was very useful. We can give access to the user we are currently on (robinarryn), in the following way and then access the flag.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_databaseprivileges.png">
<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_robinarrynb64.png">

Cool! More mumbo-jumbo. But those familiar will instantly recognize that this is base64. We can copy this text and decode it:

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_b64_decoded.png">

We get our next kingdom flag and credentials for our next destination: The Reach AKA High Garden. But before we leave the Vale there is one more table to check out.

Popular wisdom book: 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_psql_book3.png">

A lot of clues here that are useful. Especially the one about High Garden when paired with the information we received from Bran early on in regard to certain numbers!

Before we travel to High Garden, we are going to make a pit stop in Braavos. Putting together the clues and creds we got earlier, we will again access psql via command line to check out this new database. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_braavosDB_connected.png">

Checking out the tables we find another secret flag!

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_sercretbraavos_flag.png">

Alright, that was a quick trip to Braavos and now we are back on track to High Garden. As the map indicates, High Garden is IMAP, which is port 143; however, looking at our nmap scan, the port isn't really open. It's in a filtered state. Look at the clues though: we have a couple of numbers and there is the clue about knocking (a lot of mention about being polite as well, and knocking is definitely considered polite!), which can only mean one thing--we have to do some port knocking for High Garden to open up their doors! I've encountered a couple port knocking challenges before and have handy-dandy python script I found by grongor that we can use to accomplish this. https://github.com/grongor/knock

Running the script with the numbers from Bran, we then check nmap again for port 143.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_portknocker.png">

Great! It's open now. There are a couple of different ways to go about accessing this port. The first way is by telnet. I once again had to look up IMAP specific commands to get around once telneting in with the attained credentials. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_IMAP_loggedin.png">

Navigating around we find an email but it isn't giving us the whole message :(  

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_IMAP_reach_flag.png">

Back to Google to see what we can do... Instead of telnet, we can use curl to query the system so that it gives us the entire email and in much better formatting!

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_cURLed_highgardenFLAG.png">

We once again get a kingdom flag and more credentials for the next destination: Casterly Rock. Pointing our browser to port 1337, we are prompted for our creds

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_port1337.png">

Upon accessing the site we can see it is a GitList site.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_gitlist_casterlyrock.png">

Exploring the entries, there is a string of what looks like hex characters in note_under_the_bed.md

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_casterly_note.png">

Copying this string and converting it to ascii we get the following message:

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_casternote_decoded.png">

What to do with this? Well, taking to Google and Exploitdb, it becomes clear that there is a remote code execution vulnerability present in GitList, which we can use to check out this directory/file.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_gitlist_RCE.png">
<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_gitlist_exploited.png">
 
Clues and credentials. Just as all the previous clues have insinuated, it wasn't possible to remote into the King's Landing MYSQL database directly, so instead, it looks like we will have to continue using Casterly Rock's RCE vulnerability to access it. Stacking everything after our URL, we get an error but also a clue: there is a table named iron_throne. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_sqltables.png">

Now we can add the table onto the end of our command and we get the following:

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_ironthronetable.png">

Oh--morse code, eh? Let's decode it and see what it says.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_morsecode.png">

Now we know the location of our flag, but trying to traverse here doesn't work, so we need to think of a way to get around that. Davos did say that we have priviledges in the kingdom, so perhaps we can use that power to copy the contents of the flag into a new table that we can then read.

First, let's check what permissions we have:

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_sql_filegrants.png">

This is good as it seems like we will be able to execute the aforementioned idea, so let's give it a shot.

Step 1: Let's create a table called 'ihatecersei' with a column named 'derp', and then set it so it accepts TEXT, which should be enough space to hold whatever our flag file contains (Feel free to name your table/column whatever you like!).

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_mysql_tablemake.png">

Step 2: Load the flag file into the newly made table.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_loadingfileintotable.png">

Step 3: Profit! We access the table to view the contents.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_kingslandingflag.png">

There's our final kingdom flag and our path to the dragonglass we need before the final battle. Time to SSH with these newly uncovered credentials.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_sshed_dragonglass.png">

Of course it wouldn't be as easy as finding the dragonglass right when we access! Based on these clues, we are going to have to create an SSH tunnel over to the dragonglass mines. It also mentions that the fail2ban magic is not present, which means bruteforcing with Hydra is an option--and hey! digger.txt is a wordlist, so let's grab that with SCP.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_diggertxt.png">

I setup the SSH tunnel using port 31337 on localhost, but you can use any port you like.

"ssh daenerystargaryen@192.168.1.118 -L 31337:172.25.0.2:22 -N"

After this is up and running, it's time to load up Hydra and point it to port 31337 on the localhost utilizing the digger.txt as the wordlist. Just as the clue mentioned, we want to be the user 'root'.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_dragonglass.png">

With the discovered password, we can then SSH into the mines and discover the third secret flag.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_dragonglass_flag.png">

Looks like it's finally time for the final showdown with the white walkers! Let's SSH in with our new credentials.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_finalchallenge.png">

How to escalate our privileges to root? I looked around a bit on the box to enuerate possibilities, and didn't find any vectors. But looking over our clues, there was mention that this is a Docker-based system, which is confirmed when using the 'id' command. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_docker_ided.png">

Researching this a bit, Metasploit has a perfect exploit for this that utilizes the docker daemon to escalate privileges.
Booting up good 'ol Metasploit, we can establish an SSH connection through the console and then deploy our exploit to become root. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_root_exploit.png">

Now that we are root, we can explore the root directory.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_rootdirectory.png">

Cat on the checkpoint.txt file gives us some hints. 

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_finalbattle_checkpoint.png">

Saving the final_battle file to our own box, it's clear that it's a zipped file that is password protected.

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_finalbattle_password.png">

Looks like the checkpoint.txt is guiding us on how to create the password to unlock this arhive, and while you can choose whatever language you want to accomplish this, I stuck to Python. Breaking down the pseudo-code, we need to combine indexed portions of each secret flag string that we found. 

I created the following script based on what the pseudo-code was directing us to do:

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_finalbattle_python_script.png">

Running this we get the following string:

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_finalbattle_pw.png">

Using the PW to open the final_battle archive, we receive the final flag!

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_finalflag.png">

Finally, using an MD5 decoder on all of the flags in order, we get a small message:

<img src="https://github.com/Keramas/Vulnhub_VM_Walkthroughs/blob/master/GameOfThronesCTF/images/got_allflags_message.png">

Overall, this was an awesome CTF VM and I had a blast doing it. The challenges were interesting and I learned a lot through the research done to complete the tasks. A big thank you to the creator!
