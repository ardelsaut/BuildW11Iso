# **Créer son propre ISO Windows 11**


## Pour cet exemple nous allons créer '**NonoOS.iso**'

---

## **Introduction**

---

Le But de ce tutoriel est d'apprendre à créer son propre **ISO** presonnalisé Windows 11.

Pour créer son propre ISO Windows 11, il y a plusieures manières différentes.

Ici,  on va utiliser la méthode **SYSPREP** et **Generalize**.

C'est-à-dire qu'on va faire rentrer Windows dans un mode spécial (le mode **AUDIT**), nous permettant de personnaliser à notre guise et de capturer ensuite l'image de ce système personnalisé. 

L'ISO alors créé pourra être installé sur plusieures machines différentes sans problèmes avec toutes nos personnalisations, mises à jour, programmes,... . 

---

## **Prérequis**

---

- [Vmware](https://www.vmware.com/products/workstation-player.html) ou [VirtualBox](https://download.virtualbox.org/virtualbox/6.1.34/VirtualBox-6.1.34-150636-Win.exe) ou [Qemu](https://qemu.weilnetz.de/w64/qemu-w64-setup-20220419.exe)
- [Windows ADK](https://download.microsoft.com/download/1/f/d/1fd2291e-c0e9-4ae0-beae-fbbe0fe41a5a/adk/adksetup.exe)
- [Windows 11](https://www.microsoft.com/es-es/software-download/windows11) : Une machine hôte à jour avec Windows 11 installé et une VM avec win11 que l´on clonera
- Iso Live Install [**Fedora**](https://ftp-stud.hs-esslingen.de/pub/fedora/linux/releases/test/36_Beta/Workstation/x86_64/iso/Fedora-Workstation-Live-x86_64-36_Beta-1.4.iso) (Optionnel)
- Fichier ['unattend.xml'](https://github.com/ardelsaut/BuildW11Iso/blob/main/archives/Fichiers-Unattended/copyprofile.xml) (Optionnel)
- Un serveur ou une Clé Usb ou un Drive (Optionnel)
- Beaucoup d´espace Disque
- Beaucoup de Ram


## **Mise En Place de L'Espace de Travail sur le système HÔTE**:
---
### On crée les **dossiers de Travail** :

On ouvre **Powershell en Administrateur** :

```powershell
New-Item -ItemType Directory -Path 'C:\NonoOS-Build-Ssd\WinSource' -Force
```

```powershell
New-Item -ItemType Directory -Path 'C:\NonoOS-Build-Ssd\WinSource-modified-fonctionnel' -Force
```

On monte alors notre **ISO Win11 source** pour pouvoir copier les fichiers et dossiers de l'**ISO**

Dans mon cas :

```powershell
$myISO =  "D:\Images Isos\Win11_French_x64.iso" # Pensez à mettre votre chemin Iso
Mount-DiskImage -ImagePath "$myISO"
$vol = Get-DiskImage $myISO | Get-Volume
$SourceDrive = $vol.DriveLetter + ':'
```
On copie les fichiers Win11\Source dans notre **Dossier de Travail**

```powershell
Copy-Item -Path "$SourceDrive\*" -Destination "C:\NonoOS-Build-Ssd\WinSource" -Recurse -Force
```

Voilà, notre dossier backup avec un **windows clean est mis en place**, maintenant on copie ce dossier vers le dossier de notre futur **Win11 Custom**

```powershell
Copy-Item -Path "C:\NonoOS-Build-Ssd\WinSource" -Destination "C:\NonoOS-Build-Ssd\WinSource-modified-fonctionnel" -Recurse -Force
```

Le Fichier qui nous intéresse se situe (dans mon cas) :

**"C:\NonoOS-Build-Ssd\WinSource-modified-fonctionnel\sources\install.wim"**

Ce sera le fichier qui contiendra toutes nos modifications !

On peut commencer à mettre en place la VM

### **Créer une VM**

Ici plusieures approches sont possibles. Dans ce cas-ci, la mise en place ne sera pas la plus simple, mais par la suite, l'utilisation sera grandement facilitée !


#### Paramètres de la VM :
    - UEFI
    - Mode pont pour la connexion internet ou Clé Usb grosse capacité (optionnel)
    - 2 hdd min 50gb (compatible Windows)
    - 8gb ram
    - périphérique Cdrom attaché

<details>
  <summary>Imprim d'Écran setup VMWare 1</summary>
  
Écran 1

![Écran 1](https://github.com/ardelsaut/BuildW11Iso/blob/main/archives/pictures-git/VMWare/setup-1/vm1.png?raw=true)

Écran 2

![Écran 1](https://github.com/ardelsaut/BuildW11Iso/blob/main/archives/pictures-git/VMWare/setup-1/vm2.png?raw=true)

Écran 3

![Écran 1](https://github.com/ardelsaut/BuildW11Iso/blob/main/archives/pictures-git/VMWare/setup-1/vm3.png?raw=true)

Écran 4

![Écran 1](https://github.com/ardelsaut/BuildW11Iso/blob/main/archives/pictures-git/VMWare/setup-1/vm4.png?raw=true)

Écran 5

![Écran 1](https://github.com/ardelsaut/BuildW11Iso/blob/main/archives/pictures-git/VMWare/setup-1/vm5.png?raw=true)

Écran 6

![Écran 1](https://github.com/ardelsaut/BuildW11Iso/blob/main/archives/pictures-git/VMWare/setup-1/vm6.png?raw=true)

Écran 7

![Écran 1](https://github.com/ardelsaut/BuildW11Iso/blob/main/archives/pictures-git/VMWare/setup-1/vm7.png?raw=true)

Écran 8

![Écran 1](https://github.com/ardelsaut/BuildW11Iso/blob/main/archives/pictures-git/VMWare/setup-1/vm8.png?raw=true)

Écran 9

![Écran 1](https://github.com/ardelsaut/BuildW11Iso/blob/main/archives/pictures-git/VMWare/setup-1/vm9.png?raw=true)

Écran 10

![Écran 1](https://github.com/ardelsaut/BuildW11Iso/blob/main/archives/pictures-git/VMWare/setup-1/vm10.png?raw=true)

Écran 11

![Écran 1](https://github.com/ardelsaut/BuildW11Iso/blob/main/archives/pictures-git/VMWare/setup-1/vm11.png?raw=true)

Écran 12

![Écran 1](https://github.com/ardelsaut/BuildW11Iso/blob/main/archives/pictures-git/VMWare/setup-1/vm12.png?raw=true)

Écran 13

![Écran 1](https://github.com/ardelsaut/BuildW11Iso/blob/main/archives/pictures-git/VMWare/setup-1/vm13.png?raw=true)

Écran 14

![Écran 1](https://github.com/ardelsaut/BuildW11Iso/blob/main/archives/pictures-git/VMWare/setup-1/vm14.png?raw=true)

</details>

<details>
  <summary>Imprim d'Écran setup VMWare 2</summary>

Écran 1

![Écran 1](https://github.com/ardelsaut/BuildW11Iso/blob/main/archives/pictures-git/VMWare/setup-final/vm-final-1.png?raw=true)


Écran 2

![Écran 1](https://github.com/ardelsaut/BuildW11Iso/blob/main/archives/pictures-git/VMWare/setup-final/vm-final-2.png?raw=true)


Écran 3

![Écran 1](https://github.com/ardelsaut/BuildW11Iso/blob/main/archives/pictures-git/VMWare/setup-final/vm-final-3.png?raw=true)


Écran 4

![Écran 1](https://github.com/ardelsaut/BuildW11Iso/blob/main/archives/pictures-git/VMWare/setup-final/vm-final-4.png?raw=true)


</details>

Si vous essayez d'installer Win11 maintenant, Windows vous empechera d'aller plus loin que l'installation car la VM n'a pas la configuration minimum requise.

Dans ce tuto, je vais éditer le registre pour pouvoir installer Windows 11 sur une vm sans le minimum requis, si vous décidez de sauter cette étape, vous devez activez ces modules dans VMware pour pouvoir installer Windows 11.

    - Secure Boot activé (On va le bypass ici, mais optionnel)
    - encrypted (pour module TPM) (On va le bypass ici, mais optionnel)

Le problème de cette solution, est qu' il faudrait passer par par un **live-cd** pour accéder au hdd de la vm car celle-ci est encryptée et donc le hdd ne peut être monté sur la **machine Hôte**

Je choisis donc la solution du Bypass pour pouvoir mapper les lecteurs de VMware sur la machine Hôte. Si on crypte la VM comme nécessaire pour le module TPM, on ne peut plus mapper les Lecteurs.
    
Voir [ici](https://github.com/ardelsaut/BuildW11Iso/tree/main/archives/pictures-git/VMWare) la config de ma VM

On a choisit notre solution ! On va pouvoir commencer l'installation.

---

# **Lancement de la Machine Virtuelle**

On va commencer par installer **Windows 11 Officiel**, pas besoin de clés!

Lorsque l'on est sur le premier écran d'installation de Windows, on appuie sur les touches du clavier suivantes, pour éditer les clés de Registre. Et nous permettre de pouvoir continuer à installer Windows 11.
   
```powershell   
[Shift] + [F10]
```

pour ouvrir l'Invite de Commande

## En gui

On écrit sur l'invite de Commande qui vient d'apparaitre

```powershell   
regedit + [ENTER]
```

On navigue alors vers le répertoire

' **HKEY_LOCAL_MACHINE->SYSTEM->Setup** '

Clique droit sur :

```powershell
setup
```

```powershell
->nouveau->clé
```

Nom de la nouvelle clé :

```powershell
Labconfig
```

Dans la Nouvelle Clé :

```powershell
[Right + Click]->Nouvelle->Valeur DWORD (32-bit)
```

Nom de la nouvelle Valeur :

```powershell
BypassTPMCheck
```

Dans la Nouvelle Clé à nouveau :

```powershell
[Right + Click]->Nouvelle->Valeur DWORD (32-bit)
```

Nom de la nouvelle Valeur :

```powershell
BypassSecureBootCheck
```

on double clique sur chaque clés créées et on leur donne comme Valeur

```powershell
1 
```


## En CLI

```powershell
REG ADD HKLM\SYSTEM\Setup\Labconfig /v BypassTPMCheck /t REG_DWORD /d 1

REG ADD HKLM\SYSTEM\Setup\Labconfig /v BypassSecureBootCheck /t REG_DWORD /d 1
```

Voilà, on a Bypass le check de Windows 11, on peut fermer Regedit et l'Invite de Commande pour continuer l'installation.

On continue d'installer Windows **normalement**. On ne va pas plus loin que le premier redémarrage, une fois Windows installé sur C.\ .

Maintenant qu'on est sur le premier écran de configuration (écran de sélection de la Région), on va directement faire rentrer Windows en mode Audit, comme ça on évite de devoir créer un nouvel utilisateur.

Pour entrer directement en mode audit

```powershell   
[Ctrl]+[Shift]+[F3]
```

Le pc va alors redémarrer, cela va prendre un certain temps, et il démarrera automatiquement avec une session utilisateur **Administrateur** active (C'est le mode **AUDIT**). C'est dans cette session que l'on va faire tout nos changements avant de pouvoir les capturer. 

Remarquez qu'une application apparait au démarrage, c'est sysprep. Vous pouvez la fermer avec la croix.

🔴IMPORTANT 

Avant de faire tout autre chose, il est <span style="color:red">**indispensable**</span> d'activer le deuxième hdd créé avec la VM.

Éteignez la vm pour ajouter un second hdd. Une fois cela fait :

Redémarrez la VM et pensez à formatter (NTFS) le second hdd, le nommer 'Data'.

<details>
  <summary>En Gui :</summary>

se rendre dans de gestionnaire de disque.

Nommer **'Windows'** le premier hdd.

**activer** et **formatter** ainsi que **nommer** **'Data'** et **aasigner** lettre **G:\\** le second hdd.
</details>

<details>
  <summary>En CLI :</summary>

Dans Powershell

```batch
diskpart
list disk
sel disk 1 # Mon hhd 2 nouvellement crée
clean
create partition primary
format fs=ntfs
list vol
sel vol 4 # Mon hdd 2
assign letter=G
exit
# Je nomme les 2 hdds
label C:Windows
label G:Data
```
</details>

---

Sur le second hdd nommé 'Data', créer un dossier nommé **'Scratch'**.

Dans mon cas :

```powershell
# Dans la Vm
New-Item -ItemType Directory -Path 'G:\Scratch' -Force
```

Ce sera le dossier de travail pour notre **'install.wim'** lors de la création de notre **'install.wim custom'**.

---

🔴IMPORTANT 

- **Mettre à jour Windows sur la VM avant toute modification**, vous pouvez redémarrer la machine, elle reviendra en mode audit sur le compte Administrateur.
- Supprimer l'utilisateur créé lors de l'installation (Optionnel si vous avez choisi de ne pas passer directement en mode audit).

🔴IMPORTANT 

- Prendre à ce moment un snapshot de la VM pour avoir un **template** clean en cas de dysfonctionnement lors de la **personnalisation** et pour pouvoir revenir à un **état propre de la VM** sans devoir se **retaper toute l'installation**; l'appeler "Template-avant-sysprep"

---

## **1) Dans le mode audit (toujours dans la VM)**


On peut commencer alors à faire nos modifications, comme par exemple :

- mettre en place son fichier **unattend.xml**
- Installer nos programmes, suite **office**, **adobe**, etc
- **Mettre en place certains dossiers ou fichiers** pour toujours les avoir avec
- **Personnaliser l'apparence** complète de Windows
- **Supprimer des programmes** préinstallés et divers autres Bloat
- **Créer des Utilisateurs** et les personnaliser,
- ...

🔴IMPORTANT **ATTENTION** : Les applications du MicroSoft Store ne sont pas compatibles, il faut trouver des alternatives pour les installer.

## **2) Personnalisation terminée**

Une fois fini de faire tous vos changements, Il va être temps de commencer la capture.
Avant cela, il va falloir un peu nettoyer Windows. Cela évitera de compiler des données inutiles.

Dans mon cas, avant de lancer sysprep, il a fallu que je supprime.

```powershell
# Paquet Winget
Get-AppxPackage | Select-String "winget" | Remove-AppPackage
Get-AppxPackage | Select-String "TheDebianProject" | Remove-AppPackage
# Get-AppXPackage -AllUsers | Select-String "Microsoft.SecHealthU" | Foreach {Add-AppxPackage -DisableDevelopmentMode -Register “$($_.InstallLocation)\AppXManifest.xml”}
# Get-AppxPackage Microsoft.SecHealthUI -AllUsers | Reset-AppxPackage
# Get-AppXPackage -AllUsers | Foreach {Add-AppxPackage -DisableDevelopmentMode -Register “$($_.InstallLocation)\AppXManifest.xml”} 
# get-AppxPackage Microsoft.SecHealthUI -AllUsers | Reset-AppxPackage
# add-appxpackage -disable developmentmode -register ((Get-AppxPackage Microsoft.SecHealthUI -allusers).InstallLocation + '\AppxManifest.xml')
Get-AppxPackage -AllUsers | Select-String "Microsoft.SecHealthUI" | Remove-AppPackage



# Wsl Debian
Remove-AppPackage TheDebianProject.DebianGNULinux_1.1.3.0_x64__76v4gfsz19hv4
Remove-AppPackage MicrosoftTeams_22070.202.1253.1497_x64__8wekyb3d8bbwe
Remove-AppPackage Microsoft.Winget.Source_2022.406.1630.539_neutral__8wekyb3d8bbwe
Remove-AppPackage Files_2.1.15.0_x64__1y0xx7n9077q4
Remove-AppPackage Microsoft.WindowsMaps_1.0.39.0_x64__8wekyb3d8bbwe
Remove-AppPackage Microsoft.ZuneMusic_11.2202.45.0_x64__8wekyb3d8bbwe
Remove-AppPackage Microsoft.MicrosoftSolitaireCollection_4.12.3171.0_x64__8wekyb3d8bbwe
Remove-AppPackage Microsoft.MicrosoftOfficeHub_18.2110.13110.0_x64__8wekyb3d8bbwe
Remove-AppPackage Microsoft.PowerAutomateDesktop_1.0.180.0_x64__8wekyb3d8bbwe
Remove-AppPackage Microsoft.SecHealthUI_1000.22000.1.0_neutral__8wekyb3d8bbwe
Remove-AppPackage Microsoft.People_10.1909.12456.0_x64__8wekyb3d8bbwe
Remove-AppPackage Microsoft.GetHelp_10.2008.32311.0_x64__8wekyb3d8bbwe
Remove-AppPackage Microsoft.BingWeather_1.0.6.0_x64__8wekyb3d8bbwe
Remove-AppPackage Microsoft.BingNews_1.0.6.0_x64__8wekyb3d8bbwe
Remove-AppPackage Microsoft.Windows.ParentalControls_1000.22000.1.0_neutral_neutral_cw5n1h2txyewy
Remove-AppPackage Microsoft.Windows.PeopleExperienceHost_10.0.22000.1_neutral_neutral_cw5n1h2txyewy
Remove-AppPackage Microsoft.WindowsSoundRecorder_1.0.38.0_x64__8wekyb3d8bbwe
Remove-AppPackage Microsoft.WindowsFeedbackHub_1.2203.761.0_x64__8wekyb3d8bbwe

echo Y | del %appdata%\microsoft\windows\recent\automaticdestinations\*
del %0

cleanmgr /sageset:65535 & Cleanmgr /sagerun:65535

net stop wmpnetworksvc

net user nono /active:no

```

Sans cela, sysprep ne me laisse pas continuer.

Il est possible de consulter le fichier log contenant toutes les erreurs quelques par dans 'system32/sysprep/panther/setupact.log' ou quelque chose comme ça

En plus de supprimer les paquets du MicroSoft Store, je lance

```powershell
# Dans un prompt
[Win+R]
# Lancer la commande
cleanmgr
```

🔴IMPORTANT 
Prendre un snapshot de la VM avant de lancer sysprep, si ca se passe bien sysprep on continue sinon on revient au snapshot

Ensuite, j'ouvre un terminal et je lance la cqpture du système définitif avec la commande

```powershell
[Win+R]
C:\Windows\System32\Sysprep\Sysprep /generalize /oobe /shutdown /unattend:T:\PC\Windows\BuildW11Iso\archives\Fichiers-Unattended\CopyProfile.xml
 ```

La VM s'éteint alors. Il est temps de passer à l'etape suivante.

## **3) Capture de l'image de la VM vers install.wim**

Avant tout autre chose, on vérifie que l'**ISO** original de Windows est toujours monté dans le **cdrom de la VM** sur VMware, dans le cas contraire, le remettre!

```powershell
Redémarrewr la VM
```

Maintenant, au redémarrage de la VM
À l'écran de démarrage Bios de VMWare

```powershell
touche [ESC] # pour entrer dans le BIOS
```

Là on choisit de 

```powershell
Démarrer avec le CdRom.
```

Le cdrom virtuel démarre et on se retrouve à nouveau avec le programme d'installation de Windows.
On ne va pas plus loin, on appuie sur les touches

```powershell
[Shift]+[F10]
```

Un terminal s'ouvre alors. On peut remarquer que l'on est sur le lecteur **'X:\ '**

Dans le Terminal :

```batch
diskpart
```
- On Remarque que le prompt change nous indiquant que l'on est bien dans Diskpart.

On liste tous les volumes pour voir nos 2 disques durs :

```batch
list vol
```
On assigne alors une lettre a nos 2 disques durs

Si vos disques ont une lettre assignée, retirer les lettres déjà assignées.

Dans mon cas :

On sélectionne les disque concernés :

```batch
sel vol 4
```

On retire les lettres assignées :

```batch
remove letter=C
```

On peut alors assigner les lettres désirées :

**(Toujours dans diskpart)**

Mon disque source avec le Windows que je viens de configuré se trouve sur le Volume 1, donc

Pour le 1er disque, on le sélectionne d'abord :

```batch
sel vol 1
```

ensuite, on lui assigne la lettre correcte :

```batch
assign letter=E
```

Pour le 2ieme disque (Celui qui recevra le fichiers install.wim capturé), on le sélectionne d'abord :

```batch
sel vol 4
```

ensuite, on lui assigne la lettre correcte :

```batch
assign letter=G
```

On peut alors quitter DiskPart :

```batch
exit
```

Il est temps de lancer la capture de notre **install.wim custom**. Dans mon cas :

```powershell
dism /capture-image /imagefile:G:\install.wim /capturedir:E:\ /ScratchDir:G:\Scratch /name:"NonoOS" /compress:maximum /checkintegrity /verify /bootable
```

Cela va durer un certain temps !  Une fois la Capture terminée, il est temps d'aller récupérer notre fichiers **install.wim custom**. Pour rappel, il se trouve alors à la racine de notre 2ième hdd, nommé au préalable 'Data'.

## **4) Utiliser notre install.wim custom**

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

- Chemin CdRom monté (source originale Windows11)
 
    ``K:\``

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

```powershell
# Ouvrir cmd Dans Adk kit avec la Commande:
[Win+R]
C:\Windows\system32\cmd.exe /k "C:\Program Files (x86)\Windows Kits\11\Assessment and Deployment Kit\Deployment Tools\DandISetEnv.bat" 
```

et faire :

```batch
# Pour enlever la longueur du Prompt
cd\
```
```sh
# Utiliser la Commande:
# C:\Windows\system32\cmd.exe /k "C:\Program Files (x86)\Windows Kits\11\Assessment and Deployment Kit\Deployment Tools\DandISetEnv.bat" 
oscdimg.exe -m -o -u2 -udfver102 -bootdata:2#p0,e,bC:\NonoOS-Build-Ssd\WinSource-modified\boot\etfsboot.com#pEF,e,bC:\NonoOS-Build-Ssd\WinSource-modified\efi\microsoft\boot\efisys.bin C:\NonoOS-Build-Ssd\WinSource-modified C:\NonoOS-Build-Ssd\NonoOS.iso
```

## **Testez l'Iso !**

# Encore à Faire dans vm de config

mettre a jour windows


enlever check tpm et secure booot ds reg, pas sauvegardé

dossier confgi nono


---
---

# **WinPE**

```powershell
copype amd64 C:\WinpE_amd64
MakeWinPEMedia /ISO C:\WinPE_amd64 C:\WinPE_amd64\WinPE_amd64.iso
```

<!--
# $timestamp = Get-Date -UFormat "%H:%M_%d-%m-%y" 
Ctrl+Shift+F3 # Pour entrer directement en mode audit
-->
