# Deep Space — Calculateur RPN hors-ligne

Calculateur RPN (Reverse Polish Notation), **PWA 100 % hors-ligne**, UI **Space Opera** (sobre, sans image). Layout **téléphone, sans scroll**.

## ✨ Ce que ça fait
- **Pile RPN en lignes** : chaque nombre validé (↵ / Enter) devient une ligne `1:`, `2:`, `3:…` affichées au-dessus des touches (6–8 lignes selon l'espace).
- **Opérations de base** : `+` `−` `×` `÷` — opèrent sur les 2 lignes du haut (RPN).
- **↵ Enter** : valider le nombre en cours (ajout à la pile).
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

## 📟 Exemple — 10 × 5
Saisir `10` → ↵ → `5` → ↵ → ×
```
1: 10
2: 5
```
Après × :
```
1: 50
```

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
| `↵` / `Enter` | valider (push) |
| `+` `−` `×` `÷` (ou `*` `/`) | opérer (RPN) |
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