---
title: 'Goodbye Wordpress, hello Hexo! '
toc: true
date: "2015-12-05T20:59:25+00:00"
---

I've opened this blog site since 2013 when I wanted to write something for recording my study notes and improved my English writing.

At that **_time_**, I chose wordpress mainly because I'm familiar with it and I had the experience to build a blog site using it. Once I started building a blog, the installation was pretty straightforward -- just extracting the `tar.gz` and throwing the file into `/var/www/blog` (`/var/www/html` is for the static files hosting on Debian). Then I need to configure the nginx by use of fcgi to forward the dynamic contents to PHP.

So later, I started finding a theme to make my blog beautiful and some plugins to make my writing smoothly, like the code highlighter, the TOC (aka. Table of Content) plugin, category tree plugin etc.

After I completed 100+ posts, I suddenly realised it seemed to go far away the essence of writing, as I spent too much time on doing these technical things - although my writing were mostly related to technical stuff.

Today I have the needs to transfer my vultr VPS from Australia to Japan. That's because it's really slow when I connect to the VPN and shadowsocks hosted on my server.

The following is the steps after I create a new vultr instance located in the data center of Japan:

### setup the pptpd for VPN


```bash
$ apt-get update
$ apt-get install pptpd
```

uncomment the section in the tail of `/etc/pptpd.conf`
``` txt /etc/pptpd.conf
localip 192.168.0.1
remoteip 192.168.0.234-238,192.168.0.245
```

enable ipv4 forword in the `/etc/sysctl.conf`
``` text /etc/sysctl.conf
net.ipv4.ip_forward = 1
```

then make it effective


```bash
$ sysctl -p
```

The last step is adding a rule to the iptables and restart pptpd


```bash
$ iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE && iptables-save
$ service pptpd restart
```


That's it. I refer to the [digital oceal](http://digitalocean.com) official guide here: [How to setup your own vpn with pptp](https://www.digitalocean.com/community/tutorials/how-to-setup-your-own-vpn-with-pptp)

### AnyConnect VPN
Updated on 13/12/2015

Although PPTP can satisfiy	 my needs, I think it's not stable and rebust. Recent days, I always use Cisco `AnyConnect` VPN client to connect to my office internal network, so I decide to set up an AnyConnect compatible VPN server on my VPS.

I didn't spend too much time to research how to do that. I just used an automatic installation script which can be refered to [this Github repository](https://github.com/fanyueciyuan/eazy-for-ss).

### setup shadowsocks
Shadowsocks is a must-have if you live in China and need to do something by circumvention.



```bash
$ wget -O- http://shadowsocks.org/debian/1D27208A.gpg | apt-key add -
$ echo "deb http://shadowsocks.org/debian wheezy main" >> /etc/apt/sources.list
$ apt-get update
$ apt-get install shadowsocks-libev
```


Then you need to modify the `/etc/shadowsocks-libev/config.json`
``` json /etc/shadowsocks-libev/config.json
{
    "server":"my_server_ip",
    "server_port":8388,
    "local_port":1080,
    "password":"my password",
    "timeout":600,
    "method":"table"
}
```


I tend to use `aes-128-fcb` method.

Then reload the configuration:


```bash
$ service shadowsocks-libev reload
```


Everything will go well!

### nginx and dropbox for my blog hosting


```bash
$ apt-get install nginx vim
$ cd ~ && wget -O - "https://www.dropbox.com/download?plat=lnx.x86_64" | tar xzf -
$ ~/.dropbox-dist/dropboxd
$ wget https://www.dropbox.com/download?dl=packages/dropbox.py -o dropbox.py
$ chmod +x dropbox.py
$ ./dropbox.py start
$ cd ~/Dropbox  # must enter this directory and apply the wildcard. otherwise it didn't take effect
$ ~/dropbox.py exclude add *
$ ~/dropbox.py exclude remove blog
$ cd ~/Dropbox/blog
$ ~/dropbox.py exclude add *
$ ~/dropbox.py exclude remove public
```

Now you can see I just synchronize the directory where the static files of my blog site locate.

The next thing I need to do is modifying the `/etc/nginx/sites-available/default` and changing the `servername` and `root` directory.

But I also encountered a thing I failed to fix - nginx will return a `500` server error. I reviewed the nginx log and found that's the issues of permission.

So I simply changed the run user of nginx from `www-data` to `root` in `/etc/nginx/nginx.conf`. If I have spare time, I would fix the issue again.

### blogging and publishing
Since I use the static blogging tool - Hexo in my local MacBook and write the article using Markdown, I need to some apps to help me manage my publishing.

After using all sorts of markdown apps on Mac OSX, like Typera, Mou, Byword, Ulysses, TextNut, I found they always had some flaws for me - including unsupported markdown syntax, automatically modifying my file like adding some a new blank line arbitrarily, owning its own library management facility. But until I met MWeb, it's suitable to me and satify my personal needs very well.

![Blogging with MWeb](/media/blogging-with-mweb.png)




