---
layout: post
title:  "Writeup - FCSC-2020 - Intro - NES Forever"
date:   2020-05-05 19:00:00 +0200
categories: [writeup]
author: Vixepti
image: /assets/2020/05/FCSC.png
tags: [FCSC, ctf, writeup, web]
---

Writeup sur un challenge vraiment simple du *France Cybersecurity Challenge 2020*. Pour s'échauffer on dira !<!--more-->

# Énoncé

> Le bon vieux temps ! Pour trouver le flag, vous allez devoir inspecter un langage qui fait la base d'Internet.

# Solution

Bon, ce challenge est franchement facile. On a le site web suivant :

{:refdef: style="text-align: center;"}
![NES Forever - Homepage](/assets/2020/05/nes-forever-homepage.png)
{: refdef}

Et si on regarde le code source de la page :

{% highlight html %}
  <nav style="padding: 20px; border-bottom: 3px solid #ccc; margin-bottom: 60px;">
    <div class="container">
      <h2 style="margin-bottom: 0;">
        <i class="snes-jp-logo"></i>
        NES_FOREVER
      </h2>
    </div>
  </nav>
  <!--
  FCSC{a1cec1710b5a2423ae927a12db174337508f07b470fc0a29bfc73461f131e0c2}
  -->
  <div class="container">
    <div class="row">
      <!-- left -->
      <div class="col-md-4 col-sm-12 col-xs-12">
        <div class="nes-container with-title">
          <h3 class="title">A propos</h3>
	  <p>Le site de référence pour les fans de jeux 8 bits !</p>
{% endhighlight %}

On voit clairement le flag en commentaire.

# Flag

Le flag est donc : ***FCSC{a1cec1710b5a2423ae927a12db174337508f07b470fc0a29bfc73461f131e0c2}***
