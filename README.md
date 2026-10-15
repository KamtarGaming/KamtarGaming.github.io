# Mon badgeage

Application Android locale de suivi d'heures par un seul badge NFC NTAG216.

## Fonctionnement

1. Publier d'abord la page GitHub Pages préparée pour `kamtargaming.github.io`, puis écrire son URL dans le NTAG216 avec NFC Tools.
2. Premier scan : le navigateur propose de télécharger l'APK.
3. Une fois l'application installée, le même scan ouvre directement *Mon badgeage* et crée un pointage.
4. Après une réinstallation de l'application, le premier scan du badge déjà préparé réassocie automatiquement le badge et crée le pointage.

Le badge ne contient pas les heures, ni l'application elle-même. Il contient seulement l'URL de téléchargement et de lancement. Un NTAG216 ne peut pas contenir un APK Android, qui est bien plus gros. Les pointages restent uniquement dans l'espace privé de l'application sur le téléphone.

Le menu **Lier un NFC** permet d'ajouter d'autres badges ou tags sans supprimer ceux déjà associés et sans créer de pointage pendant l'association.

## Calcul du solde

Le solde commence à zéro. L'application ne contient aucune date, durée ou référence préremplie. Si l'entreprise fournit un cumul **HS normale semaine**, il peut être saisi manuellement comme **solde officiel de référence** avec sa date de fin. Les heures antérieures restent visibles dans l'archive mais ne sont alors plus recalculées.

Après cette date, l'application ajoute seulement les écarts des journées renseignées :

- lundi : objectif de 7 h ; mardi à vendredi : 8 h ; une semaine complète correspond donc à 39 h ;
- une journée enregistrée ajoute exactement `durée travaillée - objectif du jour` ;
- une semaine incomplète compte immédiatement les jours renseignés ; une journée absente est neutre tant qu'elle n'est pas validée ;
- une absence peut être laissée neutre ou marquée « à déduire » ; dans ce dernier cas, l'objectif du jour est retiré ;
- samedi et dimanche ne modifient jamais le solde.

Dans **Options → Solde de référence**, on peut saisir, corriger ou supprimer ce cumul facultatif.
Sur la page principale, un glissement vers le bas affiche brièvement un indicateur central, recalcule le solde et actualise l'affichage. Un glissement horizontal parcourt les journées enregistrées : vers la gauche pour remonter dans l'historique, vers la droite pour revenir vers aujourd'hui.

## Pointages NFC

- premier scan de la journée : entrée ;
- scan suivant : sortie/interruption et lancement du compteur visuel ;
- scan suivant : reprise, qui termine l'interruption ;
- les scans suivants alternent de la même façon ;
- une coupure de plus de 45 minutes, entièrement comprise entre 11 h et 15 h, est nommée « interruption de midi » ;
- une coupure commencée après 16 h devient automatiquement une sortie dès qu'elle dépasse 2 h ;
- lorsqu'une autre pause se termine, **Hors taxe** permet de retirer ses deux scans : elle disparaît du relevé et du compteur de pauses ;
- à partir de 6 h travaillées, 30 minutes de pauses payées sont incluses dans la journée et ne modifient jamais le solde de récupération, même si elles durent plus longtemps ;
- sans pointage détaillé, l'application suppose une entrée à 8 h, deux pauses payées de 15 minutes à 10 h et 15 h, ainsi qu'une interruption de midi de 12 h à 13 h ;
- si aucune pause payée n'a été badgée le jour ouvré précédent, l'application demande si elle a été prise sans badge ou si ses 30 minutes doivent être reportées sur la journée actuelle ;

Une sortie sans scan de reprise reste affichée comme une sortie en cours pour la journée. Les données sont enregistrées localement via les préférences privées Android, sans compte ni synchronisation.

Depuis une archive, le bouton Retour ouvre l'agenda ; depuis l'agenda, Retour ramène à la journée actuelle. L'icône d'agenda reste accessible directement en haut des pages de journée.

## Import des relevés

Le menu **Importer une feuille** accepte les fichiers Excel `.xlsx` ou `.xlsm`, les CSV et les fichiers texte. Les photos et les PDF scannés sont volontairement exclus : ils demandent un OCR, qui peut déplacer une valeur d'une ligne à l'autre. Pour Excel, la colonne **Payé Jour** est prioritaire et les colonnes **Effectifs Jour** et **HS normale semaine** ne sont jamais importées à sa place. L'ancien format binaire `.xls` doit être enregistré en `.xlsx`.

Le menu **Exporter l'agenda** enregistre un CSV local détaillé avec la référence officielle, les pointages, les durées importées, l'objectif de chaque journée, son impact après la référence, le cumul et les jours restant à valider. Ce même CSV peut être choisi dans **Importer une feuille** pour restaurer intégralement l'agenda après une réinstallation.

Toutes les données restent locales : aucun compte et aucune synchronisation.
