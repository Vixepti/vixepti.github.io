---
layout: post
title:  "Writeup - FCSC-2020 - Intro - Le Rat Conteur"
date:   2020-05-05 19:00:01 +0200
categories: [writeup]
author: Vixepti
image: /assets/2020/05/FCSC.png
tags: [FCSC, ctf, writeup, forensic]
---

Mon writeup sur le challenge *Le Rat Conteur* du *France Cybersecurity Challenge 2020*. Il s'agit d'une épreuve d'introduction en cryptographie. Simple donc..<!--more-->

# Énoncé

> Le fichier suivant a été chiffré en AES-128 en mode CTR, avec la clé de 128 bits 00112233445566778899aabbccddeeff et un IV nul.
>
> À vous de le déchiffrer pour obtenir le flag.

# Solution

Il suffit de décoder avec les informations fournies :

{% highlight shell_session %}
openssl enc -aes-128-ctr -d -K 00112233445566778899aabbccddeeff -iv 0 -in flag.jpg.enc -out flag.jpg
{% endhighlight %}

Et on obtient l'image suivante : 

{:refdef: style="text-align: center;"}
![Le Rat Conteur](/assets/2020/05/rat-conteur-flag.jpg)
{: refdef}

# Flag

Le flag est donc : ***FCSC{879C2FEE3B9EFBC651050F881841D209}***