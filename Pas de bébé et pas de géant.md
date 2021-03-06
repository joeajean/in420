---
layout: post
title: Pas de bébé et pas de géant
---

Le but de ce DM est d'implanter un algorithme pour la résolution du problème du *logarithme discret*, problème sous-jacent à la sécurité du protocole de Diffie-Hellman et de nombreux autres protocoles (El Gamal, DSA, etc.).


## Le logarithme discret

Soit $$p$$ un nombre premier. Le problème du *logarithme discret* dans un *corps fini* $$\mathbb{Z}/p\mathbb{Z}$$ est défini comme suit.

**Entrées:** Le nombre premier $$p$$, un élément $$g\in\mathbb{Z}/p\mathbb{Z}$$ d'ordre maximal, un autre élément $$a\in\mathbb{Z}/p\mathbb{Z}$$.

**Sorties:** L'unique exposant $$0 \le x < p-1$$ tel que $$a = g^x \mod p$$.


## Avant de commencer l'attaque

1. Écrivez une fonction
   
   ~~~
   public static long inverse(long a, long p)
   ~~~
   
   qui calcule l'inversion $$\frac{1}{a} \mod p$$ en utilisant l'algorithme d'Euclide étendu (voir TD [Torus Wars](Torus Wars)).

2. Écrivez une fonction
   
   ~~~
   public static long modPow(long g, long x, long p)
   ~~~
   
   qui calcule $$g^x \mod p$$ en utilisant l'algorithme d'exponentiation binaire (voir TD [Arithmétique modulaire](Arithmétique modulaire)). La fonction doit accepter des valeurs négatives pour `g` et pour `x`. Souvenez vous que $$g^{-x} = \frac{1}{g^x}$$.

3. Testez vos fonctions sur des petites valeurs pour être sûrs qu'elle marchent bien. Enfin, vérifiez que
   
   ~~~
   modPow(-2, -12345, 1073741827) == 11418853
   ~~~
   
   Si vous n'obtenez pas ce résultat, l'un des problèmes possibles est dû à un dépassement de la précision des `long` : vous n'avez pas systématiquement réduit modulo `p`.


## Attaque par recherche exhaustive

L'algorithme le plus simple que l'on puisse imaginer pour résoudre le problème du log discret consiste à essayer toutes les puissances $$0 \le x < p-1$$ jusqu'à tomber sur la bonne.

4. Écrire un programme Java qui prend en entrée $$p$$, $$g$$ et $$a$$ et qui donne en sortie le logarithme discret $$\log_g a$$.

**Remarque:** Vous serez peut-être tentés d'utiliser la fonction `modPow` dans la boucle de ce programme pour calculer $$g^1$$, $$g^2$$, etc. Observez, cependant, que dans les deux boucles ci-dessous:

~~~
pour 0 <= x < p-1 :
    tmp = modPow(g, x, p)
~~~

et 

~~~
tmp = 1
pour 0 <= x < p-1 :
    tmp = tmp * g % p
~~~

la variable `tmp` prend exactement les mêmes valeurs, mais la deuxième boucle est largement plus efficace car elle fait une seule multiplication à chaque itération.

5. Testez votre programme sur des petites entrées dont vous connaissez le logarithme. Ensuite, allez résoudre les premiers [challenges](http://it-katas.defeo.lu/katas/dlog) (jusqu'à $$p \sim 2^30$$).

**Suggestion:** la résolution des challenges peut prendre du temps. Pour avoir une idée d'où votre algorithme en est, faites des affichages de contrôle réguliers dans vos boucles. Un bon compromis entre trop et pas assez est de faire un affichage toutes les $$2^{20}$$ itérations.


## Précision arbitraire avec `BigInteger`

On a déjà fait la remarque : les données de type `long` sont limitées à 8 octets, c'est à dire 64 bits (représentation en *complément à deux*, qui utilise un bit pour le signe). Lorsque l'on multiplie entre eux deux `long` de taille supérieure à 32 bits, le résultat dépasse alors la taille maximale, et il est donc tronqué.

Pour pouvoir résoudre des logarithmes discrets plus grands, il va falloir utiliser une bibliothèque d'*entiers multi-précision*, garantissant un résultat toujours correct. Java implante cela dans l'objet [`java.math.BigInteger`](http://docs.oracle.com/javase/6/docs/api/java/math/BigInteger.html).

Les opérateurs usuels `+`, `*`, etc. ne sont pas utilisables sur les `BigInteger` : ils sont remplacés par des méthodes. Voici un bref comparatif des opérations arithmétiques sur les entiers (`int` ou `long`) et sur les `BigInteger`.

---

|                    |entiers machine  |`BigInteger`
|--------------------|-----------------|------------------------------------------
|création            |`int a = 10`;    |`BigInteger a = BigInteger.valueOf(10)`;
|opérations          |`c = a + b;`     |`c = a.add(b);`
|                    |`c = a - b;`     |`c = a.subtract(b);`
|                    |`c = a * b;`     |`c = a.multiply(b);`
|                    |`c = a / b;`     |`c = a.divide(b);`
|                    |`c = a % b;`     |`c = a.mod(b);`¹
|comparaisons        |`a == b`         |`a.equals(b)`
|                    |`a < b`          |`a.compareTo(b) == -1`
|                    |`a > b`          |`a.compareTo(b) == 1`
|$$a^{-1}\mod n$$    |                 |`b = a.modInverse(n);`
|$$a^x\mod n$$       |                 |`b = a.modPow(x, n);`
|convertir en `int`  |                 |`int b = a.intValue();`
|convertir en `long` |                 |`long b = a.longValue();`
{: .centered style="text-align: left"}

> ¹`a.mod(b)` se comporte mieux que `a % b`, en ce que le résultat est toujours positif. Si vous voulez avoir exactement le même comportement que `a % b`, il faut utiliser `a.remainder(b)`.

---

D'autres opérations encore sont définies sur les `BigInteger`, allez voir la [documentation](http://docs.oracle.com/javase/6/docs/api/java/math/BigInteger.html).

6. Récrivez votre code en utilisant des `BigInteger` à la place des `long`. Vous pouvez vous épargner le travail de récrire `inverse` et `modPow`, car ils sont déjà implantés dans la bibliothèque Java.

Utilisez votre code pour résoudre les [challenges](http://it-katas.defeo.lu/katas/dlog) suivants. Attention : cela peut prendre une dizaine de minutes de résoudre un logarithme de taille $$p\sim 2^{32}$$ (32 bits) avec l'algorithme naïf... difficile d'aller au dela des 35 bits !


## Pas de Bébé, pas de géant

Il est facile de se rendre compte que la recherche exhaustive fait en moyenne $$O(p)$$ multiplications d'entiers modulo $$p$$. Dès que $$p$$ dépasse les 30 bits, ce coût commence à devenir prohibitif.

L'algorithme *pas de bébé - pas de géant* fait partie de la famille des algorithmes *meet in the middle*. Il permet de réduire le temps de calcul de la recherche exhaustive en échange d'une occupation de mémoire accrue. Avec une occupation de mémoire de $$O(m)$$, il permet de trouver un logarithme discret en seulement $$O(\max(m, p/m))$$ multiplications. L'optimum est atteint lorsque $$m=\sqrt{p}$$ (en supposant qu'on ait assez de mémoire à disposition), alors le temps de calcul devient de $$O(\sqrt{p})$$. Avec assez de mémoire vive à disposition, il devrait alors être possible de calculer des logarithmes discrets pour $$p$$ jusqu'à 60 bits sur des machines ordinaires.

### Description de l'algorithme

**Entrées:** Le nombre premier $$p$$, un élément $$g\in\mathbb{Z}/p\mathbb{Z}$$ d'ordre maximal, un autre élément $$a\in\mathbb{Z}/p\mathbb{Z}$$.

**Paramètres:** Un entier $$m < \sqrt{p}$$ (en général une puissance de 2).

**Sortie:** L'unique exposant $$0 \le x < p-1$$ tel que $$a = g^x \mod p$$.

L'algorithme se développe en deux phases:

- Dans la première phase (les *pas de bébé*) on compile la liste des puissances $$g^0, g^1, g^2, \ldots, g^{m-1}$$ ;

- Dans la deuxième phase (les *pas de géant*) on calcule successivement $$a, ag^m, ag^{2m}, ag^{3m}, \ldots$$ jusqu'à tomber sur une valeur déjà présente dans la liste (ce que l'on appelle une *collision*). 

Lorsqu'on a une collision, on a trouvé une égalité du type

$$ag^{ym} = g^z \mod p,$$

par des opérations arithmétiques simples, il est alors immédiat que

$$a = g^{z-ym} \mod p,$$

le logarithme discret recherché est alors $$z-ym$$.


### Exemple

Démontrons l'utilisation de l'algorithme sur des petits entiers. Nous allons choisir $$p = 101$$, $$g = 2, a = 48$$. Fixons $$m = 8$$ comme paramètre, ce qui est très proche de $$\sqrt{101}$$ (on aurait pu prendre $$m=10$$, mais nous sommes paresseux).

Les pas de bébé donnent le tableau suivant, qu'on va stocker en mémoire

$$
\begin{array}{c|c}
  1  &  2^0\\
  2  &  2^1\\
  4  &  2^2\\
  8  &  2^3\\
 16  &  2^4\\
 32  &  2^5\\
 64  &  2^6\\
 27  &  2^7
\end{array}
$$

On calcule $$2^{8} = 54 \mod 101$$. On fait maintenant les pas de géant (pour cette phase, pas besoin de stocker les résultats dans une table):

- $$48$$ n'est pas dans la table,
- $$48\cdot 2^{8} = 48\cdot 54 = 67 \mod p$$,
- $$48\cdot 2^{16} = 48\cdot 54^2 = 83 \mod p$$,
- $$48\cdot 2^{24} = 48\cdot 54^3 = 38 \mod p$$,
- $$48\cdot 2^{32} = 48\cdot 54^4 = 32 \mod p$$, finalement une entrée de la table !

On déduit que 

$$48\cdot 2^{32} = 32 = 2^5,$$

d'où

$$48 = 2^{5-32} = 2^{-27} = 2^{100-27} = 2^{73},$$

où l'avant-dernière égalité découle du *petit thèorème de Fermat* (qui affirme que $$a^{p-1}=1\mod p$$ pour tout $$a$$ et tout $$p$$ premier). Le logarithme discret cherché est donc 73 (ou -27, ce qui est tout à fait équivalent).


### À vos `javac`

7. Écrivez un programme qui implante l'algorithme pas de bébé - pas de géant. Il doit prendre en entrée les nombres $$p$$, $$g$$ et $$a$$, ainsi que le paramètre $$m$$ déterminant son utilisation de la mémoire.

Vérifiez votre programme sur les [challenges](http://it-katas.defeo.lu/katas/dlog) que vous avez déjà résolu.


## Tables de hachage et optimisation de la mémoire

L'efficacité de l'algorithme pas de bébé - pas de géant dépend crucialement de la rapidité avec laquelle on peut effectuer les recherches dans la table des pas de bébé. Si l'on utilise une bête liste chaînée, par exemple, chacun des $$p/m$$ pas de géant engendrera une recherche parmi $$m$$ éléments, ce qui demande $$O(m)$$ opérations, pour un coût total moyen de $$O(m\cdot(p/m))$$. Aucun gain asymptotique par rapport à l'algorithme naïf, donc.

Les *tables de hachage* sont une structure de données qui permet de faire des recherches dans une table en temps O(1) en moyenne. Java en offre deux implantation à peu près équivalentes : [java.util.HashMap](http://docs.oracle.com/javase/6/docs/api/java/util/HashMap.html) et [java.util.Hashtable](http://docs.oracle.com/javase/6/docs/api/java/util/Hashtable.html)

Vous pouvez déclarer une table de hachage contenant des `BigInteger` comme suit:

~~~
Hashtable<BigInteger,BigInteger> table = new Hashtable<BigInteger,BigInteger>(size, 1);
~~~

où `size` est le nombre maximal d'éléments que la table va contenir ($$m$$ dans notre cas). Remarquez que `size` doit être de type `int`, sinon le compilateur vous donnera une erreur.

Vous pouvez ensuite insérer des éléments avec la méthode `.put` et les chercher avec la méthode `.get`:

~~~
table.put(BigInteger.valueOf(10), BigInteger.valueOf(1));
System.out.println(table.get(BigInteger.valueOf(10));     // Affiche 1
System.out.println(table.get(BigInteger.valueOf(1)));     // Affiche null
~~~

8. Modifiez votre code pour qu'il utilise une table de Hachage pour stocker les pas de bébé. Résolvez les [challenges](http://it-katas.defeo.lu/katas/dlog) suivants (les 40 bits devraient être atteignables).

**Remarque:** Vous aurez peut-être besoin d'augmenter la mémoire à disposition de la JVM afin de résoudre certains challenges. Pour cela, vous pouvez lancer la JVM avec le paramètre `-Xmx`, comme ceci (initialisation de la mémoire à 3 giga-octets) :

~~~
java -Xmx3g PasDeBebe
~~~

### Optimisation de la mémoire

Lorsque $$m$$ frôle les 20 bits, les tables de hachage de Java commencent à devenir insuffisants. Il y a deux raisons pour cela:

- Les `BigInteger` occupent trop de mémoire par rapport à un `long`,
- L'implantation de `Hashtable` et `HashMap` utilise des listes chaînées et de l'allocation dynamique.

En ce qui concerne le premier point, on peut réduire considérablement l'occupation mémoire en stockant des `long` dans la table. Ceci ne fait de sens que si $$p < 2^{63}$$, alors vous pourrez récupérer la valeur de vos `BigInteger` au format `long` en appelant la méthode `.longValue()`. Remarquez, cependant que les calculs (en particulier les multiplications) doivent être faits avec `BigInteger`, car le résultat du produit de deux entiers de 63 bits (avant réduction modulo $$p$$) ne tient pas dans un `long`.

En ce qui concerne le deuxième point il faudrait pouvoir se passer des listes chaînées. Rappelons le principe d'une table de Hachage. Une table de Hachage est un grand tableau, indexé de 0 jusqu'à $$n-1$$. Lorsqu'on insère un élément $$x$$ dans la table, on calcule son *hash* $$h(x)$$, c'est à dire une valeur, dépendante uniquement de $$x$$, comprise entre $$0$$ et $$n-1$$, et l'on insère $$x$$ dans la *case* $$h(x)$$ du tableau.

Lorsqu'on cherche une valeur $$x$$ dans la table, on calcule son hash $$h(x)$$, et on regarde s'il est dans la case $$h(x)$$ du tableau.

Il arrive fréquemment que deux éléments $$x$$ et $$y$$ aient le même hash $$h(x)=h(y)$$. Dans ce cas on a une *collision*, et il faut trouver un moyen de stocker les deux valeurs dans la même *case* du tableau. Deux solutions sont communément employées:

- Chaque case du tableau contient un pointeur vers une liste chaînée d'éléments appartenants à la case. C'est le choix fait par Java.
- En supposant que $$x$$ ait déjà été inséré dans la case $$h(x)$$, et que $$h(y)=h(x)$$, on essayera à la suite les cases $$h(y)+1$$, $$h(y)+2$$, $$h(y)+3$$, ... jusqu'à en trouver une libre. Cette technique est appelée "adressage ouvert avec sondage linéaire".

Remarquez que pour implanter un adressage ouvert, le tableau doit être légèrement plus grand que la quantité de données à stocker (en général on alloue entre 125% et 140% du nombre maximal d'entrées).

La [page Wikipedia](http://fr.wikipedia.org/wiki/Table_de_hachage) décrit assez clairement les différentes méthodes de Hachage.

### À vos `javac`

La technique de l'adressage ouvert est parfaitement adaptée pour l'algorithme pas de bébé - pas de géant. En effet nous savons d'avance que nous allons stocker $$m$$ couples d'entiers (des `long`), nous pouvons donc allouer au début de l'algorithme deux tableaux de `long` de taille légèrement plus grande que $$m$$. Aucune allocation dynamique de mémoire n'aura lieu lorsque l'on insérera des valeurs dans ces tableaux (contrairement au cas des `BigInteger`).

Il nous faut choisir une fonction de hachage pour calculer $$h(x)$$ pour chaque entier $$x$$. Si le tableau utilisé pour la table est de dimension $$2^n$$, un choix simple et efficace consiste à prendre les $$n$$ bits de poids faible de $$x$$ comme valeur du hash $$h(x)$$. Plus généralement, si la dimension du tableau est un entier quelconque $$N$$, la fonction $$h(x) = x \mod N$$ est un choix raisonnable.

9. Créez votre propre table de hachage à adressage ouvert, et utilisez-la dans l'algorithme précédent.

Essayez de résoudre les [challenges](http://it-katas.defeo.lu/katas/dlog) restants (50 bits devrait être faisable, 60 bits va être dur...). À moins que vous n'ayez un super-calculateur à la maison, doté de centaines de GO de RAM, vous serez sans doutes amenés à utiliser un paramètre $$m$$ bien inférieur à $$\sqrt{p}$$.


## Le mot de la fin

Déposez tous vos fichiers `.java` dans la boîte de dépôt sur e-campus2, ou envoyez-les par mail à votre enseignant. Incluez *tout le code*, même celui de l'algorithme naïf avec les `long`. Pas la peine d'envoyer les logarithmes discrets que vous aurez calculés : nous avons des traces de vos exploits ! La date limite pour envoyer vos fichiers est le vendredi 10 mai à 4h du matin. Un point de pénalité pour chaque heure de retard: le 10 mai à 23h59 c’est votre dernière chance !

Parmi les challenges proposés, il y en a qui ne sont pas raisonnablement attaquables par les pas de bébé - pas de géant. Ce n'est pas la fin de l'histoire, bien évidemment ! 

L'algorithme [rho de Pollard](http://en.wikipedia.org/wiki/Pollard%27s_rho_algorithm_for_logarithms) (aussi appelé *méthode des kangourous*) est un autre algorithme qui tourne en temps $$O(\sqrt{p})$$, mais avec une bien moindre complexité en mémoire (il utilise, en effet un nombre constant de registres). Cet algorithme est aussi basé sur une recherche de collisions entre éléments de $$\mathbb{Z}/\mathbb{Z}$$, et vous devriez, si vous le souhaitez, réussir sans difficulté quelques challenges en plus avec son aide.

Le record actuel de calcul de log discret dans $$\mathbb{Z}/p\mathbb{Z}$$ est détenu par Antoine Joux et Reynald Lercier (2005) sur un nombre $$p\sim 2^{431}$$, à l'aide de l'algorithme *Number Field Sieve*.

D'autres records plus impressionnants ont été obtenus pour d'autres structures que $$\mathbb{Z}/p\mathbb{Z}$$. Par exemple, le record de log discret dans les corps finis binaires a été obtenu le 11 avril 2013 par Robert Granger, Faruk Gologlu, Gary McGuire, and Jens Zumbragel sur un corps fini de $$2^{6120}$$ éléments, grâce à une variante du *Function Field Sieve* due à Antoine Joux et eux mêmes.

Un autre record remarquable a été annoncé le 6 avril 2013 par  Razvan Barbulescu, Cyril Bouvier, Jérémie Detrey, Pierrick Gaudry, Hamza Jeljeli, Emmanuel Thomé, Marion Videau et Paul Zimmermann, qui ont calculé un log discret dans un corps fini à $$2^{809}$$ éléments en utilisant le *Function Field Sieve*. Ce record est remarquable car 809 est un nombre premier (contrairement à 6120).

De quoi faire pâlir nos maigres exploits avec Java !
