# Crédits et composants tiers de LightCrab

LightCrab est un fichier HTML unique. Les composants ci-dessous y sont intégrés ou utilisés ; chacun reste sous sa propre licence.

| Composant | Rôle | Licence | Auteur / source |
|---|---|---|---|
| **LibRaw** (compilé en WebAssembly) + enveloppe **libraw-wasm** | Lecture des fichiers RAW | LGPL 2.1 ou CDDL 1.0 ; enveloppe ISC | https://www.libraw.org · https://github.com/LibRaw/LibRaw |
| **Lensfun** (base de profils d'objectifs) | Distorsion et aberration chromatique des objectifs Fujifilm X | CC BY-SA 3.0 | https://lensfun.github.io |
| **Presets Fuji communautaires** (161, convertis de façon approximative par LightCrab) | Presets intégrés | CC BY-NC 4.0 (usage non commercial) | ThomasHMD, dépôt `fuji-xrfc-preset-generator` : https://github.com/ThomasHMD/fuji-xrfc-preset-generator ; créateurs d'origine : Blaise Pauly, FujiXWeekly, Team Fuji et contributeurs |
| **pako** 2.2.0 | Décompression | MIT et Zlib | © Vitaly Puzrin, Andrei Tuputcyn ; zlib © Jean-loup Gailly, Mark Adler — https://github.com/nodeca/pako |
| **UTIF.js** 3.1.0 | Lecture des TIFF (embarque un décodeur JPEG de type pdf.js, Apache 2.0) | MIT | © Photopea — https://github.com/photopea/UTIF.js |
| **mp4-muxer** 5.2.2 | Écriture des MP4 du Diaporama | MIT | © Vanilagy — https://github.com/Vanilagy/mp4-muxer |

Formats (TIFF, PNG, PSD, EXIF, XMP, MP4, .cube) : implémentés d'après leurs spécifications publiques. Le reste du code est écrit pour LightCrab.

**Conception et réalisation** : idée et conception d'Adrian Margolin ; développement technique par Claude (Anthropic) — écriture du code, recherche de solutions, choix et intégration des bibliothèques, tests.

Téléchargement optionnel : si l'utilisateur clique « Télécharger depuis Lensfun » (onglet Objectif), LightCrab télécharge un fichier de la base Lensfun depuis `raw.githubusercontent.com` ; aucune photo n'est envoyée.

Fujifilm, X-Trans et les autres noms d'appareils ou d'objectifs cités appartiennent à leurs propriétaires ; LightCrab n'est lié à aucun d'eux.

À vérifier avant publication : le texte exact des licences à joindre (MIT, Zlib, LGPL 2.1, CC BY-SA 3.0, CC BY-NC 4.0) et l'origine du décodeur JPEG de UTIF.js (indiquée d'après un commentaire du code).
