---
layout: post
title:  "Writeup - FCSC-2020 - Web - Revision"
date:   2020-05-05 19:00:13 +0200
categories: [writeup]
author: Vixepti
image: /assets/2020/05/FCSC.png
tags: [FCSC, ctf, writeup, web]
---

Petit writeup sur un challenge que j'ai trouvé très intéressant. *Revision* est un challenge web du *France Cybersecurity Challenge*. <!--more-->

# Énoncé

> La société Semper est spécialisée en archivage de documents électroniques. Afin de simplifier le travail des archivistes, un outil simple de  suivi de modification a été mis en ligne. Depuis quelques temps néanmoins, cet outil dysfonctionne. Les salariés se plaignent de ne pas recevoir tous les documents et il n'est pas rare que le système plante. Le développeur de l'application pense avoir identifié l'origine du problème. Aidez-le à reproduire le bug.
> 
> **Note** : La taille totale des fichiers est limitée à 2Mo.

# Solution

On a la page suivante :

{:refdef: style="text-align: center;"}
![Revision Homepage](/assets/2020/05/fcsc-revision-welcome.png)
{: refdef}

Et le code python qui gère les fichiers.

L'énoncé indique qu'il y a un soucis et que les documents ne sont pas tous envoyés. Quand on regarde le code, on peut voir cette fonction :

{% highlight python %}
def store(self):
    self._reset_cursor()
    f1_hash = self._compute_sha1(self.f1)
    f2_hash = self._compute_sha1(self.f2)

    if self.db.document_exists(f1_hash) or self.db.document_exists(f2_hash):
        raise DatabaseError()

    attachments = set([f1_hash, f2_hash])
    # Debug debug...
    if len(attachments) < 2:
        raise StoreError([f1_hash, f2_hash], self._get_flag())
    else:
        self.m.send(attachments=attachments)
{% endhighlight %}

Ce qui est intéressant ici, c'est que si les fichiers sont connus dans le système et que les empreintes (hash SHA1) des fichiers sont identiques, le flag s'affiche. Il faut donc 2 pdf différents ayant la même empreinte SHA1.

J'ai donc cherché s'il existait des collisions sha1 possible sur des fichiers pdf et j'ai trouvé : [shattered.io](https://shattered.io/). Évidemment, les fichiers de ce site on déjà été utilisés sur le challenge. Il faut donc créer nos propres fichiers.

Grâce à [SHA1 Collider](https://alf.nu/SHA1), j'ai pu générer 2 fichiers pdf à partir de 2 images de 512px X 512px. Ces fichiers sont sensés avoir la même empreinte SHA1, donc je vérifie : 

{% highlight shell_session %}
[vixepti@computer]$ sha1sum vixcol1.pdf 
8dff8358654e967a838c7575a82ed2e6aa14f1ae  vixcol1.pdf
[vixepti@computer]$ sha1sum vixcol2.pdf 
8dff8358654e967a838c7575a82ed2e6aa14f1ae  vixcol2.pdf
{% endhighlight %}

Puis j'envoie les fichiers :

{:refdef: style="text-align: center;"}
![Revision Flag](/assets/2020/05/revision-flag.png)
{: refdef}

# Flag

Le flag est donc : ***FCSC{8f95b0fc1a793e102a65bae9c473e9a3c2893cf083a539636b082605c40c00c1}***