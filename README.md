# Deep Space — Calculateur RPN hors-ligne

Calculateur RPN (Reverse Polish Notation), **PWA 100 % hors-ligne**, UI **Space Opera** (sobre, sans image). Layout **téléphone, sans scroll**.

## ✨ Ce que ça fait
- **Pile RPN en 8 lignes fixes** (1: à 8:) : `1:` = **bas** (le plus récent), `8:` = **haut** (le plus ancien). Les 8 lignes sont **toujours affichées**, même vides (`·`).
- **En cours de saisie** : le nombre s'affiche **en bas, non-numéroté, dans le champ cyan** ; il ne devient `1:` **qu'après ↵** (le reste monte d'une case).
- **Push** : le nombre entre en `1:`, tout le reste **monte** (1:→2: … 7:→8:). Pile pleine → **erreur `STACK FULL`**, le push est refusé (voir § Stack ; bascule silencieuse possible en une ligne).
- **Opérations** `+ − × ÷` : toujours entre `2:` (1er opérande) et `1:` (2e opérande) — ordre `2: op 1:` — le résultat remplace les deux et devient `1:`, les éléments du haut **descendent** d'une case.
- **Bonus (menu `…` + clavier `s`/`d`/`r`)** : `swap` (1:↔2:), `drop` (retirer la ligne du haut), `roll` (rotation 1:→2:→3:→1:).
- **↵ Enter** : valider · **C** : effacement complet · **⌫** : caractère par caractère · **±** : signe.

## ⌨️ Touches (du bas vers le haut, 4 par ligne)
```
[ ± ]  [ ⌫ ]  [ C ]  [ … ]
[ 7 ]  [ 8 ]  [ 9 ]  [ ÷ ]
[ 4 ]  [ 5 ]  [ 6 ]  [ × ]
[ 1 ]  [ 2 ]  [ 3 ]  [ − ]
[ 0 ]  [ . ]  [ ↵ ]  [ + ]
```

## 📚 Stack — RPNStack

Module JavaScript (dans `index.html`) implementant exactement la spec : pile de **8 lignes**, `1:` (bottom, plus récente) → `8:` (top, plus ancienne). Chaque méthode renvoie `{ ok, reason, ... }` ; la pile est **intacte** si `ok === false`, et `reason` précise le motif.

| Méthode | Effet | Retour (si `ok:false`, `reason` = motif) |
|---|---|---|
| `push(value)` | `value` tombe en **1:**, le reste monte (1:→2:…). Pile pleine → refus (`STACK FULL`) — ou `8:` perdue si `OVERFLOW="silent"` | `NOT A NUMBER` / `STACK FULL` |
| `pop()` | retire la ligne **1:** (le plus récent) | `EMPTY STACK` |
| `operate(op)` | `2: op 1:` avec `op` ∈ `{add, sub, mult, div}` ; résultat en 1:, le reste descend | `EMPTY STACK` / `NOT ENOUGH VALUES` / `DIV BY ZERO` / `RESULT OVERFLOW` / `UNKNOWN OP` |
| `clear()` | vide les 8 lignes | — |
| `getStack()` | les 8 lignes : `s[0]` = 1: (bas) … `s[7]` = 8: (haut) ; `null` si ligne vide | — |
| `swap()` | échange **1: ↔ 2:** (≥2 valeurs) | `NOT ENOUGH VALUES` |
| `drop()` | retire la **ligne du haut occupée** (8: si pile pleine, sinon la plus haute) ; le reste descend | `EMPTY STACK` |
| `roll()` | rotation **1:→2:→3:→1:** (≥3 valeurs) | `NOT ENOUGH VALUES` |
| `size()` / `full()` / `reason()` | accessurs | — |

### Cas particuliers

| Cas | Comportement | `reason` |
|---|---|---|
| Pile vide + opération | refus, pile intacte | `EMPTY STACK` |
| 1 seule valeur + opération | refus, pile intacte | `NOT ENOUGH VALUES` |
| Division par zéro | refus, pile intacte | `DIV BY ZERO` |
| Résultat non fini (×) | refus, pile intacte | `RESULT OVERFLOW` |
| **Push sur pile pleine (8/8)** | **rejet du push** (choix explicite) — le nombre reste en attente dans le champ d'entrée. Alternative : `OVERFLOW = "silent"` dans le module (l'ancienne ligne 8: est perdue, le push passe) — **une seule ligne** à changer | `STACK FULL` |

> 🧭 **Pourquoi le rejet et non la perte silencieuse ?** Dans une calculatrice, perdre la plus ancienne valeur sans rien dire, c'est une perte de données. Comme les 8 lignes sont toujours visibles, le refus (⚠ dans la barre sous le stack, le nombre encore en attente) est le plus sûr. Basculer en `silent` prend une ligne.

### Exemple pas à pas — la pile 8 lignes avant / après chaque action

Chaque ligne montre les **8 lignes** de la pile, de `8:` (gauche) à `1:` (droite) ; `·` = ligne vide.

| # | Action | Avant (8: → 1:) | Après (8: → 1:) |
|---|--------|-----------------|-----------------|
| 0 | — (pile vide) | `· · · · · · · ·` | `· · · · · · · ·` |
| 1 | push 10 | `· · · · · · · ·` | `· · · · · · · 10` |
| 2 | push 5 | `· · · · · · · 10` | `· · · · · · 10 5` |
| 3 | push 3 | `· · · · · · 10 5` | `· · · · · 10 5 3` |
| 4 | **+** (5+3=8) | `· · · · · 10 5 3` | `· · · · · 10 8` |
| 5 | push 2 | `· · · · · 10 8` | `· · · · 10 8 2` |
| 6 | **×** (8×2=16) | `· · · · 10 8 2` | `· · · 10 16` |
| 7 | push 4 | `· · · 10 16` | `· · 10 16 4` |
| 8 | **÷** (16÷4=4) | `· · 10 16 4` | `· · 10 4` |
| 9 | push 6 | `· · 10 4` | `· · · 10 4 6` |
| 10 | **−** (4−6=−2) | `· · · 10 4 6` | `· · · · 10 −2` |
| 11 | push 5 | `· · · · 10 −2` | `· · · · · 10 −2 5` |
| 12 | push 8 | `· · · · · 10 −2 5` | `· · · · · 10 −2 5 8` |
| 13 | push 2 | `· · · · · 10 −2 5 8` | `· · · 10 −2 5 8 2` |
| 14 | push 4 | `· · · 10 −2 5 8 2` | `· · 10 −2 5 8 2 4` |
| 15 | push 6 | `· · 10 −2 5 8 2 4` | `· 10 −2 5 8 2 4 6` |
| 16 | push 3 | `· 10 −2 5 8 2 4 6` | `10 −2 5 8 2 4 6 3` ← **pile pleine (8/8)** |
| 17 | push 9 | `10 −2 5 8 2 4 6 3` | `10 −2 5 8 2 4 6 3` — ⚠ **STACK FULL**, refusé |
| 18 | **÷** (6÷3=2) | `10 −2 5 8 2 4 6 3` | `10 −2 5 8 2 4 2` |
| 19 | **swap** (1:↔2:) | `10 −2 5 8 2 4 2` | `10 −2 5 8 2 2 4` |
| 20 | **×** (2×4=8) | `10 −2 5 8 2 2 4` | `· 10 −2 5 8 2 2 8` |
| 21 | **roll** (1:→2:→3:→1:) | `· 10 −2 5 8 2 2 8` | `· 10 −2 5 8 2 8 8` |
| 22 | **drop** (retire 7:10) | `· 10 −2 5 8 2 8 8` | `· · −2 5 8 2 8 8` |
| 23 | **+** (8+8=16) | `· · −2 5 8 2 8 8` | `· · · −2 5 8 2 16` |
| 24 | **×** (2×16=32) | `· · · −2 5 8 2 16` | `· · · −2 5 8 32` |
| 25 | **clear** | `· · · −2 5 8 32` | `· · · · · · · ·` |
| 26 | push 5 | `· · · · · · · ·` | `· · · · · · · 5` |
| 27 | push 3 | `· · · · · · · 5` | `· · · · · · 5 3` |
| 28 | **+** (5+3=8) | `· · · · · · 5 3` | `· · · · · · · 8` |
| 29 | push 2 | `· · · · · · · 8` | `· · · · · · 8 2` |
| 30 | **×** (8×2=16) | `· · · · · · 8 2` | `· · · · · · · 16` |

→ Le résultat final, `1: 16`, est l'exemple canonique `(5 + 3) × 2`.

## 🎨 UI Space Opera
Fond espace profond, accents cyan / or / violet, scanlines légères, glows. Zéro image, seulement couleur. Nom **« Deep Space »** (renommable en une ligne).

## 🚀 Installer & exécuter
### Option 1 — GitHub Pages (recommandé)
1. Push les fichiers dans le dépôt.
2. Settings → **Pages** → source = branche `main`, folder = root.
3. Ouvrir `https://<user>.github.io/<repo>/`.
4. Premier chargement → bouton **INSTALL** → installable hors-ligne.

### Option 2 — local
```bash
npx serve .            # ou : python3 -m http.server 8000
```
Ouvrir `http://localhost:8000/`.

> ⚠️ La PWA / le service worker ne marchent **QUE sur http(s)** — pas sur `file://`.
> Le `sw.js` est versionné (`rpn-station-v3`) : chaque mise à jour du code bump le numéro → **l'app installée se met à jour automatiquement** (pas besoin de désinstaller/réinstaller).

## ⌨️ Raccourcis clavier (desktop)
| Touche | Action |
|---|---|
| `0–9` · `.` | saisir un nombre |
| `↵` / `Enter` | valider (devient 1:, le reste monte) |
| `+` `−` `×` `÷` (ou `*` `/`) | opérer sur 2: et 1: |
| `s` | swap 1: 2: |
| `d` | drop (ligne du haut) |
| `r` | roll 1: 2: 3: |
| `C` | clear tout |
| `⌫` | un caractère |
| `Esc` | fermer le menu |

## 📁 Structure
```
index.html     # UI + module RPNStack + moteur
manifest.json  # PWA
sw.js          # service worker (offline, versionné)
icon.svg       # icône PWA
README.md
```
