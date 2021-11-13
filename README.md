# OpenMQTT RF avec HA
Mais c&#39;est si simple !


![](RackMultipart20211113-4-1mj04oa_html_ea8a5a0ebcb80386.jpg)

# 


Nous passons ici en revue les étapes de la construction, de la configuration et de l&#39;intégration d&#39;un émetteur/récepteur RF 433.92 MHz pour Home Assistant. La plateforme OpenMQTT offre la possibilité de créer d&#39;autres passerelles (LoRa, Bluetooth, …), mais nous nous limitons ici à l&#39;option RF.

Ce descriptif ne remplace pas la documentation officielle qui sert de base à ce document :
[https://docs.openmqttgateway.com](https://docs.openmqttgateway.com/)

GitHub original: https://github.com/1technophile/OpenMQTTGateway.git
Crédits: https://github.com/1technophile


# Table des matières

[Construction 3](#_Toc87694757)

[Matériel 3](#_Toc87694758)

[Commentaire 3](#_Toc87694759)

[Schéma de branchement 3](#_Toc87694760)

[Configuration 4](#_Toc87694761)

[Programmation de l&#39;ESP 4](#_Toc87694762)

[Configuration du Réseau WiFi et de MQTT 4](#_Toc87694763)

[Intégration 5](#_Toc87694764)

# Construction

## Matériel

Les options choisies pour l&#39;émetteur/récepteur sont :

- É ![](RackMultipart20211113-4-1mj04oa_html_c31f9c7c7ce7d037.png) ![](RackMultipart20211113-4-1mj04oa_html_d14afb26d6abb6a4.jpg) metteur : STX822
- Récepteur : SRX822
- ESP : Wemos D1 Mini

Il existe des kits avec le STX822 et le SRX822 sur Banggood et sur Aliexpress. Bastelgarage.ch offre aussi des composants similaires à un prix très attractif.

### Commentaire

Avec les composants choisis ci-dessus, la passerelle fonctionne en 433.92 MHz, ce qui couvre une grande plage d&#39;accessoires RF. Pour les stores Somfy, il faut une passerelle 433.42 MHz. Le module émetteur/récepteur CC1101 décrit dans la documentation officielle couvre les deux plages et pourrait être une meilleure option, mais elle n&#39;a été pas testée.

## Schéma de branchement

Les composants RTX822 et STX822 ne sont pas exactement les bons sur le schéma, mais vous comprendrez avec l&#39;aide du tableau ci-dessous.

| **Composants** | **Connexions** |
 |
 |
 |
| --- | --- | --- | --- | --- |
| Wemos D1 mini | 5V | G | D1 | D2 |
| SRX822 | VCC | GND | CS |
 |
| STX822 | VCC | GND |
 | DATA |

![](RackMultipart20211113-4-1mj04oa_html_da2d288d91bb6e9f.png)

# Configuration

L&#39;option 3 du mode de configuration est choisie, avec l&#39;aide du logiciel Arduino IDE, c&#39;est l&#39;option qui offre la plus grande flexibilité de configuration.

## Programmation de l&#39;ESP

1. Assurez-vous d&#39;avoir la dernière version de Arduino IDE avec la libraire ESP8266 (ou ESP32 en fonction de l&#39;ESP choisi) ([tutoriel](https://github.com/esp8266/Arduino#installing-with-boards-manager)).
2. Télécharger la librairie spécifique à ce projet (RF avec Wemos D1 mini): [nodemcuv2-rf-librairies.zip](https://github.com/1technophile/OpenMQTTGateway/releases/download/v0.9.8/nodemcuv2-rf-libraries.zip) (Vous trouvez ici le lien pour la liste de toutes les autres librairies au cas où vous souhaitez partir sur d&#39;autres composants ou pour créer un autre type de passerelle : [lien](https://github.com/1technophile/OpenMQTTGateway/releases))
3. A l&#39;intérieur du dossier décompressé, il y a 6 dossiers qu&#39;il faut déplacer individuellement dans le dossier de vos librairies Arduino (exemple : Users/xxxx/Documents/Arduino/libraries)
4. Télécharger les fichiers de programmation depuis GitHub (Cliquer sur le bouton vert « Code » pour accéder au fichier .zip) : [https://github.com/1technophile/OpenMQTTGateway](https://github.com/1technophile/OpenMQTTGateway)
5. Décompresser ce dossier où vous souhaitez conserver l&#39;ensemble des documents relatifs à ce projet.
6. Éditer le fichier User\_config.h qui se trouve dans le dossier OpenMQTTGateway/main. Enlever les 2 caractères de commentaire « // » en début des lignes suivantes pour obtenir :
 Pour la configuration du module RF :
 - ligne 292 : #define ZgatewayRF &quot;RF&quot; //ESP8266, Arduino, ESP32
 Pour la découverte automatique des modules dans HA
 - ligne 316 : #define ZmqttDiscovery &quot;HADiscovery&quot;//ESP8266, Arduino, ESP32, Sonoff RF Bridge
7. Éditer le fichier config\_RF.h qui se trouve dans le dossier OpenMQTTGateway/main. C&#39;est ici que nos indiquons les connexions correspondantes à notre schéma de branchement :
 - ligne 127 : # define RF\_RECEIVER\_GPIO 4
- ligne 139 : # define RF\_EMITTER\_GPIO 5
8. Avec l&#39;application Arduino IDE, ouvrir le fichier main.ino qui se trouve dans le dossier OpenMQTTGateway/main.
9. Dans le menu Tools/Board, choisir « LOLIN (WEMOS) D1 mini »
10. Dans le menu Tools/Port, choisir le port USB sur lequel est branché votre ESP
11. Uploader votre code.

## Configuration du Réseau WiFi et de MQTT

Depuis un smartphone, se connecter au WiFi crée par l&#39;ESP : OpenMQTTGateway

Un page web va s&#39;ouvrir. Si vous n&#39;avez rien changer au code le mot de passe est « your\_password ». Il vous faudra ensuite introduire les paramètres du réseau WiFi sur lequel votre ESP doit se connecter et les paramètre de votre broker MQTT (voir documentation officielle pour la configuration).

# Intégration

Après redémarrage de HA votre module OpenMQTTGateway apparaîtra automatiquement dans la liste de vos devices.

Plus à suivre….
