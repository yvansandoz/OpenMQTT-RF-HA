# OpenMQTT RF avec HA
Mais c'est si simple !

![](https://github.com/yvansandoz/OpenMQTT-RF-HA/blob/4fbbad7d5b96a7a5aa151c5fdb4944f830195c67/pictures/OpenMQTTGateway_Logo.jpg)

Nous passons ici en revue les étapes de la construction, de la configuration et de l'intégration d'un émetteur/récepteur RF 433.92 MHz pour Home Assistant. La plateforme OpenMQTT offre la possibilité de créer d'autres passerelles (LoRa, Bluetooth, …), mais nous nous limitons ici à l'option RF.

Ce descriptif ne remplace pas la [documentation officielle](https://https://docs.openmqttgateway.com) qui sert de base à ce document.

GitHub original: [OpenMQTTGateway](https://github.com/1technophile/OpenMQTTGateway.git)\
Crédits: [1technophile](https://github.com/1technophile)


# Table des matières

1. [Construction](#Construction)
2. [Configuration](#Configuration)
3. [Intégration](#Intégration)


# Construction

## Matériel

Les options choisies pour les émetteur/récepteur sont :

- Émetteur : STX822
- Récepteur : SRX822

<img src="https://github.com/yvansandoz/OpenMQTT-RF-HA/blob/4fbbad7d5b96a7a5aa151c5fdb4944f830195c67/pictures/stx_srx_822.JPG" alt="822"
	title="STX822/RTX822" width="100" height="100" />

- Récepteur : Wemos D1 mini

<img src="https://github.com/yvansandoz/OpenMQTT-RF-HA/blob/4fbbad7d5b96a7a5aa151c5fdb4944f830195c67/pictures/wemos_d1_mini.jpg" alt="Wemos"
	title="Wemos D1 mini" width="100" height="100" />

Il existe des kits avec le STX822 et le SRX822 sur Banggood et sur Aliexpress. Bastelgarage.ch offre aussi des composants similaires à un prix très attractif.

### Commentaire

Avec les composants choisis ci-dessus, la passerelle fonctionne en 433.92 MHz, ce qui couvre une grande plage d'accessoires RF. Pour les stores Somfy, il faut une passerelle 433.42 MHz. Le module émetteur/récepteur CC1101 décrit dans la documentation officielle couvre les deux plages et pourrait être une meilleure option, mais elle n'a été pas testée.

## Schéma de branchement

Les composants RTX822 et STX822 ne sont pas exactement les bons sur le schéma, mais vous comprendrez avec l&#39;aide du tableau ci-dessous.

| Composants       | Connexions |&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp; &nbsp;|&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp; &nbsp;|&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp; &nbsp;|
| ---------------- | ---------- | ---- | ---- | ---- |
| Wemos D1 mini    | 5V         | G    | D1   | D2   |
| SRX822           | VCC        | GND  | CS   |      |
| STX822           | VCC        | GND  |      | DATA |

![](https://github.com/yvansandoz/OpenMQTT-RF-HA/blob/4fbbad7d5b96a7a5aa151c5fdb4944f830195c67/pictures/OpenMQTTGateway_Sketch.png)


# Configuration

L'option 3 du mode de configuration est choisie, avec l'aide du logiciel Arduino IDE, c'est l'option qui offre la plus grande flexibilité de configuration.

Les 2 fichiers (User_config.h et config_RF.h) dont la modification est décrite ci-dessous sont disponibles déjà modifiés dans le dossier ["main"](https://github.com/yvansandoz/OpenMQTT-RF-HA/tree/main/main) de ce projet.

## Programmation de l'ESP

1. Assurez-vous d'avoir la dernière version de Arduino IDE avec la libraire ESP8266 (ou ESP32 en fonction de l'ESP choisi) ([tutoriel](https://github.com/esp8266/Arduino#installing-with-boards-manager)).
2. Télécharger la librairie spécifique à ce projet (RF avec Wemos D1 mini): [nodemcuv2-rf-librairies.zip](https://github.com/1technophile/OpenMQTTGateway/releases/download/v0.9.8/nodemcuv2-rf-libraries.zip) (Vous trouvez ici le lien pour la liste de toutes les autres librairies au cas où vous souhaitez partir sur d'autres composants ou pour créer un autre type de passerelle : [lien](https://github.com/1technophile/OpenMQTTGateway/releases))
3. A l'intérieur du dossier décompressé, il y a 6 dossiers qu'il faut déplacer individuellement dans le dossier de vos librairies Arduino (exemple : Users/xxxx/Documents/Arduino/libraries)
4. Télécharger les fichiers de programmation depuis GitHub (Cliquer sur le bouton vert « Code » pour accéder au fichier .zip) : [https://github.com/1technophile/OpenMQTTGateway](https://github.com/1technophile/OpenMQTTGateway)
5. Décompresser ce dossier où vous souhaitez conserver l'ensemble des documents relatifs à ce projet.
6. Éditer le fichier User\_config.h qui se trouve dans le dossier OpenMQTTGateway/main. Enlever les 2 caractères de commentaire "//" en début des lignes suivantes pour obtenir :\
Pour la configuration du module RF :\
 &nbsp;&nbsp;- ligne 292 : #define ZgatewayRF "RF"//ESP8266, Arduino, ESP32\
 Pour la découverte automatique des modules dans HA:\
 &nbsp;&nbsp;- ligne 316 : #define ZmqttDiscovery "HADiscovery"//ESP8266, Arduino, ESP32, Sonoff RF Bridge
7. Éditer le fichier config\_RF.h qui se trouve dans le dossier OpenMQTTGateway/main. C'est ici que nos indiquons les connexions correspondantes à notre schéma de branchement :\
&nbsp;&nbsp;- ligne 127 : # define RF\_RECEIVER\_GPIO 4\
&nbsp;&nbsp;- ligne 139 : # define RF\_EMITTER\_GPIO 5
9. Avec l'application Arduino IDE, ouvrir le fichier main.ino qui se trouve dans le dossier OpenMQTTGateway/main.
10. Dans le menu Tools/Board, choisir « LOLIN (WEMOS) D1 mini »
11. Dans le menu Tools/Port, choisir le port USB sur lequel est branché votre ESP
12. Uploader votre code.


## Configuration du Réseau WiFi et de MQTT

Depuis un smartphone, se connecter au WiFi créé par l'ESP : OpenMQTTGateway

Un page web va s'ouvrir. Si vous n'avez rien changé au code le mot de passe est "your\_password". Il vous faudra ensuite introduire les paramètres du réseau WiFi sur lequel votre ESP doit se connecter et les paramètre de votre broker MQTT (voir documentation officielle pour la configuration).

# Intégration

Après redémarrage de HA votre module OpenMQTTGateway apparaîtra automatiquement dans la liste de vos devices.

Plus à suivre après les premiers essais dans HA...