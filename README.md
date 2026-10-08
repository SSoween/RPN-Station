# RPN Station — Calculateur RPN hors-ligne

Calculateur RPN (Reverse Polish Notation), **PWA 100 % hors-ligne**, UI **Space Opera** (sobre, sans image).

## ✨ Ce que ça fait
- **RPN** avec les opérations de base : `+` `−` `×` `÷`
- **E / Enter** : committer un nombre (push) — c'est ce qui "valide" une constante dans le RPN
- **C** : **effacement complet de la ligne** (clear tout)
- **⌫** : **effacement caractère par caractère** (backspace)
- **M** : stocker la valeur courante dans la mémoire active
- **R** : rappeler la mémoire active sur la pile
- **8 lignes de mémoire (M1–M8)** affichées en tout temps
  - clic sur une ligne = la sélectionner comme mémoire active
  - le `×` d'une ligne = vider cette ligne
- **PWA offline** : service worker qui met tout en cache, installable

## 🎨 UI Space Opera
Fond espace profond, accents cyan / or / violet, scanlines légères, glows. Zéro image, seulement couleur.

## 🚀 Installer & exécuter
### Option 1 — GitHub Pages (recommandé)
1. Pusher les 5 fichiers dans un dépôt GitHub.
2. Settings → **Pages** → source = branche `main`, folder = root.
3. Ouvrir `https://<user>.github.io/<repo>/`.
4. L'app se met en cache au premier chargement ; bouton **INSTALL PWAs** apparaît → installable hors-ligne.

### Option 2 — local (http requis pour la PWA)
```bash
npx serve .            # ou : python3 -m http.server 8000
```
Ouvrir `http://localhost:8000/`.

> ⚠️ La PWA / le service worker ne marchent **QUE sur http(s)** — pas sur `file://` (ouvrir directement `index.html`).

## ⌨️ Raccourcis clavier
| Touche | Action |
|---|---|
| `0–9` `.` | saisir un nombre |
| `E` / `Enter` | committer le nombre en cours (push) |
| `+` `−` `×` `÷` (ou `*` `/`) | opérateur RPN |
| `C` | clear tout |
| `⌫` (Backspace) | effacer un caractère |
| `M` | stocker dans la mémoire active |
| `R` | rappeler la mémoire active |
| `↑` / `↓` | changer la mémoire active (M1–M8) |

## 📁 Structure
```
index.html     # UI + logique (tout inline)
manifest.json  # PWA
sw.js          # service worker (cache offline)
icon.svg       # icône PWA
README.md
```

## 💡 Exemple
Calculer `(5 + 3) × 2` :
`5` → `E` → `3` → `+` → `2` → `E` → `×` = **16**