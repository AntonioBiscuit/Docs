# Guide Switch Cisco d'un apprenti sys-admin

![](img/cisco-logo.png)

- [Guide Switch Cisco d'un apprenti sys-admin](#guide-switch-cisco-dun-apprenti-sys-admin)
- [Connaissances et concepts de base](#connaissances-et-concepts-de-base)
  - [Utiliser un port Serial sur Archlinux](#utiliser-un-port-serial-sur-archlinux)
  - [NVRAM et DRAM](#nvram-et-dram)
  - [Processus de boot normal](#processus-de-boot-normal)
  - [Fichiers de configuration](#fichiers-de-configuration)
  - [Dynamic, Access et Trunk](#dynamic-access-et-trunk)
- [Recovery](#recovery)
    - [**⚠️ Warnings**](#️-warnings)
    - [Utiliser le recovery c'est **chiant** putain](#utiliser-le-recovery-cest-chiant-putain)
  - [Commandes](#commandes)
    - [**⚠️ NE JAMAIS, au grand jamais exécuter**](#️-ne-jamais-au-grand-jamais-exécuter)
    - [Initialiser la flash](#initialiser-la-flash)
    - [Lister le contenu de la flash](#lister-le-contenu-de-la-flash)
    - [Supprimer un fichier](#supprimer-un-fichier)
    - [Redémarrer](#redémarrer)
- [IOS](#ios)
  - [Exploiter les aides de l'IOS](#exploiter-les-aides-de-lios)
  - [Commandes](#commandes-1)
    - [Supprimer le config.text](#supprimer-le-configtext)
    - [Redémarrer le switch](#redémarrer-le-switch)
    - [Changer le hostname](#changer-le-hostname)
    - [Sauvegarder la configuration dans la NVRAM](#sauvegarder-la-configuration-dans-la-nvram)
    - [Afficher les VLANS](#afficher-les-vlans)
    - [Afficher les infos de toutes les interfaces](#afficher-les-infos-de-toutes-les-interfaces)
    - [Configurer un port](#configurer-un-port)
  - [**⚠️ Commandes dangereuses**](#️-commandes-dangereuses)
- [Procéures et tutoriels](#procéures-et-tutoriels)
  - [Telnet](#telnet)
    - [Changer de mot de passe](#changer-de-mot-de-passe)
    - [Assigner une IP au switch](#assigner-une-ip-au-switch)
  - [Sécuriser l'accès console (port série)](#sécuriser-laccès-console-port-série)
  - [Mot de passe](#mot-de-passe)
  - [Chiffrer les mots de passe](#chiffrer-les-mots-de-passe)
  - [Configurer des ports](#configurer-des-ports)
    - [Sélectionner le ou les ports](#sélectionner-le-ou-les-ports)
      - [Un seul port](#un-seul-port)
      - [Une plage de ports](#une-plage-de-ports)
      - [Plusieurs plages de ports](#plusieurs-plages-de-ports)
    - [Opérations possibles dans (config-if)](#opérations-possibles-dans-config-if)
  - [Configurer des VLANs](#configurer-des-vlans)
  - [Importer une config](#importer-une-config)


# Connaissances et concepts de base

## Utiliser un port Serial sur Archlinux

Puisque mon Packard Bell n'a pas de port Serial (comme pratiquement tous les ordis), il faut utiliser un adaptateur USB / Serial.

Le premier port Serial / USB est mappé par défaut sur `/dev/ttyUSB0`

Pour se connecter on peut utiliser PuTTY (cross-platform) ou dans un terminal **sudo** des programmes comme: `dterm` ou `minicom` qui, contrairement à `screen`, peuvent scroller vers le haut

Il n'y a en principe pas besoin de changer le débit qui est généralement de 9,600 bauds/s par défaut.


## NVRAM et DRAM 

NVRAM = Non-Volatile RAM  
**Persistante** : ne s'efface pas en cas de coupure ou d'arrêt.  
Contient la `startup-config`.

DRAM = Dynamic RAM  
**Volatile** : s'efface en cas d'arrêt ou de coupure.  
Contient la `running-config`.


## Processus de boot normal

- Le fichier `.bin` de l'IOS est décompressé dans la DRAM
- Si elle existe, la `startup-config` est copiée dans `running-config`
- Si elle existe, la `vlan-conf` est utilisée

```
❯ sudo dterm /dev/ttyUSB0

Using driver version 1 for media type 1
Base ethernet MAC Address: 00:25:83:6c:c3:80
Xmodem file system is available.
The password-recovery mechanism is enabled.
Initializing Flash...
mifs[2]: 0 files, 1 directories
mifs[2]: Total bytes     :    3870720
mifs[2]: Bytes used      :       1024
mifs[2]: Bytes available :    3869696
mifs[2]: mifs fsck took 1 seconds.
mifs[3]: 11 files, 1 directories
mifs[3]: Total bytes     :   27998208
mifs[3]: Bytes used      :   21873152
mifs[3]: Bytes available :    6125056
mifs[3]: mifs fsck took 8 seconds.
...done Initializing Flash.
done.
Loading "flash:c2960-lanlitek9-mz.122-55.SE9.bin"...@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
File "flash:c2960-lanlitek9-mz.122-55.SE9.bin" uncompressed and installed, entry point: 0x3000
executing...

              Restricted Rights Legend

Use, duplication, or disclosure by the Government is
subject to restrictions as set forth in subparagraph
(c) of the Commercial Computer Software - Restricted
Rights clause at FAR sec. 52.227-19 and subparagraph
(c) (1) (ii) of the Rights in Technical Data and Computer
Software clause at DFARS sec. 252.227-7013.

           cisco Systems, Inc.
           170 West Tasman Drive
           San Jose, California 95134-1706



Cisco IOS Software, C2960 Software (C2960-LANLITEK9-M), Version 12.2(55)SE9, RELEASE SOFTWARE (fc1)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2014 by Cisco Systems, Inc.
Compiled Mon 03-Mar-14 22:53 by prod_rel_team
Image text-base: 0x00003000, data-base: 0x01400000

Initializing flashfs...
Using driver version 1 for media type 1
mifs[3]: 0 files, 1 directories
mifs[3]: Total bytes     : 3870720   
mifs[3]: Bytes used      : 1024      
mifs[3]: Bytes available : 3869696   
mifs[3]: mifs fsck took 0 seconds.
mifs[3]: Initialization complete.

mifs[4]: 11 files, 1 directories
mifs[4]: Total bytes     : 27998208  
mifs[4]: Bytes used      : 21873152  
mifs[4]: Bytes available : 6125056   
mifs[4]: mifs fsck took 0 seconds.
mifs[4]: Initialization complete.

...done Initializing flashfs.

Checking for Bootloader upgrade.. not needed
POST: CPU MIC register Tests : Begin
POST: CPU MIC register Tests : End, Status Passed

POST: PortASIC Memory Tests : Begin
POST: PortASIC Memory Tests : End, Status Passed

POST: CPU MIC interface Loopback Tests : Begin
POST: CPU MIC interface Loopback Tests : End, Status Passed

POST: PortASIC RingLoopback Tests : Begin
POST: PortASIC RingLoopback Tests : End, Status Passed

POST: PortASIC CAM Subsystem Tests : Begin
POST: PortASIC CAM Subsystem Tests : End, Status Passed

POST: PortASIC Port Loopback Tests : Begin
POST: PortASIC Port Loopback Tests : End, Status Passed

Waiting for Port download...Complete


This product contains cryptographic features and is subject to United
States and local country laws governing import, export, transfer and
use. Delivery of Cisco cryptographic products does not imply
third-party authority to import, export, distribute or use encryption.
Importers, exporters, distributors and users are responsible for
compliance with U.S. and local country laws. By using this product you
agree to comply with applicable laws and regulations. If you are unable
to comply with U.S. and local laws, return this product immediately.

A summary of U.S. laws governing Cisco cryptographic products may be found at:
http://www.cisco.com/wwl/export/crypto/tool/stqrg.html

If you require further assistance please contact us by sending email to
export@cisco.com.

cisco WS-C2960-24-S (PowerPC405) processor (revision C0) with 65536K bytes of memory.
Processor board ID FOC1316W6TD
Last reset from power-on
1 Virtual Ethernet interface
24 FastEthernet interfaces
The password-recovery mechanism is enabled.

64K bytes of flash-simulated non-volatile configuration memory.
Base ethernet MAC Address       : 00:25:83:6C:C3:80
Motherboard assembly number     : 73-11471-05
Power supply part number        : 341-0097-02
Motherboard serial number       : FOC13163YGZ
Power supply serial number      : AZS131315RE
Model revision number           : C0
Motherboard revision number     : A0
Model number                    : WS-C2960-24-S
System serial number            : FOC1316W6TD
Top Assembly Part Number        : 800-29858-02
Top Assembly Revision Number    : B0
Version ID                      : V03
CLEI Code Number                : COMSJ00ARC
Hardware Board Revision Number  : 0x01


Switch Ports Model              SW Version            SW Image                 
------ ----- -----              ----------            ----------               
*    1 24    WS-C2960-24-S      12.2(55)SE9           C2960-LANLITEK9-M        




Press RETURN to get started!


*Mar  1 00:00:38.168: %LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to down
*Mar  1 00:00:39.309: %SPANTREE-5-EXTENDED_SYSID: Extended SysId enabled for type vlan
*Mar  1 00:00:48.897: %SYS-5-CONFIG_I: Configured from memory by console
*Mar  1 00:00:48.905: %SYS-5-RESTART: System restarted --
Cisco IOS Software, C2960 Software (C2960-LANLITEK9-M), Version 12.2(55)SE9, RELEASE SOFTWARE (fc1)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2014 by Cisco Systems, Inc.
Compiled Mon 03-Mar-14 22:53 by prod_rel_team% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]

*Mar  1 00:01:01.454: %SSH-5-ENABLED: SSH 1.99 has been enabled
*Mar  1 00:01:03.845: %PKI-6-AUTOSAVE: Running configuration saved to NVRAM
Galaxy>
```

## Fichiers de configuration

La `startup-config` est lue de la NVRAM au démarrage et est copiée dans la DRAM, la `running-config`.

La `runnning-config` est **volatile** puisque contenue dans la DRAM.

Lorsqu'on modifie la configuration d'un switch, on touche systématiquement à la `running-config`. En cas d'erreur il suffit de reboot pour récupérer la `startup-config`.

Pour rendre des changements de la `running-config` persistants il faut les écrire dans la NVRAM, dans la `startup-config`.  
NVRAM <- DRAM :

    copy running-config startup-config

On peut aussi faire l'inverse, c'est à dire lire la `startup-config` et la recharger dans la `running-config` de la DRAM  
NVRAM -> DRAM : 

    copy startup-config running-config


## Dynamic, Access et Trunk

Par défaut, tous les ports sont en `dynamic`. 

C'est très pratique car ils changent de mode automatiquement en fonction de ce qui est à l'autre bout.  En revanche, **il s'agit d'une énorme faille de sécurité** : il est facilement possible de faire passer un port `dynamic` en mode `trunk` en quelques trames.

# Recovery

### **⚠️ Warnings**

**TOUTE** commande executée dans le Recovery est potentiellement **DANGEREUSE**.

Réfléchir et être certain de ce qu'on fait AVANT de commencer à taper

**Il est très facile de BRICKER un switch**

### Utiliser le recovery c'est **chiant** putain

- Il n'y a ni tabulation, ni auto-complétion, ni aliases: tout doit être tapé en entier
- Le copier-coller ne fonctionne pas: tout doit être tapé à la main, caractère par c-a-r-a-c-t-è-r-e
- Il est impossible de rappeller une commande précédente avec les flèches du haut: tout doit être retapé en entier à chaque fois
- Il est impossible de corriger en utilisant les flèches gauche et droite: tout doit être tapé sans faute et du premier coup

**TL;DR**  
Toute commande dans le Recovery doit être tapée en entier, à la main, caractère par caractère et du premier coup sous peine d'être fausse. Pas de triche possible.


## Commandes

### **⚠️ NE JAMAIS, au grand jamais exécuter**

**`del flash:` au risque de formatter le switch entier avec l'IOS**  
Un IOS c'est pas gratuit, c'est même hyper cher. Et parce que ça ne suffit pas, c'est relou mais surtout hyper long à flasher. On parle d'un port série après tout.

### Initialiser la flash

`flash_init`

### Lister le contenu de la flash  

`dir flash:`

### Supprimer un fichier

`del flash:fichier`

### Redémarrer

`boot`

---

# IOS

## Exploiter les aides de l'IOS

- Tabulation, auto-complétion et aliases disponibles avec possibilité d'écrire uniquement le début si il n'y a pas d'ambiguïté (ex: `wr` au lieu de `write`)
- Copier-coller possible
- Rappel de commandes possible
- Corrections possibles
- Aide possible avec `?` pour voir une liste de commandes et/ou d'arguments attendus

## Commandes

Un grande grand nombre de commandes possèdent leur équivalent inverse comme négation: `shutdown` / `no-shutdown`.

### Supprimer le config.text

`erase startup-config`

### Redémarrer le switch

`reload`

### Changer le hostname
`hostname HOSTNAME`

### Sauvegarder la configuration dans la NVRAM
`write`

### Afficher les VLANS
`show vlans`

### Afficher les infos de toutes les interfaces
`show interfaces`

### Configurer un port



## **⚠️ Commandes dangereuses**

Plus ou moins toutes les commandes qui comportent `flash:` sont à manipuler avec grande précaution comme:  
**`delete flash:config.text`  
`delete flash:vlan.dat`**


---

# Procéures et tutoriels

## Telnet

⚠️ Toutes les trames transitent en clair avec Telnet. Il est préférable d'utiliser SSH qui lui est chiffré.

Prérequis:

- IP de configurée pour le switch

### Changer de mot de passe  

    Galaxy(config)# line vty 0 1
    Galaxy(config-line)# password telnet
    Galaxy(config-line)# login


### Assigner une IP au switch

    Switch(conf)# interface vlan 1  
    Switch(config-if)# ip address IP MASK

---

## Sécuriser l'accès console (port série)

`username [admin] password [console]`

`line console 0`

`login local`


## Mot de passe

En clair  
    
    Switch(config)# enable password PASSWORD

Hash  
    
    Switch(config)# enable secret PASSWORD

## Chiffrer les mots de passe

`service password-encryption`


## Configurer des ports

### Sélectionner le ou les ports

#### Un seul port

    (config)# interface fastEthernet 0/PORT

#### Une plage de ports

    (config)# interface range fastEthernet 0/PORT-PORT

#### Plusieurs plages de ports
(avec `PLAGE` étant `fa0/PORT-PORT`)

    (config)# interface range PLAGE0, PLAGE1, PLAGE2

### Opérations possibles dans (config-if)

- `duplex [half, full, auto]`  
- `speed`


## Configurer des VLANs

Selectionner le ou les ports

Exemple pour configurer le VLAN 2 sur la plage de ports 5-8:

    Galaxy(config)# interface range FastEthernet 0/5-8   

    Galaxy(config-if-range)# switchport access vlan 2

    % Access VLAN does not exist. Creating vlan 2

    Galaxy(config-if-range)#exit

Vérifier avec `show vlan`

## Importer une config
Après être passé en mode config:

    Switch> enable
    Switch# configure terminal

Simplement coller l'intégralité du fichier texte sauvegardé au préalable et admirer les lignes défiller.

⚠️ Selon le terminal on ne colle pas de la même manière !  
`CTRL + SHIFT + V` sur un terminal Linux et `R MOUSE` sur PuTTY ou un command prompt Windows.