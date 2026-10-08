# Deep Space — Calculateur RPN hors-ligne

Calculateur RPN (Reverse Polish Notation), **PWA 100 % hors-ligne**, UI **Space Opera** (sobre, sans image). Layout **téléphone, sans scroll**.

## ✨ Ce que ça fait
- **Pile RPN en lignes** : chaque nombre **validé** (↵ / Enter) devient une ligne. Le **plus récent est en `1:` (en bas)**, les plus anciens montent **au-dessus** (`2:`, `3:`…).
- **En cours de saisie** : le nombre s'affiche **en bas, non-numéroté, dans le champ cyan** ; il ne devient `1:` **qu'après ↵** (le reste monte d'une case).
- **Opérations de base** : `+` `−` `×` `÷` — opèrent sur les **2 lignes du bas** (les 2 plus récentes, `2:` et `1:`) ; le résultat remplace les deux et devient `1:`.
- **↵ Enter** : valider le nombre en cours (devient 1:, le reste monte).
- **C** : **effacement complet** (clear tout).
- **⌫** : **effacement caractère par caractère**.
- **±** : changer le signe du nombre en cours.
- **…** : menu (placeholder — à définir).

## ⌨️ Touches (du bas vers le haut, 4 par ligne)
```
[ ± ]  [ ⌫ ]  [ C ]  [ … ]
[ 7 ]  [ 8 ]  [ 9 ]  [ ÷ ]
[ 4 ]  [ 5 ]  [ 6 ]  [ × ]
[ 1 ]  [ 2 ]  [ 3 ]  [ − ]
[ 0 ]  [ . ]  [ ↵ ]  [ + ]
```

## 📟 Exemple — 10, 5, 3 puis ×
```
10 ↵  →  1: 10
5  ↵  →  2: 10
             1:  5
3  ↵  →  3: 10
           2:  5
           1:  3
```
Puis `×` (les 2 du bas : 5 × 3) :
```
          2: 10
          1: 15
```
(Le résultat `15` remplace les deux et est en `1:`.)

## 🎨 UI Space Opera
Fond espace profond, accents cyan / or / violet, scanlines légères, glows. Zéro image, seulement couleur. Nom **« Deep Space »** (renommable en une ligne).

## 🚀 Installer & exécuter
### Option 1 — GitHub Pages (recommandé)
1. Push les 5 fichiers dans le dépôt.
2. Settings → **Pages** → source = branche `main`, folder = root.
3. Ouvrir `https://<user>.github.io/<repo>/`.
4. Premier chargement → bouton **INSTALL** → installable hors-ligne.

### Option 2 — local
```bash
npx serve .            # ou : python3 -m http.server 8000
```
Ouvrir `http://localhost:8000/`.

> ⚠️ La PWA / le service worker ne marchent **QUE sur http(s)** — pas sur `file://`.

## ⌨️ Raccourcis clavier (desktop)
| Touche | Action |
|---|---|
| `0–9` · `.` | saisir un nombre |
| `↵` / `Enter` | valider (devient 1:, le reste monte) |
| `+` `−` `×` `÷` (ou `*` `/`) | opérer sur les 2 du bas |
| `C` | clear tout |
| `⌫` | un caractère |
| `Esc` | fermer le menu |

## 📁 Structure
```
index.html     # UI + moteur RPN
manifest.json  # PWA
sw.js          # service worker (offline)
icon.svg       # icône PWA
README.md
```