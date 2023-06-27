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

### Diagrammes

### Les registres du 6502

#### Le registre d'état

#### L'accumulateur

### Les modes d'adressage

#### Adressage

#### Adressage immédiat

#### Adressage absolue

#### Adressage absolu

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
