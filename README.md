# INCONNU BAN CHECK 

Squelette de projet pour transformer une app web (HTML/CSS/JS) en APK Android
nommé **INCONNU CHECK**, via [Capacitor](https://capacitorjs.com/) et [GitHub Actions](https://docs.github.com/actions).

## Structure du projet

```
.
├── www/                        
│   └── index.html              
├── android-icons/
│   └── logo-source.png         
├── android/                   
├── .github/workflows/
│   └── build-apk.yml          
├── capacitor.config.json       
└── package.json
```


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
