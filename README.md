# Libasm-Linux

### Plan :
#### I - Comprendre le sujet
#### II - Comment ai-je fait Libasm ? 5 étapes
 - étape 1  : Comprendre les bases
 - étape 2  : La compilation
 - étape 3  : Les fonctions
 - étape 4  : Gestion des erreurs
 - étape 5  : Adapter à Linux
#### III - Les trucs utiles que j'ai appris
 - VIM
 - Rappels sur le Makefile

# I - Comprendre le sujet
assembleur = ensemble de langages de programmation. La langue de votre ordi c’est le codage binaire. L’asm est la représentation lisible du langage machine (binaire). 
un langage assembleur par architecture de processeur. Il y a des familles de processeurs avec chacun une architecture, et donc un jeu d’instructions. 
ASM 64 bits?? C’est le langage assembleur adapté au processeur ayant pour jeu d’instruction X64.
le jeu d’instruction X64?? x86-64, ou x64, est une extension du jeu d'instructions x86 d'Intel
asm inline?? L'assembleur inline vous permet d'incorporer des instructions en langage assembleur directement dans vos programmes sources C sans code assembleur ni étapes de liaison supplémentaires. 
nasm??
syntaxe Intel?? On distingue 2 syntaxes : Intel et at&t

# II - Comment ai-je fait Libasm ? 5 étapes

## étape 1  : Comprendre les bases
## étape 2  : La compilation
nasm : 
Le outpout file format sur linux c’est : elf64
Et on fait -f avant pour : f you do not supply the -f option to NASM, it will choose an output file format for you itself. In the distribution versions of NASM, the default is always bin

## étape 3  : Les fonctions
appels systèmes dans :
ft_write
ft_read
ft_strdup
quand tu fais un printf, ce que fait vraiment printf dans son code, c'est faire des syscall à write() en fait
Les appels systèmes en assembleur :
Chaque appel système possède un numéro, qui est placé dans RAX.
Le système utilise sa propre pile, la pile du processus appelant n’est pas modifiée.
Les registres sont inchangés, sauf peut-être RCX et R11, RAX contient retour du syscall.

Un code assembleur agit directement sur le processeur - c'est à dire que tu ne peux manipuler que deux choses : les registres, et la mémoire.

Un registre c'est quoi ? C'est un entier de 32 bits ou de 64 bits selon ton architecture. Notamment, un registre ne peut pas contenir une chaine de caractères : "Hello world!" ça fait 13 octets ! Beaucoup trop gros pour rentrer dans un registre.

extern permet de dire au compilateur que l'on va appeler une fonction extérieure à notre programme.



## étape 4  : Gestion des erreurs
quand tu fais un appel système a write c’est pas la même que quand t’appelle la fonction write. l’appel système te renverra la valeur de errno (par exemple -14 avec un NULL) et pas -1 comme quand t’appelle la fonction write. l’appel system te renverra un int négatif, c’est la valeur qu’il faut envoyer a errno mais en négatif. d'où la raison pour laquelle il faut passer le retour de l’appel système en positif. sr
errno c’est un int il 
errno tu peux l’imprimer dans ton main

errno_location sur linux
 ___error sur mac
 
 errno location retourne un pointeur sur errno dans rax
 <errno.h>
int * __errno_location(void);
The __errno_location() function shall return the address of the errno variable for the current thread.

la variable entière errno qui est renseignée par les appels système (et quelques fonctions de bibliothèque) pour expliquer les conditions d'erreurs. Sa valeur n'est significative que lorsque l'appel système a échoué (généralement en renvoyant -1)

errno contient le code d’erreur


## étape 5  : Adapter à Linux
Adapter a Linux :
enlever l'underscore sur les fonctions
compiler avec : -felf64

# III - Les trucs utiles que j'ai appris