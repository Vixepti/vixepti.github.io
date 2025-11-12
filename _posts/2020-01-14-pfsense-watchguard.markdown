---
layout: post
title:  "Tuto - Installation PfSense sur un Watchguard Firebox XTM 505"
date:   2020-01-30 16:00:00 +0100
categories: [adminsys, homelab]
author: Vixepti
image: /assets/2020/01/pfsense-watchguard-thumbnail.jpg
tags: [pfsense, watchguard, XTM 5]
---
Installation de l'OS pare-feu PfSense sur un boitier Watchguard Firebox XTM 505.
<!--more-->

## Matériel requis

Pour installer PfSense sur un watchguard, on va avoir besoin de plusieurs choses :
* Un boitier Watchguard Firebox XTM 5 series (obviously)
* Une [CF Card](https://www.amazon.fr/Transcend-Carte-M%C3%A9moire-CompactFlash-TS4GCF133/dp/B000VY7HYM/) (4 Go suffisent largement)
* Un lecteur USB de CF Cards
* Un HDD / SSD 2,5" SATA

## Vidéo

L'installation en vidéo :

<div class="youtube-embed">
    <iframe width="560" height="315" src="https://www.youtube.com/embed/p2xVbr2GF-Y" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>
<br>

## Procédure d'installation

### Création du média bootable

Dans un premier temps, on va télécharger l'image de PfSense adapté à notre installation. Pour ça, on se rends sur [le site officiel](https://www.pfsense.org/download/).

Il faut ensuite définir dans le formulaire prévu à cet effet :

* **Version :** on prends la dernière
* **Architecture :** AMD64 (64-bit)
* **Installer :** USB Memstick Installer
* **Console :** Serial
* **Mirror :** Peu importe

{:refdef: style="text-align: center;"}
[![PfSense Download Form](/assets/2020/01/setup-pfsense-download.PNG)](/assets/2020/01/setup-pfsense-download.PNG)
{: refdef}

Puis on clique sur **télécharger**.

#### Windows

Cette étape n'est pas obligatoire, mais on peu néttoyer la CF Card en écrasant la table de partition :

{% highlight shell_session %}
diskpart
list disk
select disk X
clean
{% endhighlight %}

Enfin, on copie l'image pfsense sur la carte compact flash :

* On peut utiliser [win32diskImager](https://sourceforge.net/projects/win32diskimager/) ou [rufus](https://rufus.ie/).

#### Linux

Écraser la table de partition (toujours facultatif...) :

{% highlight shell_session %}
sudo dd if=/dev/zero of=/dev/sdz bs=1M count=1
{% endhighlight %}

Écrire l'image sur la carte compact flash :

{% highlight shell_session %}
gzip -dc pfSense-memstick-2.4.4-RELEASE-p3-amd64.img.gz | sudo dd of=/dev/sdz bs=1M
{% endhighlight %}

### Préparation du stockage

On va installer PfSense sur un HDD / SSD 2,5" (parce qu'il n'y a pas beaucoup de place dans le boitier). Il est préférable de supprimer toutes les données et partitions stockées dessus, même si l'installeur formatera le disque.

Une fois que le disque est prêt, il faut le placer dans le boitier. L'alimentation du watchguard est équipé d'un câble SATA. Il faut également ajouter un câble SATA (données) et le brancher sur la carte mère :


{:refdef: style="text-align: center; max-width=\"500px\""}
[![Watchguard HDD](/assets/2020/01/watchguard-hdd.jpg)](/assets/2020/01/watchguard-hdd.jpg)
{: refdef}

### Communication

Pour pouvoir communiquer avec le boitier, on va utiliser son port console, sur lequel on va brancher un câble série ***RJ45 - DB9*** (type *cisco*). Pour la liaison avec l'ordinateur, on rajouter un adaptateur ***DB9 - USB***.

Une fois la liaison physique réalisée, on peut ouvrir une console serie avec un logiciel type **Putty** (windows) ou **Minicom** (linux). Les paramètres de connexion sont les suivants :
* **Vitesse :** 115200
* **Bits de données :** 8
* **Bits de parité :** None
* **Bits de stop :** 1

* Pour se connecter avec putty :

{:refdef: style="text-align: center; max-width=\"500px\""}
[![Serial Putty](/assets/2020/01/putty-serial-connection.PNG)](/assets/2020/01/putty-serial-connection.PNG)
{: refdef}

Il faut adapter COM3 en fonction de votre configuration...

* Pour se connecter avec minicom :

{% highlight shell_session %}
minicom -D /dev/ttyUSB0 -R 115200
{% endhighlight %}

Il faut adapter /dev/ttyUSB0 en fonction de votre configuration...

### Installation

Maintenant que l'on a une connexion console ouverte, on peut **démarrer le boitier** pour débuter l'installation.

> La vidéo au début de l'article montre précisément les étapes suivantes.

1. Laisser PfSense démarrer.
2. Choisir le type de console (je laisse *vt100* par défaut, mais pas sur que ce soit la meilleure option).
3. Accepter la note concernant le copyright.
4. Choisir **Install**.
5. Sélectionner le **clavier** (keyboard map) et valider avec **Select**.
6. Choisir la méthode de partitionnement du disque. Personnellement je ne m'embête pas et je chosis **Auto** et valide avec **Ok**.
7. Validation de l'utilisation du disque entier avec **Entire Disk**.
8. Choix du schéma de partition **MBR**.
9. Il faut ensuite sélectionner le disque sur lequel installer. En fonction de la taille, on choisit le HDD / SSD et non pas la CF Card (beaucoup plus petite). Pour ma part cela correspond à **ada1**.

A présent, PfSense devrait être en train de s'installer. A la fin de l'installation, le système propose de passer sur un shell. Je n'ai pas besoin de ce shell, je sélectionne donc **no**, puis **Reboot**.

Lorsque le système s'éteint et qu'il s'apprète à redémarrer, il faut forcer le boitier à s'éteindre en appuyant sur le bouton.

L'installation étant terminée, on peut retirer la carte compact flash car nous n'en avons plus besoin.

Le prochain démarrage du boitier se fera sur PfSense, installé sur le disque dur. Il restera simplement à configurer les cartes réseaux, l'interfaces web et paraméter le système selon ses propres besoins.

Have fun !