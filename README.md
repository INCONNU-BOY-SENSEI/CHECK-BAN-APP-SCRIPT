# INCONNU CHECK — wrapper Capacitor → APK Android

Squelette de projet pour transformer une app web (HTML/CSS/JS) en APK Android
nommé **INCONNU CHECK**, via [Capacitor](https://capacitorjs.com/) et [GitHub Actions](https://docs.github.com/actions).

## Structure du projet

```
.
├── www/                        ← TON HTML VA ICI
│   └── index.html              ← renomme ton fichier .html en index.html
├── android-icons/
│   └── logo-source.png         ← logo INCONNU STUDIO, source pour générer l'icône de l'app
├── android/                    ← généré automatiquement par `npx cap add android` (ne pas committer à la main)
├── .github/workflows/
│   └── build-apk.yml           ← pipeline CI qui build l'APK à chaque push
├── capacitor.config.json       ← config Capacitor (appId: com.inconnustudio.check, appName: INCONNU CHECK)
└── package.json
```

### Où mettre ton fichier HTML

1. Renomme ton fichier en `index.html`.
2. Place-le dans `www/index.html`.
3. Si ton HTML référence des images/CSS/JS externes en local (pas des URLs distantes),
   mets-les aussi dans `www/` en respectant les chemins relatifs utilisés dans le HTML.

Le dossier `www/` est le `webDir` déclaré dans `capacitor.config.json` — c'est
exactement ce dossier que Capacitor empaquette tel quel dans l'APK (WebView Android).

## Mise en route en local

```bash
npm install
npx cap add android      # crée le dossier android/ (une seule fois)
npx cap sync android      # copie www/ vers android/ à chaque modif
npx cap open android      # ouvre Android Studio pour builder/signer/tester
```

## Build automatique via GitHub Actions

Le workflow `.github/workflows/build-apk.yml` :
- se déclenche à chaque `push` sur `main` (ou manuellement via l'onglet Actions)
- installe Node + JDK 17
- ajoute la plateforme Android si absente
- build un **APK debug** (non signé pour le Play Store, installable directement sur un téléphone)
- dépose l'APK en tant qu'artifact téléchargeable dans l'onglet **Actions → run → Artifacts**

### Pour l'utiliser

1. Crée un repo GitHub, pousse ce projet (avec `www/index.html` rempli).
2. Va dans l'onglet **Actions** du repo → le workflow tourne automatiquement.
3. Une fois terminé, télécharge l'artifact `app-debug-apk`.

### Pour un APK signé (release, distribuable hors GitHub)

Il faut générer un keystore (`keytool -genkeypair ...`), l'ajouter en secret
GitHub (`ANDROID_KEYSTORE_BASE64`, `KEYSTORE_PASSWORD`, etc.), et ajouter une
étape `assembleRelease` + signature dans le workflow. Dis-moi si tu veux que
je l'ajoute — ça demande de générer et sécuriser tes propres clés.

## Personnalisation

- **Nom de l'app / package** : édite `appId` et `appName` dans `capacitor.config.json`
  avant le premier `npx cap add android` (le package Android est généré à partir de `appId`).
- **Icône / splash screen** : le logo `INCONNU STUDIO` est déjà dans
  `android-icons/logo-source.png`. Pour générer automatiquement toutes les
  résolutions d'icône Android à partir de ce fichier :

  ```bash
  npm install -D @capacitor/assets
  npx capacitor-assets generate --iconBackgroundColor '#000000' --android
  ```

  (Le fond noir correspond au fond du logo fourni ; ajuste si besoin.)
  L'outil lit par défaut `resources/icon.png` — copie ou renomme
  `android-icons/logo-source.png` vers `resources/icon.png` avant de lancer
  la commande, ou passe `--assetPath android-icons` selon la version de l'outil.
- **Permissions Android** (réseau, etc.) : `android/app/src/main/AndroidManifest.xml`
  une fois le dossier `android/` généré.
