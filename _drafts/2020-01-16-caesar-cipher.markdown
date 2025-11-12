---
layout: post
mathjax: true
title:  "Chiffrement par décalage (César)"
date:   2020-01-16 02:07:42 +0100
categories: crypto
author: Vixepti
image: /assets/2020/02/caesar-cipher-thumbnail.jpg
tags: [cryptographie, cesar]
---
Le chiffrement par décalage aussi connu sous le nom de chiffrement de César est la méthode de chiffrement par substitution mono-alphabétique la plus simple et la plus connue.<!--more-->

## Principe

Ce chiffrement consiste à décaler les lettres de l’alphabet d’un nombre de posistions constants vers la droite ou la gauche. Ce nombre est la **clé** de chiffrement et de déchiffrement.
* Clé : $n$
* Lettre : $x$

On chiffre avec : $En(x) = (x+n)[26]$

On déchiffre avec : $Dn(x) = (x-n)[26]$

On peut utiliser le tableau suivant pour chiffrer ou déchiffrer un message à la main (ici la clé est 5) :

| A | B | C | D | E | F | G | H | I | J | K | L | M | N | O | P | Q | R | S | T | U | V | W | X | Y | Z |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 | 17 | 18 | 19 | 20 | 21 | 22 | 23 | 24 | 25 |
| F | G | H | I | J | K | L | M | N | O | P | Q | R | S | T | U | V | W | X | Y | Z | A | B | C | D | E |

Il est inutile d’enchainer les chiffrement de césar car la clé de chiffrement est alors la somme de l’ensemble des clés utilisées.

#### Cas particuliers

Avec les clés suivantes :
* **13 :** ROT13.

## Attaques

Le chiffrement de césar n’est pas un système de chiffrement sécurisé. En effet, il est très simple de retrouver la clé de chiffrement et ainsi de décoder le message chiffré. Il existe plusieurs méthode d’attaque pour ce système.

### Par recherche de la méthode de chiffrement

Tout d’abord, même si nous ne connaissons pas la méthode de chiffrement, on peut facilement décoder des parties du messages par analyse fréquencielle. On repère ainsi que la substitution de l’alphabet est régulier et donc que le chiffrement est par décalage.

### Par recherche de la valeur du décalage

Si on sait qu’un chiffrement par décalage a été utilisé, on peut chercher la valeur de décalage par force brute. Il y a 26 lettres dans l’alphabet latin, on cherche donc à décoder le message grâce aux 26 clés possibles, jusqu’à reconnaître des mots dans le message décodé.

### Analyse fréquentielle

Si on sait que le chiffrement par décalage est utilisé, on peut également effectuer une analyse fréquentielle d’utilisation de chacune des lettres de l’alphabet dans le message chiffré. En comparant la fréquence d’apparition analysé par rapport à la fréquence d’apparition des lettres de l’alphabet dans un texte en français (ou dans la langue utilisé), on peut déduire le décalage.

Pour faciliter la comparaison, on peut générer des graphiques d’utilisation des lettres. En comparant les deux graphiques, le décalage apparaît visuellement (graphique bâtons).

## Algorithmes

{% highlight python%}
def ECaesar(text, shift): #Encrypt with Caesar algorithm
    alphabet = ('A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z')
    result = ""
    for currentChar in text:
        rsltNbr = alphabet.index(currentChar)+shift
        rslIndex = rsltNbr%26
        result = result + alphabet[rslIndex]
    return(result)

def DCaesar(text, shift): #Decrypt with Caesar algorithm
    alphabet = ('A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z')
    result = ""
    for currentChar in text:
        rsltNbr = alphabet.index(currentChar)-shift
        rslIndex = rsltNbr%26
        result = result + alphabet[rslIndex]
    return(result)
{% endhighlight %}