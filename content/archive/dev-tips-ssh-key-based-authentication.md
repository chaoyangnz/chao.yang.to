---
title: 'dev-tips: SSH Key-based Authentication'
toc: true
date: "2015-12-14T21:41:25+00:00"
---

``` bash run on your macbook
ssh-kengen -t rsa
```


Thus it will generate a private-public key pair in `~/.ssh` directory. Copy the public key `~/.ssh/id_rsa.pub` to servers: VPS or github or bitbucket.

In your VPS server, copy the content of `id_rsa.pub` to `~/.ssh/authorized_keys`.

That's OK. Now you can `ssh` to your server without password.




