---
layout: post
title:  "Writeup - FCSC-2020 - Web - Enter The Dungeon"
date:   2020-05-05 19:00:12 +0200
categories: [writeup]
author: Vixepti
image: /assets/2020/05/FCSC.png
tags: [FCSC, web, ctf, writeup]
---

Hello, ceci est mon writeup pour le challenge *Enter The Dungeon* du *France Cybersecurity Challenge 2020*. J'ai passé un sacré moment dessus (je ne suis pas du tout expériementé dans le domaine du web), ça méritait bien un writeup.<!--more-->

# Énoncé
> On vous demande simplement de trouver le flag.
> 
> URL : http://challenges2.france-cybersecurity-challenge.fr:5002/

On a la page web suivante :

{:refdef: style="text-align: center;"}
![Enter The Dungeon - Homepage](/assets/2020/05/EnterTheDungeon-home.png)
{: refdef}

# Solution

## CTRL + U
Première étape d'un challenge web, consulter le code source de la page :

{% highlight html %}
<html>
<head>
	<title>Enter The Dungeon</title>
</head>
<body style="background-color:#3CB371;">
<center><h1>Enter The Dungeon</h1></center>
The flag is hidden into the dungeon ! You should come here as the <b>dungeon_master</b> to be able to open the gate and access the treasure.<br /><br />

<!-- Pour les admins : si vous pouvez valider les changements que j'ai fait dans la page "check_secret.php", le code est accessible sur le fichier "check_secret.txt" -->
<div style="font-size:85%;color:purple"><i>Les admins ont reçu un message indiquant qu'un pirate avait retrouvé le secret en contournant la sécurité du site. <br />Pour cette raison, le formulaire de vérification n'est plus fonctionnel tant que le développeur n'aura pas corrigé la vulnérabilité</i></div><br />
{% endhighlight %}

Ici, on remarque très clairement le commentaire :

> Pour les admins : si vous pouvez valider les changements que j'ai fait dans la page "check_secret.php", le code est accessible sur le fichier "check_secret.txt"

Il y a donc un fichier *check_secret.txt* contenant :

{% highlight php %}
<?php
	session_start();
	$_SESSION['dungeon_master'] = 0;
?>
<html>
<head>
	<title>Enter The Dungeon</title>
</head>
<body style="background-color:#3CB371;">
<center><h1>Enter The Dungeon</h1></center>
<?php
	echo '<div style="font-size:85%;color:purple">For security reason, secret check is disable !</div><br />';
	echo '<pre>'.chr(10);
	include('./ecsc.txt');
	echo chr(10).'</pre>';

	// authentication is replaced by an impossible test
	//if(md5($_GET['secret']) == "a5de2c87ba651432365a5efd928ee8f2")
	if(md5($_GET['secret']) == $_GET['secret'])
	{
		$_SESSION['dungeon_master'] = 1;
		echo "Secret is correct, welcome Master ! You can now enter the dungeon";
		
	}
	else
	{
		echo "Wrong secret !";
	}
?>
</body></html>
{% endhighlight %}

Bon c'est donc le système de login utilisé pour accéder au "Donjon".

## Recherches infructueuses

Je me suis d'abord penché sur le hash md5 qui se situe dans les commentaires : a5de2c87ba651432365a5efd928ee8f2

1. J'ai essayé de chercher si la chaine de caractère correspondante existe dans les bases de données de plusieurs sites :
    - [md5decrypt](https://md5decrypt.net/en/)
    - [md5hashing](https://md5hashing.net/hash/md5)
2. J'ai essayé de retouver le hash depuis plusieurs chaines de caractères probables. En fait, j'ai été induit en erreur par la phrase présente sur la page d'accueil :

> You should come here as the dungeon_master to be able to open the gate and access the treasure.

Je me suis dis que l'on pouvait peut être obtenir le hash en utilisant md5 sur :

- dungeon_master
- dungeon_master=1
- master of the dungeon
- dragon (ouais, D&D, tu connais..)
- ...

J'ai essayé pas mal de chaînes de caractères mais bien évidemment je n'ai pas trouvé.

3. J'ai également essayé de cracker le hash avec hashcat. J'ai laissé tourner une heure puis j'ai arrêté. Si c'était trouvable par ce moyen, j'aurai déjà eu un résultat.

## Magic Hashes

En continuant à chercher, j'ai finis par découvrir les magic hashes. Ce sont couples chaines de caractère/hash dont la valeur du md5 commence par "0e". C'est intéressant car en PHP la comparaison avec == au lieu de === va vérifier uniquement le type au lieu de comparer ces valeurs. Or, la chaine commençant par 0e en PHP sera reconnu comme une notation scientifique, il faut donc trouver une valeur commençant par 0e et dont le hash md5 commence également par 0e.

Et on a de la chance cette valeur existe : **0e215962017** a pour hash md5 **0e291242476940776845150308577824**.

On entre donc cette valeur dans le formulaire et on a le résultat suivant :

{:refdef: style="text-align: center;"}
![Enter The Dungeon - Homepage](/assets/2020/05/EnterTheDungeon-welcome.png)
{: refdef}

On peut ensuite retourner sur la page d'accueil :

{:refdef: style="text-align: center;"}
![Enter The Dungeon - Homepage](/assets/2020/05/EnterTheDungeon-flag.png)
{: refdef}

# Flag

Le flag est donc : ***FCSC{f67aaeb3b15152b216cb1addbf0236c66f9d81c4487c4db813c1de8603bb2b5b}***