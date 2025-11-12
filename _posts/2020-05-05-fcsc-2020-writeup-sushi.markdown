---
layout: post
title:  "Writeup - FCSC-2020 - Intro - SuSHi"
date:   2020-05-05 19:00:03 +0200
categories: [writeup]
author: Vixepti
image: /assets/2020/05/FCSC.png
tags: [FCSC, ctf, writeup]
---

Writeup sur l'éprevue SuSHI du *France Cybersecurity Challenge 2020*. Épreuve satisfaisante mais franchement trop simple.<!--more-->

# Énoncé
> Connectez-vous à cette machine en SSH avec les identifiants mentionnés, et trouvez le flag.
>
> - Adresse : *challenges2.france-cybersecurity-challenge.fr*
> - Port : *6000*
> - Utilisateur : *ctf*
> - Mot de passe : *ctf*

# Solution

- Je me connecte à la machine :

{% highlight shell_session %}
[vixepti@computer]$ ssh ctf@challenges2.france-cybersecurity-challenge.fr -p 6000
ctf@challenges2.france-cybersecurity-challenge.fr's password: 
 __    __            _                 __           _     _   ___ 
/ / /\ \ \__ _ _ __ | |_      __ _    / _\_   _ ___| |__ (_) / _ \
\ \/  \/ / _` | '_ \| __|    / _` |   \ \| | | / __| '_ \| | \// /
 \  /\  / (_| | | | | |_    | (_| |   _\ \ |_| \__ \ | | | |   \/ 
  \/  \/ \__,_|_| |_|\__|    \__,_|   \__/\__,_|___/_| |_|_|   () 
{% endhighlight %}

- Puis je cherche le fichier :

{% highlight shell_session %}
ctf@SuSHi:~$ ls -liah
total 24K
1183438 drwxr-xr-x 1 ctf-admin ctf 4.0K Apr 22 15:32 .
1183437 drwxr-xr-x 1 ctf-admin ctf 4.0K Apr 22 15:31 ..
1183439 -rw-r--r-- 1 ctf-admin ctf  220 May 15  2017 .bash_logout
1183440 -rw-r--r-- 1 ctf-admin ctf 3.5K May 15  2017 .bashrc
1183441 -r--r--r-- 1 ctf-admin ctf   71 Apr 22 15:31 .flag
1183442 -rw-r--r-- 1 ctf-admin ctf  675 May 15  2017 .profile
{% endhighlight %}

- Bon bah cool, j'ai pas cherché longtemps. Je l'affiche :

{% highlight shell_session %}
ctf@SuSHi:~$ cat .flag 
FCSC{ca10e42620c4e3be1b9d63eb31c9e8ffe60ea788d3f4a8ae4abeac3dccdf5b21}
{% endhighlight %}

# Flag

Le flag est donc : ***FCSC{ca10e42620c4e3be1b9d63eb31c9e8ffe60ea788d3f4a8ae4abeac3dccdf5b21}***