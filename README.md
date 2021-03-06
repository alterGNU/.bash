# 0 - Installation
Cette configuration personnelle de bash déclare et utilise une variable d'ENVIRONNEMENT nommée `BASH_DIR` correspondant
à la localisation de ce dossier git : `.bash/`

## Installation standart :
Par défaut, ce dossier contenant les dotfiles de bash se nomme `.bash/` et se situe dans le $HOME

Ainsi $BASH\_DIR=`$HOME/.bash`, et pour installer, de manière standart, les modifications de bash qu'il contient :

* 0 - Se placer dans le $HOME
* 1 - Cloner le repo dans le $HOME en tant que dotfile
* 2 - Lancer le script d'installaton
* 3 - Sourcer `bash_profile` pour rendre les changements effectifs dans le shell courant:

```bash
cd && git clone https://github.com/alterGNU/.bash.git && ./.bash/install.sh && source .bash/bash_profile
```

## Installation personnalisée : 
Il est aussi possible de changer l'emplacement et le nom du dossier cloné, ainsi $BASH\_DIR=`<localisation>/<nom-dossier>*`

Example avec :
- localisation=**~/.config** 
- nom-dossier=**bash**

```bash
git clone https://github.com/alterGNU/.bash.git ~/.config/bash && ~/.config/bash/install.sh && . ~/.config/bash/bash_profile
```
_Remarque : Cela est possible car à l'installation `install.sh` modifie si besoin la valeur $BASH_DIR dans
`bash_profile` ainsi que dans `bashrc` et ce en fonction du nom et de l'emplacement choisi par l'utilisateur.Ainsi cette
modification impliquera surement qu'un commit soit éfféctué car le dossier fraichement cloné est modifié :)_

## Désinstallation
Pour réstaurer la configuration initiale de bash (datant d'avant l'utilisation du script `install.sh` un script de
"désinstallation" nommé `restore_bash.sh` est présent.

Ainsi pour revenir à la configuration de bash précédente lancer ce script, ou tapez dans un terminal la commande :

```bash
restore_bash
```
_Alias lançant le script `restore\_bash.sh` suivi de cmd de sourçants les BDT initiaux pour appliquer leurs
configurations dans le shell courant. Cet alias est définit uniquement aprés installation via l'éxécution du script
`install.sh` car il utilise la varENV ${BASH_DIR}_

# 1 - Le dossier : $BASH_DIR/
Ce dossier regroupant l'ensemble de mes configurations personnelles du shell bash contient :
- les fichiers de configuration de bash (**BDF** ~ _**B**ash **D**ot**F**iles_ ) :
    - `bash_profile` : **BDF** chargé à chaque session interactive loguée (bash source bash\_login).
    - `bashrc` : **BDF** chargé à chaque session interactive non-loguée.
    - `bash_logout` : **BDF** chargé à chaque fermeture de session bash loguée.
- des scripts :
    - `bash.sh` : script d'installation des **BDF**
    - `restore_bash.sh` : script permettant de remettre à l'état initial (avant installation) l'ensemble des **BDF**
- une collection de **shell-function** (dossier `${BASH_DIR}/bin/`):
    - `ce` : Compile et Execute codes-source/scripts pour les langages: [C,Python,PythonCheckio,Java,bash]
    - `check_git` : vérifie qu'un dossier est bien dans l'arborescence d'un dépôt git local
    - `checkioWeb`: Vérifie le code passé en arg. sur checkio puis propose l'ouverture dans navigateur s'il est ok
    - `coucou` : créé pour apprendre à déclarer des fcts...inutile...
    - `is_in_arbo` : Vérifie si on se trouve bien dans
    - `is_it_wls` : Vérifie si on se trouve sous Windows Sub-Systems for Linux
    - `nbr_agent-ssh` : Retourne le nombre d'agent ssh actif (methode:parcours `procfs`)
    - `ppticopy` : permet de copier des documents se trouvant sur la ppti vers mes appareils personnels
    - `swap` : Swapper des touches CAPSLOCK et ESCAPE

## 1.1 - Les fichiers : Bash\_DotFiles
### RAPPELS : Fonctionnement et Ordre de chargement des BDF
Les fichiers lus dépendent du type de shell:
- **Non-interactive** : shell non associé à un terminal (cas lors d'éxécution de script)
- **Interactive** : shell qui lis et écris dans un terminal d'utilisateur (Interpréteur de commande)
    - Loging : cas de Co. locale ou SSH ou `$ bash --login`
    ORDRE : /etc/profile >> ~/.bash_profile >> ~/.bash_login >> ~/.profile ( -f .bash_profil => .profil pas sourcé)
    - Non\_loging: `$ bash`
    ORDRE : ~/.bashrc >> ~/.bash_login >> ~/.profile

### Le fichier `~/.bash_profile` _(Interactive loging shell=>Link ds le HOME)_
Ce fichier est le premier sourcé au démarrage d'une session interactive loguée :`$ bash --login`
On doit y placer les commandes ne devant s'exécutées qu'une seule fois, il faut donc y placer :
- les déclarations des **VarENV** :
    - BASH\_DIR : localisation des BDF personnalisés
    - HISTFILE : localisation du fichier historique `.bash_history`
- les commandes **sourçants** les fichiers devant l'être :
    - `${BASH_DIR}/bashrc` : pour charger les configurations de bash
    - `${BASH_DIR}/aliases`: pour charger les alias de bash
- les commandes ajoutant des dossiers au **$PATH**
    - `${BASH_DIR}/bin` : pour charger les fonctions personnelles de bash

### Le fichier `~/.bashrc` : *(Sourcé par bash_profile si shell-logué,Sourcé auto si shell-non-logué)*
Y placé les commandes devant s'éxécutées à chaque fois qu'on démarre le shell comme les **options et configurations** de
bash.

#### Personnalisation du prompt
Dans le bashrc se trouvent deux fonctions (`git_color`;`git_branch`) permettant d'afficher dans le prompt l'état du
dépot git local:
- vert: le dépot local est à jour
- rouge: le dépot local n'est pas à jour (commit nécessaire)

### Le fichier `/aliases` : *(Sourcé par bash\_profile)*
Contient l'ensemble des alias (certains définis conditionnellement grace à l'encapsulation dans des fonctions applées
après des tests.

#### Cas particulier, alias alert utilisant notify-send sous WSL2...
Si on veux utiliser cet alias (mis par défaut) on se rend compte qu'il faut installer 415
paquets (dont ceux pour serveur X11) et le tout sans grande chance de fonctionner!
Une alternative serait d'utiliser les outils analogue sous windows comme [BurntToast](https://github.com/Windos/BurntToast)
(Voir l'astuce de [Blog CodeLearn](https://codelearn.me/2019/01/13/wsl-windows-toast.html)

### `~/.bash_logout` : _(login shell=>Link ds le HOME)_
Y placer les commandes que l'on souhaite exécuter lors de la déconnexion de la session.

#### tuer le ssh-agent!
A la déconnexion, en utilisant la fct-bash `check_ssh` dans un test, on peut tuer l'agent ssh en cours!
```bash
nbr_agent-ssh || eval $(ssh-agent -k)
```
### Le fichier `history`
Correspond au fichier contenant l'historique des commandes de bash.
Par défaut ce fichier est `$HOME/.bash_history`, cependant, pour ne pas surcharger le $HOME de dotfiles on préfère le
placé dans le `$BASH_DIR/history`

_BUG : Bien que déplacé dans le $BASH_DIR dans `bash_profile` il arrive souvent que pour les premières commandes tapé, un fichier `~/.bash_history` soit créé...puis le changement s'effectue et ce fichier vestigial peut alors etre supprimé manuellement..._

## 1.2 - Fonctionnement des SCRIPTS
### `install.sh`
Ce script permet l'installation des configurations personnelles de bash, pour cela il:
- 1 : **Vérifie** que le shell utilisé est bien **bash**, sinon quitte l'éxécution en produisant l'erreur 66
- 2 : **Vérifie** la valeur de la variable d'environnement $BASH_DIR correspond bien à l'emplacement actuel/choisi
- 3 : **Archive** dans le dossier ~/.backupfiles/ (le crée si besoin) l'ensemble des BDF actuels.
- 4 : **Crée** trois liens symboliques (indispensable) dans le $HOME : `~/.bash_profil` & `~/.bashrc` & `~/.bash_logout`

### `restore_bash.sh`
Ce script permet "d'annuler" les modifications apportées par `install.sh`, pour ce faire il:
- 1 : **Supprime** les liens symboliques pointant vers des BDF s'ils existent
- 2 : **Restaure** dans le home l'ancienne version des BDF (Séléction automatique de la plus anciennes s'il en existe
  plusieurs)
- 3 : **Supprime** le dossier de sauvegarde ayant été créé aujourdhui... (utile pour phase de test successifs)

_Pour choisir le dossier contenant les bashdotfiles s'il en existe plusieurs, changer ds le script `${OriginFolder}`_

## 1.3 Fonctions et Commandes BASH
### `$ coucou`
Fonction créée pour apprendre les règles de base d'écriture et d'éxécution de commande shell.(utilisation argument)

- Si aucun argument, dit bonjour à l'utilisateur
    ```bash
    $ coucou 
    Bonjour alter.GNU
    ```

- Si argument passé (autre que les options acceptées) fait le perroquet!
    ```bash
    $ coucou "Y a quelqu'un qui ma dit que..."
    "Y a quelqu'un qui ma dit que..."
    ```

- La commande accepter deux options `-q/--quit` ou `-h/--help`
    ```bash
    $ coucou -q "Y a quelqu'un qui ma dit que..."
    ssshhut!
    $ coucou -h
    ...
    AFFICHE L'USAGE
    ...
    ```
### `$ nbr_agent-ssh` 
**Retourne le nombre d'agent ssh actif (methode parcourant procfs).**

Cette fonction, peut donc être utiliser comme une fonctionnelle/un test!

Elle parcours le **procfs** à la recherche de processus s'appelant `(ssh-agent)`, si elle en trouve elle incrémente une
variable...puis la retourne.

Ainsi s'il n'y a pas d'SSH-AGENT en cours, elle retourne 0, sinon elle retourne le nombre de processus portant ce nom!

À l'instar des autres fonctions elle accepte en options:
- `-v/--verbose`: la rendant tchatty!
-  `-h/--help`: affiche l'usage

### `$ check_git` 
**Vérifie qu'un dossier est bien dans l'arborescence d'un dépôt git local**:
- **Sans argument** : 
    - retourne 0 si on se trouve dans un dossier git
    - retourne 8 si pas dans un dossier git
- **Avec argument** : 
    - retoune 0 si l'argument est un dossier git
    - retourne 28 si le dossier passé en paramètre n'est pas un dossier git

_Ainsi tout autres retour d'erreurs (différents de 8 ou 28) correspond à une mauvaise utilisation de la commande_

Elle admet aussi deux options :
- `-v/--verbose`: la rendant tchatty!
-  `-h/--help`: affiche l'usage

### `$ ppticopy` 
**Permet de récupèrer des documents de la PPTI vers le PC-Perso (ssh-cp -r)**

Cette fonction est juste un alias de la commande `$ scp -r <docppti> <dossier> ` où:
    - `<docppti>` est le chemin, partant du home\_ppti, menant au dossier ou document à copier
    - `<dossier>` est le chemin, partant du pwd sur la machine actuelle, où l'on souhaite coller le dossier ou document
      précédemment copié.

- Si `<docppti>` :
    - `-h/--help` : retourne l'usage
- Si `<dossier>` 
    - n'est pas précisé, copier `<docppti>` dans l'emplacement courant ($PWD).
    - correspond à un dossier, y copie `<docppti>`.
    - n'existe pas sur la machine, propose à l'utilisateur de créer le dossier avant d'y copier `<docppti>`.

# Sources
- [Bash startup files](https://www.gnu.org/software/bash/manual/html_node/Bash-Startup-Files.html#Bash-Startup-Files)
- [bashrc vs bash_profile](https://linuxize.com/post/bashrc-vs-bash-profile/)
- [Create bash aliases](https://linuxize.com/post/how-to-create-bash-aliases/)
- [Create bash functions](https://linuxize.com/post/bash-functions/)
