# Snapchat Memories Downloader

> **[English](README.md)** | Fran&ccedil;ais

Telecharge automatiquement **toutes** tes Memories Snapchat en masse, avec les dates, la geolocalisation et les overlays (texte/dessins).

![Demo](demo.gif)

## :warning: Pourquoi utiliser cet outil ?

Snapchat propose maintenant une option pour telecharger tes memories directement. Cependant, **les fichiers telecharges sont mal organises** : noms de fichiers aleatoires, aucun tri par date, et les metadonnees ne sont pas conservees correctement.

**Cet outil resout ces problemes en :**
- Organisant tes memories par dossiers annee/mois
- Nommant les fichiers selon leur vraie date de capture
- Preservant les overlays (texte, dessins, stickers)
- Composant les images/videos finales avec les overlays appliques

## :inbox_tray: Recuperer tes donnees Snapchat

### 1. Connecte-toi a ton compte
Connecte-toi sur Snapchat : [https://accounts.snapchat.com/](https://accounts.snapchat.com/)

### 2. Demande l'export de tes donnees
Va sur la page d'export : [https://accounts.snapchat.com/accounts/downloadmydata](https://accounts.snapchat.com/accounts/downloadmydata)

### 3. Configuration de l'export
:warning: **Parametres importants :**

1. **Plage de dates** : Selectionne **"Tout le temps"** pour recuperer toutes tes memories
2. Clique sur le bouton **"Demander uniquement les Memories"** (recommande pour un export plus rapide)

Si tu choisis de selectionner les donnees manuellement, assure-toi de cocher :
- :white_check_mark: **Memories** (coche cette option)
- :white_check_mark: **Format HTML** (pas JSON)
- :white_check_mark: **Plage de dates : Tout le temps**

Confirme et attends de recevoir le lien de telechargement par email (peut prendre de quelques heures a quelques jours).

### 4. Telecharge le fichier
:warning: **Important** : Tu vas recevoir plusieurs fichiers ZIP. **Telecharge uniquement le premier ZIP** (generalement nomme `mydata~XXXXX.zip`).

Dans ce ZIP, tu trouveras un dossier `html/` contenant `memories_history.html`. C'est le seul fichier dont cet outil a besoin pour fonctionner.

**Ne telecharge PAS les autres fichiers ZIP** - ils contiennent les fichiers media mais sans organisation. Cet outil va tout telecharger et organiser correctement a partir des liens dans le fichier HTML.

---

## :rocket: Installation et utilisation

### Prerequis
- **Python 3.10+**
- **uv** (recommande) ou pip
- **ffmpeg** (optionnel, pour la composition video + overlay)

### 1. Clone ou telecharge ce repo
```bash
git clone https://github.com/your-repo/Snapchat-Download-Memories.git
cd Snapchat-Download-Memories
```

### 2. Installe les dependances

**Avec uv (recommande) :**
```bash
# Installe uv si tu ne l'as pas
curl -LsSf https://astral.sh/uv/install.sh | sh

# Installe les dependances
uv sync
```

**Avec pip :**
```bash
pip install -r requirements.txt
```

### 3. Installe ffmpeg (optionnel mais recommande)
```bash
# macOS
brew install ffmpeg

# Linux (Ubuntu/Debian)
sudo apt install ffmpeg

# Windows
# Telecharge depuis https://ffmpeg.org/download.html
```

### 4. Place ton fichier HTML
Cree un dossier `html/` et place ton `memories_history.html` dedans :
```
Snapchat-Download-Memories/
├── html/
│   └── memories_history.html  ← Ton fichier ici
├── main.py
└── src/
```

### 5. Lance le script
```bash
python main.py
```

Le script te proposera 3 choix de configuration :

#### :file_folder: Organisation des fichiers
1. **Par date (dossiers annee/mois)** - *Recommande* - Organise en `2025/12/`
2. **Tout dans un seul dossier** - Tout dans `snapchat_memories/`

#### :memo: Format des noms de fichiers
1. **20251215_213158** - Format compact *(par defaut)*
2. **2025-12-15_21-31-58** - Format lisible
3. **2025-12-15** - Date uniquement (sans heure)
4. **20251215** - Date compacte uniquement

#### :compression: Traitement des fichiers ZIP
1. **Tout garder** : original + overlay separe + version composee *(Recommande)*
2. **Compose uniquement** : version finale avec overlay uniquement
3. **Original uniquement** : media sans overlay
4. **Original + compose** : les deux versions (sans le PNG overlay separe)

### :file_folder: Emplacement de telechargement

Par defaut, toutes tes memories seront telechargees dans le dossier `snapchat_memories/` du repertoire du projet.

**Tu veux changer l'emplacement ?**
Modifie la variable `OUTPUT_DIR` dans [`src/config.py`](src/config.py) :

```python
OUTPUT_DIR = "snapchat_memories"  # Change vers le chemin de ton choix
```

Tu peux utiliser :
- Des chemins relatifs : `"mes_memories"`
- Des chemins absolus : `"/Users/tonnom/Documents/Snapchat"`
- Des dossiers cloud : `"/Users/tonnom/Dropbox/Snapchat"`

---

## :sparkles: Fonctionnalites

- :bullettrain_side: **Telechargement parallele ultra-rapide** (30 threads simultanes)
- :calendar: **Organisation automatique par date** (annee/mois)
- :label: **Noms de fichiers bases sur la vraie date** (ex : `20251215_213158.jpg`)
- :mag: **Detection intelligente du format** (Content-Type + magic bytes)
- :package: **Traitement automatique des ZIP** (media + overlay PNG)
- :art: **Composition image + overlay** (overlay automatique)
- :clapper: **Composition video + overlay** (via ffmpeg)
- :clock1: **Preservation des metadonnees de date** (date de modification du fichier)
- :round_pushpin: **Extraction de la geolocalisation** (depuis le HTML)

---

## :file_folder: Structure de sortie

Tes memories seront organisees comme ceci :

```
snapchat_memories/
├── 2025/
│   ├── 12/
│   │   ├── 20251215_213158.jpg              # Image originale
│   │   ├── 20251215_213158_overlay.png      # Overlay (texte/dessin)
│   │   ├── 20251215_213158_composed.jpg     # Image + overlay compose
│   │   ├── 20251215_214230.mp4              # Video originale
│   │   ├── 20251215_214230_overlay.png      # Overlay video
│   │   └── 20251215_214230_composed.mp4     # Video + overlay
│   └── 11/
│       └── ...
├── 2024/
│   └── ...
└── 2018/
    └── ...
```

---

## :dart: Options de traitement des ZIP

Certaines memories sont exportees en ZIP contenant :
- Le media original (image ou video)
- Un overlay PNG (texte, dessins, stickers ajoutes sur Snapchat)

Le script propose 4 options au demarrage :

| Option | Description | Fichiers crees |
|--------|-------------|----------------|
| **1. Tout garder** | Garde tout separement + version composee | `_original`, `_overlay.png`, `_composed` |
| **2. Compose uniquement** | Version finale uniquement | `20251215_213158.jpg` |
| **3. Original uniquement** | Media sans overlay | `20251215_213158.jpg` |
| **4. Original + compose** | Les deux versions | `_original`, `_composed` |

---

## :gear: Configuration avancee

Tu peux modifier les parametres dans `src/config.py` :

```python
HTML_FILE = "html/memories_history.html"  # Chemin du fichier HTML
OUTPUT_DIR = "snapchat_memories"  # Dossier de sortie
MAX_WORKERS = 30  # Nombre de threads paralleles
TIMEOUT = 30  # Timeout par telechargement (secondes)
```

---

## :wrench: Depannage

### Le script ne trouve pas le fichier HTML
- :white_check_mark: Verifie que le fichier est dans `html/memories_history.html`
- :white_check_mark: Assure-toi d'avoir extrait le ZIP recu de Snapchat

### Les liens de telechargement expirent
- :warning: Les liens dans le HTML expirent apres quelques jours
- :arrows_counterclockwise: Demande un nouvel export depuis Snapchat

### Les images composees ne sont pas creees
- :warning: Verifie que Pillow est installe : `pip install Pillow`
- :information_source: Sans Pillow, seuls les fichiers originaux et les overlays seront sauvegardes

### Les videos composees ne sont pas creees
- :warning: Verifie que ffmpeg est installe : `ffmpeg -version`
- :information_source: Sans ffmpeg, seules les videos originales et les overlays PNG seront sauvegardes

### Erreur "No module named 'src'"
- :white_check_mark: Lance le script depuis le dossier racine : `python main.py`
- :x: Ne lance pas depuis le dossier `src/`

### Certains fichiers sont en .dat
- :information_source: Certains fichiers ont un format inconnu (Content-Type incorrect)
- :arrows_counterclockwise: Relance le script, la detection par magic bytes devrait les corriger

---

## :memo: Logs et statistiques

Le script affiche en temps reel :
- :bar_chart: Nombre de memories trouvees
- :inbox_tray: Progression du telechargement (barre de progression)
- :white_check_mark: Nombre de succes / ignores / echecs
- :package: Taille totale telechargee
- :stopwatch: Temps ecoule et vitesse moyenne
- :compression: Nombre de ZIP traites

---

## :bug: Un probleme ?

Si tu rencontres un probleme non liste ci-dessus, merci de [creer une issue](https://github.com/leofilmon/Snapchat-Downloader-Memories/issues) avec :
- Le message d'erreur complet
- Ta version de Python (`python --version`)
- Ton systeme d'exploitation

---

## :scroll: Licence

MIT License - Utilise ce projet librement !
