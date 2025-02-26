---
sidebar_position: 2
sidebar_label: "SSL/TLS - Partie 2"
title: "SSL/TLS - Partie 2"
description: "Introduction aux concepts de base de SSL/TLS, leur importance et les solutions existantes"
keywords: [ssl, tls, securite, https, certificats]
contributors: ["Asabeneh"]
slug: /linux3/ssl-tls/partie2
---

# Fonctionnement de SSL/TLS et Certbot en profondeur (Partie 02)

<a name="table-des-matieres"></a>
<br/>

---
## Table des matières
---

1. [Les bases importantes de SSL/TLS](#partie-1)
   - [1.1 Objectifs principaux](#partie-1-1)
   - [1.2 Comment fonctionne SSL/TLS](#partie-1-2)
2. [Certificats numériques](#partie-2)
   - [2.1 Introduction aux certificats](#partie-2-1)
   - [2.2 Certificat auto-signé](#partie-2-2)
   - [2.3 Certificat signé par une Autorité de Certification (CA)](#partie-2-3)
   - [2.4 Certificats SSL/TLS](#partie-2-4)
3. [Certbot](#partie-3)
   - [3.1 Introduction à Certbot](#partie-3-1)
   - [3.2 Installation et configuration de Certbot](#partie-3-2)
   - [3.3 Renouvellement automatique](#partie-3-3)
4. [Outils équivalents à Certbot](#partie-4)
   - [4.1 Acme.sh](#partie-4-1)
   - [4.2 Dehydrated](#partie-4-2)
   - [4.3 lego](#partie-4-3)
   - [4.4 Caddy](#partie-4-4)
5. [Comparatif des solutions SSL/TLS](#partie-5)
   - [5.1 Certbot vs Acme.sh](#partie-5-1)
   - [5.2 Certbot vs Caddy](#partie-5-2)
   - [5.3 Certbot vs Certificat auto-signé](#partie-5-3)
6. [Exemple d'un certificat auto-signé](#partie-6)
   - [6.1 Création d'un certificat auto-signé](#partie-6-1)
   - [6.2 Exemple de certificat auto-signé](#partie-6-2)
7. [Comparaison : Certificat auto-signé vs Certificat signé par CA](#partie-7)
   - [7.1 Critères de comparaison](#partie-7-1)
8. [Conclusion](#partie-8)
   - [8.1 Utilisation en production](#partie-8-1)
   - [8.2 Utilisation pour des tests](#partie-8-2)



<a name="introduction"></a>
<br/>
---
## Introduction
---
D'abord, rappelons que le protocole **SSL (Secure Sockets Layer)** et son successeur **TLS (Transport Layer Security)** assurent une communication sécurisée sur Internet. Ils permettent de chiffrer les échanges entre un client (navigateur) et un serveur, protégeant ainsi la confidentialité et l'intégrité des données.


<a name="partie-1"></a>
<br/>

---
## 1. Les bases importantes de SSL/TLS
---
<a name="partie-1-1"></a>
### 1.1. Objectifs principaux 
   - Chiffrement : Garantir que les données échangées restent privées.
   - Authentification : Assurer que le serveur est bien celui qu'il prétend être.
   - Intégrité des données : Garantir que les données n'ont pas été altérées en transit.

<a name="partie-1-2"></a>
### 1.2. Comment fonctionne SSL/TLS 
   - Utilisation de certificats numériques pour authentifier les parties.
   - Établissement d'une connexion sécurisée via un handshake TLS.
   - Les données sont ensuite chiffrées avec des clés symétriques pour des performances optimales.

[🔙 Retour à la table des matières](#table-des-matieres)


<a name="partie-2"></a>
<br/>
---
## 2. Certificats numériques
---
<a name="partie-2-1"></a>
### 2.1. Introduction aux certificats 
Un certificat numérique est un document électronique utilisé pour prouver l'identité d'une entité (un site web, une entreprise, etc.).
<a name="partie-2-2"></a>
### 2.2. Certificat auto-signé 
   - Créé par l'entité elle-même sans intervention d'une autorité tierce.
   - Utilisé principalement à des fins de test ou pour des environnements internes.
   - Inconvénient : Les navigateurs le marquent comme non fiable.

<a name="partie-2-3"></a>
### 2.3. Certificat signé par une Autorité de Certification (CA) 
   - Délivré par une **CA (Certificate Authority)**, comme Let's Encrypt, DigiCert, ou GlobalSign.
   - Reconnus comme fiables par les navigateurs.
   - Fournit une authentification et une reconnaissance par les systèmes externes.

<a name="partie-2-4"></a>
### 2.4. Certificats SSL/TLS 
   - **DV (Domain Validation)** : Valide uniquement la propriété du domaine.
   - **OV (Organization Validation)** : Vérifie également l'existence de l'organisation.
   - **EV (Extended Validation)** : Vérification approfondie, souvent utilisée pour les entreprises.


[🔙 Retour à la table des matières](#table-des-matieres)



<a name="partie-3"></a>
<br/>
---
## 3. Certbot
---
<a name="partie-3-1"></a>
### 3.1. Certbot 
- Certbot est un outil gratuit et open source développé par l'EFF pour automatiser la génération et le renouvellement de certificats SSL/TLS auprès de **Let's Encrypt**.

<a name="partie-3-2"></a>
### 3.2. Installation et configuration de Certbot 
   - Installez Certbot via votre gestionnaire de paquets (apt, yum, etc.).
   - Utilisez la commande suivante pour obtenir et configurer un certificat :
     ```bash
     sudo certbot --apache
     # ou
     sudo certbot --nginx
     ```
   - Certbot configure automatiquement votre serveur web pour qu'il utilise HTTPS.

<a name="partie-3-3"></a>
### 3.3. Renouvellement automatique 
   - Let's Encrypt délivre des certificats valides pendant 90 jours.
   - Certbot ajoute une tâche cron pour les renouveler automatiquement.


[🔙 Retour à la table des matières](#table-des-matieres)



<a name="partie-4"></a>
<br/>
---
## 4. Outils équivalents à Certbot
---
<a name="partie-4-1"></a>
### 4.1. Acme.sh 
- Client ACME léger écrit en bash.
<a name="partie-4-2"></a>
### 4.2. Dehydrated 
- Script ACME simple et minimaliste.
<a name="partie-4-3"></a>
### 4.3. lego 
- Client ACME en Go, compatible avec plusieurs serveurs DNS.
<a name="partie-4-4"></a>
### 4.4. Caddy 
- Serveur web avec SSL/TLS intégré (certificats Let's Encrypt automatiques).


[🔙 Retour à la table des matières](#table-des-matieres)



<a name="partie-5"></a>
<br/>
---
## 5. Comparatif des solutions SSL/TLS
---
<a name="partie-5-1"></a>
### 5.1. Comparatif des solutions SSL/TLS
| **Caractéristique**            | **Certbot**       | **Acme.sh**     | **Caddy**        | **Certificat auto-signé** |
|---------------------------------|-------------------|-----------------|------------------|---------------------------|
| **Gratuité**                   | ✅                | ✅              | ✅               | ✅                        |
| **Configuration automatique**  | ✅ (Apache/Nginx) | ❌              | ✅ (serveur web) | ❌                        |
| **Autorité de certification**  | Let's Encrypt     | Let's Encrypt   | Let's Encrypt    | Non                       |
| **Renouvellement automatique** | ✅                | ✅              | ✅               | ❌                        |
| **Utilisation en production**  | ✅                | ✅              | ✅               | ❌ (non recommandé)       |


[🔙 Retour à la table des matières](#table-des-matieres)


<a name="partie-6"></a>
<br/>
---
## 6. Exemple d'un certificat auto-signé
---
<a name="partie-6-1"></a>
### 6.1. Exemple d'un certificat auto-signé
Pour créer un certificat auto-signé sous Linux :
```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout selfsigned.key -out selfsigned.crt
```

<a name="partie-6-2"></a>
### 6.2. Exemple de contenu d'un certificat auto-signé
Un exemple de contenu d'un certificat auto-signé est présenté ci-dessous :

```bash
openssl x509 -in selfsigned.crt -text -noout
```

Le résultat de la commande affichera des informations détaillées sur le certificat auto-signé, notamment :

1. **Informations sur le certificat** :
   - Version du certificat
   - Numéro de série
   - Algorithme de signature
   - Dates de validité (début et fin)

2. **Informations sur l'émetteur** (Issuer) :
   - Pays (C)
   - État/Province (ST) 
   - Ville (L)
   - Organisation (O)
   - Unité organisationnelle (OU)
   - Nom commun (CN)
   - Email

3. **Informations sur le sujet** (Subject) :
   - Mêmes champs que l'émetteur car auto-signé
   - Le sujet et l'émetteur sont identiques

4. **Informations sur la clé publique** :
   - Type d'algorithme (RSA)
   - Taille de la clé (2048 bits)
   - Exposant
   - Modulus

5. **Extensions X509** :
   - Basic Constraints
   - Key Usage
   - Subject Key Identifier
   - Authority Key Identifier

Exemple de sortie :
```bash
openssl x509 -in selfsigned.crt -text -noout
```

La sortie affichera des informations détaillées comme ceci :
```bash
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            b2:3c:5d:81:2e:ac:8c:0b
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: C=FR, ST=Ile-de-France, L=Paris, O=Mon Organisation, OU=IT, CN=monsite.local
        Validity
            Not Before: Jan 1 00:00:00 2023 GMT
            Not After : Dec 31 23:59:59 2023 GMT
        Subject: C=FR, ST=Ile-de-France, L=Paris, O=Mon Organisation, OU=IT, CN=monsite.local
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
            RSA Public Key: (2048 bit)
                Modulus:
                    00:c4:6e:cd:8b:12:d4:0d:cf:74:2c:1a:db:0b:
                    [...]
                    15:23
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Basic Constraints: 
                CA:FALSE
            X509v3 Key Usage: 
                Digital Signature, Key Encipherment
            X509v3 Subject Key Identifier: 
                88:5A:55:C0:52:FF:61:CD:52:A3:35:0F:EA:5A:9C:24:38:22:F7:5C
            X509v3 Authority Key Identifier: 
                88:5A:55:C0:52:FF:61:CD:52:A3:35:0F:EA:5A:9C:24:38:22:F7:5C
    Signature Algorithm: sha256WithRSAEncryption
        84:2e:0c:c4:5f:21:86:6d:1f:9c:5b:c5:f4:68:f3:af:
        [...]
        12:34
```





[🔙 Retour à la table des matières](#table-des-matieres)


<a name="partie-7"></a>
<br/>
---
## 7. Comparaison : Certificat auto-signé vs Certificat signé par CA
---
<a name="partie-7-1"></a>
### 7.1. Comparaison : Certificat auto-signé vs Certificat signé par CA
| **Critère**                  | **Certificat Auto-signé** | **Certificat signé par CA** |
|------------------------------|---------------------------|-----------------------------|
| **Fiabilité**                | Faible                   | Élevée                     |
| **Reconnaissance des navigateurs** | ❌                      | ✅                          |
| **Coût**                     | Gratuit                  | Gratuit (Let's Encrypt) ou payant |
| **Complexité de configuration** | Faible                  | Moyenne                    |
| **Utilisation recommandée**  | Tests/Environnements internes | Production                  |

[🔙 Retour à la table des matières](#table-des-matieres)



<a name="partie-8"></a>
<br/>
---
## 8. Conclusion
---
<a name="partie-8-1"></a>
<br/>
### 8.1 Utilisation en production
- Pour les environnements de production, privilégiez toujours un certificat signé par une **CA** reconnue :
  - Let's Encrypt via Certbot est une solution gratuite et fiable
  - Les certificats sont reconnus par tous les navigateurs
  - Le renouvellement est automatisé
  - La configuration est simplifiée

<a name="partie-8-2"></a>
<br/>
### 8.2 Utilisation pour des tests
- Les certificats auto-signés sont adaptés pour :
  - Les environnements de développement et de test
  - Les réseaux internes non accessibles depuis Internet
  - Les tests de configuration SSL/TLS
  - La validation de concepts avant mise en production


[🔙 Retour à la table des matières](#table-des-matieres)
<br/>






