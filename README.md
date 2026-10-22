# Mon badgeage

Application Android locale de suivi d'heures par un seul badge NFC NTAG216.

## Version 2.0.1

L'interface et le moteur métier de la version 2.0.1 sont écrits en C avec
AromaUI. Il n'y a plus de code Kotlin. Un petit pont Java reste nécessaire pour
les services fournis par Android : activité native, NFC/NDEF, notifications,
clavier, sélection de fichiers et installation des mises à jour. Les calculs,
les écrans et les règles de pointage restent dans le moteur C.

La version 2.0.1 corrige l'agrandissement excessif de toute l'interface sur les
écrans Android à forte densité. La question sur les pauses payées utilise
désormais une page normale avec trois boutons verticaux, sans fenêtre modale.

## Fonctionnement

1. Publier d'abord la page GitHub Pages préparée pour `kamtargaming.github.io`, puis écrire son URL dans le NTAG216 avec NFC Tools.
2. Premier scan : le navigateur propose de télécharger l'APK.
3. Une fois l'application installée, le même scan ouvre directement *Mon badgeage* et crée un pointage.
4. Après une réinstallation de l'application, le premier scan du badge déjà préparé réassocie automatiquement le badge et crée le pointage.

Le badge ne contient pas les heures, ni l'application elle-même. Il contient seulement l'URL de téléchargement et de lancement. Un NTAG216 ne peut pas contenir un APK Android, qui est bien plus gros. Les pointages restent uniquement dans l'espace privé de l'application sur le téléphone.

Le menu **Lier un tag NFC** permet d'ajouter d'autres badges ou tags sans supprimer ceux déjà associés et sans créer de pointage pendant l'association.

Sur Android récent, une URL NFC seule peut être présentée comme une notification à toucher. Le menu **Réécrire le tag NFC** écrit le lien GitHub et un Android Application Record propre à Mon Badgeage, relit le tag, vérifie la présence des deux enregistrements puis lie ce tag à l'application avant de confirmer la réussite. Cette opération n'est nécessaire qu'une fois par tag. Lorsque l'application est déjà visible, le mode lecteur NFC direct intercepte le badge depuis la journée actuelle, une archive ou l'agenda, même si l'écran est resté ouvert longtemps. Si Android intercepte le lien lorsque l'application est fermée, la page propose d'abord **Ouvrir Mon Badgeage**, puis le téléchargement de l'APK si l'application n'est pas installée.

Le menu **Badger manuellement** enregistre le même événement que le badge à l'heure actuelle après confirmation. Seule une journée composée exclusivement de scans NFC restés intacts affiche **Journée badgée uniquement par NFC**. Un import texte ou Excel, une saisie manuelle, une heure ajoutée par l'application ou la correction d'un scan affiche **Journée modifiée**, avec le détail de sa provenance. Les modifications saisies sont validées explicitement avant de quitter le mode d'édition.

## Calcul du solde

Le solde commence à zéro. L'application ne contient aucune date, durée ou référence préremplie. Si l'entreprise fournit un cumul **HS normale semaine**, il peut être saisi manuellement comme **solde officiel de référence** avec sa date de fin. Les heures antérieures restent visibles dans l'archive mais ne sont alors plus recalculées.

Après cette date, l'application ajoute seulement les écarts des journées renseignées :

- lundi : objectif de 7 h ; mardi à vendredi : 8 h ; une semaine complète correspond donc à 39 h ;
- une journée complète ajoute exactement `durée travaillée - objectif du jour` ; une journée NFC encore ouverte reste visible mais n'affecte pas le solde ;
- une semaine incomplète compte immédiatement les jours renseignés ; une journée absente est neutre tant qu'elle n'est pas validée ;
- une absence peut être laissée neutre ou marquée « à déduire » ; dans ce dernier cas, l'objectif du jour est retiré ;
- samedi et dimanche ne modifient jamais le solde.

Dans **Options → Définir le solde de référence**, on peut saisir, corriger ou supprimer ce cumul facultatif.
Sur la page principale, un glissement vers le bas affiche brièvement un indicateur central, recalcule le solde et actualise l'affichage. Un glissement horizontal parcourt les journées enregistrées avec une transition latérale : vers la droite pour remonter dans l'historique, vers la gauche pour revenir vers aujourd'hui.
La page principale affiche le total de la semaine puis les heures travaillées du mois en cours.
Lorsqu'une période de travail est ouverte, elle indique aussi l'heure de départ estimée pour atteindre l'objectif du jour. Cette heure tient compte des pointages déjà effectués et de l'interruption de midi.

## Pointages NFC

- premier scan de la journée : entrée ;
- scan suivant : sortie/interruption et lancement du compteur visuel ;
- scan suivant : reprise, qui termine l'interruption ;
- les scans suivants alternent de la même façon ;
- une coupure de plus de 45 minutes, entièrement comprise entre 11 h et 15 h, est nommée « interruption de midi » ;
- une coupure commencée après 16 h devient automatiquement une sortie dès qu'elle dépasse 2 h ;
- une nouvelle sortie devient immédiatement la fin de journée lorsque l'objectif de 7 h le lundi ou 8 h du mardi au vendredi est atteint et qu'au moins 26 des 30 minutes de pauses payées ont déjà été prises ;
- lorsqu'une autre pause se termine, **Hors taxe** permet de retirer ses deux scans : elle disparaît du relevé et du compteur de pauses ;
- à partir de 6 h travaillées, 30 minutes de pauses payées sont incluses dans la journée et ne modifient jamais le solde de récupération, même si elles durent plus longtemps ;
- aucune interruption de midi n'est inventée lorsqu'elle n'a pas été badgée ;
- si aucune pause payée n'a été badgée le jour ouvré précédent, l'application demande si elle a été prise sans badge ou si ses 30 minutes doivent être reportées sur la journée actuelle ;
- pendant une pause payée, une notification persistante affiche le compteur actualisé chaque seconde et calcule à la seconde près le temps encore disponible ainsi que l'heure de reprise en déduisant les pauses déjà prises ; une interruption commencée entre 11 h et 15 h n'annonce pas arbitrairement un retour dans 30 minutes, et une sortie n'affiche aucune notification de pause ;
- pendant la minute qui suit un pointage NFC, **Annuler le dernier scan** permet de supprimer immédiatement un badgeage accidentel.
- chaque scan affiche une confirmation centrale colorée — entrée, pause, reprise ou sortie — accompagnée d'une vibration propre à l'action ;
- le soir, si la dernière alternance est restée ouverte, une notification signale la sortie probablement oubliée. L'application propose alors une heure calculée, modifiable avant validation.

Une sortie sans scan de reprise reste affichée comme une sortie en cours pour la journée. Les données sont enregistrées localement via les préférences privées Android, sans compte ni synchronisation.

Depuis une archive, le bouton Retour ouvre l'agenda ; depuis l'agenda, Retour ramène à la journée actuelle. L'icône d'agenda reste accessible directement en haut des pages de journée.

L'accueil présente les heures de la semaine, le solde de récupération, le total du mois et les pointages dans des blocs sobres sur fond noir. Le menu Options forme un panneau noir unique, avec une croix de fermeture intégrée et des groupes séparés par des lignes fines. La liste reste défilable sur les petits écrans.

## Analyses et congés

Toucher le solde, ou choisir **Analyses et bilan**, ouvre l'évolution hebdomadaire du solde, le détail « référence + écarts des journées terminées », le bilan mensuel, la prévision du vendredi et les jours restant à vérifier. Le bilan de la semaine affiche les heures réalisées, l'objectif ajusté et le temps restant pour atteindre les 39 heures.

**Définir des congés** permet de marquer une date ou une période, avec congé payé ou non payé, entier ou en demi-journée. Ces congés sont recensés dans le bilan mensuel et restent neutres pour le solde de récupération. L'application conserve aussi les corrections d'heures ; l'écran d'analyses permet d'annuler la dernière correction. Lors d'un import, chaque date déjà renseignée affiche la durée existante, la durée du fichier et l'écart avant validation.

Pendant la modification d'une journée, la croix à côté d'une heure supprime ce seul pointage après confirmation. Le bouton **Réinitialiser cette journée** efface tous les pointages, la durée importée et la validation de la date ouverte, y compris dans une archive.

Pendant la modification d'une journée, l'écran se redimensionne avec le clavier et fait défiler automatiquement le champ actif pour que l'heure saisie reste visible, y compris en bas d'une archive.

Chaque page d'archive indique le solde de récupération atteint à la fin de cette journée. Pour une date antérieure au solde de référence, l'application le reconstitue en remontant les écarts journaliers connus depuis cette référence.

## Import des relevés

Le menu **Importer l'agenda** accepte les fichiers Excel `.xlsx` ou `.xlsm`, les CSV et les fichiers texte. Les photos et les PDF scannés sont volontairement exclus : ils demandent un OCR, qui peut déplacer une valeur d'une ligne à l'autre. Pour Excel, la colonne **Payé Jour** est prioritaire et les colonnes **Effectifs Jour** et **HS normale semaine** ne sont jamais importées à sa place. L'ancien format binaire `.xls` doit être enregistré en `.xlsx`.

Le menu **Exporter l'agenda** enregistre un CSV local détaillé avec la référence officielle, les pointages, les durées importées, l'objectif de chaque journée, son impact après la référence, le cumul et les jours restant à valider. Ce même CSV peut être choisi dans **Importer l'agenda** pour restaurer intégralement l'agenda après une réinstallation.

Toutes les données restent locales : aucun compte et aucune synchronisation.
