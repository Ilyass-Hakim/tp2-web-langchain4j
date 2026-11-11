# Utilisation de l'IA pour le TP 2
## Application Web avec LangChain4j

**IA utilisée :** Claude (Anthropic) et ChatGPT (OpenAI).

J'ai réalisé moi-même la création du projet et du dépôt GitHub, la recherche des dernières versions des dépendances et des plugins sur le dépôt Maven, la copie des fichiers repris du TP 0, ainsi que tous les tests de l'application.

J'ai utilisé l'IA pour les points ci-dessous.

---

## 1. Classe `LlmClient`

**Problème rencontré :**  
Écrire la classe qui utilise LangChain4j pour communiquer avec Gemini, avec une mémoire de conversation et le rôle système.

**Raison de l'utilisation de l'IA :**  
Obtenir de l'aide pour structurer la classe `LlmClient`, comprendre comment ajouter correctement le rôle système dans la mémoire et anticiper les erreurs possibles à cette étape (je savais déjà que le modèle gemini-2.5-flash n'était pas disponible pour mon compte).

**Prompts utilisés :**

1. « Quelles sont les étapes nécessaires pour créer la classe `LlmClient` conformément à l'énoncé ? »
2. « Comment déclarer la classe avec les annotations `@Dependent` et `Serializable`, et quelles variables faut-il ajouter ? »
3. « Réalise le constructeur permettant de configurer Gemini avec le modèle `gemini-3.5-flash-lite` et d'initialiser `MessageWindowChatMemory`. »
4. « Comment implémenter le setter `setSystemRole()` pour vider la mémoire et ajouter le rôle système sous forme de `SystemMessage` ? »
5. « Implémente la méthode `envoyerRequete()` permettant d'envoyer une question à l'assistant et d'obtenir sa réponse. »
6. « Vérifie la cohérence de la classe avec l'énoncé et explique le rôle de chaque partie. »

**Réponse de l'IA (extrait) :**

**1. Prompt : « Quelles sont les étapes nécessaires pour créer la classe LlmClient conformément à l'énoncé ? »**

La création de LlmClient nécessite les étapes suivantes :
- Déclarer la classe avec les annotations @Dependent et Serializable.
- Déclarer les trois variables : systemRole, assistant et chatMemory.
- Configurer le modèle Gemini dans le constructeur.
- Initialiser la mémoire avec MessageWindowChatMemory.
- Créer l'assistant avec AiServices.builder().
- Ajouter une méthode pour définir le rôle système et une autre pour envoyer les questions.
**2. Prompt : « Comment déclarer la classe avec les annotations @Dependent et Serializable, et quelles variables faut-il ajouter ? »**

L'annotation @Dependent permet de rendre la classe compatible avec l'injection de dépendances CDI. L'interface Serializable permet de rendre ses instances sérialisables.

Les trois variables nécessaires sont :
- systemRole : stocke le rôle système de l'assistant.
- assistant : représente l'assistant créé avec LangChain4j.
- chatMemory : conserve l'historique des messages de la conversation.
**3. Prompt : « Réalise le constructeur permettant de configurer Gemini avec le modèle gemini-3.5-flash-lite et d'initialiser MessageWindowChatMemory. »**

Le constructeur récupère la clé API depuis la variable d'environnement GEMINI_KEY, configure le modèle Gemini avec GoogleGenAiChatModel.builder(), puis initialise une mémoire pouvant conserver jusqu'à 20 messages.

Enfin, AiServices.builder() permet de créer l'assistant en lui associant le modèle Gemini et la mémoire conversationnelle.

**4. Prompt : « Comment implémenter le setter setSystemRole() pour vider la mémoire et ajouter le rôle système sous forme de SystemMessage ? »**

Le setter doit effectuer trois opérations dans cet ordre :

```java
this.systemRole = systemRole;
this.chatMemory.clear();
this.chatMemory.add(SystemMessage.from(systemRole));
```

La méthode clear() supprime les anciens messages de la conversation, puis add() ajoute le nouveau rôle système sous forme de SystemMessage. Cela permet de commencer une nouvelle conversation avec le rôle choisi.

**5. Prompt : « Implémente la méthode envoyerRequete() permettant d'envoyer une question à l'assistant et d'obtenir sa réponse. »**

La méthode utilise assistant.chat(question) pour transmettre la question à l'assistant. LangChain4j gère automatiquement l'utilisation de la mémoire conversationnelle et retourne la réponse sous forme de chaîne de caractères.

```java
public String envoyerRequete(String question) {
    return this.assistant.chat(question);
}
```

**6. Prompt : « Vérifie la cohérence de la classe avec l'énoncé et explique le rôle de chaque partie. »**

La classe respecte la structure demandée dans l'énoncé :
- Les annotations définissent la portée et la sérialisation de la classe.
- Les trois variables permettent de gérer le rôle système, l'assistant et la mémoire.
- Le constructeur configure Gemini et crée l'assistant avec une mémoire de 20 messages.
- setSystemRole() initialise le rôle système et réinitialise la mémoire.
- envoyerRequete() transmet les questions à l'assistant et récupère ses réponses.

L'ensemble permet de communiquer avec Gemini tout en conservant le contexte des échanges et en respectant le rôle système défini.

**Vérification :**  
J'ai comparé chaque partie du code avec les exigences de l'énoncé, puis effectué un test avec le rôle système « Traducteur Anglais-Français ». Le message « Bonjour » a correctement été traduit par « Hello ».

**Ce que j'ai appris :**  
J'ai appris à structurer un client IA avec LangChain4j, à configurer un modèle Gemini, à gérer la mémoire des conversations et à intégrer un rôle système pour orienter les réponses de l'assistant.

---

## 2. Adaptation du backing bean `Bb`

**Problème rencontré :**  
Adapter le backing bean du TP 0 pour qu'il utilise `LlmClient` au lieu de `GenerateurReponse`.

**Raison de l'utilisation de l'IA :**  
Utiliser l'IA pour comprendre et réaliser les modifications nécessaires dans le backing bean `Bb` afin de remplacer `GenerateurReponse` par `LlmClient`, tout en conservant le fonctionnement existant de l'application.

**Prompts utilisés :**

1. « Analyse le backing bean `Bb` du TP 0 et identifie les modifications nécessaires pour utiliser `LlmClient` à la place de `GenerateurReponse`. »
2. « Modifie la méthode `envoyer()` pour initialiser le rôle système uniquement au début de la conversation, puis envoyer les questions à travers `llmClient.envoyerRequete()`. »
3. « Explique pourquoi il faut utiliser `=` au lieu de `+=` pour affecter la réponse retournée par `envoyerRequete()`. »

**Réponse de l'IA (extrait) :**

**1. Prompt : « Analyse le backing bean Bb du TP 0 et identifie les modifications nécessaires pour utiliser LlmClient à la place de GenerateurReponse. »**

Pour adapter le backing bean, il faut remplacer l'ancien service GenerateurReponse par LlmClient. Les principales modifications concernent :
- L'injection de LlmClient à la place de GenerateurReponse.
- L'initialisation du rôle système à travers la méthode setSystemRole().
- Le remplacement de l'ancien appel à genererReponse() par envoyerRequete().

Le reste du backing bean, notamment la gestion des questions vides, l'affichage de la conversation et le bouton Nouveau chat, peut être conservé.

**2. Prompt : « Modifie la méthode envoyer() pour initialiser le rôle système uniquement au début de la conversation, puis envoyer les questions à travers llmClient.envoyerRequete(). »**

Il faut vérifier si la conversation est vide avant d'initialiser le rôle système. Ainsi, le rôle est défini uniquement lors du premier message.

```java
if (this.conversation.isEmpty()) {
    this.llmClient.setSystemRole(this.roleSysteme);
    this.roleSystemeChangeable = false;
}

this.reponse = this.llmClient.envoyerRequete(this.question);
```

Cette condition évite de réinitialiser la mémoire à chaque question et permet de conserver le contexte des échanges précédents.

**3. Prompt : « Explique pourquoi il faut utiliser = au lieu de += pour affecter la réponse retournée par envoyerRequete(). »**

L'opérateur = permet de remplacer le contenu de reponse par la nouvelle réponse retournée par l'assistant.

Contrairement à +=, qui ajoute le nouveau texte au contenu déjà présent dans la variable, = permet de conserver uniquement la dernière réponse dans cette variable.

La conversation complète reste conservée séparément dans conversation, grâce à la méthode afficherConversation().

**Vérification :**

J'ai effectué plusieurs tests :

- **Mémoire :** poser une question sur la capitale du Maroc, puis demander sa population pour vérifier la conservation du contexte.
- **Nouveau chat :** vérifier que l'ancienne conversation est réinitialisée.
- **Rôle système :** tester le rôle « Guide touristique » avec une question sur Casablanca.
- **Question vide :** vérifier que le message d'erreur s'affiche correctement.

Les tests ont confirmé le bon fonctionnement des différentes fonctionnalités.

**Ce que j'ai appris :**

J'ai appris à adapter un backing bean existant pour intégrer un nouveau client IA, à gérer l'initialisation du rôle système au début d'une conversation et à assurer la continuité du contexte grâce à la mémoire conversationnelle. 
J'ai également compris l'importance de tester les fonctionnalités après chaque modification.
