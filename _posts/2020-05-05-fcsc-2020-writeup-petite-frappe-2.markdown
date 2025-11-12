---
layout: post
title:  "Writeup - FCSC-2020 - Forensics - Petite Frappe 2"
date:   2020-05-05 19:00:06 +0200
categories: [writeup]
author: Vixepti
image: /assets/2020/05/FCSC.png
tags: [FCSC, ctf, writeup, forensic]
---

Comme pour le [Petite frappe 1](/writeup/2020/05/05/fcsc-2020-writeup-petite-frappe-1.html), j'ai flag salement. Je vais éditer rapidement avec une amélioration dans les prochains jours...<!--more-->

# Énoncé

> Lors de l’investigation d’un poste GNU/Linux, vous analysez un nouveau fichier qui semble être généré par un programme d’enregistrement de frappes de clavier. Retrouvez ce qui a bien pu être écrit par l’utilisateur de ce poste à l’aide de ce fichier !
>
> Format du flag : *FCSC{xxx}*, où *xxx* est la chaîne que vous aurez trouvée.

# Solution

Bon comme je disais en intro, c'est vraiment dégueulasse, mais j'ai récupéré chaque valeur en face d'un *key press* :

> 46 46 24 65 24 65 39 32 46 30 28 31 32 57 65 24 55 26 54 65 54 65 53 31 57 33 30 28 65 57 26 65 26 65 39 26 26 47 56 46 26 65 26 65 33 24 39 65 39 65 39 30 33 26 27 65 33 27 24 28 31 38 30 26 65 24 65 24 65 40 26 40 26 54 32 40 26 27 62 59 59 62 65 46 26 65 26 65 41 46 24 42 65 26 39 28 65 30 57 17 54 46 24 55 31 26 27 17 24 25 26 27 28 29 17 26 57 17 55 24 30 28 17 40 26 30 53


Puis j'ai comparé avec les [keycode linux](https://gist.github.com/rickyzhang82/8581a762c9f9fc6ddb8390872552c250) :

> lla a solution avec c xinput ne e seemble e pas s super pratique a a dedecoder62mm62 le e flag est un_clavier_azerty_en_vaut_deux

# Flag

Le flag est donc : ***FCSC{un_clavier_azerty_en_vaut_deux}***

# Edit

En fait, la solution propre était d'utiliser le script pour décoder présent sur : [blog.rom1v.com](https://blog.rom1v.com/2011/11/keylogger-sous-gnulinux-enregistrer-les-touches-tapees-au-clavier/) :

{% highlight python %}
#!/usr/bin/env python
# -*- coding: UTF-8 -*-
import re, sys
from subprocess import *

def get_keymap():
    keymap = {}
    table = Popen(['xmodmap', '-pke'], stdout=PIPE).stdout
    for line in table:
        m = re.match('keycode +(\d+) = (.+)', line.decode())
        if m and m.groups()[1]:
            keymap[m.groups()[0]] = m.groups()[1].split()[0]
    return keymap

if __name__ == '__main__':
    keymap = get_keymap();
    for line in sys.stdin:
        m = re.match('key press +(\d+)', line.decode())
        if m:
            keycode = m.groups()[0]
            if keycode in keymap:
                print keymap[keycode],
            else:
                print '?' + keycode,
{% endhighlight %}

En excécutant ce script sur le fichier text fournit :

{% highlight shell_session %}
[vixepti@microwave]$ cat petite_frappe_2.txt | python2.7 xinput-decoder.py 
l a space s o l u t i o n space a v e c space x i n p u t space n e space s e m b l e space p a s space s u p e r space p r a t i q u e space a space d e c o d e r Shift_R semicolon space l e space f l a g space e s t space u n underscore c l a v i e r underscore a z e r t y underscore e n underscore v a u t underscore d e u x
{% endhighlight %}