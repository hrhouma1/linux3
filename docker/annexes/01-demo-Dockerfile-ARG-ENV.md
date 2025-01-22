### **Démonstration 1 : Dockerfile avec ARG pour une application personnalisable**

Dans cette démo, on montre comment personnaliser un message d'accueil pour une application lors de la construction de l'image.

**Dockerfile :**
```Dockerfile
# Utilise une image de base légère
FROM alpine:latest

# Définir une variable ARG (modifiable lors de la construction)
ARG WELCOME_MESSAGE="Bienvenue sur notre serveur Docker!"

# Installer les outils nécessaires
RUN apk add --no-cache bash

# Configurer une variable d'environnement en utilisant ARG
ENV MESSAGE=${WELCOME_MESSAGE}

# Commande exécutée par le conteneur
CMD echo "$MESSAGE"
```

**Explications :**
- `ARG WELCOME_MESSAGE`: Définit un message d'accueil avec une valeur par défaut.
- `ENV MESSAGE=${WELCOME_MESSAGE}` : Transmet la valeur de l'ARG dans une variable d'environnement.

**Exécution :**
1. Construire l'image avec une valeur personnalisée :
   ```bash
   docker build -t demo-arg --build-arg WELCOME_MESSAGE="Hello, Docker learners!" .
   ```

2. Lancer un conteneur basé sur cette image :
   ```bash
   docker run demo-arg
   ```

**Résultat attendu :**
- Le conteneur affiche : `Hello, Docker learners!`
- Si aucun `--build-arg` n'est passé, il affiche la valeur par défaut : `Bienvenue sur notre serveur Docker!`

---

### **Démonstration 2 : Dockerfile avec ENV pour configurer une API simple**

Dans cette démo, on montre comment utiliser des **variables d'environnement** pour configurer une application simulant un serveur d'API.

**Dockerfile :**
```Dockerfile
# Utilise une image de base Python
FROM python:3.9-slim

# Installer Flask pour créer une API simple
RUN pip install flask

# Définir des variables d'environnement pour l'API
ENV FLASK_APP=app.py
ENV HOST=0.0.0.0
ENV PORT=5000

# Copier le fichier de l'API dans l'image
COPY app.py /app/app.py

# Configurer le répertoire de travail
WORKDIR /app

# Commande pour exécuter le serveur Flask
CMD flask run --host=$HOST --port=$PORT
```

**Fichier `app.py` (simple API Flask) :**
```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "Bienvenue sur notre API Docker!"

if __name__ == '__main__':
    app.run()
```

**Explications :**
- `ENV FLASK_APP=app.py` : Indique à Flask quel fichier exécuter.
- `ENV HOST=0.0.0.0` et `ENV PORT=5000` : Permettent de configurer l’hôte et le port via des variables.

**Exécution :**
1. Construire l'image :
   ```bash
   docker build -t demo-env .
   ```

2. Lancer un conteneur basé sur cette image, en changeant le port si nécessaire :
   ```bash
   docker run -d -p 8080:5000 demo-env
   ```

3. Tester l'API :
   - Accéder à `http://localhost:8080` dans un navigateur ou utiliser `curl` :
     ```bash
     curl http://localhost:8080
     ```

**Résultat attendu :**
- La réponse de l'API : `Bienvenue sur notre API Docker!`

---

### **Résumé des Démos**
1. **Démonstration 1** montre comment utiliser `ARG` et `ENV` pour configurer un message d'accueil simple.
2. **Démonstration 2** démontre l'utilisation d'`ENV` dans une configuration d'API Flask, mettant en avant la modularité des variables d'environnement.

