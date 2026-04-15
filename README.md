# 🗄️ Inventaire Armoire Double

Application web mobile + desktop pour gérer l'inventaire d'une armoire double (gauche / droite), avec sections, contenants, objets et fiche 5S imprimable.

**Frontend** → GitHub Pages (ce dépôt)  
**Backend** → Google Apps Script + Google Sheets (données)

---

## 📁 Structure du dépôt

```
inventaire-armoire/
├── index.html      ← L'application complète (ce fichier)
├── Code.gs         ← Script Google Apps Script (backend)
└── README.md       ← Ce fichier
```

---

## 🚀 Déploiement en 3 étapes

### Étape 1 — Mettre en ligne sur GitHub Pages

1. Crée un nouveau dépôt GitHub (ex : `inventaire-armoire`)
2. Upload les fichiers `index.html` et `README.md`
3. Va dans **Settings → Pages**
4. Source : **Deploy from a branch** → branche `main` → dossier `/ (root)`
5. Clique **Save**
6. Ton app sera disponible à l'adresse :  
   `https://<ton-pseudo>.github.io/inventaire-armoire/`

---

### Étape 2 — Configurer le backend Google Apps Script

> **Obligatoire** pour que la sauvegarde dans Google Sheets fonctionne.

1. Ouvre [script.google.com](https://script.google.com) et crée un **nouveau projet**
2. Renomme-le (ex : `Inventaire Armoire`)
3. Supprime le contenu par défaut et **colle le contenu de `Code.gs`**
4. Dans le menu, va dans **Déployer → Nouveau déploiement**
5. Type : **Application Web**
6. Paramètres :
   - Exécuter en tant que : **Moi**
   - Qui a accès : **Tout le monde** *(nécessaire pour que GitHub Pages puisse appeler l'API)*
7. Clique **Déployer** et **autorise** les permissions demandées
8. Copie l'**URL de déploiement** (format `https://script.google.com/macros/s/…/exec`)

> ⚠️ À chaque modification de `Code.gs`, tu dois créer un **nouveau déploiement** (pas "gérer les déploiements") pour que les changements soient pris en compte.

---

### Étape 3 — Lier le frontend au backend

1. Ouvre ton app sur GitHub Pages
2. Un bandeau jaune ⚙️ apparaît en haut : **"Configuration requise"**
3. Colle l'URL Apps Script copiée à l'étape 2
4. Clique **Enregistrer**
5. L'URL est mémorisée dans le navigateur (localStorage) — tu ne la saisiras qu'une seule fois par appareil

---

## 🔧 Modification du Code.gs pour accepter les appels externes

Le `Code.gs` fourni utilise `google.script.run` en interne. Pour GitHub Pages, il faut qu'il accepte des appels `fetch` via GET. **Remplace la fonction `doGet`** par cette version :

```javascript
function doGet(e) {
  const action  = e.parameter.action;
  const payload = e.parameter.payload ? JSON.parse(e.parameter.payload) : {};

  let result;

  if (action === 'loadData') {
    result = loadData();
  } else if (action === 'saveData') {
    result = saveData(payload.data);
  } else {
    result = { success: false, error: 'Action inconnue : ' + action };
  }

  return ContentService
    .createTextOutput(JSON.stringify(result))
    .setMimeType(ContentService.MimeType.JSON);
}
```

> Toutes les autres fonctions (`loadData`, `saveData`, `writeInventoryToSheet`, etc.) restent **identiques**.

---

## 📱 Installer l'app sur mobile (optionnel)

L'app est responsive et fonctionne comme une app native depuis le navigateur.

**Sur iPhone (Safari) :**
1. Ouvre l'URL GitHub Pages dans Safari
2. Appuie sur le bouton **Partager** (carré avec flèche)
3. **"Sur l'écran d'accueil"**
4. L'icône apparaît sur ton bureau comme une vraie app

**Sur Android (Chrome) :**
1. Ouvre l'URL dans Chrome
2. Menu (⋮) → **"Ajouter à l'écran d'accueil"**

---

## 🗄️ Fonctionnalités

- **2 armoires** (gauche / droite) avec labels personnalisables
- **Sections** : étagères ou tiroirs
- **Contenants** : caisses, bacs, etc.
- **Objets** : nom, quantité (±1), référence
- **Recherche** temps réel sur tous les objets
- **Fiche 5S** imprimable avec checklist
- **Sauvegarde** dans Google Sheets (historique journalier + feuille courante)
- Interface **mobile** (onglets) et **desktop** (2 colonnes côte à côte)

---

## 🛠️ Dépannage

| Problème | Solution |
|---|---|
| Bandeau config qui revient | L'URL n'est pas valide ou le déploiement GAS n'est pas publié |
| Erreur réseau à la sauvegarde | Vérifier que le déploiement GAS est bien en accès "Tout le monde" |
| Les données ne se chargent pas | Vérifier que la feuille `Inventaire_Actuel` existe dans le Google Sheet |
| Modifications GAS non prises en compte | Créer un **nouveau déploiement** (pas modifier l'existant) |

---

## 📄 Licence

Usage libre pour usage personnel ou professionnel interne.
