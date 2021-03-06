---
layout: post
title: Codes correcteurs d'erreurs
---

## L'espace vectoriel $$\mathbb{F}_2^n$$

On donne ici quelques notions élémentaires d'algèbre.

Un corps est la donne d'un ensemble $$K$$ muni d'une
*addition*, notée $$+$$, et d'une *multiplication*, notée $$\times$$ (ou
$$\cdot$$), avec les propriétés suivantes:

- $$(K,+)$$ forme un groupe abélien: c'est à dire l'addition est
  associative, commutative, a un élément neutre, noté $$0$$, et tout
  élément a un inverse.
- On note $$K^\ast$$ l'ensemble $$K$$ privé de l'identité additive
  $$0$$. $$(K^\ast,\times)$$ est un groupe abélien, son élément neutre est
  noté $$1$$.

On note $$\mathbb{F}_2$$ le *corps à deux éléments*, c'est à dire
l'ensemble $$\{0,1\}$$ avec l'addition et la multiplication définies
comme suit:

$$0 + 0 = 1 + 1 = 0, \qquad 0 + 1 = 1 + 0 = 1,$$

$$0 \cdot 0 = 0 \cdot 1 = 1 \cdot 0 = 0, \qquad 1 \cdot 1 = 1.$$

Vous aurez reconnu le XOR dans l'addition et le ET dans la
multiplication. Oui, vous avez aussi reconnu les opérations modulo
$$2$$.

L'*espace vectoriel* $$\mathbb{F}_2^n$$ est l'ensemble de tous les
vecteurs à $$n$$ composantes à coefficients dans $$\mathbb{F}_2$$. Voici,
par exemple, les éléments de $$\mathbb{F}_2^3$$:

$$\begin{pmatrix}0\\0\\0\end{pmatrix},\;
\begin{pmatrix}0\\0\\1\end{pmatrix},\;
\begin{pmatrix}0\\1\\0\end{pmatrix},\;
\begin{pmatrix}1\\0\\0\end{pmatrix},\;
\begin{pmatrix}0\\1\\1\end{pmatrix},\;
\begin{pmatrix}1\\0\\1\end{pmatrix},\;
\begin{pmatrix}1\\1\\0\end{pmatrix},\;
\begin{pmatrix}1\\1\\1\end{pmatrix}.$$

Les éléments de $$\mathbb{F}_2^n$$ sont munis d'une *addition* et d'une
*multiplication externe* (dit aussi *produit par un
scalaire*). L'addition se fait composante par composante,
$$\mathbb{F}_2^n$$ muni de l'addition forme un *groupe abélien*.


**Exercices :**

1. Calculer les sommes suivantes:

   $$\begin{pmatrix}0\\0\\0\end{pmatrix} + \begin{pmatrix}1\\1\\0\end{pmatrix},\qquad
   \begin{pmatrix}1\\1\\0\end{pmatrix} + \begin{pmatrix}1\\0\\1\end{pmatrix},\qquad
   \begin{pmatrix}0\\1\\1\end{pmatrix} + \begin{pmatrix}0\\1\\1\end{pmatrix}.\qquad$$

2. Calculer l'*opposé* (ou *inverse additif*) de
   $$\begin{pmatrix}1\\0\\1\end{pmatrix}.$$

La *multiplication externe*, souvent notée en mettant les opérandes
côte à côte, est une lois
$$\mathbb{F}_2\times\mathbb{F}_2^n\to\mathbb{F}_2^n$$, c'est à dire
qu'on multiplie un vecteur par un élément, dit *scalaire*, de
$$\mathbb{F}_2^n$$ (soit un $$0$$, soit un $$1$$). Le résultat est
calculé en appliquant le multiplieur à chacune des composantes, ce qui
donne la lois suivante (on peut difficilement faire plus simple):

$$0\begin{pmatrix}a\\b\\\vdots\end{pmatrix} = \begin{pmatrix}0\\0\\\vdots\end{pmatrix},$$

$$1\begin{pmatrix}a\\b\\\vdots\end{pmatrix} = \begin{pmatrix}a\\b\\\vdots\end{pmatrix}.$$


La structure d'espace vectoriel de $$\mathbb{F}_2^n$$ permet de définir
les *applications linéaires de $$\mathbb{F}_2^n$$ vers
$$\mathbb{F}_2^m$$*. Sans vouloir trop s'attarder sur les définitions
mathématiques, une application linéaire est juste une **matrice**
$$m\times n$$ à coefficients dans $$\mathbb{F}_2$$. Les produits
matrice-vecteur et matrice-matrice se calculent comme sur les entiers,
sauf que l'addition et la multiplication sont maintenant celles de
$$\mathbb{F}_2$$ (ce qui est, avouez-le, beaucoup plus simple!).

**Exercices :**

3. Calculer les produits matrice-vecteur suivants:

   $$\begin{pmatrix}0&0&0&0\\0&0&0&0\\0&0&0&0\end{pmatrix} \begin{pmatrix}1\\1\\0\\1\end{pmatrix},\qquad
   \begin{pmatrix}1&0&0&0\\0&1&0&0\\0&0&0&1\end{pmatrix} \begin{pmatrix}1\\1\\0\\1\end{pmatrix},\qquad
   \begin{pmatrix}1&0&1\\1&0&0\\1&1&0\\1&1&1\end{pmatrix} \begin{pmatrix}1\\1\\0\end{pmatrix}.$$

4. Calculer le produit matrice-matrice suivant:

   $$\begin{pmatrix}1&0&1&0\\1&0&0&0\\1&0&1&1\end{pmatrix} \begin{pmatrix}1&0\\0&1\\0&0\\1&1\end{pmatrix}.$$


Vous l'avez compris, une matrice $$m\times n$$ est juste une fonction
$$\mathbb{F}_2^n\to\mathbb{F}_2^m$$ un peu spéciale.

**Exercice :** Les applications linéaires suivantes, sont-elles injectives, surjectives, bijectives?

   $$\begin{pmatrix}0&0&0\\0&0&0\\0&0&0\end{pmatrix},\qquad
   \begin{pmatrix}1&0&0\\0&1&0\\0&0&1\end{pmatrix},\qquad
   \begin{pmatrix}1&0&0\\0&1&0\end{pmatrix},$$

   $$\begin{pmatrix}1&0\\0&1\\1&1\end{pmatrix},\qquad
   \begin{pmatrix}1&0&1\\0&1&0\\0&0&1\end{pmatrix},\qquad
   \begin{pmatrix}1&0&0\\0&1&1\\0&1&1\end{pmatrix}.$$
   

## Codes correcteurs d'erreurs

Nous avons vu en cours les définitions de base des codes *correcteurs d'erreurs* et des *codes linéaires*.


## Forme systématique

Nous avons vu en cours comment mettre les matrices génératrice et de parité sous forme systématique.


## Codes de Hamming

Nous avons vu en cours les définitions de base des codes *correcteurs d'erreurs* et des *codes linéaires*.

## Décodage syndrome

voir [Décodage syndrome](Décodage syndrome).


## Références


J.-C. Chappelier. *Introduction à la Théorie de l'Information et ses applications*
:   Chapitre 6. <http://icwww.epfl.ch/~chappeli/it/courseFR/I2chap_moduleI2.php?shownav=yes>


B. Martin. *Codage, cryptologie et applications*.
:   Presse polytechniques et universitaires romandes. ISBN: 2–88074–569–1. Côte BU: 005.82 MAR. Chapitres 3-5.