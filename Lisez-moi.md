# NESEmu

- [Introduction](#introduction)
- [Schémas de la console](#schémas-de-la-console)
- [Le processeur](#le-processeur)
  - [Le processeur 6502](#le-processeur-6502)
    - [Diagrammes](#diagrammes)
    - [Les registres](#les-registres)
      - [L'accumulateur](#laccumulateur)
    - [Les modes d'addressage](#les-modes-dadressage)
      - [Accumulateur](#)
      - [Adressage immédiat](#adressage-immédiat)
      - [Absolute]()
      - [Absolute, X]()
      - [Absolute, Y]()
      - [Zero Page]()
      - [Implied]()
    - [Les instructions du 6502](#les-instructions)
    - NTSC version, named Ricoh 2A03 or RP2A03, running at 1.79 MHz.
    - PAL version, named Ricoh 2A07 or RP2A07, running at 1.66 MHz.
    - [Les cartouches de jeu](#les-cartouches)
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

# Le processeur

## Le processeur 6502

### Diagrammes

### Les registres du 6502

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
| ----------- | ----------- | ------- | ---------- | ---------- |
| **Mode**    | **Syntaxe** | **HEX** | **Taille** | **Cycles** |
| Immediate   | ADC #$44    | $69     | 2          | 2          |
| Zero Page   | ADC $44     | $65     | 2          | 3          |
| Zero Page,X | ADC $44,X   | $75     | 2          | 4          |
| Absolute    | ADC $4400   | $6D     | 3          | 4          |
| Absolute,X  | ADC $4400,X | $7D     | 3          | 4+         |
| Absolute,Y  | ADC $4400,Y | $79     | 3          | 4+         |
| Indirect,X  | ADC ($44,X) | $61     | 2          | 6          |
| Indirect,Y  | ADC ($44),Y | $71     | 2          | 5+         |

- ajouter 1 cycle si la limite de page est franchie

Les résultats de l'instruction ADC dépendent de la position de l'indicateur décimal. En mode décimal, l'addition est effectuée en supposant que les valeurs concernées sont emballées en BCD (Binary Coded Decimal).
Il n'y a aucun moyen d'additionner sans retenue.

#### **AND (Et bit à bit avec l'accumulator)**

Drapeaux affecté : N Z

|             |             |         |            |            |
| ----------- | ----------- | ------- | ---------- | ---------- |
| **Mode**    | **Syntaxe** | **HEX** | **Taille** | **Cycles** |
| Immediate   | AND #$44    | $29     | 2          | 2          |
| Zero Page   | AND $44     | $25     | 2          | 3          |
| Zero Page,X | AND $44,X   | $35     | 2          | 4          |
| Absolute    | AND $4400   | $2D     | 3          | 4          |
| Absolute,X  | AND $4400,X | $3D     | 3          | 4+         |
| Absolute,Y  | AND $4400,Y | $39     | 3          | 4+         |
| Indirect,X  | AND ($44,X) | $21     | 2          | 6          |
| Indirect,Y  | AND ($44),Y | $31     | 2          | 5+         |

- add 1 cycle if page boundary crossed

### Émulation du 6502

# Le PPU

# Les cartouches

# Les manettes

![image](img/mario.png)
