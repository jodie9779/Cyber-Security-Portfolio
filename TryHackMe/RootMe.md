## RootMe

### Difficulty
Easy

### Skills gained
Reverse shell exploit, SUID privilege escalation

### Link
https://tryhackme.com/room/rrootme

### Writeup

#### Reconnaissance

• I started with a quick nmap scan to show the open ports, then a more in-depth nmap scan to get more information about the ports open

     nmap -T4 -p- TARGETIP
     
<!--- ADD NMAP OUTPUT 1 IMAGE HERE --->
     
     nmap -T4 -A -p22,80 TARGETIP
     
<!--- ADD NAMP OUTPUT 2 IMAGE HERE --->

• I then ran a dirb scan to see what directories could be found on the website

    dirb http://TARGETIP wordlist.txt
    
<!--- ADD DIRB OUTPUT IMAGE HERE --->
    
• Once I got the directories within the website, I had a look at them to see if anything immediately popped out at me. On the panels page, I noted that you   could upload something to the website. I noted this down for the next task

<!--- ADD IMAGE OF UPLOAD PAGE --->

• All the information obtained through these scans was enough to answer all the questions within this task

#### Getting a Shell

• The first thing I tried to upload was the php reverse shell from pentestmonkey. A message quickly came up to say php files were not allowed

<!--- ADD IMAGE OF REJECTING PHP FILES --->

• I did a quick google and found a page showing different file extensions to use if php is blocked. I changed the file extension to php2, which the webpage   accepted. 

• This uploaded file could be found in the uploads page

• I set up a listener to get the reverse shell

    nc -lvnp 3434
    
• I had some issues with the php2 file extension not producing a shell, so I went through the different file extensions until I got to php5, which produced   a shell as the 'rootme' user

• I used the find command to find the 'user.txt' file which contained the flag to this task
    find / -name user.txt 2>/dev/null

<!--- ADD IMAGE TO FIND COMMAND --->

#### Privilege escalation

• I used the find command to find the files owned by the root user which had SUID permissions, which allowed me to spot a file I could use for privilege     escalation

<!--- ADD IMAGE TO SECOND FIND COMMAND --->

• I then had a look on gtfobins, which had the command to run for this exact situation to get a root shell

    ./python -c 'import os; os.execl("/bin/sh", "sh", "-p")'

• After checking that I was a root user, I then used the find command to find the root.txt file, which contained the last flag.

    find / -name root.txt 2>/dev/null
    
<!--- ADD IMAGE OF ROOT USER STUFF --->

• This gave me the last flag, and we had successfully rooted the machine!
