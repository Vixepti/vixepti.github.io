---
layout: post
title:  "SSH et SFTP sur Windows 10"
date:   2018-11-28 23:07:42 +0100
permalink: /ssh-et-sftp-sur-windows-10/
categories:
  - adminsys
author: Vixepti
image: /assets/2018/11/ssh_windows_10_thumbnail.png
tags:
  - openssh
  - sftp
  - ssh
  - windows 10
---
J'ai découvert il y a peu que microsoft à intégré un client SSH nativement dans windows 10. C'est une très bonne chose selon moi et puisque cet outil existe, autant l'utiliser. Pour ce faire il faut tout d'abord installer une fonctionnalité.

<!--more-->

## Installation du client OpenSSH
Il est possible d'installer le client OpenSSH de deux façon différentes. La première, graphiquement et la seconde en ligne de commande powershell.  

### Avec l'interface graphique

1. Se rendre dans les **Paramètres Windows** 10.
2. Dans la section **Applications et fonctionnalités**.
3. Cliquer sur **Gérer les fonctionnalités facultatives**.
4. Puis **Ajouter une fonctionnalité**.
5. Dans la liste on retrouve **Client OpenSSH**, cliquer sur **Installer**.

### En powershell

1. Lancer **Powershell** en tant qu'administrateur.
2. Exécuter la ligne suivante :

{% highlight powershell %}
Get-WindowsCapability -Online | ? Name -like 'OpenSSH*'
{% endhighlight %}

Le résultat attendu est le suivant :

{:refdef: style="text-align: center;"}
[![Résultat commande powershell](/assets/2018/11/01-SSH-SFTP_Windows_10.png)](/assets/2018/11/01-SSH-SFTP_Windows_10.png)
{: refdef}

## Utilisation du client SSH

Une fois l'installation terminée, il est possible d'utiliser le client SSH avec windows 10 de la même façon qu'avec linux. On lance une invite de commande (CMD) et on exécute ssh :

{:refdef: style="text-align: center;"}
[![Exe SSH sur CMD](/assets/2018/11/02-SSH-SFTP_Windows_10.png)](/assets/2018/11/02-SSH-SFTP_Windows_10.png)
{: refdef}

On obtient l'aide, l'installation à donc bien fonctionné, profitons-en pour regarder les paramètres possibles.

Je peux maintenant me connecter à une machine distante en ssh directement dans mon invite avec la commande habituelle sous linux :

{% highlight shell_session %}
ssh user@host
{% endhighlight %}

{:refdef: style="text-align: center;"}
[![SSH Connecté](/assets/2018/11/03-SSH-SFTP_Windows_10.png)](/assets/2018/11/03-SSH-SFTP_Windows_10.png)
{: refdef}

Pour utiliser un port différent on peut utiliser l'option -p :

{% highlight shell_session %}
ssh user@host -p port
{% endhighlight %}

## Utilisation de SFTP

On peut également utiliser SFTP nativement dans windows 10 pour envoyer et recevoir des fichiers. Plus besoin d'installer winscp, filezilla ou autre pour l'envoie et la réception d'un simple fichier.

Voici les options disponibles :  

{:refdef: style="text-align: center;"}
[![Options SFTP](/assets/2018/11/04-SSH-SFTP_Windows_10.png)](/assets/2018/11/04-SSH-SFTP_Windows_10.png)
{: refdef}

Avant tout, il faut se connecter au serveur sur lequel on souhaite envoyer ou recevoir des fichiers. Pour ce faire, on utilise la commande suivante :

{% highlight shell_session %}
sftp user@host
{% endhighlight %}

{:refdef: style="text-align: center;"}
[![SFTP connecté](/assets/2018/11/05-SSH-SFTP_Windows_10.png)](/assets/2018/11/05-SSH-SFTP_Windows_10.png)
{: refdef}

Nous voilà maintenant connecté. On peut afficher les commandes disponibles avec _help_.

{:refdef: style="text-align: center;"}
[![SFTP aide](/assets/2018/11/06-SSH-SFTP_Windows_10.png)](/assets/2018/11/06-SSH-SFTP_Windows_10.png)
{: refdef}

### Envoie de fichier

Pour envoyer un fichier, il faut utiliser la commande suivante :
{% highlight shell_session %}
sftp> put nom_du_fichier
{% endhighlight %}

Pour vérifier le nom du fichier que l'on souhaite envoyer, la commande suivante permet de lister le répertoire courant sur la machine cliente :

{% highlight shell_session %}
lls
{% endhighlight %}

{:refdef: style="text-align: center;"}
[![SFTP lls](/assets/2018/11/07-SSH-SFTP_Windows_10.png)](/assets/2018/11/07-SSH-SFTP_Windows_10.png)
{: refdef}

Ici j'envoie le fichier nommé _test.txt_ :

{:refdef: style="text-align: center;"}
[![SFTP envoie de fichier](/assets/2018/11/08-SSH-SFTP_Windows_10.png)](/assets/2018/11/08-SSH-SFTP_Windows_10.png)
{: refdef}

Il est possible de vérifier que le fichier distant existe bien à présent :

{% highlight shell_session %}
ls
{% endhighlight %}

{:refdef: style="text-align: center;"}
[![SFTP envoie de fichier](/assets/2018/11/09-SSH-SFTP_Windows_10.png)](/assets/2018/11/09-SSH-SFTP_Windows_10.png)
{: refdef}

### Réception de fichier

Pour recevoir un fichier, la procédure est la même, avec la commande suivante :

{% highlight shell_session %}
get nom_du_fichier
{% endhighlight %}

{:refdef: style="text-align: center;"}
[![SFTP envoie de fichier](/assets/2018/11/10-SSH-SFTP_Windows_10.png)](/assets/2018/11/10-SSH-SFTP_Windows_10.png)
{: refdef}

## Conclusion

L'intégration de OpenSSH nativement dans Windows 10 est une très bonne chose. Cela permet d'éviter d'installer des logiciels (qu'il faudra par la suite maintenir à jour) pour des fonctionnalités pourtant basiques. Cela ouvre également des possibilités, notamment en terme de scripting. En effet, il est maintenant plus aisé de créer des scripts de manipulation de fichier par exemple à partir d'une machine windows.