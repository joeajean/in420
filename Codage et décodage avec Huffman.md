---
layout: post
title: Codage et décodage avec Huffman
---

Ce DM constitue la suite du TD [Construction du code de Huffman](Construction du code de Huffman). Vous êtes libres d'utiliser le code que vous avez écrit pour le TD, y compris la solution qui y est fournie.

Le but de ce DM est d'écrire un programme permettant le codage et le décodage de textes par le [code de Huffman](Codage de Huffman). Le texte à coder/décoder, ainsi que les fréquences d'apparition des symboles sont des entrées pour le programme. Pour simplicité, nous allons utiliser seulement les caractères (ASCII) de 'a' à 'z'.

Plus précisément, votre programme doit:

- Lire en entrée un fichier contenant les fréquences des symboles, par exemple dans le format du TD [Construction du code de Huffman](Construction du code de Huffman).
- Générer le code de Huffman correspondant aux fréquences fournies (déjà fait en TD).
- Offrir une option pour lire un fichier au format ASCII et l'encoder avec le code de Huffman.
- Offrir une option pour lire un fichier binaire et le décoder en un fichier ASCII avec le code de Huffman.
- Décoder correctement le [challenge](#challenge) fourni à la fin du sujet.


Dans la suite on va suggérer une façon de structurer votre code, cependant vous n'êtes pas obligés de la suivre à la lettre, tant que votre programme atteint le but recherché.


## Flux de bits

La lecture/écriture d'un fichier ASCII peut se faire aussi bien en mode binaire (avec [`FileInputStream`](http://docs.oracle.com/javase/6/docs/api/java/io/FileInputStream.html) et [`FileOutputStream`](http://docs.oracle.com/javase/6/docs/api/java/io/FileOutputStream.html)), qu'en mode texte (avec [`FileReader`](http://docs.oracle.com/javase/6/docs/api/java/io/FileReader.html) et [`FileWriter`](http://docs.oracle.com/javase/6/docs/api/java/io/FileWriter.html)). Allez voir les derniers TDs et la page [Entrées-Sorties](Entrées-Sorties en Java) en Java pour l'usage de ces classes.

Au contraire, l'encodage de Huffman est à longueur variable, et basé sur les bits. Il n'est donc pas commode de lire des fichiers encodés avec un code de Huffman en utilisant les classes par défaut de Java. La première étape consiste à créer des classes permettant de lire ou écrire un flux de données bit par bit.

Un *encapsuleur* (*wrapper* en anglais) est une classe qui prend en entrée de son constructeur un objet et qui en étend les fonctionnalités. Des exemples classiques d'encapsuleurs en Java sont [`BufferedReader`](http://docs.oracle.com/javase/6/docs/api/java/io/BufferedReader.html) et [`BufferedWriter`](http://docs.oracle.com/javase/6/docs/api/java/io/BufferedWriter.html), qui encapsulent respectivement `FileReader` et `FileWriter`, comme dans l'exemple suivant.

~~~
BufferedReader in = new BufferedReader(new FileReader("in.txt"));
BufferedWriter out = new BufferedWriter(new FileWriter("out.txt"));
~~~

Écrire une classe `InputBitStream` qui encapsule un `InputStream`. Elle utilise le `InputStream` sous-jacent en lisant octet par octet, mais offre une interface permettant de lire un à la fois les bits de chaque octet. Elle pourra contenir, par exemple, les méthodes suivantes

- Un constructeur
  
  ~~~
  public InputBitStream(InputStream in)
  ~~~
  
  qui prend en entrée le `InputStream` à encapsuler.

- Une méthode 
  
  ~~~
  public int readBit()
  ~~~
  
  qui renvoie le bit suivant dans le flux (0 ou 1), ou -1 s'il n'y a plus de données dans le flux.


Dans le même esprit, écrire une classe `OutputBitStream` qui encapsule un `OutputStream`. Elle pourra contenir, par exemple, les méthodes suivantes

- Un constructeur
  
  ~~~
  public OutputBitStream(OutputStream out)
  ~~~
  
  qui prend en entrée le `OutputStream` à encapsuler.

- Une méthode 
  
  ~~~
  public void writeBit(int bit)
  ~~~
  
  qui écrit un bit dans le flux de sortie. Les bits à écrire doivent être gardés dans un tampon jusqu'à atteindre 8 bits, moment auquel un octet complet pourra être écrit dans le `OutputStream`.

- Une méthode
  
  ~~~
  public void close()
  ~~~
  
  qui écrit les bits restants dans le `OutputStream`, même si le tampon n'a pas atteint les 8 bits (il sera opportun, dans ce cas, d'ajouter assez de zéros pour arriver à un octet complet ; voir aussi [Endianness](Endianness)). Après une `close`, aucune autre écriture dans le flux devrait être possible.

Pour le stockage des bits à l'intérieur d'un octet, vous êtes libres de choisir la [Endianness](Endianness) que vous préférez.

Avant de passer à la suite, il est conseillé de bien débugguer cette partie en vérifiant que la lecture et l'écriture bit par bit d'un fichier de texte réalisent une copie parfaite. Pour vous aider à débugguer cette partie du code, vous pouvez utiliser un outil de lecture de fichiers binaires. Sous Linux et MacOS, la commande `hd` fournit un tel outil. Sous Windows vous pouvez installer [Hexdump](http://www.richpasco.org/utilities/hexdump.html).


## Encoder et décoder

Le pire est passé. Maintenant que vous avez à disposition des flux de bits, encoder revient à 

- Créer le tableau d'encodage avec la méthode `createCode` du TD [Construction du code de Huffman](Construction du code de Huffman).
- Lire le fichier en entrée, caractère par caractère, avec un `FileReader`.
- Écrire le fichier de sortie, bit par bit, avec un `OutputBitStream`.

Décoder demande à peine plus d'effort :

- Créer l'arbre de décodage avec la méthode `createTree` du TD [Construction du code de Huffman](Construction du code de Huffman).
- Lire le fichier en entrée, bit par bit, avec un `InputBitStream`.
- Écrire le fichier de sortie, caractère par caractère, avec un `FileWriter`.

Le décodage est légèrement plus compliqué que l'encodage. L'algorithme consiste à parcourir l'arbre de décodage en accord avec les bits provenants du `InputBitStream` (0 correspond à aller à gauche, 1 à droite). Lorsqu'on atteint une feuille, on écrit le symbole correspondant sur la sortie.

Finalisez votre code avec un `main` qui permet de choisir le mode d'opération (encodage ou décodage), et qui prend en paramètre les noms des fichiers à lire/écrire.


## Challenge

Visitez [cette page](http://swift.prism.uvsq.fr/py/in420-dms13.py/huffman) (testé avec Firefox 10 et Chrome 24) et décodez le message fourni en suivant les instructions.

Envoyez votre code source, ainsi que le texte décodé à l'aide de la boîte de dépot sur [e-campus 2](http://e-campus2.uvsq.fr/) (si e-campus ne devait pas marcher, envoyez-les directement par mail à votre [enseignant](http://www.prism.uvsq.fr/~dfl). La date limite pour envoyer vos fichiers est le mercredi 6 mars à 4h du matin. Un point de pénalité pour chaque heure de retard: le 6 mars à 23h59 c'est votre dernière chance !

