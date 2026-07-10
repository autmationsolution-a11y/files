---
name: rhetores-sharepoint-finder
description: >
  Retrouver UN document précis d'un client dans le SharePoint Rhétorès Finance :
  pièce d'identité, passeport, CNI, titre de séjour, justificatif de domicile, RIB /
  coordonnées bancaires, KYC, contrat, bulletin, fiscalité. À déclencher dès qu'on
  demande « trouve / récupère / où est [type de document] de [client] ». Donne le
  routage par type de document, la procédure de recherche, et un exemple validé.
---

# Trouver un document client dans le SharePoint Rhétorès

À coller dans un Claude personnalisé connecté au connecteur **Microsoft / SharePoint**.
Objectif : localiser **le bon fichier** (ex. la carte d'identité de M. X) sans explorer
à l'aveugle dans ~110 bibliothèques / 120 000+ fichiers.

## Outils utilisés (connecteur Microsoft)
- `sharepoint_search(query, fileType?, folderName?, author?, dates?)` — recherche plein-texte. Rapide. Renvoie un `webUrl`.
- `sharepoint_folder_search(name)` — trouve un dossier par nom (ex. le dossier d'un client).
- `read_resource(uri)` — **liste** un dossier ou **lit** un fichier. C'est l'outil de navigation.

## Règle d'or : 2 chemins, dans cet ordre
1. **Chercher** d'abord (`sharepoint_search`) avec `nom du client` + `type de doc`. Le plus rapide.
2. **Naviguer** si la recherche est bruitée OU si elle ne couvre pas le périmètre client
   (cas d'un **partage externe** : la recherche plein-texte peut rester limitée au drive
   connecté et NE PAS traverser le tenant client — voir « Périmètre » plus bas). Dans ce cas :
   `sharepoint_folder_search(nom du client)` → `read_resource` sur le dossier du client →
   descendre dans la **rubrique numérotée** correspondant au type de document.

## Routage par TYPE de document (la table clé)

Chaque dossier client `NOM Prénom` est rangé en rubriques numérotées. Le type de document
demandé pointe directement la rubrique :

| Le client demande… | Rubrique du dossier client | Mots-clés de recherche |
|---|---|---|
| **Carte d'identité / CNI / passeport / titre de séjour / permis** | **01 - Pièces officielles** | `CNI` ; `carte identite` ; `passeport` ; `piece identite` ; `titre sejour` |
| **Justificatif de domicile / livret de famille / acte de naissance** | **01 - Pièces officielles** | `justificatif domicile` ; `livret famille` ; `acte naissance` |
| **RIB / IBAN / relevé bancaire / coordonnées bancaires** | **06 - Bancaire** (parfois joint à la souscription d'un contrat) | `RIB` ; `IBAN` ; `releve bancaire` ; `coordonnees bancaires` |
| **KYC / connaissance client / questionnaire** | **01 - Pièces officielles** ou **Connaissance client** | `KYC` ; `connaissance client` ; `questionnaire` |
| **Contrat / bulletin de souscription / avenant** | **05 - Assurance Vie** (ou 07 Retraite, 08 SCPI, 14 PE selon le produit), sous `01 - Souscription` | `souscription` ; `bulletin` ; `avenant` ; nom compagnie (`Generali`, `Swiss Life`…) |
| **Fiscalité / IFI / déclaration** | **04 - Fiscalité** | `IFI` ; `fiscalite` ; `declaration` |
| **Correspondance / CR de RDV / e-mail** | **03 - Correspondance** | `compte rendu` ; `courrier` |

Plan complet d'un dossier client (rubriques) :
```
01 - Pièces officielles · 02 - Documents Généraux · 03 - Correspondance · 04 - Fiscalité
05 - Assurance Vie · 06 - Bancaire · 07 - Retraite · 08 - SCPI · 09 - Défiscalisation
10 - Épargne salariale · 11 - Immobilier · 12 - Prévoyance · 13 - Entreprise · 14 - Private Equity
```
Dans un produit (ex. `05 - Assurance Vie`) : un sous-dossier par contrat/compagnie (préfixe `PM - …`),
puis `01 - Souscription` / `02 - Vie du contrat`.

## Procédure pas à pas (exemple : « la pièce d'identité de Léa REMY »)

1. **Router par type** → « pièce d'identité » = rubrique **01 - Pièces officielles**.
2. **Localiser le client** :
   `sharepoint_folder_search("REMY Lea")` → renvoie l'URI du dossier client.
3. **Ouvrir le dossier client** :
   `read_resource(uri_du_dossier)` → liste les rubriques `01 - Pièces officielles … 14 - Private Equity`.
4. **Ouvrir la rubrique** :
   `read_resource(uri_du "01 - Pièces officielles")` → liste les fichiers.
5. **Restituer** : on trouve `Justif_domicile_REMY_Lea.pdf` (chemin validé le 2026-06). Donner le **`webUrl`**
   pour ouverture directe ; lire le fichier avec `read_resource` seulement si on doit en extraire une info.

> Chemin réellement validé (sandbox de test) :
> `Middle Back Office / Clients / REMY Lea / 01 - Pièces officielles / Justif_domicile_REMY_Lea.pdf`

Variante « recherche directe » (si le connecteur couvre le périmètre client) :
`sharepoint_search("REMY justificatif domicile", fileType="pdf")` → le `webUrl` donne déjà la bibliothèque.

## Site réel Rhétorès (quand le connecteur y est relié)
- Site : `rhetoresfinancegroup.sharepoint.com/sites/rhetores`.
- Lister **toutes les bibliothèques** (nom → driveId, à jour) :
  `read_resource("drive:///sites/rhetoresfinancegroup.sharepoint.com,01e03444-1ccf-4c16-958e-da3974b0bd1f,6ae0bf98-2777-493f-8eae-62e5f6316ebf")`
- Lister une bibliothèque : `read_resource("file:///{driveId}/root")` ; un sous-dossier : `file:///{driveId}/{itemId}`.
- La carte pré-calculée nom → driveId est dans `references/library-map.md` (bundle complet).

## Périmètre & garde-fous (à respecter)
- **Partage externe / recherche limitée** : si la connexion passe par un **dossier partagé**
  (« Rhétorès Finance Middle Back Office »), `sharepoint_search` peut ne PAS traverser le tenant
  client et ne renvoyer que le drive connecté. Dans ce cas, **naviguer par dossier** (chemin 2),
  ne pas conclure « introuvable » sur la seule recherche plein-texte.
- **Homonymes** (deux DUPONT) ou client chez plusieurs conseillers : présenter **tous les candidats**
  ou demander une précision — **jamais** choisir en silence.
- **Zéro résultat** : le dire clairement et proposer la navigation. **Jamais** renvoyer un document
  « approchant mais faux ».
- **RGPD** : `01 - Pièces officielles` contient CNI, passeports, données sensibles. Restituer le lien,
  ne pas recopier inutilement le contenu (numéro de pièce, etc.) hors du strict besoin.
- **Bibliothèques vides** : beaucoup de `Clients X` sont vides/non listables → repasser par la recherche.

## Fichiers du bundle complet (optionnels, pour aller plus loin)
`references/library-map.md` (110 bibliothèques → driveId) · `references/architecture.md` (rôle de chaque
bibliothèque) · `references/keyword_index.md` (index mots-clés → bibliothèque) · `references/acronyms.md`
(LCB-FT, PEA, IFI, PP/PM…).
