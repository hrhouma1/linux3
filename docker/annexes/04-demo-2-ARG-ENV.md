### **Méthode 1 : Utilisation de `ARG` uniquement (Approche 1)**

Dans cette méthode, **`ARG`** est utilisé pour personnaliser le message de `cowsay` uniquement pendant la construction.

#### Dockerfile :
```Dockerfile
# Utiliser une image de base
FROM debian:latest

# Ajouter les outils nécessaires
RUN apt-get update && apt-get install -y cowsay

# Définir une variable ARG
ARG COWSAY_MESSAGE="Bonjour, Docker avec ARG !"

# Commande par défaut
CMD /usr/games/cowsay "$COWSAY_MESSAGE"
```

#### Étapes :
1. **Construire l’image :**
   ```bash
   docker build -t cowsay-arg --build-arg COWSAY_MESSAGE="Message personnalisé avec ARG" .
   ```

2. **Exécuter le conteneur :**
   ```bash
   docker run cowsay-arg
   ```

#### Résultat attendu :
- Le message affiché est celui fourni avec `--build-arg` pendant la construction.
- Si aucun `--build-arg` n'est passé, le message par défaut est affiché :  
  ```
  Bonjour, Docker avec ARG !
  ```

---

### **Méthode 2 : Utilisation de `ENV` uniquement (Approche 2)**

Dans cette méthode, **`ENV`** est utilisé pour définir un message modifiable lors de l'exécution du conteneur.

#### Dockerfile :
```Dockerfile
# Utiliser une image de base
FROM debian:latest

# Ajouter les outils nécessaires
RUN apt-get update && apt-get install -y cowsay

# Définir une variable d'environnement avec une valeur par défaut
ENV MESSAGE="Bonjour, Docker avec ENV !"

# Commande par défaut
CMD /usr/games/cowsay "$MESSAGE"
```

#### Étapes :
1. **Construire l’image :**
   ```bash
   docker build -t cowsay-env .
   ```

2. **Exécuter le conteneur avec une variable personnalisée :**
   ```bash
   docker run -e MESSAGE="Message personnalisé avec ENV" cowsay-env
   ```

3. **Exécuter le conteneur sans personnalisation :**
   ```bash
   docker run cowsay-env
   ```

#### Résultat attendu :
- Si une variable est spécifiée avec `-e`, le message personnalisé est affiché.  
  Exemple :  
  ```
  Message personnalisé avec ENV
  ```
- Sinon, la valeur par défaut est affichée :  
  ```
  Bonjour, Docker avec ENV !
  ```

---

### **Méthode 3 : Combinaison de `ARG` et `ENV`**

Dans cette méthode, **`ARG`** est utilisé pour initialiser **`ENV`**, offrant une personnalisation à la fois pendant la construction et l'exécution.

#### Dockerfile :
```Dockerfile
# Utiliser une image de base
FROM debian:latest

# Ajouter les outils nécessaires
RUN apt-get update && apt-get install -y cowsay

# Définir une variable ARG
ARG DEFAULT_MESSAGE="Bonjour, Docker avec ARG et ENV !"

# Utiliser ARG pour initialiser une variable d'environnement
ENV MESSAGE=${DEFAULT_MESSAGE}

# Commande par défaut
CMD /usr/games/cowsay "$MESSAGE"
```

#### Étapes :
1. **Construire l’image avec un message par défaut personnalisé :**
   ```bash
   docker build -t cowsay-combined --build-arg DEFAULT_MESSAGE="Message par défaut modifié avec ARG" .
   ```

2. **Exécuter le conteneur avec un message personnalisé à l'exécution :**
   ```bash
   docker run -e MESSAGE="Message personnalisé avec ENV (et ARG en fallback)" cowsay-combined
   ```

3. **Exécuter le conteneur sans personnalisation :**
   ```bash
   docker run cowsay-combined
   ```

#### Résultat attendu :
- **Cas 1** : Si `-e MESSAGE` est spécifié, ce message est affiché.  
  Exemple :  
  ```
  Message personnalisé avec ENV (et ARG en fallback)
  ```
- **Cas 2** : Si aucune valeur `-e MESSAGE` n'est donnée, la valeur de l'`ARG` est utilisée comme fallback.  
  Exemple :  
  ```
  Message par défaut modifié avec ARG
  ```

---

### **Résumé des Méthodes**
| **Méthode**                | **Utilisation**                                              | **Cas d'usage**                                   |
|----------------------------|------------------------------------------------------------|-------------------------------------------------|
| **Méthode 1 : `ARG`**      | Personnalisation au moment de la construction.             | Fixer des valeurs non modifiables après construction. |
| **Méthode 2 : `ENV`**      | Personnalisation au moment de l'exécution.                 | Configurer des paramètres d'exécution dynamiques. |
| **Méthode 3 : `ARG + ENV`**| Combiner les deux pour offrir une flexibilité maximale.     | Définir des valeurs par défaut tout en permettant des modifications dynamiques. |

Ces trois approches montrent comment adapter les variables en fonction des besoins de l'application.

--------------------
# Annexe: **"Choix entre Crochets (`[]`) et Chaînes Simples (`""`) dans CMD"**
-------------------

En général, l'utilisation de **crochets (`[]`) ou chaînes simples (`""`) dans `CMD`** dépend du comportement souhaité et de la manière dont Docker interprète la commande. Voici la distinction :

---

### **1. Syntaxe en Liste :**
```Dockerfile
CMD ["/usr/games/cowsay", "$COWSAY_MESSAGE"]
```
- **Interprétation :** Chaque élément de la commande est un argument séparé.
- **Avantage :** Pas de traitement par un shell, ce qui évite des problèmes comme l'expansion des variables ou l'interprétation incorrecte des caractères spéciaux.
- **Recommandé :** Lorsque vous voulez éviter des comportements inattendus dus à l'expansion du shell ou pour une meilleure portabilité.

---

### **2. Syntaxe en Chaîne :**
```Dockerfile
CMD /usr/games/cowsay "$COWSAY_MESSAGE"
```
- **Interprétation :** Utilise un shell pour exécuter la commande.
- **Avantage :** Permet l'expansion des variables d'environnement comme `$COWSAY_MESSAGE`.
- **Limite :** Moins sécurisé et légèrement moins performant car un shell est invoqué.

---

### **Quand Utiliser l'une ou l'autre ?**
- **Syntaxe en Liste (`[]`)** :
  - Si la commande n’a pas besoin d’un shell pour fonctionner.
  - Si vous souhaitez une exécution plus rapide et sécurisée.
  - Exemple : Commandes simples ou scripts.
- **Syntaxe en Chaîne (`""`)** :
  - Si vous avez besoin d'un shell pour traiter les variables d'environnement ou les opérateurs shell.
  - Exemple : Commandes nécessitant une expansion comme `$COWSAY_MESSAGE`.

---

### **Conclusion pour votre Cas :**
Si vous avez besoin de l’expansion de la variable `COWSAY_MESSAGE`, il est préférable d’utiliser la **syntaxe en chaîne (`""`)** comme suit :
```Dockerfile
CMD /usr/games/cowsay "$COWSAY_MESSAGE"
```

Cependant, si vous passez une valeur fixe ou voulez éviter l’utilisation d’un shell, préférez la **syntaxe en liste (`[]`)** :
```Dockerfile
CMD ["/usr/games/cowsay", "Bonjour, Docker avec ARG !"]
```
