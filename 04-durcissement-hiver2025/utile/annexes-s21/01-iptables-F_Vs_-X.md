
# Introduction

Les commandes `iptables -F` et `iptables -X` sont utilisées pour nettoyer les règles et les chaînes de règles dans le pare-feu `iptables`. Voici leur signification :


### 🔹 **`iptables -F` (Flush)**
- **Signification :** Cette commande supprime toutes les règles existantes dans toutes les chaînes des tables actuellement chargées.
- **Effet :** Elle ne supprime pas les chaînes personnalisées mais vide leur contenu.
- **Utilisation typique :** Réinitialiser toutes les règles du pare-feu pour repartir à zéro.

**Exemple d'utilisation :**
```bash
iptables -F
```
Cela efface toutes les règles dans les chaînes par défaut (`INPUT`, `FORWARD`, `OUTPUT`).

Pour vider une table spécifique (par exemple, `nat`), on peut préciser :
```bash
iptables -t nat -F
```

---

### 🔹 **`iptables -X` (Delete User-Defined Chains)**
- **Signification :** Supprime les **chaînes personnalisées** définies par l'utilisateur.
- **Effet :** Cela ne touche pas aux chaînes par défaut (`INPUT`, `FORWARD`, `OUTPUT`), mais cela supprime toutes les chaînes créées manuellement **à condition qu'elles soient vides**.
- **Utilisation typique :** Supprimer des chaînes inutilisées après avoir vidé les règles.

**Exemple d'utilisation :**
```bash
iptables -X
```
Cela supprime toutes les chaînes personnalisées existantes **après les avoir vidées** avec `iptables -F`.

---

### 🔥 **Exemple combiné : Tout réinitialiser !**
Si tu veux remettre `iptables` à zéro, la séquence complète est :
```bash
iptables -F        # Supprime toutes les règles existantes
iptables -X        # Supprime toutes les chaînes personnalisées
iptables -t nat -F # Supprime toutes les règles de la table nat
iptables -t mangle -F # Supprime toutes les règles de la table mangle
iptables -t raw -F # Supprime toutes les règles de la table raw
```

---

### 🛑 **Attention**
- Ces commandes réinitialisent `iptables` mais **ne modifient pas les politiques par défaut** (DROP, ACCEPT, etc.).
- Après un `iptables -F`, si ta politique par défaut est `DROP`, toutes les connexions réseau risquent d’être bloquées. Tu peux vérifier avec :
  ```bash
  iptables -L -v
  ```
- Si nécessaire, remets une politique par défaut :
  ```bash
  iptables -P INPUT ACCEPT
  iptables -P FORWARD ACCEPT
  iptables -P OUTPUT ACCEPT
  ```

