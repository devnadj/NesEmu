# NESEmu

- [NESEmu](#nesemu)
- [Introduction](#introduction)
- [Recherche de documentation](#recherche-de-documentation)
- [Entrailles de la console](#entrailles-de-la-console)
- [Schémas de la console](#schémas-de-la-console)
  - [La puce Ricoh 2A03/2A07 (Processeur + APU)](#la-puce-ricoh-2a032a07-processeur--apu)
  - [La puce Ricoh 2C02 (PPU)](#la-puce-ricoh-2c02-ppu)
- [Émulation](#émulation)
  - [Le processeur 6502](#le-processeur-6502)
    - [Diagrammes](#diagrammes)
    - [Les registres du 6502](#les-registres-du-6502)
      - [Le registre d'état](#le-registre-détat)
      - [L'accumulateur](#laccumulateur)
    - [Les modes d'adressage](#les-modes-dadressage)
      - [Adressage](#adressage)
      - [Adressage immédiat](#adressage-immédiat)
      - [Adressage absolue](#adressage-absolue)
      - [Adressage absolu](#adressage-absolu)
    - [Les vecteurs d'interruption](#les-vecteurs-dinterruption)
      - [Reset](#reset)
      - [IRQ](#irq)
    - [NMI](#nmi)
    - [Les instructions du 6502](#les-instructions-du-6502)
      - [**ADC (ADd with Carry) - Addition avec retenue**](#adc-add-with-carry---addition-avec-retenue)
      - [**AND - Et bit à bit avec l'accumulator**](#and---et-bit-à-bit-avec-laccumulator)
      - [**ASL (Arithmetic Shift Left)**](#asl-arithmetic-shift-left)
      - [**BIT (test BITs)**](#bit-test-bits)
      - [**Instructions de branchement**](#instructions-de-branchement)
    - [Émulation du 6502](#émulation-du-6502)
  - [La puce sonore du 2A03/2A07](#la-puce-sonore-du-2a032a07)
    - [Émulation de la puce sonore](#émulation-de-la-puce-sonore)
  - [Le PPU](#le-ppu)
    - [Émulation du PPU](#émulation-du-ppu)
- [Les cartouches](#les-cartouches)
- [Les manettes](#les-manettes)

# Introduction

La Nintendo Entertainment System (NES) est une console de jeux vidéo 8 bits de troisième génération produite par Nintendo. Elle a été commercialisée pour la première fois au Japon en 1983 sous le nom de Family Computer (FC), communément appelée **Famicom**. La NES, une version redessinée, a été lancée sur les marchés tests américains le 18 octobre 1985, avant de devenir largement disponible en Amérique du Nord et dans d'autres pays.

|     ![Famicom](img/Famicon.png?raw=true)     |
| :------------------------------------------: |
| **Famicom : lancée le 15/07/1983 au Japon.** |

|                 ![NES](img/NES-Console-Set.png?raw=true)                  |
| :-----------------------------------------------------------------------: |
| **NES : lancée le 18/10/1985 aux États-Unis et le 01/09/1985 en Europe.** |

# Recherche de documentation

La chose la plus importante avant de commencer à écrire la moindre ligne de code, est de rechercher de la documentation sur la console. Les sources d'informations sont légion, mais pas toujours très claires et souvent très disparates. Il est donc nécessaire d'analyser les entrailles de la console pour en étudier les composants principaux. Pas besoin pour cela de posséder la console, d'autres l'ont fait bien avant nous.

# Entrailles de la console

| ![PCB](img/motherboard.png?raw=true)  |
| :-----------------------------------: |
| **Carte mère de la NES version NTSC** |

| ![PCB](img/Famicom-motherboard.jpg?raw=true) |
| :------------------------------------------: |
|         **Carte mère de la Famicom**         |

# Schémas de la console

Dans cette partie nous survolons les composants de la NES. Ils seront étudiés bien plus en détail dans la partie concernant chacun d'entre eux et aussi dans leur émulation.

| ![Schéma de la console](/img/nes-diagramme.png?raw=true) |
| :------------------------------------------------------: |
|              **Schéma simplifié de la NES**              |

## La puce Ricoh 2A03/2A07 (Processeur + APU)

La puce 2A03/2A07 a été créée par Ricoh et intègre un processeur et un puce de génération sonore. Ce processeur est basé sur le 6502 (connu notamment pour s'être retrouvé dans les Apple I & II ou le Commodore 64) et la puce sonore est appelée APU pour Audio Processing Unit (soit unité de traitement audio).

|               |                     |                     |
| ------------- | :-----------------: | :-----------------: |
|               |   **Version PAL**   |  **Version NTSC**   |
| **Puce**      | Ricoh 2A07 / RP2A03 | Ricoh 2A07 / RP2A07 |
| **Fréquence** |      1,66 MHz       |      1,79 MHz       |

Le processeur qui compose le 2A03/2A07 ne possède pas le mode BCD alors que celui-ci est présent dans le 6502. C'est la seule différence entre les deux puces.

## La puce Ricoh 2C02 (PPU)

La puce graphique de la NES génère un signal composite de 240 lignes de pixels, destiné à être reçu par un téléviseur.

# Émulation

## Le processeur 6502

Comme nous l'avons vu, la puce 2A03/2A07 est basée pour la partie processeur sur le fameux 6502 castré de sa partie BCD. Pour émuler le processeur de la NES, il est donc évident de se baser sur le 6502. D'ailleurs la littérature sur ce processeur est très abondante, et nous ne manquerons pas de ressources. Par ailleurs, nous l'émulerons complètement (mode BCD compris), ce qui sera utile si nous décidons de travailler sur d'autres machines à l'avenir composées de ce processeur.

Le 6502 est un processeur 8 bits, c'est-à-dire que sont bus de données est de 8 bits et qu'il traite des mots de 8 bits. L'ensemble des opérations arithmétiques et logiques du processeur travaillent sur des mots de 8 bits. Le bus d'adresse quant à lui est de 16 bits, de ce fait le processeur est capable d'adresser au maximum 2<sup>16</sup> = 65 536 positions mémoires.

### Diagrammes

### Les registres du 6502
#### L'accumulateur (A)
L'accumulateur est un registre huits bits. Un certain nombre d'opérations arithmétiques ou logiques sont exclusives à l'accumulateur, en l'occurence : ADC, SBC, AND, ORA, EOR.

#### Le registre d'état (P)
Le registre d'état est un registre de huits bits. Il est mis à jour automatiquement à jour au fur et à mesure de l'exécution des instructions. Pour cela, chacun des bits le constituant a une signification particulière. On appelera drapeau chacun des bits signifcatifs du registre d'état.


| Bit |  7  |  6  |  5  |  4  |  3  |  2  |  1  |  0  |
|  -  |  -  |  -  |  -  |  -  |  -  |  -  |  -  |  -  |
|  Drapeau   |  N  |  V  |  *  |  B  |  D  |  I  |  Z  |  C  |

- #### Drapeau C (Carry)
  Ce drapeau permet d'indiquer qu'une opération (ADC, SBC, CMP, ASL, LSR, ROL, ROR) a généré une retenue. Ce drapeau peut être modifié directement avec les instruction SEC (SEt Carry) et CLC (CLear Carry).

- #### Drapeau Z (Zero)
  Ce drapeau permet d'indiquer que le resultat d'une opération est égal à zéro.

- #### Drapeau I
  lorsque ce drapeau est positionné à 1, toutes les interruptions à l'exception de NMI sont inhibées. Ce drapeau est modifiable grâce aux instructions SEI (Set Interrupt Disable) et CLI (Clear Interrupt Disable). Tant que ce drapeau est a

- #### Drapeau D
  Ce drapeau est utilisé pour sélectionner le mode binaire codé décimal (BCD : Binary Coded Decimal) pour faciliter les opération arithmétique en base 10. Comme déjà précisé plus haut, le processeur de la NES, le mode décimal est désactivé, mais il sera tout de même implémenter.

- #### Drapeau B (Break)

- #### Drapeau V (oVerflow)
  Ce drapeau est positionné à 1 lors d'un débordement lors d'une opération (en général une opération d'addition ou de soustraction). Nous entrerons plus en détail plus loin lorsque nous devrons générer le code permettant de positionner où non ce drapeau lors d'une opération.

- #### Drapeau N (Negative)
  Ce drapeau est positionné à 1 lorsque le bit 7 résultantt d'une opéaration logique ou arithmétique passe à 1, c'est à dire si le résultat d'une opération logique ou mathématique est négatif. Pour rappel un nombre est considéré comme négatif lorsque le bit le plus à gauche de ce nombre est égal à 1. Ici nous parlons de mot de 8 bits, donc lorsque le bit 7 est à 1.

Nous entrerons plus en détail sur le fonctionnement de chacun de ces drapeaux lors du codage des instructions, celles-ci les modifiant allégrement.

### Le compteur de programme (PC)

Ce registre de 16 bits contient l'adresse physique en cours d'exécution. Il est mis à jour automatiquement lors du déroulement d'un programme, ou il est peut être modifié directement après une interruption (NMI, Reset, IRQ / BRK) soit en utilisant une instruction de branchement RTS, JMP, JSR, Branch... Sa taille étant de 16 bits, il est possible d'adresser au maximum 65 536 octets (2<sup>16</sup>).

### Le pointeur de pile (S)
Le 6502 prend en charge une pile de 256 octets située entre 0100 et 01FF. Ces deux valeurs sont fixées et ne peuvent pas être modifiées. La pile permet de sauvegarder diverses données lors d'une interruption, ou de sauvegarder l'adresse courante lors d'un saut à sous-programme. Cela permet au processeur lors du retour de ce sous-programme de continuer le déroulement à l'adresse d'origine.
Le pointeur de pile est un registre de 8 bits et contient le prochain emplacement libre sur la pile. Lorsque la pile est vide, la valeur du pointeur de pile est de 00, et pointe donc sur l'adresse 01FF (01FF - 00). Lorsque la pile est pleine, la valeur du pointeur de pile est de 255 et celui-ci point alors sur l'emplacement 0100 (01FF - FF). Pousser des éléments sur la pile entraine la décrémentation du pointeur de pile, et à l'inverse extraire des éléments sur la pile entraine l'incrémentation du pointeur de pile. Attention toutefois, tout débordement de pile n'est pas indiqué par le processeur et peut créer des erreurs.

### Instructions, opérateurs, opérandes, opcode

En assembleur, il y a des opérations et des opérandes. Chaque opération est en général associé à un opérande qui est une variable sur laquelle agit l'opération. L'instruction suivante est plus explicite.

- LDA #25 ; 

LDA est l'opération qui permet de charger une variable dans l'accumulateur. #25 est l'opérande, c'est-à-dire la valeur qu'utilise l'opération pour effectuer cette tâche. Le processeur 6502 est fournit avec un ensemble d'opérations (opcodes). Cela nous conduit au mode d'adressage. En effet si l'instruction précédente permettait de charger une valeur immédiatement dans l'accumateur, il est souvent nécessaire de charger l'accumulateur à partir d'un emplacement mémoire. Pour cela il existe un certain nombre de modes d'adressage.


### Les modes d'adressage

Le mode d'adressage est un aspect important d'un microprocesseur et de son jeu d'instructions. Les modes d'adressage définissent la manière dont une opération a accès à son opérande.

- #### Adressage immédiat

  La valeur qui suit l'opcode est utilisée comme opérande. 

  Exemples 

  - LDA #3C ; la valeur 3C est chargée dans l'accumulateur
  - ADC #22 ; la valeur 22 est additionnée au contenu de l'accumulateur

  Pour préciser qu'il s'agit de l'adressage immédiat, la valeur qui suit immédiatement l'instruction est précédée du signe #.

- #### Adressage implicite

  Ce mode d'adressage n'utilise pas d'opérande mais seulement un opcode qui se suffit à elle même. Toutes les instructions de manipulation de drapeaux sont des instructions implicites puisqu'elles n'ont pas besoin d'opérande pour fonctionner.

  Exemples

  - INX ; incrémenter de 1 le contenu du registre X
  - NOP ; ne rien faire
  - CLC ; on efface le bit de retenue

#### Adressage absolu

L'adressage absolu consiste à récupérer la valeur (8 bits) située à l'adresse (16 bits) qui suit l'opcode

Exemple :

Mise en situation - soit le contenu mémoire compris entre 12FE et 1301

| Adresse | Contenu |
|:-------:| :-----: |
| 12 FE   | 1E      |
| 12 FF   | 2F      |
| 13 00   | 31      |
| 13 01   | 00      |

Ensuite on décide d'exécuter les deux instructions suivantes :

- LDA 12FF
- LDX 1300

LDA charge dans l'accumulateur la valeur située à l'adresse 12FF, et LDX charge dans le registre X la valeur situé à l'adresse 1300.
Après l'exécution de ces deux instructions, l'accumulateur A contiendra 2F et le registre d'index X contiendra 31.

#### Adressage en page zéro

L'adressage en page zéro est très similaire à l'adressage absolu. Dans l'adressage absolu, l'opcode est suivi de deux octets qui donne l'adresse à laquelle récupérer la valeur à utiliser dans tout l'espace adressable disponible. L'adressage en page zéro va se limiter à récupérer la valeur situé entre les adresses 00 00 et 00 FF. Comme la plage d'adresse n'est que de 256 positions possibles (soit 8 bits), au lieu de fournir comme ce sertait le cas avec l'adressage absolu une valeur sur 16 bits, ici on se limitera à une seule valeur de 8 bits. Le fait de n'avoir à récupérer qu'un seul octet au lieu de deux, se traduit par un code plus court et une augmentation significative de son efficacité.

Mise en situation - soit le contenu mémoire compris entre 00 1E et 00 21

| Adresse | Contenu |
|:-------:| :-----: |
| 00 1E   | 1E      |
| 00 1F   | 2F      |
| 00 20   | 31      |
| 00 21   | 00      |

Ensuite on décide d'exécuter les instructions suivantes :

- LDA 1F
- LDX 20

Le contenu de l'accumulateur sera de 2F et le contenu du registre d'index X sera de 31.



### Les vecteurs d'interruption

#### Reset

#### IRQ

### NMI

### Les instructions du 6502

#### **ADC (ADd with Carry) - Addition avec retenue**

Drapeaux affectés : N V Z C

|             |             |         |            |            |
| ----------- | ----------- | ------: | ---------: | ---------: |
| **Mode**    | **Syntaxe** | **HEX** | **Taille** | **Cycles** |
| Immediate   | ADC #$44    |     $69 |          2 |          2 |
| Zero Page   | ADC $44     |     $65 |          2 |          3 |
| Zero Page,X | ADC $44,X   |     $75 |          2 |          4 |
| Absolute    | ADC $4400   |     $6D |          3 |          4 |
| Absolute,X  | ADC $4400,X |     $7D |          3 |         4+ |
| Absolute,Y  | ADC $4400,Y |     $79 |          3 |         4+ |
| Indirect,X  | ADC ($44,X) |     $61 |          2 |          6 |
| Indirect,Y  | ADC ($44),Y |     $71 |          2 |         5+ |

- ajouter un cycle si la limite de page est franchie

Les résultats de l'instruction ADC dépendent de la position de l'indicateur décimal. En mode décimal, l'addition est effectuée en supposant que les valeurs concernées sont emballées en BCD (Binary Coded Decimal).
Il n'y a aucun moyen d'additionner sans retenue.

#### **AND - Et bit à bit avec l'accumulator**

Drapeaux affecté : N Z

|             |             |         |            |            |
| ----------- | ----------- | ------- | ---------: | ---------: |
| **Mode**    | **Syntaxe** | **HEX** | **Taille** | **Cycles** |
| Immediate   | AND #$44    | $29     |          2 |          2 |
| Zero Page   | AND $44     | $25     |          2 |          3 |
| Zero Page,X | AND $44,X   | $35     |          2 |          4 |
| Absolute    | AND $4400   | $2D     |          3 |          4 |
| Absolute,X  | AND $4400,X | $3D     |          3 |         4+ |
| Absolute,Y  | AND $4400,Y | $39     |          3 |         4+ |
| Indirect,X  | AND ($44,X) | $21     |          2 |          6 |
| Indirect,Y  | AND ($44),Y | $31     |          2 |         5+ |

- ajouter un cycle si la limite de page est franchie

#### **ASL (Arithmetic Shift Left)**

Drapeaux affectés : N Z C

|             |             |         |            |            |
| ----------- | ----------- | ------: | ---------: | ---------: |
| **Mode**    | **Syntaxe** | **HEX** | **Taille** | **Cycles** |
| Accumulator | ASL A       |     $0A |          1 |          2 |
| Zero Page   | ASL $44     |     $06 |          2 |          5 |
| Zero Page,X | ASL $44,X   |     $16 |          2 |          6 |
| Absolute    | ASL $4400   |     $0E |          3 |          6 |
| Absolute,X  | ASL $4400,X |     $1E |          3 |          7 |

ASL décale tous les bits d'une position vers la gauche. Le 0 (bit Z du redistre d'état) est décalé dans le bit 0 et le bit 7 d'origine est décalé dans le Carry (bit C du registre d'état).

#### **BIT (test BITs)**

Affecte les drapeaux : N V Z

|           |             |         |            |            |
| --------- | ----------- | ------- | ---------: | ---------: |
| **Mode**  | **Syntaxe** | **HEX** | **Taille** | **Cycles** |
| Page zéro | BIT $44     | $24     |          2 |          3 |
| absolu    | BIT $4400   | $2C     |          3 |          4 |

BIT active le drapeau Z comme si la valeur de l'adresse testée était « ANDée » avec l'accumulateur. Les drapeaux N et V sont positionnés pour correspondre respectivement aux bits 7 et 6 de la valeur stockée à l'adresse testée.
BIT est souvent utilisé pour sauter un ou deux octets suivants, comme dans l'exemple suivant :

```ASM
CLOSE1 LDX #$10       S'il est saisi ici, nous
       .BYTE $2C      effectuons effectivement
CLOSE2 LDX #$20       un test BIT sur $20A2,
       .BYTE 2C       un autre sur $30A2,
CLOSE3 LDX #$30       et nous nous retrouvons avec le registre X
CLOSEX LDA #12        registre toujours à $10
       STA ICCOM,X    à l'arrivée ici.
```

Attention : une instruction BIT utilisée de cette façon comme NOP a des effets : les drapeaux peuvent être modifiés, et la lecture de l'adresse absolue, s'il s'agit d'accéder à un périphérique d'E/S, peut provoquer une action non désirée.

#### **Instructions de branchement**

Drapeaux affecté : aucun

Tous les branchements sont en mode relatif et ont une longueur de deux octets. La syntaxe est « Bxx Déplacement » ou (mieux) « Bxx étiquete ». Voir les notes sur le compteur de programme pour plus d'informations sur les déplacements.

Les branchements dépendent de l'état des bits de drapeau lorsque le code op est rencontré. Un branchement non effectué nécessite deux cycles machine. Ajoutez-en un si le branchement est pris et ajoutez-en un autre si le branchement traverse une limite de page.

MNEMONIC HEX
BPL (Branchement sur PLus) 10
BMI (Branchement sur MInus) 30
BVC (Branche sur oVerflow Clear) 50
BVS (Branch on oVerflow Set) 70
BCC (Branch on Carry Clear) 90
BCS (Branch on Carry Set) $B0
BNE (Branch on Not Equal) $D0
BEQ (Branchement sur EQual) $F0

Il n'existe pas d'instruction BRA (BRanch Always) mais elle peut être facilement émulée en effectuant un branchement sur la base d'une condition connue. L'un des meilleurs drapeaux à utiliser à cette fin est l'oVerflow, qui est inchangé par toutes les opérations, à l'exception des opérations d'addition et de soustraction.
Il y a franchissement de limite de page lorsque la destination du branchement se trouve sur une page différente de celle de l'instruction qui suit l'instruction de branchement. Par exemple :

SEC
BCS LABEL
NOP
Il y a franchissement de limite de page (c'est-à-dire que le BCS prend 4 cycles) lorsque (l'adresse de) LABEL et le NOP se trouvent sur des pages différentes. Cela signifie que
CLV
BVC LABEL
LABEL NOP
l'instruction BVC prendra 3 cycles quelle que soit l'adresse à laquelle elle se trouve.

### Émulation du 6502

## La puce sonore du 2A03/2A07

### Émulation de la puce sonore

## Le PPU

### Émulation du PPU

# Les cartouches

# Les manettes

![image](img/mario.png)
