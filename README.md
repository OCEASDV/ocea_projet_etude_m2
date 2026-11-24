# Guide de développement — Node.js & React Native

> **Référentiel de conventions pour l'équipe**  
> Ce document définit les règles de développement, de nommage, de qualité, de sécurité et de collaboration du projet. Il est à adapter sur l'ensemble du projet.

## Sommaire

1. [Principes généraux](#1-principes-généraux)
2. [Arborescence du dépôt](#2-architecture-du-dépôt)
3. [Conventions de nommage](#3-conventions-de-nommage)
4. [Application React Native](#4-application-react-native)
5. [Commentaires et documentation](#5-commentaires-et-documentation)
6. [Tests et qualité](#6-tests-et-qualité)
7. [Git : branches et commits](#7-git--branches-et-commits)
8. [Intégration Jira](#8-intégration-jira)

---

## 1. Principes généraux

- **Langue du code : anglais** pour les noms de fichiers, classes, fonctions, variables, branches et commits. Les textes affichés à l'utilisateur peuvent être en français et doivent idéalement être centralisés pour l'internationalisation.
- **Cohérence** : respecter les conventions existantes du dépôt et les règles automatisées de lint/format.
- **Petites modifications vérifiables** : commits ciblés, tests pertinents et pull requests faciles à relire.

Les mots-clés **OBLIGATOIRE**, **RECOMMANDÉ** et **À ÉVITER** indiquent le niveau d'exigence. Les exceptions doivent être justifiées en revue de code.

## 2. Arborescence du projet

Exemple d'arborescence de projet (ceci est un exemple qui a déjà été implémenté dans le cadre de mes anciens projets, des choses peuvent être ajoutés/modifiés/supprimés en conséquence) :

```text
project/
├── github/
    ├── workflows
        ├── ci_develop.yml
        ├── ci_main.yml
├── backend/
│   ├── src/
│   │   ├── config/          # Configuration de l'application
│   │   ├── controllers/     # Adaptateurs HTTP
│   │   ├── services/        # Logique métier
│   │   ├── repositories/    # Accès aux données
│   │   ├── models/          # Modèles de domaine / ORM
│   │   ├── routes/          # Déclaration des routes
│   │   ├── middlewares/     # Authentification, erreurs, etc.
│   │   ├── validators/      # Validation des entrées
│   │   ├── utils/           # Utilitaires réutilisables
│   │   ├── app.js           # Configuration Express (si Express)
│   │   └── server.js        # Démarrage du serveur
│   └── tests/
├── frontend/
│   ├── src/
│   │   ├── components/      # Composants UI réutilisables
│   │   ├── screens/         # Écrans
│   │   ├── navigation/      # Navigation
│   │   ├── hooks/           # Hooks personnalisés
│   │   ├── services/        # Clients API et intégrations
│   │   ├── store/           # État global, si nécessaire
│   │   ├── contexts/        # Contextes React
│   │   ├── constants/       # Constantes
│   │   ├── theme/           # Design tokens et styles globaux
│   │   ├── utils/           # Fonctions utilitaires
│   │   └── assets/          # Images, icônes, polices autorisées
│   └── __tests__/
├── docs/                   # Documentation technique complémentaire
├── .editorconfig
├── .gitignore
├── Fiches élèves Lootopia
├── README.md
└── sonar-project.properties
```

## 3. Conventions de nommage

| Élément | Convention | Exemple 
|---|---|---
| Variables | `camelCase` | `currentUser`, `isLoading` 
| Fonctions | `camelCase`, verbe explicite | `fetchUserProfile()` 
| Classes | `PascalCase` | `UserService`, `ApiError` | `user_service` 
| Composants React Native | `PascalCase` | `ProfileScreen`, `UserCard`
| Objets de configuration | `camelCase` | `apiConfig` 
| Fichiers de composants | `PascalCase` | `UserCard.tsx` / `UserCard.jsx`
| Fichiers de services/utilitaires | `camelCase` | `userService.js`, `formatDate.js`
| Dossiers | `kebab-case` pour dossiers métier ; conventions de framework conservées | `user-profile/`, `auth/` 
| Tests | Même nom que le fichier ciblé + suffixe | `userService.test.js`
| Variables d'environnement | `UPPER_SNAKE_CASE` | `DATABASE_URL`
| Routes HTTP | Noms de ressources au pluriel, `kebab-case` si composé | `/api/users`, `/api/user-profiles`
| Champs JSON d'API | `camelCase` | `createdAt`, `userId`
| Tables SQL (convention proposée) | `snake_case` au pluriel | `users`, `order_items`
| Colonnes SQL (convention proposée) | `snake_case` | `created_at`, `user_id`

**Règle :** conserver les noms imposés par les frameworks, bibliothèques, APIs externes et schémas existants. Ne pas renommer une base de données existante sans migration contrôlée.

## 4. Application React Native

- Utiliser des **composants fonctionnels** et des hooks React.
- Les composants et écrans portent un nom en `PascalCase` et sont exportés clairement.

### Exemple de composant

```jsx
import { Pressable, StyleSheet, Text } from 'react-native';

export function UserCard({ user, onPress }) {
  return (
    <Pressable
      accessibilityRole="button"
      accessibilityLabel={`Open profile of ${user.name}`}
      onPress={() => onPress(user.id)}
      style={styles.card}
    >
      <Text style={styles.name}>{user.name}</Text>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  card: { padding: 16 },
  name: { fontSize: 16, fontWeight: '600' },
});
```

## 5. Commentaires et documentation

**Principe : documenter le pourquoi, pas répéter le quoi.** Le code doit rester compréhensible sans commentaires superflus.
- Penser à commenter/supprimer les console.log qui ne sont plus utilisés.

### Commentaires utiles

```js
// La synchronisation est différée pour éviter les conflits en mode hors ligne.
await queueSync();
```

### Commentaires inutiles

```js
// Incrémente le compteur de 1.
count += 1;
```

## 6. Tests et qualité

### Contrôles CI recommandés

À chaque pull request : installation reproductible des dépendances, lint, formatage, tests, vérification TypeScript si applicable et audit de sécurité adapté. Les builds mobile et les tests E2E peuvent être déclenchés selon le coût et le temps d'exécution.

## 7. Git : branches et commits

### Branches

Utiliser une branche principale protégée `main`. Toute fonctionnalité ou correction passe par une branche dédiée et une pull request, sauf procédure d'urgence explicitement prévue.

main
develop
chore
feature

Lorsqu'une branche a été merge, on la supprime. 

### Conventional Commits

Doc Gitmoji -> https://gitmoji.dev/

Format :
git commit -m ":sparkles: LOOTOPIA-42 - add hunt feature"

"LOOTOPIA-XX" est la référence du ticket en question.

Type de commit : 
feat 
fix
doc
style
refactor
test
chore

## 8. Intégration Jira

- Une branche liée à une tâche doit contenir sa clé Jira : `LOOTOPIA-XX`.
- Ajouter la clé dans le titre de la PR et/ou les messages de commit, selon les règles de l'intégration GitHub–Jira.
- Une tâche doit décrire un besoin vérifiable, avec critères d'acceptation.
- Mettre à jour le statut du ticket au fur et à mesure du travail.
- Lier la PR au ticket ; ne pas considérer un ticket comme terminé s'il n'a pas été tester au préalable.

Exemple de ticket :

```text
LOOTOPIA-XX — Implement user login
```

## Gouvernance du document

Ce README constitue la **base commune** de l'équipe. Toute modification d'une convention doit être discutée en pull request, validée par l'équipe. Les décisions techniques structurantes peuvent être consignées dans `docs/adr/` sous forme d'Architecture Decision Records.
