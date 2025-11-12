---
layout: post
title:  "Writeup - FCSC-2020 - Forensics - Académie de l'investigation - Porte Dérobée"
date:   2020-05-05 19:00:10 +0200
categories: [writeup]
author: Vixepti
image: /assets/2020/05/FCSC.png
tags: [FCSC, forensic, ctf, writeup]
---

Encore un writeup sur la série *Académie de l'investigation*, pour le France CyberSecurity Challenge de 2020, sur le challenge *Porte dérobée*. Le but est de retrouver des infos sur une porte dérobée dans un dump mémoire. <!--more-->

# Énoncé

> Un poste distant est connecté au poste en cours d'analyse via une porte dérobée avec la capacité d'exécuter des commandes.
> 
> - Quel est le numéro de port à l'écoute de cette connexion ?
> - Quelle est l'adresse IP distante connectée au moment du dump ?
> - Quel est l'horodatage de la création du processus en *UTC* de cette porte dérobée ?
>
> Format du flag : *FCSC{port:IP:YYYY-MM-DD HH:MM:SS}*
>
> Le fichier de dump à analyser est identique au challenge *C'est la rentrée*.

# Solution

On cherche une porte dérobée qui est actuellement utilisée. Je cherche donc des connexions *ESTABLISHED* grâce à la commande volatility *linux_netstat* :

{% highlight shell_session %}
[vixepti@computer]$ python2.7 vol.py -f ../dmp.mem --profile=LinuxDebian5404-zipx64 linux_netstat | grep ESTABLISHED
Volatility Foundation Volatility Framework 2.6.1
TCP      10.42.42.131    :36970 116.203.52.118  :  443 ESTABLISHED                   tor/706  
TCP      10.42.42.131    :37252 163.172.182.147 :  443 ESTABLISHED                   tor/706  
TCP      fd:6663:7363:1000:c10b:6374:25f:dc37:36280 fd:6663:7363:1000:55cf:b9c6:f41d:cc24:58014 ESTABLISHED                  ncat/1515 
TCP      10.42.42.131    :47106 216.58.206.226  :  443 ESTABLISHED              chromium/119187
TCP      10.42.42.131    :55224 151.101.121.140 :  443 ESTABLISHED              chromium/119187
TCP      10.42.42.131    :55226 151.101.121.140 :  443 ESTABLISHED              chromium/119187
TCP      10.42.42.131    :53190 104.124.192.89  :  443 ESTABLISHED              chromium/119187
TCP      10.42.42.131    :45652 35.190.72.21    :  443 ESTABLISHED              chromium/119187
TCP      10.42.42.131    :47102 216.58.206.226  :  443 ESTABLISHED              chromium/119187
TCP      10.42.42.131    :47104 216.58.206.226  :  443 ESTABLISHED              chromium/119187
TCP      10.42.42.131    :38186 216.58.213.142  :  443 ESTABLISHED              chromium/119187
TCP      10.42.42.131    :47100 216.58.206.226  :  443 ESTABLISHED              chromium/119187
TCP      10.42.42.131    :50612 104.93.255.199  :  443 ESTABLISHED              chromium/119187
TCP      10.42.42.131    :58772 185.199.111.154 :  443 ESTABLISHED              chromium/119187
TCP      10.42.42.131    :38184 216.58.213.142  :  443 ESTABLISHED              chromium/119187
TCP      10.42.42.131    :57000 10.42.42.134    :   22 ESTABLISHED                   ssh/119468
TCP      fd:6663:7363:1000:c10b:6374:25f:dc37:36280 fd:6663:7363:1000:55cf:b9c6:f41d:cc24:58014 ESTABLISHED                    sh/119511
TCP      127.0.0.1       :38498 127.0.0.1       :34243 ESTABLISHED                   cli/119514
TCP      127.0.0.1       :34243 127.0.0.1       :38498 ESTABLISHED                   cli/119514
TCP      10.42.42.131    :51858 10.42.42.128    :  445 ESTABLISHED             smbclient/119577
{% endhighlight %}

Je procède par élimination :
- tor : Quelqu'un est sûrement en train de lire des théories du complot sur le COVID-19.
- chromium : Le navigateur internet.. 
- ssh : Certainement utilisé pour se connecter à la machine par l'utilisateur Lesage.
- cli : Utilise l'adresse 127.0.0.1, ce serait étonnant que la backdoor soit ici.
- smbclient : Utilisé pour accéder à un répertoire partagé sur le réseau, j'avais vu je ne sais plus où en analysant le dump la commande pour monter ce répertoire qui semblait tout à fait légitime.

En fait ce qui me saute vraiment aux yeux c'est le ncat. Nous avons donc certainement :
- **Port à l'écoute** : 36280
- **Adresse IP distante** : fd:6663:7363:1000:55cf:b9c6:f41d:cc24

Et pour retrouver l'horodatage, j'utilise la commande volatility *linux_pslist* en filtrant avec *grep* sur le PID qui m'intéresse, celui de ncat (1515) :
{% highlight shell_session %}
[vixepti@computer]$ python2.7 vol.py -f ../dmp.mem --profile=LinuxDebian5404-zipx64 linux_pslist | grep 1515
Volatility Foundation Volatility Framework 2.6.1
0xffff9d72c014be00 ncat                 1515            1513            1001            1001   0x000000003e3d0000 2020-03-26 23:24:20 UTC+0000
0xffff9d72c5d50000 sh                   119511          1515            1001            1001   0x00000000128ac000 2020-03-26 23:32:36 UTC+0000
{% endhighlight %}

- **Horodatage :** 2020-03-26 23:24:20

# Flag

Le flag est donc : ***FCSC{36280:fd:6663:7363:1000:55cf:b9c6:f41d:cc24:2020-03-26 23:24:20}***