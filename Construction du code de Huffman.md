---
layout: post
title: Construction du code de Huffman
scripts: ['js/show-after.js']
---

Dans ce TD nous allons nous occuper de la partie du [Codage de Huffman](Codage de Huffman) qui consiste à construire l'arbre de décodage. Le TD est divisé en deux parties : dans un premier temps nous allons nous occuper de la construction de l'arbre avec des fréquences prédéfinies dans le code source ; dans un deuxième temps, nous allons récupérer ces fréquences d'un fichier de texte.

## Construction de l'arbre de Huffman

Créez un fichier `Huffman.java` contenant le code suivant.

~~~
import java.util.ArrayList;
import java.util.Collections;

/*
  Cette classe implante le noeud d'un arbre binaire contenant
  contenant les donnees necessaires a l'algorithme de Huffman (le
  symbole encode et sa frequence).
 */
class Node implements Comparable<Node> {
    char symbol;
    double freq;
    Node left, right;

    /* 
       Methode implantant l'interface Comparable. Renvoie -1 si la
       frequence de ce noeud est inferieur a celle de other, 1 sinon.
    */
    public int compareTo(Node other) {
        return 0;
    }
}

/*
  Cette classe implante l'algorithme de Huffman
*/
public class Huffman {
    // Quelques alphabets predefinis avec frequences
    /*
    static char[] symbols = {
        'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i',
        'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r',
        's', 't', 'u', 'v', 'w', 'x', 'y', 'z'};
    static double[] freqs = {
        0.03, 0.12, 0.01, 0.06, 0.04, 0.04, 0.04, 0.01,        0.04,
        0.06, 0.04, 0.03, 0.02, 0.02, 0.01, 0.10, 0.01, 0.06,
        0.01, 0.03, 0.01, 0.03, 0.02, 0.03, 0.06, 0.07 };
    */

    static char[] symbols = { 'a', 'b', 'c', 'd' };
    static double[] freqs = { 0.25, 0.45, 0.20, 0.10 };

    /*
      Cette methode construit un arbre binaire de Node correspondant a
      l'arbe de Huffman. En entree il attend un tableau de symboles et
      le tableau des frequences correspondantes. Elle renvoie la
      racine de l'arbre.
     */
    public static Node createTree(char[] symbols, double[] freqs) {
        return null;
    }

    /*
      Cette fonction construit la table d'encodage a partir de l'arbre
      de Huffman. La valeur de retour est un tableau de chaines,
      contenant l'encodage (en binaire) de chaque symbole de a jusqu'a
      z.
     */
    public static String[] createCode(Node root) {
        return null;
    }

    public static void main(String[] args) {
        String[] codes = createCode(createTree(symbols, freqs));
        for (int i = 0 ; i < codes.length ; i++) {
            System.out.println((char)(i + 'a') + ": " + codes[i]);
        }
    }
}
~~~

Un arbre binaire est un arbre dans lequel chaque nœud a soit deux soit aucun fils. La classe `Node` représente un nœud d'un arbre binaire : les champs `left` et `right` permettent de pointer vers les nœuds fils, et ils sont `null` lorsque le nœud est une feuille. En plus de ces informations, notre classe `Node` contient deux champs nécessaires à l'algorithme de Huffman : un champ `symbol` qui sert à stocker un symbole de l'alphabet à encoder, et un champ `freq` qui contient sa fréquence. Enfin, la classe `Node` implante l'interface [`Comparable`](http://docs.oracle.com/javase/6/docs/api/java/lang/Comparable.html), ce qui permet d'ordonner des listes de nœuds

1. Complétez la classe `Node` en remplissant la méthode `compareTo`. La comparaison doit se faire par rapport au champ `freq`. Ajoutez aussi autant de constructeurs que vous le retiendrez nécessaire.

---

Maintenant on peut passer à l'algorithme de Huffman à proprement parler. Le `main` a déjà été complété avec du code qui permet de tester l'algorithme lorsque toutes les méthodes auront été implantées. N'hésitez pas à le modifier pour tester les méthodes en cours d'écriture.

La méthode `createTree` sert à créer l'arbre de Huffman. Ses entrées sont deux tableaux, qu'on suppose de la même longueur, contenant respectivement les symboles de l'alphabet et leurs fréquences. On rappelle brièvement le principe de l'algorithme de Huffman, et on en suggère une implantation possible.

- Créer les feuilles de l'arbre: une par symbole de l'alphabet, chacune marquée par sa fréquence. Vous pouvez utiliser un [`ArrayList`](http://docs.oracle.com/javase/6/docs/api/java/util/ArrayList.html) pour créer une liste de `Node` : un pour chaque symbole.

- Tant que l'arbre n'est pas complet, prendre les deux nœuds avec les plus petites probabilités et les relier avec un nouveau nœud. Le nouveau nœud est marqué avec la somme des fréquences des deux fils. Vous pouvez utiliser la méthode statique [`Collections.sort`](http://docs.oracle.com/javase/6/docs/api/java/util/Collections.html) pour ordonner une liste de `Comparable`, ou encore la méthode [`Collections.min`](http://docs.oracle.com/javase/6/docs/api/java/util/Collections.html) pour en trouver l'élément le plus petit. À chaque fois que vous unissez deux nœuds, retirez-les de la liste et ajoutez-y le nouveau nœud.

- Lorsque tous les nœud sont connectés à la racine, l'algorithme se termine. La liste de nœuds sera réduite à un seul élément, la racine, et il suffit de la renvoyer.
 

2. Complétez la méthode `createTree` avec l'algorithme décrit ci-dessus. Elle doit renvoyer la racine de l'arbre.

---

L'arbre de Huffman permet de décoder de façon très pratique, il n'est par contre pas très utile lorsqu'il s'agit de coder.

3. Complétez la méthode `createCode`, qui prend en entrée la racine de
   l'arbre de Huffman, et qui renvoie un tableau de `String`,
   contenant dans l'ordre les codages (binaires) des symboles de 'a' à
   'z'. Pour cette méthode, un algorithme récursif est le plus adapté
   à parcourir l'arbre. Par exemple, vous pouvez ajouter une méthode
   
   ~~~
   private static void traversal(Node node, String code, String[] codes)
   ~~~
   
   qui parcourt tout le sous-arbre en dessous de `node`, en
   remplissant le tableau de sortie `codes`.

Lorsque vous aurez implanté correctement les méthodes `createTree` et `createCode`, la sortie du `main` doit être égale à l'un des deux codes suivants (selon si vous avez assigné 0 à gauche et 1 à droite, ou *vice-versa*):

~~~
a: 10
b: 0
c: 111
d: 110
e: null
...
~~~

ou

~~~
a: 01
b: 1
c: 000
d: 001
e: null
...
~~~

Pour être sûr que votre code est bon, testez avec d'autres jeux de symboles plus grands que 'a', 'b', 'c', 'd'. Pour qu'un code soit valide il ne doit pas y avoir de dupliqués (deux symboles ne peuvent pas être encodés par les mêmes symboles) et, plus généralement, aucun code ne doit être le *préfixe* d'un autre.

<div id="solution" class="show-after" data-show-after="2014-02-19">

Voici la solution de cette partie.

~~~
import java.util.ArrayList;       // Cliquez pour découvrir
import java.util.Collections;

/*
  Cette classe implante le noeud d'un arbre binaire contenant
  contenant les donnees necessaires a l'algorithme de Huffman (le
  symbole encode et sa frequence).
 */
class Node implements Comparable<Node> {
    char symbol;
    double freq;
    Node left, right;

    public Node(char s, double f) {
        this.symbol = s;
        this.freq = f;
        this.left = this.right = null;
    }

    public Node(Node left, Node right) {
        this.symbol = (char)0;
        this.left = left;
        this.right = right;
        this.freq = left.freq + right.freq;
    }
    
    /* 
       Methode implantant l'interface Comparable. Renvoie -1 si la
       frequence de ce noeud est inferieur a celle de other, 1 sinon.
    */
    public int compareTo(Node other) {
        return this.freq < other.freq ? -1 : 1;
    }
}

/*
  Cette classe implante l'algorithme de Huffman
*/
public class Huffman {
    // Quelques alphabets predefinis avec frequences
    /*
    static char[] symbols = {
        'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i',
        'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r',
        's', 't', 'u', 'v', 'w', 'x', 'y', 'z'};
    static double[] freqs = {
        0.03, 0.12, 0.01, 0.06, 0.04, 0.04, 0.04, 0.01,        0.04,
        0.06, 0.04, 0.03, 0.02, 0.02, 0.01, 0.10, 0.01, 0.06,
        0.01, 0.03, 0.01, 0.03, 0.02, 0.03, 0.06, 0.07 };
    */

    static char[] symbols = { 'a', 'b', 'c', 'd' };
    static double[] freqs = { 0.25, 0.45, 0.20, 0.10 };

    /*
      Cette methode construit un arbre binaire de Node correspondant a
      l'arbe de Huffman. En entree il attend un tableau de symboles et
      le tableau des frequences correspondantes. Elle renvoie la
      racine de l'arbre.
     */
    public static Node createTree(char[] symbols, double[] freqs) {
        // Initialise une liste contenant les feuilles de l'arbre
        ArrayList<Node> leaves = new ArrayList<Node>();
        for (int i = 0 ; i < symbols.length ; i++) {
            leaves.add(new Node(symbols[i], freqs[i]));
        }
        
        // Reduit la liste de un element a la fois
        // jusqu'a ce qu'elle soit reduite a la seule racine
        while (leaves.size() > 1) {
            Collections.sort(leaves);
            Node n1 = leaves.remove(0);
            Node n2 = leaves.remove(0);
            leaves.add(new Node(n1, n2));
        }

        // Renvoie la racine de l'arbre
        return leaves.get(0);
    }

    /*
      Cette fonction recursive est utilise par createCode pour
      construire le code a partir de l'arbre.

      node est un noeud quelconque de l'arbre, code est une chaine
      contenant le code du chemin parcouru jusqu'au noeud, codes est
      un tableau qui va etre rempli avec les encodages.
    */
    private static void traversal(Node node, String code, String[] codes) {
        if (node.left == null && node.right == null) {
            codes[node.symbol - 'a'] = code;
        } else {
            traversal(node.left, code + "0", codes);
            traversal(node.right, code + "1", codes);
        }
    }

    /*
      Cette fonction construit la table d'encodage a partir de l'arbre
      de Huffman. La valeur de retour est un tableau de chaines,
      contenant l'encodage (en binaire) de chaque symbole de a jusqu'a
      z.
     */
    public static String[] createCode(Node root) {
        String[] codes = new String[26];
        traversal(root, "", codes);
        return codes;
    }

    public static void main(String[] args) {
        String[] codes = createCode(createTree(symbols, freqs));
        for (int i = 0 ; i < codes.length ; i++) {
            System.out.println((char)(i + 'a') + ": " + codes[i]);
        }
    }
}
~~~
{: .collapsible .collapsed }

</div>


## Lecture des fréquences d'un fichier texte

On voudrait maintenant que les fréquences des lettres nous soient données dans un ficher de texte, plutôt que codées en dur dans le code source. Pour cela nous allons utiliser les classes Java qui permettent la lecture des fichiers en mode texte.

La classe [`java.io.FileReader`](http://docs.oracle.com/javase/6/docs/api/java/io/FileReader.html) permet de lire un fichier caractère par caractère (plutôt qu'octet par octet), en tenant compte de l'encodage du fichier. Combinée avec [`java.io.BufferedReader`](http://docs.oracle.com/javase/6/docs/api/java/io/BufferedReader.html), elle permet de lire directement un fichier ligne par ligne. Voici un exemple de code qui lit un fichier ligne par ligne et l'affiche sur la console.

~~~
BufferedReader in = new BufferedReader(new FileReader("fichier.txt"));

String line;
while ( (line = in.readLine()) != null)
    System.out.println(line);
~~~

La méthode `readline()` renvoie une ligne entière prise du flux, sous forme de `String`. Si le fichier ne contient plus de lignes, elle renvoie `null`.

Nous voulons encoder les fréquences dans un fichier obéissant au format suivant.

~~~
a: 0.0133
b: 0.123
c: 0.001
...
~~~

Un symbole par ligne, suivi de '`: `' et de sa fréquence.

1. Modifiez `Huffman.java` en sorte que les symboles et les fréquences soient pris d'un fichier passé par la ligne de commande.

Pour découper une chaîne de caractères vous pouvez utiliser les méthodes de [`String`](http://docs.oracle.com/javase/6/docs/api/java/lang/String.html) :

- `charAt`, qui donne le caractère à une position donnée,
- `substring`, qui donne la partie de la chaîne entre une position de départ et une position de fin,
- `split`, qui découpe une chaîne en utilisant un séparateur,
- etc...

Pour convertir une chaîne de caractères en `double`, vous pouvez utiliser [`Double.parseDouble()`](http://docs.oracle.com/javase/6/docs/api/java/lang/Double.html).

---

2. Modifiez votre code pour supporter d'autres symboles que ceux de 'a' à 'z' (potentiellement, tous les caractères Unicode !).

