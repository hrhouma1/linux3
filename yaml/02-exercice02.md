# Question 1

**Lequel des éléments suivants est utilisé pour séparer la clé et la valeur dans YAML ?**

- Deux-points
- Tiret
- Virgule
- Point-virgule

# Question 2

**Combien de clés de tableau (array keys) y a-t-il dans l'extrait YAML suivant ?**

```yaml
Cars:
  - Honda
  - Toyota
  - BMW
Colors:
  - Red
  - Blue
  - Green
```

- 2
- 3
- 6
- 5

# Question 3

**Laquelle des affirmations suivantes est vraie ?**

A. Le dictionnaire est une collection non ordonnée, tandis que la liste est une collection ordonnée.  
B. Le dictionnaire est une collection ordonnée, tandis que la liste est une collection non ordonnée.  
C. Le dictionnaire et la liste sont toutes deux des collections ordonnées.  
D. Le dictionnaire et la liste sont toutes deux des collections non ordonnées.


# Question 4

Il y a un fichier YAML nommé **garage.yaml** sous le répertoire **/home/jane/garage/** avec une clé **car1** et la valeur **Honda**.

Contenu actuel du fichier **garage.yaml** :

```yaml
car1: Honda
```


# Question 5

Le fichier **/home/jane/garage.yaml** a été mis à jour avec la clé **model** et la valeur **Civic**.

Contenu actuel du fichier **garage.yaml** :

```yaml
model: Civic
```


# Question 6

Le fichier **/home/jane/garage.yaml** a été mis à jour avec un dictionnaire nommé **car**.

Complétez ce dictionnaire avec les propriétés restantes en utilisant les informations du tableau ci-dessous :

| Clé/Propriété | Valeur  |
|---------------|---------|
| brand         | Toyota  |
| model         | Corolla |
| year          | 2020    |

Contenu actuel du fichier **garage.yaml** :

```yaml
car:
  brand: Toyota
```


# Question 7

Ajoutez un dictionnaire nommé **owner** pour ajouter les informations du propriétaire dans le fichier.

Détails du propriétaire à ajouter :

| Clé/Propriété | Valeur      |
|---------------|-------------|
| name          | Jane Doe    |
| license       | AB1234XYZ   |
| city          | Los Angeles |

- Contenu actuel du fichier **garage.yaml** :

```yaml
car:
  brand: Toyota
  model: Corolla
  year: 2020
```

Complétez le fichier avec les informations du propriétaire à partir du tableau fourni.


# Question 8

Nous avons mis à jour le fichier **/home/jane/garage.yaml** avec une liste de **services** effectués. Ajoutez un nouveau service pour en faire un total de **4**.

- Contenu actuel du fichier **garage.yaml** :

```yaml
services:
  - Oil change
  - Tire rotation
  - Brake inspection
```

Complétez le fichier en ajoutant un nouvel élément à cette liste pour atteindre le total demandé.


# Question 9

Dans le fichier **/home/jane/garage.yaml**, ajoutez deux valeurs supplémentaires pour atteindre un total de **6 services**.

- Contenu actuel du fichier **garage.yaml** :

```yaml
services:
  - Oil change
  - Tire rotation
  - Brake inspection
  - Battery check
```

Complétez le fichier en ajoutant deux autres éléments à cette liste pour atteindre le total demandé.


# Question 10

Le fichier **/home/jane/garage.yaml** a été mis à jour avec des informations pour **car1**, **car2**, et **car3**. Comme pour **car1**, nous souhaitons ajouter des détails supplémentaires pour chaque véhicule, tels que la couleur et l'année. Modifiez les éléments restants pour correspondre aux données ci-dessous.

| Car2    |         |         |
|---------|---------|---------|
| color   | blue    |         |
| year    | 2019    |         |

| Car3    |         |         |
|---------|---------|---------|
| color   | black   |         |
| year    | 2021    |         |

- Contenu actuel du fichier **garage.yaml** :

```yaml
- car1:
    color: red
    year: 2018
- car2:
- car3:
```

Complétez les informations manquantes pour **car2** et **car3** avec les détails fournis.


# Question 11

Le fichier **/home/jane/garage.yaml** a été mis à jour avec un dictionnaire nommé **car**. Nous souhaitons enregistrer les informations de plusieurs voitures. Convertissez le dictionnaire nommé **car** en un tableau nommé **cars**.

- Contenu actuel du fichier **garage.yaml** :

```yaml
car:
  brand: Toyota
  model: Corolla
  year: 2020
```

Modifiez ce contenu pour convertir le dictionnaire **car** en un tableau **cars**.


# Question 12

Mettez à jour le fichier **/home/jane/garage.yaml** pour ajouter une nouvelle voiture (sous l'entrée existante) à la liste en utilisant les informations ci-dessous.

| Clé/Propriété | Valeur    |
|---------------|-----------|
| brand         | Honda     |
| model         | Accord    |
| year          | 2021      |

- Contenu actuel du fichier **garage.yaml** :

```yaml
cars:
  - brand: Toyota
    model: Corolla
    year: 2020
```

Ajoutez les informations de la nouvelle voiture sous l'entrée actuelle.


# Question 13

Nous avons mis à jour le fichier **/home/jane/garage.yaml** pour ajouter plus de détails. Maintenant, ajoutez les informations d'assurance. Rappelez-vous que, tandis que **owner** est un dictionnaire, **insurance** est un tableau contenant **company** et **policy number**.

| Mois        | Montant   |
|-------------|-----------|
| june        | 1400      |
| july        | 2400      |
| august      | 3400      |

- Contenu actuel du fichier **garage.yaml** :

```yaml
cars:
  - brand: Toyota
    model: Corolla
    year: 2020
  owner:
    name: Jane Doe
    license: AB1234XYZ
    city: Los Angeles
```

Complétez le fichier en ajoutant les informations d'assurance sous la clé **insurance**.
