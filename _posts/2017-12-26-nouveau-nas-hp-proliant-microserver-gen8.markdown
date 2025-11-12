---
layout: post
title:  "NAS avec un HP Proliant Microserver Gen8"
date:   2017-12-26 20:21:39 +0100
permalink: /nouveau-nas-hp-proliant-microserver-gen8/
categories:
  - homelab
author: Vixepti
image: /assets/2017/12/nas-thumbnail.jpg
tags:
  - NAS
  - hp proliant microserver gen8
  - storage
  - homelab
  - openmediavault
---
Présentation de mon nouveau NAS, basé sur un HP Proliant Microserver Gen8 et OpenMediaVault.<!--more-->

## État des lieux

Actuellement mon NAS est un Dell optiplex 745 légèrement modifié. Sa configuration matériel est la suivante :

  * CPU : [Intel Core 2 Duo E6400](https://ark.intel.com/fr/products/27249/Intel-Core2-Duo-Processor-E6400-2M-Cache-2_13-GHz-1066-MHz-FSB)
  * RAM : 4,5 Go ( 2Go + 2x1Go + 512Mo)
  * NIC : 1x Gigabit intégré à la carte mère
  * Stockage : 
      * Seagate 2To
      * WD 1500Go
      * WD 500Go
      * Seagate 500Go

J'utilise FreeNAS 9.10. Niveau stockage, j'ai un seul volume de 4To. Les 4 disques sont striped, si je perds un disque, je perds toutes mes données. Ces disques durs sont de la récup, ils sont donc dans un état douteux. Étonnement ça fait 2 ans que je suis dans cette situation et je n'ai eu aucun problème.

Évidement je ne peux avoir confiance en ce système, je fais donc des sauvegardes sur des disques durs externes de toutes mes données perso, des photos de familles etc.. je ne sauvegarde bien évidemment pas mes films et séries, je ne considère pas que ce sont des données importantes. Je dispose de 2 sauvegardes de toutes mes données et de 3 sauvegardes des données vraiment importantes (par exemple les documents dont je ne dispose plus de l'original papier).

## Le microserver

Pour mon nouveau NAS, j'ai étudié plusieurs solutions.

1. Utiliser un NAS Synology ou QNAP ? Non, ça ne me convenait pas, leurs OS ne me plaît pas et le fait d'avoir un matériel qui ne peut pas être utilisé pour autre chose dans un futur plus ou moins proche me déplaisait fortement.
2. Utiliser des composants que j'avais déjà ? Non plus, je voulais un système plutôt basse conso, je n'ai pas de composants qui répondent à ce critère (et en plus je viens de tout virer).
3. HP Proliant Microserver Gen8. Je m'étais intéressé un peu à ce serveur, sans être franchement convaincu, puis en me baladant sur Leboncoin, j'ai remarqué que les prix étaient tout à fais abordable. J'en ai discuté avec quelques personnes et j'en suis arrivé à la conclusion que ce serveur était ce qu'il me fallait.

### Pourquoi ?

* Format : Pas gros, c'est plaisant.
* Évolutivité : la carte mère permet d'accueillir une carte PCI, accepte jusqu’à 16go de RAM, possibilité de mettre un Xeon.
* Nombre de disque : 4 disques en baies facilement accessibles, plus 1 port sata qui peut être utilisé pour un lecteur de CD ou un disque supplémentaire (dans mon cas SSD pour l'OS)
* Connectivité : 2 ports gigabit + 1 port HP ilo
* Consommation : Plutôt faible
* Prix : 150€ sur ebay et il était neuf, dans l'emballage d'origine jamais servi.

### Le système d'exploitation

J'ai décidé d'abandonner FreeNAS car je n'ai pas suffisamment de RAM pour gérer correctement le ZFS. J'ai toujours bien apprécié OpenMediaVault, je me suis donc tourné vers cet OS qui est très peu gourmand en ressources.

## Après 2 mois d'utilisation

Ça fait maintenant 2 mois que je l'utilise, tous les jours.. Je n'ai aucun problème de débit, il ne s'est jamais arrêté. Je n'ai jamais eu de problème de surchauffe avec un ventilateur qui tourne à fond, il est vraiment silencieux. J'en suis très content, ça fonctionne à merveille et surtout ça prend beaucoup moins de place qu'avant !

## Vidéo

Enfin pour ceux qui veulent voir à quoi ça ressemble, j'ai fais une petite vidéo vite fait mal fait pour donner une grossière idée de l'installation.

<div class="youtube-embed">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/lN2DqbYeigc" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>