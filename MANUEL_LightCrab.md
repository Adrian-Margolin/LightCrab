# LightCrab — manuel d'utilisation et fiche technique

> Document destiné aux utilisateurs **et** aux assistants (IA) chargés de répondre à leurs questions.
> État : 20 octobre 2026. Source de vérité du code : `LightCrab.html` (fichier unique). Langue source : français (interface FR / EN / UK).
> Les durées ci-dessous sont des **mesures réalisées dans un bac à sable** (Chromium sans tête, essentiellement 1 cœur de calcul). Sur un ordinateur récent à plusieurs cœurs, c'est en général **plus rapide** ; sur un téléphone ou un vieux portable, plus lent.

## 1. Qu'est-ce que LightCrab ?

Application web **mono-fichier** (un seul fichier HTML, sans serveur, sans compte, sans IA générative) qui réunit :
mise en page de livre photo (**Maquette**), développement et retouche non destructifs (**Retouche**), fusions de photos (**HDR**, **Fusion de zones / collage par calques**, **Sandwich**), diaporama, et export (JPEG, PNG, TIFF 16 bits et 32 bits flottant, PSD à calques, page HTML…).
Tout est calculé dans le navigateur (Chrome ou Edge recommandés ; Firefox et Safari fonctionnent avec quelques limites : choix de dossier et corbeille sont réservés à Chrome/Edge).
Les fichiers d'origine ne sont **jamais modifiés** (seuls les exports sont écrits ; exception voulue : la corbeille déplace un fichier dans un sous-dossier `trash`).

Onglets : **Import · Retouche · Maquette · Visionnage · Diaporama · Export**.

## 2. Formats de fichiers pris en charge

| Famille | Formats | Remarques |
|---|---|---|
| Images courantes | JPEG, PNG, WebP… | Ouverture directe. |
| TIFF | 8, 16 et 32 bits (flottant) | Lecture et écriture. |
| **RAW** | RAF (Fujifilm), NEF (Nikon), CR2/CR3 (Canon), ARW (Sony), RW2 (Panasonic), DNG, ORF, PEF, SRW, 3FR/IIQ, ERF, MRW, KDC, DCR, RWL, CRW, NRW | Décodage par **LibRaw (WebAssembly)** embarqué. Vérifiés sur fichiers réels : RAF (X-T30, compressé ou non), CR3 (EOS R, R10), CR2, ARW (A7 III), NEF (D90, D3100), RW2, DNG (téléphones, reflex). **Non vérifiés** : ORF, PEF, SRW, 3FR/IIQ. Le NEF Z « HE / HE* » n'est probablement pas pris en charge par LibRaw. |
| PSD | écriture (à plat, 16 bits, **à calques**) | Calques, masques, opacité, modes de fusion. Lecture d'un PSD : pas encore. |

**Moteur RAW** (Options ▸ Lecture des fichiers RAW) : `rapide` = démosaïque maison pour les Fujifilm X-Trans + LibRaw qualité 2 pour les autres ; `référence` = LibRaw qualité 3 (AHD / Markesteijn) partout.

## 3. Vitesses mesurées (RAW)

| Opération | Durée mesurée |
|---|---|
| Miniature d'un RAW (aperçu JPEG intégré) | 0,1 à 1,4 s |
| NEF Nikon D90 (4310×2868) plein format | ≈ 2,0 à 2,7 s (rapide ou référence) |
| NEF Nikon D3100 (4644×3084) plein format | ≈ 1,9 à 2,4 s |
| Autres Bayer plein format (CR2, ARW, DNG…) | 1,1 à 2,9 s en mode rapide ; ≈ 4 à 7 s en qualité maximale |
| RAF Fujifilm X-T30 (6240×4160), démosaïque maison, 1 cœur | ≈ 13 s (≈ 5 s de calcul + lecture) ; ≈ 1 s de calcul sur 6 cœurs |
| Même RAF avec LibRaw (Markesteijn) | ≈ 42 s → la démosaïque maison est **plus de 2× plus rapide** |
| Rouvrir un RAF déjà décodé | instantané (cache des 2 derniers décodages plein format) |

Le rendu de la démosaïque maison est un peu moins contrasté/saturé que celui de LibRaw (réglable ensuite dans Retouche).

## 4. Retouche (développement non destructif)

Onglets du panneau de droite : **Base** (exposition, contraste, hautes lumières, ombres, blancs, noirs, température, teinte, saturation, vibrance, noir et blanc), **Courbes**, **HSL**, **Étalonnage** (Grading), **Calibration**, **Détails**, **Objectif**, **Correction** (retouche locale), **Filtres**, **Presets / LUT** (dont presets Fuji), **Historique**.
Outils : rognage (R), redressement, miroirs, masques (pinceau, radial, linéaire, baguette, ciel), copier/coller des réglages (menu clic droit), historique global (Ctrl+Z).
Chaîne 16 bits : pour les exports en 16 bits, la retouche est appliquée avec une précision supérieure à 8 bits (cube de couleurs 65³ + détails calculés en 8 bits).

### Notes (étoiles et couleurs) et filtre
- Touches : **1 à 5** = étoiles, **6 à 9** = rouge, jaune, vert, bleu, **0** = effacer la note (sur la photo survolée).
- Bouton **⭐ Filtre** : fenêtre de filtrage par étoiles (nombre exact), couleurs, état (retouchées / non retouchées). Les photos qui ne correspondent pas sont masquées dans le bac à photo et dans la bande de Retouche, et grisées dans la maquette. La fenêtre reste ouverte tant qu'on ne clique pas ailleurs.
- Le mot **Note** (à gauche des étoiles, en bas de Retouche) est aussi un **interrupteur** : actif (cadre bleu) = les étoiles et couleurs s'affichent en bas de la photo en plein écran, avec un contour épais pour rester lisibles ; le message flottant de changement de note est alors désactivé pour ne pas cacher la note.
- Dans les menus clic droit des vignettes, les étoiles / couleurs restent ouvertes après un choix.

## 5. Fusion HDR (clic droit sur 2 à 5 photos ▸ « Fusion HDR »)

Fonctionne avec **des JPEG, des RAW, ou un mélange**. Pas besoin d'un trépied : l'alignement automatique (décalage + petite rotation, affiné au sous-pixel) rattrape une prise à main levée, et l'image est **recadrée automatiquement** sur la zone commune.

Réglages : photo principale (par défaut l'exposition **du milieu**), poids (contraste, saturation, exposition), **Anti-fantômes** (0 à 100, défaut 80 : garde surtout la photo principale là où quelque chose a bougé), ombres, hautes lumières, contraste, saturation, luminosité, taille maximale (jusqu'à 3200 px), **format** (JPEG ou **TIFF 16 bits**).

Deux méthodes :
1. **Exposition** (défaut) : fusion d'exposition par pyramides. Rendu naturel, tolère bien les JPEG et le mouvement.
2. **Radiance + tone mapping** : reconstitue la lumière réelle de la scène (linéaire) puis compresse la plage (curseurs « Compression de la lumière » et « Détail local »). Plage maximale, rendu plus « HDR ». **À réserver aux RAW bien alignés ou pris sur trépied** : sur des JPEG à main levée (courbes de tons d'appareil, objets qui bougent), des plaques et du flou peuvent apparaître.

Précision : pour les photos RAW, la création finale utilise les **données 16 bits** décodées (pas l'image 8 bits d'aperçu) ; l'aperçu pendant les réglages reste en 8 bits pour la rapidité. Si le décodage 16 bits d'un RAW échoue, ce fichier retombe sur le chemin 8 bits.

Durées mesurées (3 photos, sandbox, 1 cœur) :
| Cas | Durée |
|---|---|
| Alignement + premier aperçu (JPEG ou RAF) | ≈ 1 à 3 s |
| Mise à jour de l'aperçu après un réglage | ≈ 1 s |
| Création 3 JPEG, 2400 px, méthode Exposition | ≈ 7 à 8 s |
| Création 3 JPEG, 1600 px, méthode Radiance | ≈ 5 s |
| Création 3 RAF (6240×4160 → 2400 px), sources 16 bits | ≈ 25 à 35 s (l'essentiel = décodage des 3 RAW) |
| Création 3 JPEG → TIFF 16 bits, 1600 px | ≈ 6 s (fichier ≈ 8 Mo) |

## 6. Fusion de zones et collage par calques (clic droit sur 2 à 4 photos)

Chaque photo est un **calque avec son propre masque** (la photo 1 sert de base). Les photos sont alignées automatiquement.

Outils de peinture : **Pinceau**, **Gomme**, **Dégradé**, **Main** (déplacer un calque). Cases : harmonisation exposition/couleurs, « montrer en rouge ce qui a bougé », « garder la photo choisie là où ça a bougé ».

Outils de **sélection** (collage) : **Rectangle**, **Triangle** (3 clics), **Polygone** (un clic par point, double-clic ou Entrée pour fermer, Retour arrière = enlever un point, Échap = annuler), **Lasso** (à main levée), **Pipette** (sélection par couleur, tolérance réglable, option « zone contiguë »). **Maj** ajoute à la sélection, **Alt** retire. Curseur « Bord adouci » pour fondre les contours.

Actions sur la sélection : **Garder la photo choisie ici**, **Retirer la photo choisie ici**, **Transition** (fondu gauche→droite, haut→bas, ou radial, avec inversion du sens), **Copier la zone** puis **📌 Coller ici** (la zone devient un **nouveau calque** copie, jusqu'à 8 calques ; Ctrl+Z annule), inverser / effacer la sélection.
Sorties : **Créer la photo** (JPEG) ou **PSD à calques** (copies de zones comprises).

## 7. Sandwich
Superpose les photos sélectionnées (opacité, modes de fusion) ; exportable en PSD à calques.

## 8. Maquette, Visionnage, Diaporama
- **Maquette** : mise en page de livre photo par pages, bac à photos (glisser-déposer, réordonnancement), couleur de fond, export HTML et images.
- **Visionnage** : la maquette page par page, **F** = plein écran.
- **Diaporama** : lecture, musique, export MP4 ; option « comme la maquette » (plusieurs photos par page).

## 9. Import, dossiers, corbeille, projets
- Import de fichiers ou de **dossiers** (Chrome/Edge : dossiers mémorisés).
- **Corbeille** : supprimer « sur le disque » déplace la photo dans un sous-dossier `trash` avec un manifeste ; l'onglet Import ▸ Corbeille permet de restaurer (au dossier d'origine) ou de supprimer définitivement.
- **Sauvegarde du projet** : fichier `.lightcrab` (c'est la vraie sauvegarde ; le stockage du navigateur est lié à l'adresse du site).
- Menu clic droit d'une photo : copier le chemin (à coller dans l'Explorateur / le Finder), copier l'image, infos EXIF, dupliquer, retirer.
  **Limite des navigateurs** : une page web ne connaît pas le chemin complet d'un fichier et ne peut pas ouvrir l'Explorateur ni y sélectionner un fichier. « Copier le chemin » donne le chemin **relatif au dossier importé** (ou le nom du fichier).

## 10. Profondeur de couleur : 8, 16 et 32 bits
- **8 bits** : JPEG, aperçus, la plupart des sorties. 256 niveaux par couche.
- **16 bits** : TIFF, PNG et PSD 16 bits ; c'est la précision de travail pour les RAW (les capteurs font 12 à 14 bits, 16 bits suffit largement). Évite les « marches » dans les dégradés (ciel) et permet de pousser les ombres.
- **32 bits flottant** : **existe dans LightCrab** (export TIFF 32 bits flottant ; lecture des TIFF 32 bits). Utile surtout pour garder des valeurs **au-delà du blanc** (HDR). Limite actuelle : les valeurs supérieures à 1,0 sont encore comprimées en douceur dans les hautes lumières, donc non « récupérables » avec l'exposition. Le HDR de LightCrab est aujourd'hui écrit en **JPEG 8 bits ou TIFF 16 bits** ; un TIFF 32 bits flottant « radiance » serait une évolution possible.

## 11. Questions fréquentes (pour l'assistant)
- **Faut-il éviter les JPEG pour le HDR ?** Non. La méthode « Exposition » marche très bien avec des JPEG, même à main levée. Les RAW apportent surtout plus de précision (16 bits) et conviennent mieux à la méthode « Radiance ».
- **Mon HDR a des doubles contours.** Augmenter « Anti-fantômes » ; vérifier que la photo principale est l'exposition du milieu ; éviter la méthode « Radiance » avec des JPEG à main levée.
- **Un RAW est lent à ouvrir.** Normal pour le premier décodage (voir §3) ; le cache rend la réouverture instantanée. Options ▸ Moteur RAW = « rapide » pour gagner du temps.
- **Puis-je ouvrir le dossier d'une photo depuis LightCrab ?** Pas depuis un navigateur (sécurité). Utiliser « Copier le chemin » puis le coller dans la recherche de l'Explorateur.
- **Où sont mes réglages ?** Dans le projet (`.lightcrab`) ; les fichiers d'origine ne sont jamais modifiés.
- **Téléphone ?** L'interface actuelle est conçue pour ordinateur ; une version tactile verticale est prévue plus tard.

## 12. Licence
Usage libre et gratuit, **vente interdite**, code lisible mais **tous droits réservés** (pas de réutilisation ni de versions dérivées). LibRaw (décodage RAW) est embarqué sous ses propres licences (LGPL 2.1 / CDDL 1.0) — voir « À propos ».

## Mises à jour du 21 octobre 2026
- **Anti-moiré (couleur)** : onglet Détails. Lisse la chrominance (pas la netteté). Corrige le moiré arc-en-ciel, pas le moiré de luminosité.
- **Raccourcis** indépendants de la disposition du clavier (Ctrl+C/V/Z… en ukrainien, russe, etc.).
- **Chiffres** : 0–5 = noter, 6–9 = couleur. **Zoom : Maj + 1…6** (Ajusté, 25, 50, 100, 200, 500 %), échelle réelle.
- **Échelle et taille en pixels** affichées en bas à gauche de la photo (clic pour masquer / afficher).
- **Clic droit sur un onglet** de Modifier : réinitialiser l'onglet (le bouton a été retiré).
- **Collage de réglages** : double-clic sur un curseur de force = 100 %.
- **Pipette des masques (baguette)** : la couleur est comparée à la photo avant développement ; la zone ne bouge plus quand on change l'exposition. Les anciens masques baguette sont à ré-échantillonner.
- **Copier le chemin** : chemin complet Windows, le dossier racine est demandé une fois puis mémorisé.
- **Nodes** : interrupteur par branche. **Échap** dans le rognage : ferme sans question s'il n'y a pas de changement, sinon propose d'appliquer.
- **EXIF (ℹ)** : position GPS modifiable, collage depuis Google Maps ; écrite à l'export.
- **Comparaison** : 🗑 supprimer la photo, notes et couleurs.

## Mises à jour (suite) — 21 octobre 2026
- **Écran adaptatif** : la mise en page dépend de la taille et de l'orientation de la fenêtre, pas du système. Portrait ≤ 900 px (téléphone, tablette verticale) : photo en haut, réglages en « feuille » en bas (poignée pour changer la hauteur), boutons ‹ › pour changer de photo, pincer pour zoomer, double-tap Ajusté ↔ 100 %. Téléphone couché et tablette en paysage : mise en page compacte.
- **Onglets de Retouche** : une seule rangée, défilante (molette ou doigt).
- **Pipette (baguette)** : après le 1er clic, glisser le point pour l'ajuster ; bouton « ➕ Nouvelle baguette » pour une autre couleur. Affichage de la zone : rouge, le reste s'assombrit, zébrures, noir et blanc.
- **Maquette** : bac à photo avec titre, bouton « Masquer » et outils sur des rangées séparées ; tri compact « ⇅ A→Z » avec « date d'ajout au bac » ; notes et couleurs des vignettes modifiables seulement si la photo est sélectionnée.
- **Diaporama** : sélection, filtre, tri et retrait de photos ; transitions : fondu, zoom avant, zoom arrière, cœur, étoile, cercle, glissement. Le bac ne remonte plus en haut après un glisser-déposer (Export et Diaporama).
- **EXIF (ℹ)** : tous les champs sont modifiables (objectif, auteur, date, ISO, GPS…), GPS collable depuis Google Maps, option « ne pas écrire de GPS ». Écrit dans les fichiers exportés (sauf mode « Minimales »).
- **Export** : première option = exporter la sélection ; textes corrigés.
- **Copier le chemin** : pour les photos glissées-déposées, le dossier est demandé une fois par photo (le navigateur ne le connaît pas).
- **Ctrl+Z dans la comparaison** : la photo supprimée revient dans la comparaison.
- **Zoom du pavé tactile** progressif. **Viewer** : image haute définition, plein écran réel.


## Mises à jour du 22 oct. 2026
- **Ouverture** : LightCrab s'ouvre directement sur l'onglet **Retouche**.
- **Options ▸ Interface** (à côté du mode débutant) : *Automatique* (selon la taille de l'écran), *Téléphone* ou *Ordinateur*. Le choix est mémorisé.
- **Copier le nom du fichier** (clic droit sur une vignette de la bande de Retouche) : copie le nom, sans poser de question. Colle-le dans la barre de recherche de l'Explorateur, dans le bon dossier. Un navigateur ne connaît jamais le dossier réel d'un fichier glissé-déposé.
- **Onglets de Retouche** (Basique, Courbes, HSL…) : ils passent à la ligne au lieu de déborder du panneau.
- **Surbrillance des masques** : elle n'apparaît que dans l'outil Filtres/Masques ; elle se coupe quand on change d'outil ou de photo et revient quand on retourne dans l'outil (réglage « Afficher les masques » conservé).

## Mises à jour du 22 oct. 2026 (2e série)
- **Options** : « Interface » est sur la même ligne que « Mode débutant » (l'explication du mode débutant est dessous).
- **Curseur** : main (et non croix) quand un masque Baguette est sélectionné ; main fermée pendant le déplacement du point.
- **Tailles** : Taille du pinceau jusqu'à 400 %, Taille de la baguette jusqu'à 300.
- **Filtres** : clic droit sur l'onglet **Filtres** = réinitialiser les réglages de tous les filtres (formes conservées) ; clic droit sur un filtre = réinitialiser celui-là.
- **Sélection multiple** (bouton au-dessus de la liste) : cocher plusieurs filtres, « Tout / Aucun », puis **Supprimer** en un clic.
- **Ciel** : détection plus stricte (bleu net ou gris clair lisse relié au haut de l'image, poids qui décroît vers le bas) ; ne couvre plus toute la photo. Réglable avec la Sensibilité.
- **Baguette** : bouton « Nouvelle baguette » au-dessus de la couleur échantillonnée ; case **Zone contiguë seulement** (activée par défaut pour les nouvelles baguettes ; sans elle, toute la couleur est prise dans la zone) ; HSL local remplacé par **modification directe de la couleur sélectionnée** (Teinte −180…180°, Saturation, Luminosité) + **carré de couleurs** (teinte × saturation) pour choisir la couleur d'arrivée.
- **Points sur la photo** : sans filtre sélectionné, chaque filtre est un point numéroté ; un clic le sélectionne.

## Mises à jour du 22 oct. 2026 (3e série)
- **Plus de bandeau « Ancienne session trouvée »**. À l'ouverture, LightCrab rouvre tout seul la bibliothèque du navigateur ; si elle est vide, il rouvre la **sauvegarde de secours** (voir ci-dessous).
- **Options ▸ Sauvegarde de secours** : choisis un dossier (disque, clé USB, dossier synchronisé). LightCrab y copie photos, maquette et retouches (`session.json` + une copie de chaque photo), en tâche de fond, 20 s après chaque enregistrement. Si le navigateur perd sa bibliothèque, tout revient automatiquement (Chrome peut demander « Autoriser à chaque visite » : accepte).
- **Options ▸ Interface** : sur sa propre ligne, sous « Mode débutant ».
- **Options ▸ Lecture des fichiers RAW** : le choix est maintenant « LightCrab — NOTRE démosaïque (jusqu'à 4× plus rapide) » ou « LibRaw — AHD / Markesteijn ».
- **Traductions** : les fenêtres de confirmation (Réinitialiser les filtres, Supprimer…, Dupliquer…) sont traduites en anglais et en ukrainien.
- Si la bibliothèque est vide mais qu'une ancienne maquette existait, Options propose « 🧯 Ancienne maquette retrouvée » (Rouvrir… / Supprimer).

## Mises à jour du 23 oct. 2026 : Restaurer la session
- Bouton **🛟 Restaurer la session…** dans ⚙ **Options** (il n'est plus dans la barre du haut) : fenêtre avec 5 sources : (1) dossier de sauvegarde de secours, (2) réessayer la bibliothèque du navigateur, (3) ancienne maquette retrouvée, (4) chercher les photos dans un dossier d'originaux, (5) copie des données de Chrome.
- **Limite technique** : Chrome interdit aux pages web de lire son propre dossier `AppData\Local\Google\Chrome\User Data`. Il faut donc en faire une **copie** (Chrome fermé), p. ex. `IndexedDB\file__0.indexeddb.blob` sur le Bureau, puis choisir la copie. LightCrab reconnaît les photos à leur **taille** (connue grâce aux retouches enregistrées) et réattache les retouches ; les images non identifiées peuvent être importées sous « Récupérée-001… ».
- Si le dossier `.blob` n'existe pas, Chrome a effacé les photos : utiliser l'option 4 avec les originaux (les retouches reviennent seules si nom, taille et date du fichier sont identiques).

## Mises à jour du 24 oct. 2026
- **Retouche** : bouton **⇅** (à côté de « Filtre ») pour trier la bande de photos : nom, date du fichier, date d'ajout, note, couleur, **retouchées d'abord / non retouchées d'abord**. Un clic sur une photo de la bande ne la recentre plus.
- **Fusion de zones** : chaque photo garde ses **proportions** (une photo verticale n'est plus étirée dans un cadre horizontal, et inversement ; elle n'est pas alignée automatiquement, on la place avec ✋ Main). Rectangle, triangle, polygone, lasso et pipette **s'appliquent tout de suite** à la photo choisie (case décochable ; Maj = ajouter, Alt = retirer). **Ctrl+Z** annule aussi une sélection, et fonctionne avec un clavier cyrillique.
- **Filtre Pipette (baguette)** : après avoir cliqué une couleur d'arrivée dans le carré, **Luminosité = 0 donne exactement cette couleur**. Les sections Color Grading et Calibration ne sont plus proposées pour la pipette.
- **Options › Couleur de l'interface** : thèmes neutre, rose, vert, bleu, violet, orange (clair et sombre), mémorisés dans le navigateur.
- **À propos** : liste complète des composants tiers (voir aussi `CREDITS.md`).

## Mises à jour du 24 oct. 2026 (suite)
- **Dialogues** : toutes les confirmations (« Supprimer ce masque ? »…) sont des fenêtres de LightCrab (Entrée = OK, Échap = Annuler), plus des boîtes du navigateur.
- **Options** : « Enregistrer maintenant » met aussi à jour le dossier de secours (le bouton « Copier maintenant » est supprimé) ; boutons réorganisés en grille ; **Molette dans Retouche** (zoom vers le curseur, Maj + molette = horizontal, Ctrl + molette = vertical, ou comportement classique) ; **Couleur de l'interface** : thèmes rose, vert, bleu, violet, orange, clairs et sombres.
- **Pipette et filtres** : le carré de couleur (teinte × saturation) existe pour **tous** les filtres ; la couleur choisie **remplace** la teinte et la saturation de la zone. Luminosité 0 = clarté du carré pour la pipette.
- **Masques et objectif** : les masques, pinceaux et corrections suivent la photo quand on change l'objectif, le rognage ou la rotation.
- **Objectif** : bouton « Télécharger depuis Lensfun » (une connexion, le profil est gardé dans le navigateur).
- **Barre d'espace** = Main, même quand un curseur a le focus.
- **Limites** : voir « À propos › Limites actuelles » pour la profondeur de couleur (8 bits à l'écran, chaîne 16 bits à l'export).

## Mises à jour du 24 oct. 2026 (soir)
- **Guide de première visite** : 7 petites cartes très courtes (« Ok, compris »), qui illuminent l'élément concerné ; se rejoue depuis Options › « Revoir le guide de démarrage ».
- **Aperçus RAW rapides** : à l'import de RAW, LightCrab propose de créer des aperçus réduits (Options › « Aperçus RAW à l'import » : demander / toujours / jamais). Bouton ⚡ dans Retouche pour les créer plus tard. Les aperçus sont gardés dans le navigateur ; ouvrir un RAW affiche l'aperçu tout de suite, puis le plein format le remplace en arrière-plan. L'export utilise toujours le plein format.
- **Profil d'objectif automatique** : à l'ouverture d'une photo dont l'objectif a un profil connu, il est appliqué une fois (Options pour le désactiver). Si on le retire, il ne revient pas.
- **Opacité du filtre** : un curseur pour chaque masque (pinceau, radial, linéaire, ciel, baguette/pipette).
- Barre du haut : couleurs de thème plus saturées.

---

## Nouveautés (octobre 2026)

**Tags (mots-clés).** Dans Retouche, bouton 🏷 Tags à côté de ⭐ Filtre : tapez un nom (virgule = plusieurs), Entrée pour l’ajouter ; cliquez un tag pour l’ajouter ou le retirer sur toutes les photos sélectionnées. ✕ supprime un tag, « Supprimer les tags non utilisés » nettoie ceux qui ne sont appliqués à aucune photo. Dans le filtre, tapez pour chercher un tag puis cliquez pour le choisir (« Sans tag » existe aussi ; case « tous les tags » pour un ET). Dans Export, case « Ajouter les tags… au nom du fichier », à côté du photographe : `IMG-6546 Adrian Margolin - Anna - Marc.jpg`.

**Flou gaussien et mosaïque.** Boutons 🌫 et ▦ dans la barre du bas de Retouche. Glissez sur la photo pour dessiner une zone (rond/ovale ou rectangle ; « proportions verrouillées » = cercle/carré), puis réglez Force, Contour progressif et (mosaïque) Bruit. Non destructif, appliqué aussi à l’export. Suppr = effacer la zone, clic droit = déplacer la photo.

**Noyau flottant 32 bits.** L’export 16 bits évalue maintenant la chaîne de couleur (tons, courbes, HSL, étalonnage, LUT…) en Float32, sans arrondi à 8 bits.

**Divers.** Netteté dans « Base » ; Ciel : douceur de transition et sensibilité jusqu’à 100 (déborde sur les branches fines) ; couleur du pinceau au choix ; clic droit dans l’historique (supprimer une étape / une branche / tout effacer) ; un défilement rapide de presets ne laisse qu’un pas d’historique.
