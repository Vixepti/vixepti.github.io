---
layout: post
title:  "Writeup - FCSC-2020 - Forensics - Académie de l'investigation - C'est la rentrée"
date:   2020-05-05 19:00:08 +0200
categories: writeup
author: Vixepti
image: /assets/2020/05/FCSC.png
tags: [FCSC, forensic, ctf, writeup]
---

Voici mon writeup sur la première épreuve de forensic de la série *Académie de l'investigation* pour France CyberSecurity Challenge de 2020. Le but est de retrouver des infos sur l'utilisateur et la machine dont un dump mémoire a été fournit. <!--more-->

# Énoncé

> Bienvenue à l'académie de l'investigation numérique ! Votre mission, valider un maximum d'étapes de cette série afin de démontrer votre dextérité en analyse mémoire GNU/Linux.
> 
> Première étape : retrouvez le **HOSTNAME**, le nom de l'**utilisateur** authentifié lors du dump et la **version** de Linux sur lequel le dump a été fait.
> 
> Format du flag : *FCSC{hostname:user:x.x.x-x-amdxx}*

# Solution

Nous avons donc un dump de la mémoire d'une machine linux, on peut donc utiliser volatility pour l'analyser. En revanche, nous ne savons pas quel profile nous devons créer pour analyser ce dump. J'utilise donc dans un premier temps la commande *Strings* qui va me permettre de déterminer la distribution et la version du noyau.

## Obtention d'informations avec Strings

- Je créé un fichier text avec toutes les chaines de caractères du dump :
{% highlight shell_session %}
strings dmp.mem > dmp.txt
{% endhighlight %}

### Version
- Je cherche avec *grep*, par exemple :

{% highlight shell_session %}
cat dmp.txt | grep amd64
{% endhighlight %}

- Cela me permet de retrouver la version du noyau :

> 5.4.0-4-amd64

- Ainsi que la distribution linux utilisé :

> 0-4-amd64 **Debian** GNU/Linux **bullseye/sid**

### Hostname
Et puisque j'ai mon fichier text j'en profite pour voir si je ne peux pas trouver d'autres informations.

Je repère le hostname :

{% highlight shell_session %}
[vixepti@computer]$ cat dmp.txt | grep HOSTNAME
_HOSTNAME=challenge.fcsc
{% endhighlight %}

### Utilisateur

Enfin pour le nom d'utilisateur :

{% highlight shell_session %}
[vixepti@computer]$ cat dmp.txt | grep /home/
/home/Lesage/.local/share
/home/Lesage/.icons/gnome
/home/Lesage/.mozilla/firefox/peyjyk3f.default-esr/logins.json
{% endhighlight %}

# Flag

On a donc :
- Hostname : challenge.fcsc
- Username : Lesage
- Version : 5.4.0-4-amd64

Le flag est donc : ***FCSC{challenge.fcsc:Lesage:5.4.0-4-amd64}***

## Création du profile volatility

Grâce aux informations récupérées avec strings, je créé un profile volatility pour Debian bullseye/sid 5.4.0-4-amd64. Ce profile me permettra de réaliser les prochains challenge de cette série.