---
layout: post
title:  "Configurer un onduleur avec nut"
date:   2018-02-09 20:21:39 +0100
categories:
  - adminsys
author: Vixepti
image: /assets/2018/02/nut-onduleur-thumbnail.png
tags:
  - onduleur
  - nut
---
Cet article montre la procédure à suivre pour configurer le logiciel de gestion d'onduleur "NUT".<!--more-->

## Matériel utilisé

* Le materiel qui a été utilisé pour cette documentation est un onduleur Eaton 9SX5000
* Le port USB de celui-ci semblait compliqué à utiliser, il n’était pas détecté par l’ordinateur sur lequel l’onduleur était branché. Le port série a donc été utilisé à la place.
* Le système utilisé est Debian.

## Configuration du maître

### Configuration de l’onduleur

Installation de nut :

{% highlight shell_session %}
apt-get install nut
{% endhighlight %}


Il faut ensuite savoir quel est le driver a utiliser pour l’onduleur dont il est question. On peut par exemple utiliser [ce site](http://networkupstools.org/stable-hcl.html). Dans ce cas, le driver pour cet onduleur (le port série est utilisé, pas l’usb) est **mge-shut**.

La commande suivante permet de lister les drivers installés avec nut :

{% highlight shell_session %}
# ls /lib/nut
al175 bcmxcp bestfcom blazer_ser dummy-ups genericups liebert-esp2 mge-utalk oldmge-shut powerpanel
riello_usb ripplitesu upsmon apcsmart bcmxcp_usb bestfortress blazer_usb etapro isbmex masterguard 
microdowell oneac rhino safenet tripplite_usb usbhid-ups apcsmart-old belkin bestuferrups clone everups
ivtscd etasys nutdrv_atcl_usb optiups richcomm_usb solis upscode2 victronups apcupsd-ups belkinunv
bestups e-outlet gamatronic liebert mge-shut nutdrv_qx powercom riello_ser tripplite
upsd
{% endhighlight %}


Le driver **mge-shut** est bien présent.
Il faut donc créer l’onduleur dans la configuration de nut, dans */etc/nut/ups.conf* :


{% highlight bash %}
[onduleur]
    driver = mge-shut
    port = /dev/ttyS0
{% endhighlight %}

Pour que nut puisse accéder à l’onduleur par le port série, il va falloir créer une règle udev */etc/udev/rules.d/eaton.rules* :

{% highlight bash %}
KERNEL=="ttyS0", GROUP="nut"
{% endhighlight %}

Recharger les règles :

{% highlight bash %}
udevadm trigger 
udevadm control --reload-rules
{% endhighlight %}

Changer le groupe de */dev/ttyS0* :

{% highlight shell_session %}
chgrp nut /dev/ttyS0
{% endhighlight %}

### Paramétrage de nut

Maintenant il faut définir le mode de nut dans */etc/nut/nut.conf* :

{% highlight bash %}
MODE=standalone
{% endhighlight %}

Le maître doit pouvoir contrôler les esclaves. Il doit donc pouvoir écouter le réseau. Pour cela dans */etc/nut/upsd.conf* :

{% highlight bash %}
LISTEN 127.0.0.1 3493
LISTEN 192.168.0.50 3493 #adresse du serveur sur le réseau
{% endhighlight %}

### Configurer les utilisateurs

Nous allons configurer 2 utilisateurs, un utilisateur pour le maître et un pour les esclaves, dans */etc/nut/upsd.users* :

{% highlight bash %}
[admin]
    password = mypass
    actions = SET
    instcmds = ALL
    upsmon master
[slave]
    password = pass 
    upsmon slave
{% endhighlight %}

### Configuration du moniteur

On ajoute cette ligne dans */etc/nut/upsmon.conf* :

{% highlight bash %}
MONITOR onduleur@localhost 1 admin mypass master
{% endhighlight %}

### Configuration de l’action

On souhaite lancer un signal d’extinction 5 minutes après la perte du courant. Il va donc falloir créer une action différé.

Dans */etc/nut/upsmon.conf*

{% highlight bash %}
NOTIFYCMD "/sbin/upssched"
NOTIFYFLAG ONBATT SYSLOG+WALL+EXEC 
NOTIFYFLAG ONLINE SYSLOG+WALL+EXEC
{% endhighlight %}

Puis dans */etc/nut/upssched.conf* :

{% highlight bash %}
CMDSCRIPT /opt/nut/upsalert
PIPEFN /var/lib/nut/upssched/upssched.pipe
LOCKFN /var/lib/nut/upssched/upssched.lock
AT ONBATT * START-TIMER onbatt 30 # Ici, le timer est de 30 secondes
AT ONLINE * CANCEL-TIMER onbatt
{% endhighlight %}

Nut doit pouvoir écrire dans */var/run/upssched* :

{% highlight shell_session %}
mkdir /var/lib/nut/upssched
chown root:nut /var/lib/nut/upssched
chmod g+sw /var/lib/nut/upssched
{% endhighlight %}

### Création du script d’arrêt

Nut doit pouvoir accéder au script que nous allons créer, il faut donc lui donner les bonnes permissions :

{% highlight bash %}
mkdir /opt/nut
touch /opt/nut/upsalert 
chmod -R 750 /opt/nut
chgrp -R nut /opt/nut 
chmod 750 /opt/nut/upsalert
chgrp -R nut /opt/nut/upsalert
{% endhighlight %}

Dans *upsalert*, on met ceci :

{% highlight bash %}
#!/bin/sh
echo "$date >> UPS ALERT, shutting down in a few seconds..." >> /var/log/ups.log
/sbin/upsmon -c fsd
{% endhighlight %}

## Configuration des esclaves

### Debian

Sur l’esclave, on installe nut :

{% highlight shell_session %}
apt install nut
{% endhighlight %}

Puis, on configure le mode a utiliser dans */etc/nut/nut.conf* :

{% highlight bash %}
MODE=netclient
{% endhighlight %}

On configure le moniteur dans */etc/nut/upsmon.conf* :

{% highlight bash %}
MONITOR onduleur@192.168.0.50 1 slave pass slave
{% endhighlight %}

### Windows

Télécharger WinNUT : https://storage.googleapis.com/google-code-archive-downloads/v2/code.google.com/winnut/WinNUT-2.0.0.4a-Installer.exe

Dans WinNUT configuration tool, éditer *upsmon.conf* et ajouter :

{% highlight bash %}
MONITOR onduleur@192.168.0.50 1 slave pass slave
{% endhighlight %}

* Cocher **Install as service**
* Cocher **Automatic startup**
* Cliquer sur **Apply And Start WinNUT**, puis sur **OK**

{:refdef: style="text-align: center;"}
[![WinNUT](/assets/2018/02/winnut.png)](/assets/2018/02/winnut.png)
{: refdef}


### pfSense

* Dans l’interface de pfSense, dans **System → Package Manager → Available Packages → installer NUT**
* Lorsque NUT est installé, dans **Services → UPS → UPS Settings** :
  * **UPS Type :** Remonte NUT Server
  * **UPS Name :** Onduleur
  * **Remote IP address or hostname :** 192.168.0.50
  * **Remote username :** slave
  * **Remote password :** XXXXXX

