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
