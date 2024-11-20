#   SETUP
https://sec.cybbh.io/public/security/latest/index.html
Stack Number	Username	  Password	        jump
2 	          ALFA-007-M 	SiatKd1eRB2Q4ns 	10.50.33.233 (only running ping from jump box)
http://10.50.20.103:8000/
Linux Ops 10.50.27.254               Windows Ops 10.50.31.208

#  NMAP
NMAP scripting engine location /usr/share/nmap/scripts

#  USAGE & EXAMPLES 
-nmap --script <filename>|<category>|<directory>
-nmap --script-help "ftp-* and discovery"
-nmap --script-args <args>
-nmap --script-args-file <filename>
-nmap --script-help <filename>|<category>|<directory>
-nmap --script-trace

#  Master Socket ssh to jump
ssh -MS /tmp/jump student@10.50.33.233

#  PingSweep
for i in {97..126}; do (ping -c 1 192.168.28.$i | grep "bytes from" &); done

#  Set Up Dynamic Port forward 
-S for socket file
-O for forward options
-D9050 proxychains
requires a filler word to act as credentials (in this case its jump)
ssh -S /tmp/jump jump -O forward -D9050












































































