# App exemple pour YunoHost

[![Niveau d'intégration](https://dash.yunohost.org/integration/domoticz.svg)](https://dash.yunohost.org/appci/app/domoticz) ![](https://ci-apps.yunohost.org/ci/badges/domoticz.status.svg) ![](https://ci-apps.yunohost.org/ci/badges/domoticz.maintain.svg)  
[![Installer domoticz avec YunoHost](https://install-app.yunohost.org/install-with-yunohost.png)](https://install-app.yunohost.org/?app=domoticz)

*[Read this readme in english.](./README.md)* 

> *Ce package vous permet d'installer domoticz rapidement et simplement sur un serveur YunoHost.  
Si vous n'avez pas YunoHost, consultez [le guide](https://yunohost.org/#/install) pour apprendre comment l'installer.*

## Vue d'ensemble
Domoticz est un système de domotique permettant de controler différents objets et de recevoir des données de divers senseurs
Il peut par exemple être utilisé avec :

* des interrupteurs
* des senseurs de portes
* des sonnettes d'entrées
* des systèmes de sécurité
* des stations météo pour les UV, la pluie, le vent...
* des sondes de températures
* des sondes d'impulsions
* des voltmètres
* Et bien d'autres

**Version incluse :** Toujours la dernière version stable. La dernière version compilée est récupérée dans [ce répertoire](https://releases.domoticz.com/releases/?dir=./beta)
Une fois installée, **les mises à jour de l'application sont gérées depuis les menus de l'application elle même.**. Le script de mise à jour Yunohost mettra uniquement à jour de nouvelles version du package.

## Configuration

### Senseurs, langue et ce genre de choses
Toute la configuration de l'application a lieu dans l'application elle même
Main configuration of the app take place inside the app itself.

### Accès et API
Par défaut, l'accès aux [API JSON](https://www.domoticz.com/wiki/Domoticz_API/JSON_URL's) est autorisé sur cette URL `/votredomaine.tld/api_/chemindedomoticz`.
Donc, si vous accédez à domoticz par https://votredomaine.tld/domoticz, utilisez le chemin suivant pour l'api: `/votredomaine.tld/api_/domoticz/json.htm?votrecommandeapi`

Par défaut, seuls la mise à jour de senseur et les interrupteurs sont autorisés. Pour autoriser une nouvelle commande, vous devez (pour l'instant) manuellement éditer le fichier de configuration nginx :
````
sudo nano /etc/nginx/conf.d/yourdomain.tld.d/domoticz.conf
````
Puis éditer le bloc suivant en y ajoutant le regex de la commmande à autoriser :
````
  #set the list of authorized json command here in regex format
  #you may retrieve the command from https://www.domoticz.com/wiki/Domoticz_API/JSON_URL's
  #By default, sensors updates and toggle switch are authorized
  if ( $args ~* type=command&param=udevice&idx=[0-9]*&nvalue=[0-9]*&svalue=.*$|type=command&param=switchlight&idx=[0-9]*&switchcmd=Toggle$) {
    set $api "1";
    }
````
Par exemple, pour ajouter la commmande json pour retrouver le statut d'un équipement (/json.htm?type=devices&rid=IDX),il faut modifier la ligne comme ceci:
````
  #set the list of authorized json command here in regex format
  #you may retrieve the command from https://www.domoticz.com/wiki/Domoticz_API/JSON_URL's
  #By default, sensors updates and toggle switch are authorized
  if ( $args ~* type=command&param=udevice&idx=[0-9]*&nvalue=[0-9]*&svalue=.*$|type=command&param=switchlight&idx=[0-9]*&switchcmd=Toggle$|type=devices&rid=[0-9]* ) {
    set $api "1";
    }
````

Toutes les adresses IPv6 et les adresses IPv4 du réseau local (192.168.0.0/24) sont autorisées pour l'API.
A ma connaissance, il n'y a pas moyen d'effectuer un tel filtre pour les adresses IPv6, vous pouvez donc retirer leur autorisation en enlevant ou en commentant la ligne suivante dans `/etc/nginx/conf.d/yourdomain.tld.d/domoticz.conf`:
````
allow ::/1;
````
Ceci autorisera seulement les adresses IPv4 local a accéder aux API de domoticz.
Vous pouvez ajouter des adresses IPv6 de la même façon.

## Documentation

 * Documentation officielle : Utilisez le [wiki](https://www.domoticz.com/wiki/Main_Page) et le  [manuel utilisateurs](https://www.domoticz.com/DomoticzManual.pdf) pour plus d'informations
 * Documentation YunoHost : Si une documentation spécifique est nécessaire, n'hésitez pas à contribuer.

## Caractéristiques spécifiques YunoHost


#### Architectures supportées

A la fois les architectures ARM et x86 devraient être supportées.
Les sources compilées sont directement téléchargées par le package.

## Limitations

* Pas de gestion d'utilisateurs ni d'intégration LDAP. L'application ne [prévoit pas de gérer les utilisateurs par LDAP](https://github.com/domoticz/domoticz/issues/838), donc le package non plus.
* Un backup ne peut pas être restauré sur un type de machine différente de celle d'origine (x86, arm...) car les sources compilées doivent être différente


**Plus d'informations sur la page de documentation :**  
https://yunohost.org/packaging_apps

## Liens

 * Signaler un bug : https://github.com/Yunohost-Apps/domoticz_ynh/issues
 * Site de l'application : https://domoticz.com/
 * Dépôt de l'application principale : https://github.com/domoticz/domoticz
 * Site web YunoHost : https://yunohost.org/

---

Informations pour les développeurs
----------------

**Seulement si vous voulez utiliser une branche de test pour le codage, au lieu de fusionner directement dans la banche principale.**
Merci de faire vos pull request sur la [branche testing](https://github.com/YunoHost-Apps/domoticz_ynh/tree/testing).

Pour essayer la branche testing, procédez comme suit.
```
sudo yunohost app install https://github.com/Yunohost-Apps/domoticz_ynh/tree/testing --debug
ou
sudo yunohost app upgrade domoticz -u https://github.com/Yunohost-Apps/domoticz_ynh/tree/testing --debug
```
