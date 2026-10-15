# Mon badgeage

Application Android locale de suivi d'heures par un seul badge NFC NTAG216.

## Fonctionnement

1. Publier d'abord la page GitHub Pages préparée pour `kamtargaming.github.io`, puis écrire son URL dans le NTAG216 avec NFC Tools.
2. Premier scan : le navigateur propose de télécharger l'APK.
3. Une fois l'application installée, le même scan ouvre directement *Mon badgeage* et crée un pointage.
4. Après une réinstallation de l'application, le premier scan du badge déjà préparé réassocie automatiquement le badge et crée le pointage.

Le badge ne contient pas les heures, ni l'application elle-même. Il contient seulement l'URL de téléchargement et de lancement. Un NTAG216 ne peut pas contenir un APK Android, qui est bien plus gros. Les pointages restent uniquement dans l'espace privé de l'application sur le téléphone.

## Règles de calcul de cette première version

- premier scan de la journée : entrée ;
- scan suivant : sortie/interruption et lancement du compteur visuel ;
- scan suivant : reprise, qui termine l'interruption ;
- les scans suivants alternent de la même façon ;
- une coupure de plus de 45 minutes, entièrement comprise entre 11 h et 15 h, est nommée « interruption de midi » ;
- à partir de 6 h travaillées, 30 minutes de pauses payées sont incluses dans la journée et ne modifient jamais le solde de récupération, même si elles durent plus longtemps ;
- sans pointage détaillé, l'application suppose une entrée à 8 h, deux pauses payées de 15 minutes à 10 h et 15 h, ainsi qu'une interruption de midi de 12 h à 13 h ;
- si aucune pause payée n'a été badgée le jour ouvré précédent, l'application demande si elle a été prise sans badge ou si ses 30 minutes doivent être reportées sur la journée actuelle ;
- lundi : objectif de 7 h ; mardi à vendredi : objectif de 8 h ; total de référence : 39 h par semaine.

Une sortie sans scan de reprise reste affichée comme une sortie en cours pour la journée. Les données sont enregistrées localement via les préférences privées Android, sans compte ni synchronisation.

## Import des relevés

Le menu **Importer une feuille** accepte les photos et les scans, les PDF de plusieurs pages, les fichiers Excel `.xlsx` ou `.xlsm`, les CSV et les fichiers texte. Les PDF scannés sont analysés page par page sur le téléphone. Pour Excel, la colonne **Payé Jour** est prioritaire et les colonnes **Effectifs Jour** et **HS normale semaine** ne sont pas importées à sa place. L'ancien format binaire `.xls` doit être enregistré en `.xlsx`.

Le menu **Exporter l'agenda** enregistre un CSV local détaillé avec les pointages, les durées importées, l'objectif de chaque journée, son impact sur le solde, le cumul et les jours restant à valider. Les semaines partielles comptent toutes les journées réellement renseignées ; une journée absente reste neutre jusqu'à sa validation.

Le solde est recalculé directement depuis les journées enregistrées, sans conserver d'ancien ajustement interne. Samedi et dimanche sont toujours neutres.
