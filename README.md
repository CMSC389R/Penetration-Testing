# Penetration-Testing

## Part 1

For this part, I opened the metasploit console, and began to search for CVE-2014-0160, which led me to the heartbleed exploit which I selected to use. I then used an nmap scan to scan for all the open ports that were available on briong.com. After a short scan I discovered that ports 22, 80, and 443 were open, the nmap scan also helped me discover the IP address of the briong website: ```165.227.204.80```. I set the IP address of Mark's server to be the host machine, and kept the port at 443. I enabled verbose output, and then ran the exploit on the target machine. This gave me a large amount of output, but from all this data a few keywords popped out at me, the username, password, flag, and easter egg. The easter egg was something not recognizable, but one thing that led me to believe that it was encrpyted was the '=' character at the end of the long string of letters, I knew that this is used in padding for 64bit encryption. 


```
Username: mnthomp22
Password: pass1234
Flag: h3art bl33d
Easter Egg: WAIT THIS ISN'T ENCRYPTION?? CMSC389R-{base64_is_still_used_for_crypt0}
```

## Part 2

The first thing I did for this part was connect to Mark's server with ```nc briong.com 45``` to run a few commands and implement some black box testing. 
