# Exercice 1

Vous avez un fichier nommé **garage.yaml** contenant une liste de voitures avec des informations sur la marque, le modèle et l'année de fabrication.

**Instruction :**  
Modifiez la liste des voitures en ajoutant une section supplémentaire appelée **maintenance history** pour chaque voiture. Cette section doit contenir un tableau des maintenances effectuées, avec les informations suivantes pour chaque maintenance :

| Propriété  | Valeur                   |
|------------|--------------------------|
| date       | Date de la maintenance    |
| type       | Type de maintenance       |
| cost       | Coût de la maintenance en $|

Contenu actuel du fichier **garage.yaml** :

```yaml
cars:
  - brand: Toyota
    model: Corolla
    year: 2020
  - brand: Honda
    model: Accord
    year: 2021
```

Complétez ce fichier en ajoutant l'historique de maintenance pour chaque voiture.

---

# Exercice 2

Dans le fichier **garage.yaml**, on a ajouté une section **owner** qui contient des informations sur le propriétaire de chaque voiture. Vous devez créer un tableau supplémentaire nommé **drivers** pour indiquer les conducteurs autorisés pour chaque voiture. Chaque conducteur doit avoir les propriétés suivantes :

| Propriété  | Valeur             |
|------------|--------------------|
| name       | Nom du conducteur  |
| age        | Âge du conducteur  |
| license    | Numéro de permis   |

**Instruction :**  
Ajoutez un tableau **drivers** sous chaque entrée de voiture dans le fichier **garage.yaml**, en incluant au moins deux conducteurs pour chaque voiture.

Contenu actuel du fichier **garage.yaml** :

```yaml
cars:
  - brand: Toyota
    model: Corolla
    year: 2020
    owner:
      name: Jane Doe
      license: AB1234XYZ
  - brand: Honda
    model: Accord
    year: 2021
    owner:
      name: John Smith
      license: XY9876LMN
```

---

# Exercice 3

Le fichier **garage.yaml** contient maintenant une section **insurance** pour chaque voiture, avec des informations sur la compagnie d'assurance et le numéro de la police d'assurance.

**Instruction :**  
Vous devez vérifier que chaque voiture dispose d'une assurance valide pour l'année en cours. Si une voiture n'a pas d'assurance valide, ajoutez une entrée **insurance** avec les informations suivantes :

| Propriété    | Valeur                         |
|--------------|--------------------------------|
| company      | Nom de la compagnie d'assurance |
| policy_number| Numéro de la police d'assurance |

Contenu actuel du fichier **garage.yaml** :

```yaml
cars:
  - brand: Toyota
    model: Corolla
    year: 2020
    insurance:
      company: XYZ Insurance
      policy_number: 12345ABC
  - brand: Honda
    model: Accord
    year: 2021
    # Pas d'information d'assurance pour cette voiture
```

Ajoutez l'assurance manquante pour les voitures.

---

# Exercice 4

Dans le fichier **garage.yaml**, on a ajouté une section **fuel** pour chaque voiture. Cette section contient deux propriétés : **type** (essence ou diesel) et **consumption** (litres aux 100 kilomètres).

**Instruction :**  
Ajoutez la section **fuel** pour chaque voiture, et calculez une estimation du coût de carburant pour parcourir 1000 kilomètres. Ajoutez une nouvelle propriété **fuel_cost_estimation** à chaque voiture avec le coût estimé, en supposant que le prix de l'essence est de 1,5 $/litre.

Contenu actuel du fichier **garage.yaml** :

```yaml
cars:
  - brand: Toyota
    model: Corolla
    year: 2020
    fuel:
      type: essence
      consumption: 7.5
  - brand: Honda
    model: Accord
    year: 2021
    fuel:
      type: diesel
      consumption: 6.0
```

Complétez le fichier avec l'estimation du coût de carburant pour chaque voiture.

---

# Exercice 5

Le fichier **garage.yaml** contient des informations sur les services de maintenance de chaque voiture. Les services sont listés avec une date et un coût. Vous devez ajouter une nouvelle section appelée **total_cost** qui résume le coût total de toutes les maintenances pour chaque voiture.

**Instruction :**  
Calculez le coût total des maintenances pour chaque voiture et ajoutez-le au fichier YAML sous la propriété **total_cost**.

Contenu actuel du fichier **garage.yaml** :

```yaml
cars:
  - brand: Toyota
    model: Corolla
    year: 2020
    services:
      - date: 2021-06-01
        type: Oil change
        cost: 50
      - date: 2021-12-15
        type: Tire rotation
        cost: 80
  - brand: Honda
    model: Accord
    year: 2021
    services:
      - date: 2022-03-10
        type: Brake replacement
        cost: 300
```

Ajoutez le coût total des maintenances pour chaque voiture sous **total_cost**.
