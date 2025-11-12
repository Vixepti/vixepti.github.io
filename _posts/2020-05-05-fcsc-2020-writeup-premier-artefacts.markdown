---
layout: post
title:  "Writeup - FCSC-2020 - Forensics - Académie de l'investigation - Premier artéfacts"
date:   2020-05-05 19:00:09 +0200
categories: [writeup]
author: Vixepti
image: /assets/2020/05/FCSC.png
tags: [FCSC, forensic, ctf, writeup]
---

Toujours dans la série *Académie de l'investigation*, ceci est mon writeup pour le France CyberSecurity Challenge de 2020, sur le challenge *Premier Artéfacts*. Le but est de retrouver des infos sur un processus, une commande et une connexion réseau. <!--more-->

# Énoncé

> Pour avancer dans l'analyse, vous devez retrouver :
>
> - Le nom de processus ayant le PID 1254.
> - La commande exacte qui a été exécutée le 2020-03-26 23:29:19 UTC.
> - Le nombre d'IP-DST unique en communications TCP établies (état ESTABLISHED) lors du dump.
> 
> Format du flag : *FCSC{nom_du_processus:une_commande:n}*
>
> Le fichier de dump à analyser est identique au challenge *C'est la rentrée*.

# Solution

Le premier challenge de la série m'a permis d'obtenir les informations nécessaire à la création d'un profile volatility. Je peux donc maintenant utiliser ce logiciel.

## Processus

La liste des processus peut être obtenu grâce à *linux_psscan*. Puis, grâce à *grep* je peux filtrer selon le PID qui nous intéresse :

{% highlight shell_session %}
[vixepti@computer]$ python2.7 vol.py -f ../dmp.mem --profile=LinuxDebian5404-zipx64 linux_psscan |grep 1254
Volatility Foundation Volatility Framework 2.6.1
0x000000003fdccd80 pool-xfconfd         1254            -               -1              -1     0x0fd08ee88ee08ec0 -
{% endhighlight %}

Le nom du processus est donc : ***pool-xfconfd***

## Commande

La commande ayant été executé le *2020-03-26 23:29:19* peut être trouvé grâce à la commande *linux_bash*, qui affiche l'historique des commandes. Je filtre sur la date avec *grep* pour avoir uniquement la commande qui nous intéresse.

{% highlight shell_session %}
[vixepti@computer]$ python2.7 vol.py -f ../dmp.mem --profile=LinuxDebian5404-zipx64 linux_bash | grep "2020-03-26 23:29:19"
Volatility Foundation Volatility Framework 2.6.1
    1523 bash                 2020-03-26 23:29:19 UTC+0000   nmap -sS -sV 10.42.42.0/24
{% endhighlight %}

La commande est donc : ***nmap -sS -sV 10.42.42.0/24***

## Nombre d'IP-DST

Pour obtenir la liste des connexions j'utilise la commande *linux_netstat* que je filtre avec *grep* sur les connexions ayant un état *ESTABLISHED* uniquement :

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

Sauf qu'ici, nous avons des doublons d'adresse de destination. Il n'y a pas beaucoup de résultats, je peux donc supprimer des lignes ou la destination est la même à la main, ce qui donne :

{% highlight shell_session %}
TCP      10.42.42.131    :36970 116.203.52.118  :  443 ESTABLISHED                   tor/706  
TCP      10.42.42.131    :37252 163.172.182.147 :  443 ESTABLISHED                   tor/706  
TCP      fd:6663:7363:1000:c10b:6374:25f:dc37:36280 fd:6663:7363:1000:55cf:b9c6:f41d:cc24:58014 ESTABLISHED                  ncat/1515 
TCP      10.42.42.131    :47106 216.58.206.226  :  443 ESTABLISHED              chromium/119187
TCP      10.42.42.131    :55224 151.101.121.140 :  443 ESTABLISHED              chromium/119187
TCP      10.42.42.131    :53190 104.124.192.89  :  443 ESTABLISHED              chromium/119187
TCP      10.42.42.131    :45652 35.190.72.21    :  443 ESTABLISHED              chromium/119187
TCP      10.42.42.131    :38186 216.58.213.142  :  443 ESTABLISHED              chromium/119187
TCP      10.42.42.131    :50612 104.93.255.199  :  443 ESTABLISHED              chromium/119187
TCP      10.42.42.131    :58772 185.199.111.154 :  443 ESTABLISHED              chromium/119187
TCP      10.42.42.131    :57000 10.42.42.134    :   22 ESTABLISHED                   ssh/119468
TCP      127.0.0.1       :38498 127.0.0.1       :34243 ESTABLISHED                   cli/119514
TCP      10.42.42.131    :51858 10.42.42.128    :  445 ESTABLISHED             smbclient/119577
{% endhighlight %}

Le nombre de connexion est donc : ***13***

# Flag

Le flag est donc : ***FCSC{pool-xfconfd:nmap -sS -sV 10.42.42.0/24:13}***