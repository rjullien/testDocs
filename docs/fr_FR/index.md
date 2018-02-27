# Description

Plugin permettant de récupérer les informations des Xiaomi Plants ou MiFlora.

Les Xiaomi Plants sont des sticks Bluetooth de la marque Xiaomi permettant de mesurer l'hygrométrie, la température, la lumière, l'engrais d'une plante. Nul besoin d'autre chose qu'une connexion Bluetooth, y compris via un appareil déporté.


# Market

Retrouvez le sur le Market Jeedom [ici](https://www.jeedom.com/market/index.php?v=d&p=market&type=plugin&&name=MiFlora)

# Prévisualisation

![scrennshot1](../images/MiFlora-Screenshot1.png)

# Configuration

### Configuration du plugin

La partie configuration du plugin permet :

* de choisir la fréquence de relevés des informations du MiFlora, de toutes les heures à toutes les 12 heures,
* de choisir le dongle/port Bluetooth (`hci0` en général, `hciconfig` permet de lister ceux disponibles sur votre système),
* de choisir le niveau de sécurité de la communication Bluetooth (<b>high</b> sauf si ce n'est pas supporté par votre système),
* de choisir entre un mode local et un mode déporté.

Pour chaque équipement, il faut rentrer l'adresse Bluetooth de l'équipement.

Il est possible de trouver celle-ci en utilisant les commandes :

```
bluetoothctl
scan on
```

Les adresses des MiFlora apparaissent comme ci-dessous:
```
[NEW] Device C4:7C:8D:xx:xx:xx Flower mate
[NEW] Device C4:7C:8D:xx:xx:xx Flower care
scan off
quit
```

### Utilisation en déporté

Il est possible que Jeedom pilote un appareil déporté qui se chargera de la communication Bluetooth avec les MiFlora.

Dans ce cas, il faut renseigner les paramètres de connexion SSH entre Jeedom et cet appareil déporté :

Le choix déporté active la partie configuration qui permet de saisir une adresse IP, un port, un nom d'utilisateur ainsi que le mot de passe associé pour l'appareil distant.
Le plugin va alors se connecter en SSH à l'IP saisie précédemment et récupérer les informations du MiFlora en Bluetooth grâce à la commande *gatttools*

Aucun Jeedom n'est nécessaire sur l'équipement distant.

### Pré-requis

Il faut installer le Bluetooth et s'assurer que *gatttool --device=hci0 -b _macAddMiFlora_ --char-read -a 0x35 --sec-level=high* fonctionne sur l'appareil cible (selon le choix local ou déporté).

### Configuration avancée

*hci :* permet de choisir le dongle Bluetooth pour ceux qui en ont plusieurs.

*niveau de sécurité :* permet de choisir le niveau de sécurité Bluetooth, *high* semble bien dans la majorité des cas, cependant changer le niveau de sécurité semble résoudre certains problèmes de connexions.

### Mode debug

Le mode debug permet de lancer en permanence le recuperation des données MiFlora. Il convient de limiter son utilisation au debug.

# FAQ

### Est-ce que ce plugin s'appuie sur des API tiers ?

Le plugin utilise le Bluetooth pour récupérer les informations du MiFlora.
Il faut installer le Bluetooth et s'assurer que gatttool -b macAddMiFlora --char-read -a 0x35 fonctionne sur le device cible.

### Est ce que ce plugin est compatible avec le plugin FlowerPower ?

Oui, validé par Nechry.


### Est ce que ce plugin monopolise le Bluetooth ?

Non pas du tout, il a besoin du Bluetooth pour chaque relevé, cf question suivante pour plus de détails sur le nombre de relevés par jour.


### Combien de fois par jour les mesures sont-elles récupérées ?

C'est défini dans la configuration globale du plugin, pour tous les objets : de toutes les heures à toutes les 12 heures.
J'utilise le modulo de l'heure actuelle avec la fréquence saisie en paramètre. +
Attention: en mode debug, les données sont recuperés en permanence independament de la configuration.

Les informations statiques (batterie, nom de l appareil, version du firmware) sont récupérées toutes les 12 heures : à minuit et midi.


### Avec quelle version de firmware ce plugin est-il compatible ?

Il est compatible avec toutes les versions connues à ce jour (2.9.2) depuis la version 1.0 du plugin.


### Je possède un Rpbi3, J'ai dû désactiver le Bluetooth interne pour ne pas avoir d'interférence avec le Zwave (razberry). Est-ce qu'il faut toujours garder le Bluetooth interne désactivé pour résoudre ce souci ? Sinon est-ce que n'importe quelle clef USB BT fait l'affaire pour être compatible avec les MiFlora et le rpbi3 ?

Il faut prendre un dongle BLE sans faute. Le problème avec le razberry c'est seulement si on utilise le contrôleur interne.


### Je souhaite contribuer à l'amélioration de ce plugin, est ce possible ?

Bien sur, le code est sur GitHub : rjullien/jeedom_MiFlora, vous pouvez soumettre des pull requests.

### gatttool est instable et se bloque sur RPI

Il y a beaucoup de configuration qui peuvent génèrer ce problème. Avec Pixel il faut faire attention d'avoir un seul gestionaire de bluetooth.
BlueZ est incompatible avec blueman (sudo apt-get remove blueman)

### Le plugin fonctionne bien: que puis je faire avec ?

Les valeurs d'humidité, de fertilité, de luminosité et de temperatures sont accesible depuis des scenarios.

Il est possible de lire ces valeurs, de les comparer à un seuil et d'alerter en cas de dépassement du seuil, par exemple pour arroser une plante.

Les alertes peuvent etre données par du 'text to speech' (plugin playTTS par exemple), par notification sur smartphone (plugin pushbullet), par SMS ...

Les seuils peuvent etre trouvés en utilisant la base de plantes de Xiaomi ou celle de Parrot à defaut un seuil entre 14 et 16 semble convenir à une majorité de plantes d'interieur.
