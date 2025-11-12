---
layout: post
title:  "Writeup - FCSC-2020 - Forensics - Find Me"
date:   2020-05-05 19:00:07 +0200
categories: [writeup]
author: Vixepti
image: /assets/2020/05/FCSC.png
tags: [FCSC, ctf, writeup, forensic]
---

*Find Me* est une des épreuves de forensic du *France Cybersecurity Challenge 2020* qui m'a apporté le plus de satisfaction. En effet ayant suivi la formation gratuite de 8h sur autopsy (que je ne connaissais pas avant), j'ai pu appliquer ce que j'ai appris sur cette formation.<!--more-->

# Énoncé
> Vous avez accès à un fichier *find_me* qui semble renfermer un secret bien gardé, qui n'existe peut-être même plus. Retrouvez son contenu !

# Solution

Dans un premier temps, je check le type du fichier *find_me* :

{% highlight shell_session %}
[vixepti@computer]$ file find_me 
find_me: Linux rev 1.0 ext4 filesystem data, UUID=9c0d2dc5-184c-496a-ba8e-477309e521d9, volume name "find_me" (needs journal recovery) (extents) (64bit) (large files) (huge files)
{% endhighlight %}

On a donc un système de fichier ext4, que je m'empresse d'ouvrir dans *Autopsy*. Une fois le scan terminé, je remarque des fichiers qui ont été supprimés.

- Celui qui attire le plus mon attention est *unlock_me*. C'est un fichier qui semble chiffré avec [LUKS](https://en.wikipedia.org/wiki/Linux_Unified_Key_Setup).

{:refdef: style="text-align: center;"}
![Find Me - unlock_me](/assets/2020/05/findme-unlockme.png)
{: refdef}

- Bien évidemment le fichier *pass.b64* ne donne pas la clé pour déchiffrer, mais elle donne une indication :

{:refdef: style="text-align: center;"}
![Find Me - pass.b64](/assets/2020/05/findme-passb64.png)
{: refdef}

- Je rassemble donc tous les fichiers *partXX* et j'obtiens :

> TWYtOVkyb01OWm5IWEtzak04cThuUlRUOHgzVWRZ

Ce qui donne *Mf-9Y2oMNZnHXKsjM8q8nRTT8x3UdY* une fois décodé depuis base64.

- Puis j'ai un peu galérer pour déchiffrer mais finalement :

{% highlight shell_session %}
[vixepti@computer]$ sudo cryptsetup open --type luks --key-file=keyfile --cipher=aes-xts-plain64 unlock_me unlockedfile
[vixepti@computer]$ sudo mount /dev/mapper/unlockedfile mount/
[vixepti@computer]$ cd mount/
[vixepti@computer]$ ls -liah
total 4,0K
    271 drwxr-xr-x 2 root    root      92  1 avril 21:54 .
1892645 drwxr-xr-x 7 vixepti vixepti 4,0K 25 avril 17:45 ..
    272 -r-------- 1 root    root      70  1 avril 21:54 .you_found_me
[vixepti@computer]$ cat .you_found_me
[vixepti@computer]$ sudo cat .you_found_me 
FCSC{750322d61518672328c856ff72fac0a80220835b9864f60451c771ce6f9aeca1}[vixepti@microwave mount]$ 
{% endhighlight %}


# Flag

La flag est donc : ***FCSC{750322d61518672328c856ff72fac0a80220835b9864f60451c771ce6f9aeca1}***
