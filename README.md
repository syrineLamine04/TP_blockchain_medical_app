# Dossier Médical — Application Web (Médecin / Patient)

Application simple de gestion de dossiers médicaux (Médecin / Patient).
La blockchain n'est volontairement pas intégrée pour l'instant — le
code est structuré (routes/services séparés) pour pouvoir l'ajouter
plus tard sans tout réécrire, mais rien n'y fait référence aujourd'hui.

## Stack technique

- **Frontend** : HTML / CSS / JavaScript (vanilla, sans framework)
- **Backend** : Node.js + Express
- **Base de données** : SQLite (via `better-sqlite3`)
- **API** : REST (JSON)
- **Authentification** : JWT (JSON Web Token)

## Installation

```bash
cd medical-app
npm install
npm run seed     # crée un compte médecin et un compte patient de démo
npm start
```

L'application est ensuite accessible sur : http://localhost:3000

## Comptes de démonstration (après `npm run seed`)

| Rôle    | Email              | Mot de passe |
|---------|--------------------|--------------|
| Médecin | medecin@demo.com   | password123  |
| Patient | patient@demo.com   | password123  |

Vous pouvez aussi créer de nouveaux comptes via l'onglet "Créer un compte".

## Structure du projet

```
medical-app/
├── server.js                 # point d'entrée Express
├── seed.js                   # script de données de démo
├── config/db.js              # connexion SQLite + schéma des tables
├── middleware/auth.js        # vérification JWT + contrôle de rôle
├── utils/password.js         # hachage bcrypt des mots de passe
├── routes/
│   ├── auth.routes.js        # /api/auth (register, login)
│   ├── doctor.routes.js      # /api/doctor (patients, consultations, diagnostics, prescriptions)
│   └── patient.routes.js     # /api/patient (profil, dossier, consultations, prescriptions)
└── public/                   # frontend (HTML/CSS/JS)
    ├── index.html             # connexion / inscription
    ├── doctor/                # espace médecin
    └── patient/               # espace patient
```

## Fonctionnalités

### Médecin
- Connexion
- Liste de ses patients
- Consultation du dossier médical complet d'un patient
- Ajout d'une consultation
- Ajout d'un diagnostic (rattaché à une consultation)
- Ajout d'une prescription (rattachée à une consultation)
- Consultation de l'historique médical

### Patient
- Connexion
- Consultation de son profil
- Consultation de son dossier médical
- Consultation de ses consultations
- Consultation de ses prescriptions

## Évolution future vers la blockchain

La blockchain n'est pas branchée dans cette version. Quand vous serez
prêt à l'ajouter, l'idée recommandée est de créer un dossier
`services/blockchain/` avec :
- un module `blockchainService.js` exposant une interface simple, par
  exemple `anchorRecord(type, id, payload)` ;
- un adapter concret (Ethereum, Hyperledger, etc.) derrière cette
  interface.

Les routes (`routes/doctor.routes.js`) appelleraient alors ce service
juste après chaque `INSERT` (consultation, diagnostic, prescription),
et le hash renvoyé serait stocké dans une colonne dédiée. Comme la
logique métier actuelle est déjà isolée dans des routes propres, cet
ajout n'impactera pas le reste du code.

## Sécurité (à renforcer avant une mise en production réelle)

- Changez `JWT_SECRET` dans `.env`.
- Ajoutez une validation plus stricte des entrées (ex: `zod`, `joi`).
- Ajoutez HTTPS, rate limiting, et des règles CORS plus restrictives.
- Envisagez le chiffrement des données médicales sensibles au repos.
