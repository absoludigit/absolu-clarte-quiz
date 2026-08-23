# Absolu Clarté — Quiz (lead magnet)

Quiz interactif en une seule page (`index.html`, sans build, sans dépendance) qui :

1. accueille la personne (landing),
2. **demande prénom + email avant de démarrer** (capture RGPD-friendly, envoyée à Brevo),
3. pose 8 questions,
4. affiche un résultat parmi 3 profils,
5. renvoie vers le workbook « Trouvez votre pourquoi » avec un lien trackable (`utm_source`, `profil`).

Aucune donnée n'est stockée côté serveur : le seul appel réseau est l'envoi du formulaire vers Brevo.

## 1. Configurer Brevo (5 minutes)

Le quiz utilise un **formulaire classique Brevo** (pas de clé API exposée dans le code, c'est important pour la sécurité).

1. Dans Brevo : **Contacts → Formulaires → Créer un formulaire → Formulaire classique**.
2. Ajoute les champs **Email** (obligatoire) et **Prénom** (`FIRSTNAME`).
3. Choisis ou crée la liste de contacts qui doit recevoir ces leads (ex. « Quiz Pourquoi »).
4. Enregistre, puis va dans l'onglet **Partager** du formulaire → **Code HTML**.
5. Dans le code HTML fourni par Brevo, repère :
   - l'attribut `action="https://XXXXXX.sibforms.com/serve/...."` de la balise `<form>` → c'est ton `formAction`.
   - les `<input type="hidden" name="..." value="...">` (souvent `locale`, parfois un identifiant de liste) → à reporter dans `hiddenFields`.
6. Ouvre `index.html`, tout en haut du `<script>`, et remplace :

```js
const BREVO_CONFIG = {
  formAction: 'REMPLACER_PAR_URL_ACTION_BREVO', // <- colle ici l'action copiée
  emailField: 'EMAIL',
  firstNameField: 'FIRSTNAME',
  hiddenFields: { locale: 'fr' } // <- ajoute ici tout autre champ caché exigé par Brevo
};
```

Tant que `formAction` contient encore `REMPLACER_PAR_URL_ACTION_BREVO`, le quiz fonctionne normalement mais n'envoie rien à Brevo (juste un avertissement dans la console) — pratique pour tester avant de brancher le vrai formulaire.

**Important RGPD :** pense à activer la double opt-in sur la liste Brevo si tu veux rester strictement conforme, et à garder le lien vers ta politique de confidentialité à jour (variable `PRIVACY_URL` en haut du script).

## 2. Personnaliser

Tout se modifie dans `index.html` :
- `questions` : les 8 questions (texte, sous-texte, 3 réponses `"Titre|Sous-titre"`).
- Le tableau `data` dans `result()` : les 3 profils de résultat (titre, texte, prochain pas).
- `WORKBOOK_URL` : le lien vers ton offre payante.
- `PRIVACY_URL` : le lien vers ta politique de confidentialité.

## 3. Tester en local

Aucune installation nécessaire, ouvre simplement `index.html` dans un navigateur. Ou, avec Python :

```bash
python3 -m http.server 8000
# puis ouvrir http://localhost:8000
```

## 4. Mettre en ligne sur GitHub Pages

Depuis ce dossier :

```bash
git init
git add .
git commit -m "Quiz Absolu Clarté - lead magnet"
git branch -M main
git remote add origin https://github.com/TON-COMPTE/TON-REPO.git
git push -u origin main
```

Puis sur GitHub : **Settings → Pages → Source : Deploy from a branch → Branch : main / (root)**.

Le quiz sera accessible à `https://TON-COMPTE.github.io/TON-REPO/` en quelques minutes.

Si tu préfères un domaine perso (ex. `quiz.absolu-digit.fr`), ajoute un fichier `CNAME` à la racine contenant ce domaine, et configure un enregistrement DNS de type `CNAME` pointant vers `TON-COMPTE.github.io`.

## 5. Suivre les conversions

Le lien vers le workbook contient automatiquement `?utm_source=quiz&utm_medium=lead-magnet&utm_campaign=pourquoi&profil=...` (`enfoui`, `emergent` ou `boussole` selon le résultat). Si ton site de vente track déjà les UTM (Google Analytics, Plausible, etc.), tu verras quel profil de quiz convertit le mieux vers l'achat.
