# Penetration-Testing

## Part 1

For this part, I opened the metasploit console, and began to search for CVE-2014-0160, which led me to the heartbleed exploit which I selected to use. I then used an nmap scan to scan for all the open ports that were available on briong.com. After a short scan I discovered that ports 22, 80, and 443 were open, the nmap scan also helped me discover the IP address of the briong website: ```165.227.204.80```. I set the IP address of Mark's server to be the host machine, and kept the port at 443. I enabled verbose output, and then ran the exploit on the target machine. This gave me a large amount of output, but from all this data a few keywords popped out at me, the username, password, flag, and easter egg. The easter egg was something not recognizable, but one thing that led me to believe that it was encrpyted was the '=' character at the end of the long string of letters, I knew that this is used in padding for 64bit encryption. 


```
Username: mnthomp22
Password: pass1234
Flag: CMSC389R-{h3art_bl33d}
Easter Egg: WAIT THIS ISN'T ENCRYPTION?? CMSC389R-{base64_is_still_used_for_crypt0}
```

## Part 2

The first thing I did for this part was connect to Mark's server with ```nc briong.com 45``` to run a few commands and implement some black box testing. After inputing a few commands with little success, I remembered that I could use ```&&``` to run two commands one after the other I started exploring the server running commands such as ```google.com && ls -a``` to look inside Mark's server. I located the home directory and ran ```google.com && ls home``` which revealed the ```flag.txt``` file which contained the flag:

```Flag: CMSC389R-{p1ng_c0mmand_inj3ction}```

In order to figure out how Mark could fix this vulnerability I searched for his script on his machine, while looking through the ```opt``` directory I found a script called ```container_startup.sh```. After looking it up by inputing ```google.com && cat /opt/container_startup.sh```. This resulted in the following script:

```
#!/bin/bash

unset domain

echo -n "Ping an IP or domain: "
read domain
>&2 echo "[$(date)] IP/DOMAIN: $domain"
echo "Pinging: $domain"
python -c "import commands; s,c = commands.getstatusoutput('ping -c 5 $domain'); print c"
```

It seems that Mark's problem is that he does not sanitize his input, anything that a user enters goes unchecked and is run, Mark could easily check his input by using regular expressions, something like this could work: 

```
/((([A-Za-z]{3,9}:(?:\/\/)?)(?:[-;:&=\+\$,\w]+@)?[A-Za-z0-9.-]+|(?:www.|[-;:&=\+\$,\w]+@)[A-Za-z0-9.-]+)((?:\/[\+~%\/.\w-_]*)?\??(?:[-\+=&;%@.\w_]*)#?(?:[\w]*))?)/
```

Aternatively, Mark could use input sanitization, to remove any additional commands that someone may try to run while using his service. Something simple like this can work:

```
# first, strip underscores
CLEAN=${STRING//_/}
# next, replace spaces with underscores
CLEAN=${CLEAN// /_}
# now, clean out anything that's not alphanumeric or an underscore
CLEAN=${CLEAN//[^a-zA-Z0-9_]/}
# finally, lowercase with TR
CLEAN=`echo -n $CLEAN | tr A-Z a-z`
```
