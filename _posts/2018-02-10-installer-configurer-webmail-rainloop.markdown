---
layout: post
title:  "Installer et configurer le webmail RainLoop"
date:   2018-02-10 16:34:50 +0100
permalink: /installer-configurer-webmail-rainloop/
categories: 
  - adminsys
author: Vixepti
image: /assets/2018/02/rainloop-thumbnail.png
tags:
  - client mail
  - mail
  - configuration
  - installation
  - rainloop
  - webmail
---
Rainloop, c'est mon client webmail préféré. Parce qu'il est libre, parce qu'il est joli et parce qu'il fonctionne très bien. Voyons donc comment l'installer et le configurer.

<!--more-->

## Installation
Il faut d'abord avoir un serveur web fonctionnel avec php.

Pour installer rainloop, il faut se rendre dans _/var/www/html_ puis exécuter le script d'installation :

{% highlight shell_session %}
wget -qO- https://repository.rainloop.net/installer.php | php
{% endhighlight %}

Il faut ensuite appliquer les bonnes permissions :

{% highlight shell_session %}
cd /var/www/html/rainloop
find . -type d -exec chmod 755 {} \;
find . -type f -exec chmod 644 {} \;
{% endhighlight %}

et 

{% highlight shell_session %}
chown -R www-data:www-data /var/www/html/rainloop
{% endhighlight %}

## Configuration

Sur [http://IP_Address/rainloop/?admin](http://IP_Address/rainloop/?admin), utilisateur **admin** et mot de passe **12345**. On commence par changer le mot de passe admin en cliquant sur **change** comme nous l'indique le message suivant :

{:refdef: style="text-align: center;"}
[![Rainloop avertissement Password](/assets/2018/02/install-rainloop-etape1.png)](/assets/2018/02/install-rainloop-etape1.png)
{: refdef}

On change le mot de passe : 

{:refdef: style="text-align: center;"}
[![Admin Panel Password](/assets/2018/02/install-rainloop-etape2.png)](/assets/2018/02/install-rainloop-etape2.png)
{: refdef}

### Changer la langue

Dans **General** il suffit de choisir la langue : 

{:refdef: style="text-align: center;"}
[![Admin Panel Language](/assets/2018/02/install-rainloop-etape3.png)](/assets/2018/02/install-rainloop-etape3.png)
{: refdef}

### Ajouter des comptes

Dans l'interface admin, puis dans **Domaines**, on ajoute un domaine (ici chez OVH) :

{:refdef: style="text-align: center;"}
[![Ajout domaine](/assets/2018/02/install-rainloop-etape4.png)](/assets/2018/02/install-rainloop-etape4.png)
{: refdef}

Le domaine étant configuré on peut se déconnecter et se reconnecter avec l'identifient et l'utilisateur souhaité :

{:refdef: style="text-align: center;"}
[![Interface rainloop](/assets/2018/02/install-rainloop-etape5.png)](/assets/2018/02/install-rainloop-etape5.png)
{: refdef}