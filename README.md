# Recto-Verso

## Presentation

Recto-Verso is a prototype consisting mainly of two big devices. You can see here one of them :

![rectoverso_borne.jpg](https://photos.erasme.org/images/2020/10/02/rectoverso_borne.jpg)

Each of these two devices has a touchscreen :

![rectoverso_affichage_principal.jpg](https://photos.erasme.org/images/2020/10/02/rectoverso_affichage_principal.jpg)

Using the touchscreen, a user can play a card game named _Concentration_
([english desciption](https://en.wikipedia.org/wiki/Concentration_(card_game)),
[french description](https://fr.wikipedia.org/wiki/Memory_(jeu))).

Both players start simultaneously, and the first who finds all pairs of cards wins.



## Usage

## Installation

### Requirements

Technically, you can have as many players as you want but this would require some minor tweaks in the code...
and each player needs a touchscreen. So two players are perfectly fine.
 
This implies at least one computer with two touchscreens. Each screen would display a certain browser page
generated by the server on the computer.

***Remark :*** One computer and two screens are the minimal requirements but in the case of our devices
we put a computer with its touchscreen in both machines.


### Main server

#### Requirements

You need a server which have apache2, PHP (5 or 7), sqlite and all plugins connecting them each other. This
changes greatly depending on your operating system. You might want *git* too to update safely the application. 


#### Installing the server app

Go to your server file system (most of the time something like `/var/www/html`) and clone the repository.

```sh
# Browse until you're in the correct directory.
cd /path/to/your/www/

# And clone the repository there using git.
git clone https://github.com/urbanlab/rectoverso.git
```

And remember to make sure your server has the rights to read and write the data folder.


#### Uodate the code server side

If you want to apply some changes on the server, just update the depot using *git*.

```sh
# Browse until you're in the correct directory.
cd /path/to/your/www/rectoverso

# And clone the repository there using git.
git pull
```

### Secondary devices

#### System installation

We are going to install two ***raspberry pi*** in each playable device. They only need to display a web page.

For instance, we choose to use ***raspbian*** as our OS. We must proceed this way :   

1. [download](https://www.raspberrypi.org/downloads/raspberry-pi-os/) the last version of ***raspbian OS***.You can
choose whatever version of raspbian you want. The *Lite* may be a bit... too light and require additional actions while
the desktop version has all you need (although do not take the one with all recommended software).
1. Create a disk image on the SD Card using a common software like *gnome-disk-utility* (on Ubuntu, type
`sudo apt install gnome-disk-utility` to install it and `gnome-disks` to start it)
- Turn on your raspberry (while its SD card is in) and follow the instructions (choose the language, keyboard, 
Wi-Fi network, install the last updates...)

#### Autostart rectoverso

Create a file somewhere and give it an evident name like `rectoverso_autostart.sh` and place it in an obvious and
easily accessible emplacement like the desktop. Now, write this inside (*remember to modify the url !*).

```sh
#!/usr/bin/env bash
DISPLAY=:0 chromium-browser --kiosk url/to/access/rectoverso/play.php
```

Once the file is written, give it execution rights, then create a cronjob :

```sh
crontab -e
```

The precedent command will open a file where you have to add an extra line to its end :

```log
@reboot /path/to/your/rectoverso_autostart.sh > /home/pi/Desktop/cron_logs.txt  2>&1
``` 

That will launch the command at each stratup of the *pi*.


####  Rotate the screen

The way to solve this problem changes depending on which *raspberry* you have.

***Raspberry 3***


```bash
# Open the configuration file in a text editor.
sudo nano /boot/config.txt
```


```yaml
# Check if the option display_hdmi_rotate exists.
# - if yes, change it to 3 (or to whatever rotation you need)
# - if no, just copy this line in the text.
# The number increments for each rotation of 90° you do to the screen counter-clockwise (or the trigonometric way).

# Here a rotation of 90° clockwise
display_hdmi_rotate=3
```

Once you added/modified the option in the config file, reboot tour system.

```sh
sudo reboot
```

#### Rotate the touchscreen

```bash
# Install the calibrator 
sudo apt install xinput-calibrator

# Open the file to rotate the touchscreen
sudo nano /usr/share/X11/xorg.conf.d/40-libinput.conf
```

Look for the block of text concerning your touchscreen and add this line :

```text
Section "InputClass"
        Identifier "libinput touchscreen catchall"
        MatchIsTouchscreen "on"
        MatchDevicePath "/dev/input/event*"
        Driver "libinput"
        Option "TransformationMatrix" "0 -1 1 1 0 0 0 0 1"
```

Adding the option `TransformationMatrix` rotates your touchscreen 90° clockwise.


##### Automatic updates

todo

#### Frequent problems

##### My raspberry won't start

The *pi* might need to be connected to a screen. Connect one to your *pi* and turn it on. 


## Old version of the game (using node.js)

The precedent version of the application was written with node.js but we couldn't manage to make it work
again. If you want to access this code, [check the saved v1-branch](https://github.com/urbanlab/rectoverso/tree/sauvegarde-V1).








todo : documentation of
- configuration of the application
- add/modify/choose a language
- document how to set up the screen vertically
- check everything in the github depot (desciption/actions/projects...
- img folder -> what is used ? 

## TODO

Les paires choisies dans la version ajax ne sont pas aléatoires (urgence oblige).
todo : placer toutes les infos côté serveur dans un fichier les regroupant toutes (json ?)


## But de l'application

Recto-Verso a pour but de connecter deux bornes entre elles pour permettre à deux joueurs de jouer
à un jeu de mémoire avec des cartes à retourner par paires. (type [Mémory](https://fr.wikipedia.org/wiki/Memory_(jeu)))


## Fonctionnement général de l'application

Pour le moment, et par souci de simplification, trois machines sont nécessaires.
- une machine-serveur qui sera le maître du jeu
- deux machines-joueuses au code strictement identique

### Actions côté machine serveur

La machine-serveur doit gérer plusieurs choses :
1. attendre un message de départ de la part des deux machines-joueuses et leur répondre en même temps qu'elles
peuvent commencer
1. attendre un message de la part d'une des machines-joueuses annonçant qu'elle a fini et envoyer à l'autre
machine-joueuse qu'elle a perdu


### Actions côté machines-joueuses

Les machines joueuses ont strictement le même comportement. Elles doivent :
- requêter en Ajax la machine-serveur si elles veulent jouer
- dire en Ajax à la machine-serveur si elles ont gagné

## Installation

### SUr la machine-serveur

todo

### Sur les machines-joueuses

todo

## Cas d'utilisation

### Modify datasets general folder

todo : explain configuration file

### Modifier le jeu de données

Vous pouvez avoir plusieurs jeux de données d'images. Pour en changer, placez un dossier contenant ces
images sur la machine-serveur dans le dossier `images/`. Puis modifiez le fichier _configuration.json_
situé à la racine.
```json
{
  "image_directory": "images_rectoverso" 
}
```

### Informations sur les jeux de données

- todo : formats acceptés
- todo : organisation  de l'arborescence du dossier
- todo : quantité de paires acceptées
- todo : changer la langue de l'application
- todo : ajouter une nouvelle traduction
- todo : explain all possibilities in the configuration.json

## Installation

git pull
chmod 777 sur player_queue.txt



## TODOS 

- Use sqlite instead of json file...
- when a pair of cards has been correctly fount, give them an appropriate design ie the fount pairs are clearly distinct of played cards.