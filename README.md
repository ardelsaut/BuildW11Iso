# **Créer son propre ISO Windows 11**

## Pour cet exemple nous allons créer **NonoOS.iso**
---

### **Introduction**
---
Pour créer son propre iso Windows, Il y a plein de manières différentes. Ici,  on va utiliser **sysprep**.
C'est-à-dire qu'on va faire rentrer Windows dans un mode spécial nous permettant de personnaliser à notre guise, et de capturer ensuite l'image de ce système. 

L'iso alors créé pourra être installé sur plein de machines sans problèmes avec toutes nos personnalisations et programmes. 

### **Prérequis**

    - Vmware
    - Windows ADK
    - 2 Iso Windows 11, Une machine hôte à jour avec Windows 11 installé une VM avec win11 que l´on clonera
    - Fichier 'unattend.xml' (Optionnel)
    - Un serveur ou une Clé Usb ou un Drive
    - Beaucoup d´espace Disque
    - Beaucoup de Ram

### **Tout d'abord, Il faut créer une VM**

#### Paramètres de la VM :

    - Uefi + Secure Boot activé
    - Mode pont pour la connexion internet ou Clé Usb grosse capacité
    - encrypted (pour module TPM)
    - 2 hdd min 50gb (compatible Windows)
    - 8gb ram


# **Lancement de la Machine Virtuelle**

On Installe Win11 **normalement**, on crée un utilisateur, etc.

Ctrl+Shift+F3 # Pour entrer directement en mode audit

Une fois Windows installé, on peut **commencer la procédure** !

#### Sur le nouveau **Bureau** dans la VM :

    On fait apparaitre la fenêtre "exécuter"
    [Win+R]

#### On lance le mode Audit et on redémarre le pc pour commencer la procédure :

```sh
%windir%\system32\sysprep\sysprep.exe /audit /reboot
```

Le pc va alors redémarrer, cela va prendre un certain temps, et il démarrera automatiquement avec une session utilisateur **Administrateur** active. C'est dans cette session que l'on va faire tout nos changements avant de pouvoir les capturer. 

Remarquez qu'une application apparait au démarrage, c'est sysprep. N'y touchez pas pour l'instant. Ne la fermez pas non plus !

---

**!!!** Il est **indispensable** de se rendre dans de gestionnaire de disque pour **activer et formatter ainsi que nommer 'Data'** le second hdd. Une fois le disque formatté, créer un dossier nommé **Scratch** à la racine de celui-ci  **!!!**

---

**IMPORTANT** :

- Penser dabord à :
    - Mettre à jour la Windows sur la VM avant toute modification                     
    - Supprimer l'utilisateur créé lors de l'installation

## **1) Dans le mode audit (toujours dans la VM)**

Une fois cela fait, pensez à prendre un Snapshot et l'appeler "Template-avant-sysprep", cela nous servira pour pouvoir retourner à un état d'origine stable en cas de dysfonctionnement, sans devoir se retaper toute l'installation.  

On peut commencer alors à faire nos modifications, comme par exemple :

- mettre en place son fichier **unattend.xml**
- Installer nos programmes, suite **office**, **adobe**, etc
- **Mettre en place certains dossiers ou fichiers** pour toujours les avoir avec
- **Personnaliser l'apparence** complète de Windows
- **Supprimer des programmes** préinstallés et divers autres Bloat
- **Créer des Utilisateurs** et les personnaliser,
- ...

**ATTENTION** : Les applications du MicroSoft Store ne sont pas compatibles, il faut trouver des alternatives pour les installer.


## **2) Personnalisation terminée**

Une fois fini de faire tous vos changements, Il va être temps de commencer la capture.
Avant cela, il va falloir un peu nettoyer Windows. Cela évitera de compiler des données inutiles.

Dans mon cas, avant de lancer sysprep, il a fallu que je supprime.

```sh
# Paquet Winget
Remove-AppPackage Microsoft.Winget.Source_2022.404.1342.992_neutral__8wekyb3d8bbwe
# Wsl Debian
Remove-AppPackage TheDebianProject.DebianGNULinux_1.1.3.0_x64__76v4gfsz19hv4
```

Sans cela, sysprep ne me laisse pas continuer.

Il est possible de consulter le fichier log contenant toutes les erreurs quelques par dans 'system32/sysprep/panther/uctupate.log' ou quelque chose comme ça

En plus de supprimer les paquets du MicroSoft Store, je lance

```sh
# Dans un prompt
[Win+R]
[cmd.exe]
# Lancer la commande
cleanmgr
```

Ensuite, j'ouvre un terminal et je lance sysprep définitif avec la commande

```sh
 c:\windows\system32\sysprep\sysprep /generalize /oobe /shutdown /unattend:c:\windows\system32\sysprep\unattend.xml
 ```

La VM s'éteint alors. Il est temps de passer à l'etape suivante

## **3) Capture de l'image de la VM vers install.wim**

Avant tout autre chose, on vérifie que l'iso original de Windows est toujours dans le cdrom de la VM sur VMware, dans le cas contraire, le remettre!

Maintenant, au redémarrage de la VM, à l'écran de démarrage Bios de VmWare, on mattraque la touche [ESC] pour entrer dans le BIOS. Là on sélectionne de démarrer avec le CdRom pour cette fois-ci en premier.

Le cdrom virtuel démarre et on se retrouve à nouveau avec le programme d'installation de Windows.
On ne va pas plus loin, on appuie sur les touches

```sh
[Shift]+[F10]
```

Un terminal s'ouvre alors. On peut remarquer que l'on est sur le lecteur **'X:\ '**

Dans le Terminal :

```sh
diskpart
# On Remarque que le prompt change nous indiquant que l'on est bien dans Diskpart.
```

```sh
# On list tous les volumes pour voir nos 2 disques durs.
list vol
```

Je constate que mes disques dur n'ont pas de lettres assignées, donc je les assigne!

Mon disque source avec le Windows que je viens de configuré se trouve sur le Volume 1, donc

Pour le 1er disque

```sh
sel vol 1
# Ensuite
assign letter=E
```

Pour le 2ieme disque (Celui qui recevra le fichiers install.wim capturé)

```sh
sel vol 2
# Ensuite
assign letter=G
```

On peut alors quitter DiskPart

```sh
exit
```

Il est temps de lancer la Capture. Dans mon cas

```sh
dism /capture-image /imagefile:G:\install.wim /capturedir:E:\ /ScratchDir:G:\Scratch /name:"NonoOS" /compress:maximum /checkintegrity /verify /bootable
```

Cela va durer un certain temps! Une fois la Capture terminée, il est temps d'aller récupérer notre fichiers **install.wim**.

## **4) Utiliser notre insatll.wim custom**

Pour cela, plusieurs possibilités :
- On reboot l'OS installé sur la VM (Windows 11 customisé)
- On lance une live install de Linux. (Option préférée avec Fedora)

J'attache donc à la VM **un second CdRom**. 

Le premier CdRom garde toujours l'Iso original de Windows.

J'attache **Fedora au deuxième CdRom**.

Une fois Fedora démarrée, **j'ouvre 'Fichiers'**, j'explore mon hdd 'Data' pour récupérer **install.wim**, il se trouve **à la racine du disque 'Data'**.

Depuis 'Fichiers' toujours, je me connecte à mon Nas et transfers **install.wim** de la vm vers mon Nas.

Si vous avez une clé usb, utilisez cela ou bien même Google Drive.

    'T:\PC\Windows\BuildW11Iso\install.wim'


# **Sur pc hôte :**

On monte le même Iso de Windows original

    # Chemin CdRom monté (source originale Windows11)
    K:\

On crée un dossier :

    C:\NonoOS-Build-Ssd

On crée un dossier :

    C:\NonoOS-Build-Ssd\WinSource

On crée un dossier :

    'C:\NonoOS-Build-Ssd\CaptureVM'

On crée un dossier :

    'C:\NonoOS-Build-Ssd\WinSource-modified'

On copie le contenu du Dossier 'k.\' vers 'WinSource\'

    'K:\*'
    -->
    'C:\NonoOS-Build-Ssd\WinSource'

Une fois copié, On copie notre dossier

    C:\NonoOS-Build-Ssd\WinSource
    -->
    C:\NonoOS-Build-Ssd\WinSource-modified


Une fois **install.wim custom** copié sur le Nas, on peut transférer **install.wim custom** sur le PC hôte

    'T:\PC\Windows\BuildW11Iso\install.wim'
    -->
    'C:\NonoOS-Build-Ssd\CaptureVM'

On remplace **install.wim** par **install.wim custom** venant de 'CaptureVM' vers 'WinSource-modified'

    'C:\NonoOS-Build-Ssd\CaptureVM'
    -->
    'C:\NonoOS-Build-Ssd\WinSource-modified'

On remplace alors **install.wim** dans le dossier par notre **install.wim custom**

Notre Dossier avec tout Windows est alors **FINI**.

```sh
# Ouvrir cmd Dans Adk kit avec la Commande:
[Win+R]
C:\Windows\system32\cmd.exe /k "C:\Program Files (x86)\Windows Kits\11\Assessment and Deployment Kit\Deployment Tools\DandISetEnv.bat" 
```

et faire :

```sh
# Pour enlever la longueur du Prompt
cd\
```
```sh
# Utiliser la Commande:
# C:\Windows\system32\cmd.exe /k "C:\Program Files (x86)\Windows Kits\11\Assessment and Deployment Kit\Deployment Tools\DandISetEnv.bat" 


oscdimg.exe -m -o -u2 -udfver102 -bootdata:2#p0,e,bC:\NonoOS-Build-Ssd\WinSource-modified-fonctionnel\boot\etfsboot.com#pEF,e,bC:\NonoOS-Build-Ssd\WinSource-modified-fonctionnel\efi\microsoft\boot\efisys.bin C:\NonoOS-Build-Ssd\WinSource-modified-fonctionnel C:\NonoOS-Build-Ssd\NonoOS.iso

```

## **Testez l'Iso !**

---
---

# **WinPE**

<!--
# $timestamp = Get-Date -UFormat "%H:%M_%d-%m-%y" 
Ctrl+Shift+F3 # Pour entrer directement en mode audit
-->
