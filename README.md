# Libasm-Linux

# I - Comprendre le sujet
#### En gros y a différentes composantes dans ton ordi :
- la ram (= mémoire ram ou mémoire vive)
- le stockage
- la carte graphique
- MAIS SURTOUT : le processeur (= le CPU) : c'est le cerveau de l'ordinateur : il calcule tout. Et y a différents types de processeurs, qui utilisent différents types de jeu d'instruction.

#### Et c'est justement le processeur qui exécute les programmes qu'on code, sauf qu'il comprend que le langage binaire.

Assembleur = ensemble de langages de programmation. L’asm est la représentation lisible du langage binaire que comprend le processeur. Y a un langage assembleur par jeu d’instructions. 

Jeu d'instruction =  c’est les commandes que peut faire le processeur (jeu d’instruction ARM, X86, X64...)

ASM 64 bits = langage assembleur adapté au processeur ayant pour jeu d’instruction X64.

Asm inline = l'assembleur inline permet d'incorporer des instructions en langage assembleur directement dans des programmes sources C sans code assembleur ni étapes de liaison supplémentaires. 

Nasm = assembleur à utiliser pour tes fichiers .s

# II - Comment ai-je fait Libasm ? 5 étapes

## étape 1  : Comprendre les bases
#### Documentation :
Meilleure documentation à mes yeux. Elle utilise le format as et non Intel mais les deux se ressemblent beaucoup : [ici](https://perso.univ-st-etienne.fr/ezequel/L2info/coursAssembleur_x86_64.pdf). Deux autres documentations pas mal : [celle-ci](http://asmongueur.free.fr/Apprendre/Nasm/Intro_Nasm_Linux.htm) et [celle-la](https://www.lacl.fr/tan/asm).

Un code assembleur agit directement sur le processeur - c'est à dire que tu ne peux manipuler que deux choses : les registres, et la mémoire. 
Un registre c'est quoi ? C'est un entier de 32 bits ou de 64 bits selon ton architecture. Notamment, un registre ne peut pas contenir une chaine de caractères : "Hello world!" ça fait 13 octets ! Beaucoup trop gros pour rentrer dans un registre.

Dans tes fonctions en assembleur tu vas utiliser des instructions, manipuler des registres, utiliser des segments, et des drapeaux.

#### Segments, registres, drapeaux :

| SEGMENTS | REGISTRES | DRAPEAUX |
|----------|-----------|----------|
|  Sorte de boîte où on va mettre des instructions.                   | Petite zone de stockage d’accès très rapide située dans le microprocesseur qui a une fonction particulière     |  De longueur 64 bits, dont seuls les 32 premiers sont utilisés. Chaque bit porte une information sur l’état du processeur, ou sur le résultat de la dernière opération.| 
| **.bss** = on met les variables qui ne sont pas initialisées (comme int a) **.data** = on y met les variables initialisées **.text** = on y met les instructions, le code exécutable |  **rsp** = contient l’adresse de la donnée qui se trouve au sommet de la pile. **rax** = registre général, accumulateur, contient la valeur de retour des fonctions. **rbx** = registre général. **rcx** = registre général, compteur de boucle. **rdx** = registre général, partie haute d’une valeur 128 bits. **rsi** = registre général, adresse source pour déplacement ou comparaison. **rdi** = registre général, adresse destination pour déplacement ou comparaison. **rsp**  = registre général, pointeur de pile (stack pointer). **rbp** = registre général, pointeur de base (base pointer). **r8,r9, et r15** = registres généraux | **ZF** = Zero Flag (bit 6) vaut 1 lorsque le résultat de la dernière opération est 0. **SF** = Vaut 1 si le résultat de la dernière opération est négatif. ...|

#### Instructions :
| Au niveau de la pile  | Déplacement de données  | Arithmétique  | Comparaison et branchement|
|----------|-------|----------|---------|
| **push** = ajoute une donnée sur la pile de taille qword (64 bits) (au sommet de la pile). décrément rsp de 8. **pop** = retire la donnée de taille qword qui se trouve au sommet de la pile. incrémente rsp de 8. | **mov opérandecible, opérandesource** = copier opérande source dans une opérande destinataire | **add op1,op2** = op1 ← op1 + op2 (=on met le résultat de op1 + op2 dans dans op1). **sub op1, op2** = op1 ← op1 − op2. **neg reg** = reg ← −reg (reg = registre). **inc reg** = reg ← reg + 1. **dec reg** = reg ← reg − 1. | **cmp op1,op2** = compare deux opérandes. Pour cela fait op1 − op2. si = à 0 c’est les mêmes, si != 0 c’est pas les mêmes, où drapeau ZF vaut 1 lorsque le résultat est 0. jmp op : branchement inconditionnel à l’adresse op. **jz op** = branchement à l’adresse op si ZF=1. **jnz op** = branchement à l’adresse op si ZF=0. **jo op** = branchement à l’adresse op si OF=1. **jno op** = branchement à l’adresse op si OF=0. **js op** = branchement à l’adresse op si SF=1 ...|

#### Paramètres envoyés à notre fonction assembleur :
ft_example(param1, param2, param3, param4,param5, param6)
- param1 sera stocké dans rdi
- param2 dans rsi
- param3 dans rdx
- param4 dans rcx 
- param5 dans r8 
- param6 dans r9

#### Syntaxe :
  ```
  extern   *fonction*      ; permet de dire au compilateur que l'on va appeler une fonction extérieure à notre programme, par exemple : extern malloc
  global   *fonction*      ; pour déclarer une fonction, par exemple : global ft_strlen
  ; commentaire
  segment.                 ; pas obligatoire d’écrire les segments, par exemple data.
  étiquette:               ; par exemple _ft_strlen:
  inst   dest, src, last   ; instruction opérande de destination, opérande cible, par exemple : mov rax, rdi
  [UnSymbole]              ; adresse mémoire du symbole
  UnSymbole                ; valeur du symbole
  [adresse]                ; représente la valeur stockée à l'adresse adresse.
  [Registre]               ; représente la valeur stockée à l’adresse contenue dans le registre
  [01234ABC]               ; emplacement mémoire absolue
  BYTE[]                   ; parfois on précise la taille pour lever l'ambiguïté (byte : 1 octet, word 2 : octets, dword : 2 octets ...)

   ```

## étape 2  : La compilation
installer nasm : 
   ```
brew install nasm
   ```
Imaginons que j’ai un fichier main.c un fichier test.s et un fichier libasm.h

   ```
nasm -f macho64 -o test.o test.s
gcc main.c test.o libasm.h
   ```
Sur Linux tu peux compiler avec le flag : -felf64. 
- -elf64 = c'est outpout file format pour Linux
- -f = si tu mets pas le -f à NASM il va choisir un outpout file format tout seul

Sur MacOs tu peux compiler avec le flag : -fmacho64

## étape 3  : Les fonctions
#### ft_strlen.s : size_t strlen(const char *s);
| A faire | Informations |
|----------|-----------|
| initialiser une variable à 0 ; compare si s[variable] et 0 sont égaux, si sont égaux ; return variable ; sinon je continue à looper avec jmp | rdi = char *s ; xor *registre1*, *registre1* permet de mettre la valeur 0 dans le registre *registre1* ; cmp op1,op2 =  fait op1 - op2. Si = 0 ça veut dire que op1 = op2 et alors ZF (drapeau) vaut 1 ; jz op = si ZF=1 on va à op ; inc = incrémente |

   ```
xor rcx, rcx            ; pour fixer la valeur 0 à un registre : mauvaise pratique d’utiliser l'instruction «MOV». Ainsi, l'instruction xor eax, eax occupera 2 octets contre 6 pour move eax, 0
cmp BYTE[rdi + rax], 0  ; [ceRegistre] représente la valeur stockée à l’adresse contenue dans le registre ceRegistre.
jmp loop                ; j’avais d'abord utilisé l’instruction loop mais son utilisation avec rcx décrémente rcx.
mov rax, rcx            ; on copie le compteur rcx dans rax car c'est rax qu'on renvoie
ret                     ; retourne rax
   ```

#### ft_strcpy : char *strcpy(char *dest, const char *src);
copie la chaîne pointée par src (y compris l'octet nul « \0 » final) dans la chaîne pointée par dest. Les deux chaînes ne doivent pas se chevaucher. La chaîne dest doit être assez grande pour accueillir la copie.

| A faire | Informations |
|----------|-----------|
| initialiser une variable à 0 ; compare si src[variable] et 0 sont égaux ; si sont égaux : return dest ; sinon je continue ; je copie src[variable] dans dest[variable] ; je loop avec jmp | rdi = char *dest ; rsi = char *src |

   ```
mov dl, [rsi + rax]       ; copie le caractère à copier (rsi[rax]) dans dl, avec mov : les deux opérandes doivent être de la même taille donc on utilise dl qui fait 1 octet, L'opérande source peut être  : une valeur immédiate, un registre à usage général, un registre de segment ou un emplacement de mémoire, Le registre de destination peut être un registre à usage général, un registre de segment (CS, DS, ES, FS, GS et SS) ou un emplacement de mémoire 
mov [rdi + rax], dl       ; copie le caractere à copier qui est dans dl dans rdi[rcx]
mov byte [rdi + rcx], 0   ; on ajoute le 0 final
mov rax, rdi              ; on met dans rax la char * qu'on renvoie
   ```







#### Les appels systèmes dans :
- ft_write
- ft_read
- ft_strdup
quand tu fais un printf, ce que fait vraiment printf dans son code, c'est faire des syscall à write() en fait
Les appels systèmes en assembleur :
Chaque appel système possède un numéro, qui est placé dans RAX.
Le système utilise sa propre pile, la pile du processus appelant n’est pas modifiée.
Les registres sont inchangés, sauf peut-être RCX et R11, RAX contient retour du syscall.



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
