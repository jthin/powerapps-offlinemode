# PowerApps - Mode offline
Le mode offline de PowerApps est encore en cours de développement au moment de la publication des fichiers de ce repo. 

Le but de ce projet est de vous montrer ce qui est possible de faire.

Solution|Version|Auteur(s)
--------|---------|---------
PowerApps-OfflineTasksList|1.0|Jérôme THIN ([Cellenza](https://github.com/Cellenza) - @JeromeThin)

## Comment tester ce projet ?
### Prérequis
- Un compte Office 365 avec SharePoint et PowerApps activés
- Un site SharePoint pour pouvoir créer une liste
- Un navigateur moderne (si Internet Explorer, IE >= 10)

### Liste SharePoint (source de données)
- Créer une liste SharePoint personnalisée (Custom List).

    * Nom : TasksList

- Ajouter les colonnes suivantes à la liste :
    
| Nom de la colonne  | Type  | Obligatoire  | Commentaire |
|---|---|---|---|
| Description   | Multilignes | Non  | |
| Status  | Choix (bouton radio)  | Non  | Valeurs possibles : To do, In progress, Acceptance, Done |
- Ajouter quelques tâches.

### Démarrer sur PowerApps
Je vous recommande de passer votre navigateur en anglais (ou langue anglaise en langue préférée).
- Se rendre au lien https://eu.create.powerapps.com/studio/
- Open > Browse. Uploader le fichier "PowerApps-OfflineTasksList.msapp".
- Dans la pop-up, valider la connexion avec SharePoint.
- Dans l'éditeur PowerApps, View > Data sources. 
    
    * Supprimer la data source TasksList existante.
    * Ajouter une data source.
    * Rechercher SharePoint puis saisir l'url de la liste TasksList.
    * Dans l'écran "Choose a list", sélectionner "TasksList".
    * Une fois la connexion validée, vous devriez voir les tâches déjà créées dans la liste.