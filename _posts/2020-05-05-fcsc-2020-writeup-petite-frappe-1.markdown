---
layout: post
title:  "Writeup - FCSC-2020 - Intro - Petite Frappe 1"
date:   2020-05-05 19:00:04 +0200
categories: [writeup]
author: Vixepti
image: /assets/2020/05/FCSC.png
tags: [FCSC, ctf, writeup, forensic]
---

J'ai pas mal hésité à publier ce writeup ainsi que celui pour le challenge [Petite frappe 2](/writeup/2020/05/05/fcsc-2020-writeup-petite-frappe-2.html), parce que j'ai flag vraiment salement. Mais finalement je publie avec pour objectif d'apporter une amélioration dans les prochains jours pour faire ça plus proprement. Ça soulagera ma conscience.<!--more-->

# Énoncé

> Lors de l’investigation d’un poste GNU/Linux, vous analysez un fichier qui semble être généré par un programme d’enregistrement de frappes de clavier (enregistrement de l’activité de chaque touche utilisée).
>
> Retrouvez ce qui a bien pu être écrit par l’utilisateur de ce poste à l’aide de ce fichier !
>
> Format du flag : Pour valider ce challenge, vous devez insérer le contenu de ce qui a été écrit sur le clavier de ce poste dans la balise suivante *FCSC{xxxxxx}* puis soumettre votre réponse.

# Solution

J'ai relevé chaque lettre de chaque *EV_KEY*. Exemple :

> Event: time 1584656705.424839, -------------- SYN_REPORT ------------
>
> Event: time 1584656706.404214, type 4 (EV_MSC), code 4 (MSC_SCAN), value 16
>
> Event: time 1584656706.404214, type 1 (EV_KEY), code 22 (**KEY_U**), value 1
>
> Event: time 1584656706.404214, -------------- SYN_REPORT ------------
>
> Event: time 1584656706.508350, type 4 (EV_MSC), code 4 (MSC_SCAN), value 16
>
> Event: time 1584656706.508350, type 1 (EV_KEY), code 22 (**KEY_U**), value 0
>
> Event: time 1584656706.508350, -------------- SYN_REPORT ------------
>
> Event: time 1584656706.674591, type 4 (EV_MSC), code 4 (MSC_SCAN), value 31
>
> Event: time 1584656706.674591, type 1 (EV_KEY), code 49 (**KEY_N**), value 1

Ça donne : **UUN**

# Flag

Le flag est donc : ***FCSC{UNEGENTILLEINTRODUCTION}***

# Edit

Bon du coup, comme promis j'ai rapidement repris ce challenge plus proprement. J'utilise donc la commande **awk** :

{% highlight shell_session %}
[vixepti@computer]$ cat petite_frappe_1.txt | grep 'EV_KEY' | awk '{if(NR % 2) print substr($9,6,1)}' ORS=''
UNEGENTILLEINTRODDCTION
{% endhighlight %}

Et pour mieux comprendre, je vais détailler :

- J'affiche le contenu du fichier fournit :

{% highlight shell_session %}
cat petite_frappe_1.txt
{% endhighlight %}

- Je filtre sur la chaîne de caractères *EV_KEY* :

{% highlight shell_session %}
| grep 'EV_KEY'
{% endhighlight %}

- J'affiche uniquement une ligne sur deux (sinon je me retrouve avec chaque caractère en double) :

{% highlight shell_session %}
| awk '{if(NR % 2)
{% endhighlight %}

- J'affiche le 6<sup>ème</sup> caractère de la 9<sup>ème</sup> colonne (celle qui contient la touche clavier : KEY_?) :

{% highlight shell_session %}
print substr($9,6,1)}'
{% endhighlight %}

- Et enfin, je supprime l'*output record separator* (par défaut : \n) pour avoir tous les caractères sur une seule ligne :

{% highlight shell_session %}
ORS=''
{% endhighlight %}

C'était finalement pas si compliqué, j'aurai même gagné du temps je pense...