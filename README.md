# Créer son propre ISO Windows 11

## Pour cet exemple nous allons créer **NonoOS**
---

### **Introduction**
---
Pour créer son propre iso Windows, Il y a plein de manières différentes. Ici,  on va utiliser **sysprep**.
C'est-à-dire qu'on va faire rentrer Windows dans un mode spécial nous permettant de personnaliser à notre guise, et de capturer ensuite l'image de ce système. L'iso alors créé pourra être installé sur plein de machines sans problèmes avec toutes nos personnalisations et programmes. 

### **Prérequis**
```sh
- sysprep.exe
- Vmware
- Windows ADK
- 2 Iso Windows 11, Une machine hôte à jour avec Windows 11 installé une VM avec win11 que l´on clonera
- Beaucoup d´espace Disque
- Beaucoup de temps
- Savoir utiliser des machines virtuelles et savoir les configurer
```

### **1) Tout d'abord, Il faut créer une VM**

### Création de VM :
```sh
# Paramètres de la VM :
- Uefi + Secure Boot activé
- encrypted (pour module TPM)
- 2 hdd min 50gb

```


### **2) Lancement de la Machine Virtuelle**
On Installe Win11 **normalement**, on crée un utilisateur, etc.
Une fois Windows installé, on peut **commencer la procédure** !

Sur le nouveau **Bureau** dans la VM :

```sh
# On fait apparaitre la fenêtre "exécuter"
    Win + R
```
```sh
# On lance le mode Audit et on redémarre le pc pour commencer la procédure
    %windir%\system32\sysprep\sysprep.exe /audit /reboot
```

Le pc va alors redémarrer, cela va prendre un certain temps, et il démarrera automatiquement avec une session utilisateur **Administrateur** active. C'est dans cette session que l'on va faire tout nos changements avant de pouvoir les capturer. 

Remarquez qu'une application apparait au démarrage, c'est sysprep. N'y touchez pas pour l'instant. Ne la fermez pas non plus !

**IMPORTANT** :
- Penser dabord à :
    - Mettre à jour la Windows sur la VM avant toute modification                     
    - Supprimer l'utilisateur créé lors de l'installation

### **3) Dans le mode audit (toujours dans la VM)**
Une fois cela fait, pensez à prendre un Snapshot et l'appeler "Template-avant-sysprep", cela nous servira pour pouvoir retourner à un état d'origine stable en cas de dysfonctionnement, sans devoir se retaper toute l'installation.  

On peut commencer alors à faire nos modifications, comme par exemple :

- Installer nos programmes, suite office, adobe, etc
- Mettre en place certains dossiers ou fichiers pour toujours les avoir avec
- Personnaliser l'apparence
- Supprimer des programmes préinstallés
- Créer des Utilisateurs et les personnaliser,
- ...

**ATTENTION** : Les applications du MicroSoft Store ne sont pas compatibles, il faut trouver des alternatives pour les installer.


### **4) Personnalisation terminée**
Une fois fini de faire tous vos changements, Il va être temps de commencer la capture.
Avant cela, il va falloir un peu nettoyer Windows. Cela évitera de compiler des données inutiles.

Dans mon cas, avant de lancer sysprep, il a fallu que je supprime

```sh
# Paquet Winget
Remove-AppPackage Microsoft.Winget.Source_2022.404.1342.992_neutral__8wekyb3d8bbwe
# Wsl Debian
Remove-AppPackage TheDebianProject.DebianGNULinux_1.1.3.0_x64__76v4gfsz19hv4
```
sans cela, sysprep ne me laisse pas continuer.

Il est possible de consulter le fichier log contenant toutes les erreurs quelques par dans 'system32/sysprep/panther/uctupate.log' ou quelque chose comme ça

En plus de supprimer les paquets du MicroSoft-Store
 
Cleanmgr
sysprep  - oobe - generalize - shutdowm
reboot on win cd

```sh
Shift+F10
Ctrl+Shift+F3 # Pour entrer directement en mode audit
```
 cmd

# $timestamp = Get-Date -UFormat "%H:%M_%d-%m-%y"

diskpart
list vol
sel vol 1
assign letter=E
sel vol 1
assign letter=G
exit

dism /capture-image /imagefile:G:\install.wim /capturedir:E:\ /ScratchDir:G:\Scratch /name:"NonoOS" /compress:maximum /checkintegrity /verify /bootable

```
When Finished capture, reboot vm to transfer archive

## Sur pc hôte :
Une fois Install.wim copié sur le Nas, 
On copie le dossier source iso, en le montant dans windows et selectionnant tout pour le copier dans notres Dossier : 

```sh
# Chemin CD Source Win11
K:\
# On copie l'intégralité du dossier dans
C:\NonoOS-Build-Ssd\WinSource
```
On remplace alors le fichiers Wim original par le fichier Wim custom.

On déplace d'abord (et remplace si nécessaire)

```sh
'T:\PC\Windows\BuildW11Iso\install.wim'

dans

'C:\NonoOS-Build-Ssd\CaptureVM'
```
Une fois copié, On copie notre dossier
```sh
C:\NonoOS-Build-Ssd\WinSource
```
vers
```sh
C:\NonoOS-Build-Ssd\WinSource-modified-fonctionnel
```
On remplace alors
**install.wim** dans le dossier par notre custom .wim


```sh
"C:\NonoOS-Build-Ssd\CaptureVM\install.wim"
 --> "C:\NonoOS-Build-Ssd\WinSource\sources"
```
Notre Dossier avec Tout Windows est alors Fait. On peut ajouter, avant de créer l'iso un fichier auto.xml a la racine de ce dossier si on veut une install unattended

```sh
# Ouvrir cmd Dans Adk kit avec la Commande:
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
Testez l'Iso !

# Prend un certain Temps
```
Ouvrir Le Programme **Assistant Gestion d’installation**
et Créer un nouveau Fichier Unatended
```

```sh
Shift+F10
Ctrl+Shift+F3 # Pour entrer directement en mode audit
```

La prochaine étape sera **WinPE**

---
---

# WinPE

