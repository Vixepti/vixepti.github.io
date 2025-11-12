---
layout: post
title:  "Contrôler un relais en bluetooth avec un Raspberry Pi"
date:   2020-02-02 23:55:00 +0100
categories: [smarthome]
author: Vixepti
image: /assets/2020/02/thumbnail-rpi-relay-bt.png
tags: [raspberry pi, arduino, bluetooth]
---
Controler un relais depuis un Raspberry Pi, en utilisant le bluetooth. C'est ce que nous allons rapidement voir dans cet article.<!--more-->

## Matériel requis

* Raspberry Pi (peu importe la version)
* Arduino (peu importe le modèle)
* Module bluetooth HC-06
* Dongle USB Bluetooth
* Relais

Les programmes dont on va avoir besoin sont sur mon github : [📁 Par ici.](https://github.com/Vixepti/RPi-Arduino-bluetooth-automation)

## Vidéo

La réalisation de ce mini projet en vidéo :

<div class="youtube-embed">
    <iframe width="560" height="315" src="https://www.youtube.com/embed/qIX_xFcow0U" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>
<br>

## Raspberry Pi

Le raspberry pi doit être installé avec un linux (j'utilise raspbian). Je ne détaillerai pas l'installation dans ce tuto.

Il est préférable qu'il ait une adresse IP fixe.

L'activation du serveur SSH est vivement conseillé pour pouvoir copier/coller les commandes.

Il faut que le dongle Bluetooth soit branché.

### Installation des logiciels

On va avoir besoin de quelques logiciels, on les installes donc :

{% highlight shell_session %}
sudo apt update && apt upgrade -y
sudo apt install bluetooth bluez bluez-utils apache2 php7.1
{% endhighlight %}

## Montage de l'arduino

L'arduino doit être monté selon le schéma suivant :

{:refdef: style="text-align: center;"}
[![Schéma branchement arduino bluetooth](/assets/2020/02/arduino-schema-bluetooth.png)](/assets/2020/02/arduino-schema-bluetooth.png)
{: refdef}

### Paramétrage du module HC-06

Il faut paramétrer le module HC-06 pour changer son nom et le code PIN de connexion. Pour cela, il faut téléverser le programme suivant sur l'arduino :

{% highlight cpp %}
#include <SoftwareSerial.h>

SoftwareSerial hc06(2,3);

void setup(){
  //Initialize Serial Monitor
  Serial.begin(9600);
  Serial.println("ENTER AT Commands:");
  //Initialize Bluetooth Serial Port
  hc06.begin(9600);
}

void loop(){
  //Write data from HC06 to Serial Monitor
  if (hc06.available()){
    Serial.write(hc06.read());
  }
  
  //Write from Serial Monitor to HC06
  if (Serial.available()){
    hc06.write(Serial.read());
  }  
}
{% endhighlight %}

Puis se connecter au moniteur série pour lui envoyer les commandes AT suivante :

* *AT+NAMEnouveau_nom :* Changement du nom visible en bluetooth.
* *AT+PINxxxx :* Changement du code PIN de connexion.

Ici j'ai fais :

{% highlight shell_session %}
AT+NAMEhc-06
AT+PIN1234
{% endhighlight %}

## Création de la liaison bluetooth

L'appairage avec le module bluetooth ne se fait qu'une fois. Au redémarrage, la liaison devrait persister.

* On démarre le service bluetooth
{% highlight shell_session %}
systemctl start bluetooth
{% endhighlight %}

* On fait l'appairage avec **bluetoothctl**:
{% highlight shell_session %}
    power on
    agent on
    scan on
    ... wait ...
    scan off
    pair <addr>
{% endhighlight %}

* On créé la liaison série :
{% highlight shell_session %}
rfcomm bind 0 <addr>
{% endhighlight %}

Ici, j'utilise **0** car c'est la première connexion série bluetooth que je paramètre. Si on en ajoute une deuxième on notera **rfcomm bind 1 \<addr\>** et on utilisera ensuite **/dev/rfcomm1**.

Exemple avec mon appairage :

{% highlight shell_session %}
root@raspberrypi:~# bluetoothctl
Agent registered
[bluetooth]# power on
Changing power on succeeded
[bluetooth]# agent on
Agent is already registered
[bluetooth]# scan on
Discovery started
[CHG] Controller 00:19:0E:0B:F8:90 Discovering: yes
[NEW] Device 20:15:10:09:66:56 20-15-10-09-66-56
[CHG] Device 20:15:10:09:66:56 LegacyPairing: no
[CHG] Device 20:15:10:09:66:56 Name: HC06.module

[CHG] Device 20:15:10:09:66:56 Alias: HC06.module

[bluetooth]# scan off
[CHG] Device 20:15:10:09:66:56 RSSI is nil
[CHG] Controller 00:19:0E:0B:F8:90 Discovering: no
Discovery stopped
[bluetooth]# pair 20:15:10:09:66:56
Attempting to pair with 20:15:10:09:66:56
[HC06.module
[CHG] Device 20:15:10:09:66:56 Connected: yes
[HC06.module
Request PIN code
[HC06.module
[agent] Enter PIN code: 1234
[HC06.module
[CHG] Device 20:15:10:09:66:56 UUIDs: 00001101-0000-1000-8000-00805f9b34fb
[HC06.module
[CHG] Device 20:15:10:09:66:56 ServicesResolved: yes
[HC06.module
[CHG] Device 20:15:10:09:66:56 Paired: yes
[HC06.module
Pairing successful
[HC06.module
[CHG] Device 20:15:10:09:66:56 ServicesResolved: no
[HC06.module
[CHG] Device 20:15:10:09:66:56 Connected: no
[bluetooth]# exit
root@raspberrypi:~# rfcomm bind 0 20:15:10:09:66:56
{% endhighlight %}

## Paramétrage du serveur web

L'interface de pilotage est minimaliste, j'ai réutilisé un code que j'avais écrit il y a 6 ~ 7 ans. *L'intéret ici est simplement de démontrer le fonctionenment d'une liaison série à travers une communication bluetooth.*

* On créé un fichier /var/www/html/index.php :

{% highlight shell_session %}
rm /var/www/html/index.html
nano /var/www/html/index.php
{% endhighlight %}

* Et on colle le contenu de [ce code](https://github.com/Vixepti/RPi-Arduino-bluetooth-automation/blob/master/www/index.php).

* Il est ensuite nécessaire d'ajouter l'utilisateur **www-data** (qui gère le serveur web) au groupe **dialout** (qui gère les ports série) pour que notre application puisse communiquer via */dev/rfcomm0* :

{% highlight shell_session %}
sudo adduser www-data dialout
sudo systemctl restart apache2.service
{% endhighlight %}

## Test

A présent, tout devrait être opérationnel. On peut donc se connecter à notre application web dans un navigateur en entrant l'adresse IP du Raspberry PI :

{:refdef: style="text-align: center;"}
[![Application web](/assets/2020/02/webapp-pi-bt.png)](/assets/2020/02/webapp-pi-bt.png)
{: refdef}

Si on clique sur le boutton allumer ou éteindre, la liaison avec l'arduino doit se faire et le relais doit agir en fonction. Si la communication ne se fait pas, un message *pas de comm avec l'arduino* devrait apparaître. Enfin, si le message d'erreur n'apparait pas mais que le relais ne réagit pas, il peut s'agir d'un problème de branchement de ce dernier, ou d'un problème de paramétrage du message envoyé ("h" ou "l").

J'espère que cet article vous aura plu. La méthode est simpliste et ce n'est certainement pas la meilleure façon de faire, mais ça peut donner des idées. A +