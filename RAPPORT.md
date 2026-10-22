# Projet 5 : Conception réseau complète pour une PME multi-sites

**Classe :** E1A – Geneva Institute of Technology
**Auteur :** Rrudhani Deaa
**Date :** 15 octobre 2026
**Niveau :** Avancé – PPE (1 semaine)
**Outils :** GNS3 (3 routeurs Cisco c7200, 2 Ethernet switch, 5 VPCS) et Wireshark

---

- [1. Contexte et objectifs](#1-contexte-et-objectifs)
  - [1.1 Contexte](#11-contexte)
  - [1.2 Objectifs pédagogiques](#12-objectifs-pédagogiques)
  - [1.3 Livrables](#13-livrables)
- [2. Recueil des besoins](#2-recueil-des-besoins)
  - [2.1 Postes par site](#21-postes-par-site)
  - [2.2 Services partagés](#22-services-partagés)
  - [2.3 Exigences et solutions retenues](#23-exigences-et-solutions-retenues)
- [3. Schéma d'architecture](#3-schéma-darchitecture)
  - [3.1 Les 3 zones](#31-les-3-zones)
  - [3.2 Rôle de chaque équipement](#32-rôle-de-chaque-équipement)
  - [3.3 Le principe du tunnel VPN](#33-le-principe-du-tunnel-vpn)
- [4. Plan d'adressage détaillé](#4-plan-dadressage-détaillé)
  - [4.1 Règle d'adressage](#41-règle-dadressage)
  - [4.2 Répartition des adresses dans chaque VLAN](#42-répartition-des-adresses-dans-chaque-vlan)
  - [4.3 Réseaux des VLAN](#43-réseaux-des-vlan)
  - [4.4 Liaisons WAN (Internet simulé)](#44-liaisons-wan-internet-simulé)
  - [4.5 Tableau complet par équipement](#45-tableau-complet-par-équipement)
  - [4.6 Résumé des réseaux par site](#46-résumé-des-réseaux-par-site)
- [5. Rapport de justification technique](#5-rapport-de-justification-technique)
  - [5.1 Pourquoi des VLAN ?](#51-pourquoi-des-vlan-)
  - [5.2 Pourquoi le router-on-a-stick ?](#52-pourquoi-le-router-on-a-stick-)
  - [5.3 Pourquoi cet adressage ?](#53-pourquoi-cet-adressage-)
  - [5.4 Pourquoi des routes statiques par défaut ?](#54-pourquoi-des-routes-statiques-par-défaut-)
  - [5.5 Méthode d'interconnexion : VPN IPsec ou ligne dédiée ?](#55-méthode-dinterconnexion--vpn-ipsec-ou-ligne-dédiée-)
  - [5.6 Choix des paramètres de sécurité IPsec](#56-choix-des-paramètres-de-sécurité-ipsec)
  - [5.7 Choix du matériel de la maquette](#57-choix-du-matériel-de-la-maquette)
- [6. Maquette GNS3 : topologie et switchs](#6-maquette-gns3--topologie-et-switchs)
  - [6.1 Topologie](#61-topologie)
  - [6.2 Câblage](#62-câblage)
  - [6.3 Configuration des switchs](#63-configuration-des-switchs)
- [7. Configuration de R-FAI (Internet simulé)](#7-configuration-de-r-fai-internet-simulé)
- [8. Configuration de R-GENEVE (siège)](#8-configuration-de-r-geneve-siège)
  - [8.1 Interface WAN et route par défaut](#81-interface-wan-et-route-par-défaut)
  - [8.2 Sous-interfaces VLAN (router-on-a-stick)](#82-sous-interfaces-vlan-router-on-a-stick)
  - [8.3 Vérifications](#83-vérifications)
- [9. Configuration de R-LAUSANNE (agence)](#9-configuration-de-r-lausanne-agence)
  - [9.1 Vérifications](#91-vérifications)
- [10. Configuration des PC (VPCS)](#10-configuration-des-pc-vpcs)
- [11. Tests AVANT le VPN](#11-tests-avant-le-vpn)
  - [11.1 Routage inter-VLAN au siège (depuis PC-DIR-A)](#111-routage-inter-vlan-au-siège-depuis-pc-dir-a)
  - [11.2 Routage inter-VLAN à l'agence (depuis PC-DIR-B)](#112-routage-inter-vlan-à-lagence-depuis-pc-dir-b)
  - [11.3 Communication entre les sites sans VPN](#113-communication-entre-les-sites-sans-vpn)
- [12. Configuration du VPN IPsec site-à-site](#12-configuration-du-vpn-ipsec-site-à-site)
  - [12.1 Principe : 2 phases](#121-principe--2-phases)
  - [12.2 Configuration de R-GENEVE](#122-configuration-de-r-geneve)
  - [12.3 Configuration de R-LAUSANNE](#123-configuration-de-r-lausanne)
  - [12.4 Explication des commandes](#124-explication-des-commandes)
- [13. Tests APRÈS le VPN](#13-tests-après-le-vpn)
  - [13.1 Ping entre les sites](#131-ping-entre-les-sites)
  - [13.2 Vérification de la phase 1 : show crypto isakmp sa](#132-vérification-de-la-phase-1--show-crypto-isakmp-sa)
  - [13.3 Vérification de la phase 2 : show crypto ipsec sa](#133-vérification-de-la-phase-2--show-crypto-ipsec-sa)
  - [13.4 Vérification sur R-LAUSANNE](#134-vérification-sur-r-lausanne)
- [14. Preuve du chiffrement avec Wireshark](#14-preuve-du-chiffrement-avec-wireshark)
  - [14.1 Objectif](#141-objectif)
  - [14.2 Méthode](#142-méthode)
  - [14.3 Filtre isakmp : la négociation du tunnel](#143-filtre-isakmp--la-négociation-du-tunnel)
  - [14.4 Filtre esp : les données chiffrées](#144-filtre-esp--les-données-chiffrées)
  - [14.5 Filtre icmp : aucun ping visible sur Internet](#145-filtre-icmp--aucun-ping-visible-sur-internet)
  - [14.6 Comparaison : capture sur le trunk du siège](#146-comparaison--capture-sur-le-trunk-du-siège)
  - [14.7 Correspondance des SPI entre Wireshark et le routeur](#147-correspondance-des-spi-entre-wireshark-et-le-routeur)
  - [14.8 Bilan Wireshark](#148-bilan-wireshark)
- [15. Problèmes rencontrés](#15-problèmes-rencontrés)
  - [15.1 Port 1 du switch resté en mode access](#151-port-1-du-switch-resté-en-mode-access)
  - [15.2 Capture Wireshark impossible (accents dans le chemin)](#152-capture-wireshark-impossible-accents-dans-le-chemin)
  - [15.3 Filtre isakmp vide dans Wireshark](#153-filtre-isakmp-vide-dans-wireshark)
  - [15.4 Autres points rencontrés](#154-autres-points-rencontrés)
- [16. Pistes d'amélioration](#16-pistes-damélioration)
- [17. Conclusion](#17-conclusion)
- [18. Annexe – Commandes par équipement](#18-annexe--commandes-par-équipement)
  - [18.1 R-FAI](#181-r-fai)
  - [18.2 R-GENEVE](#182-r-geneve)
  - [18.3 R-LAUSANNE](#183-r-lausanne)
  - [18.4 SW-GNV (pas de ligne de commande, configuration à la souris)](#184-sw-gnv-pas-de-ligne-de-commande-configuration-à-la-souris)
  - [18.5 SW-LSN (pas de ligne de commande, configuration à la souris)](#185-sw-lsn-pas-de-ligne-de-commande-configuration-à-la-souris)
  - [18.6 PC-DIR-A](#186-pc-dir-a)
  - [18.7 PC-EMP-A](#187-pc-emp-a)
  - [18.8 SRV-FICHIERS](#188-srv-fichiers)
  - [18.9 PC-DIR-B](#189-pc-dir-b)
  - [18.10 PC-EMP-B](#1810-pc-emp-b)
  - [18.11 Wireshark (filtres utilisés)](#1811-wireshark-filtres-utilisés)
- [19. Liste des captures](#19-liste-des-captures)

---

## 1. Contexte et objectifs

### 1.1 Contexte

La PME **Alpes Services** possède **2 sites distants** :
- le **siège** à **Genève** ;
- une **agence** à **Lausanne**.

Les deux sites doivent pouvoir **communiquer de façon sécurisée**. En particulier, l'agence doit pouvoir accéder aux serveurs installés au siège. Il faut concevoir l'architecture réseau cible, la valider avec une maquette sur GNS3, puis prouver le chiffrement avec Wireshark.

### 1.2 Objectifs pédagogiques

- Concevoir une architecture réseau **multi-sites** cohérente.
- Prévoir l'**adressage IP**, les **VLAN** et l'**interconnexion** des sites.
- **Documenter et justifier** les choix techniques.

### 1.3 Livrables

| Livrable demandé | Où le trouver |
|---|---|
| Schéma d'architecture réseau complet | Section 3 |
| Plan d'adressage détaillé | Section 4 |
| Rapport de justification technique | Section 5 |
| Maquette GNS3 de validation (en plus) | Sections 6 à 13 |
| Preuve du chiffrement avec Wireshark (en plus) | Section 14 |

---

## 2. Recueil des besoins

### 2.1 Postes par site

| Site | Service | Nombre de postes |
|---|---|---|
| Genève (siège) | Direction | 5 |
| Genève (siège) | Employés | 15 |
| Lausanne (agence) | Direction | 2 |
| Lausanne (agence) | Employés | 8 |
| **Total** | | **30 postes** |

### 2.2 Services partagés

| Service | Emplacement | Utilisé par |
|---|---|---|
| Serveur de fichiers (SRV-FICHIERS) | Siège – Genève | Les 2 sites |
| Serveur d'annuaire et DNS (Active Directory) | Siège – Genève | Les 2 sites |
| Imprimante réseau | Siège – Genève | Le siège |

Les serveurs sont **centralisés au siège** : c'est plus simple à gérer et à sauvegarder qu'un serveur sur chaque site. L'agence y accède à travers l'interconnexion.

### 2.3 Exigences et solutions retenues

| N° | Exigence | Solution retenue |
|---|---|---|
| E1 | Séparer les services (Direction / Employés / Serveurs) | **VLAN** |
| E2 | Permettre aux services d'un même site de communiquer | **Routage inter-VLAN (router-on-a-stick)** |
| E3 | Relier les 2 sites | **Interconnexion à travers Internet (FAI)** |
| E4 | Communication **sécurisée** entre les sites | **VPN IPsec site-à-site** (chiffrement AES 256) |
| E5 | Adressage clair et évolutif | **Règle 10.[site].[VLAN].0/24** |
| E6 | Coût adapté à une PME | **VPN sur Internet** plutôt qu'une ligne dédiée |

---

## 3. Schéma d'architecture

![Capture d'écran 2026-10-15 180508](https://hackmd.io/_uploads/r1FkFUyhMx.png)




### 3.1 Les 3 zones

| Zone | Équipements | Rôle |
|---|---|---|
| **Site A – Genève (siège)** | R-GENEVE, SW-GNV, PC-DIR-A, PC-EMP-A, SRV-FICHIERS | Siège avec 3 VLAN et les serveurs partagés |
| **Internet simulé** | R-FAI | Simule le fournisseur d'accès Internet entre les 2 sites |
| **Site B – Lausanne (agence)** | R-LAUSANNE, SW-LSN, PC-DIR-B, PC-EMP-B | Agence avec 2 VLAN |

### 3.2 Rôle de chaque équipement

| Équipement | Type | Rôle |
|---|---|---|
| R-GENEVE | Routeur Cisco c7200 | Passerelle des 3 VLAN du siège, sortie vers Internet, extrémité du VPN |
| R-FAI | Routeur Cisco c7200 | Simule Internet : relie les 2 sites mais **ne connaît pas** leurs réseaux privés |
| R-LAUSANNE | Routeur Cisco c7200 | Passerelle des 2 VLAN de l'agence, sortie vers Internet, extrémité du VPN |
| SW-GNV | Ethernet switch GNS3 | Relie les postes du siège et sépare les VLAN 10, 20 et 30 |
| SW-LSN | Ethernet switch GNS3 | Relie les postes de l'agence et sépare les VLAN 10 et 20 |
| PC-DIR-A, PC-EMP-A | VPCS | Représentent les postes Direction et Employés du siège |
| SRV-FICHIERS | VPCS | Simule le serveur de fichiers du siège |
| PC-DIR-B, PC-EMP-B | VPCS | Représentent les postes Direction et Employés de l'agence |

Un **seul poste par VLAN** suffit dans la maquette : il représente tous les postes de ce service.

### 3.3 Le principe du tunnel VPN

Un **tunnel VPN IPsec** relie R-GENEVE et R-LAUSANNE à travers R-FAI. Quand un PC de Lausanne envoie un paquet vers le siège :

1. R-LAUSANNE **chiffre** le paquet complet.
2. Il le met dans un **nouveau paquet** adressé de 198.51.100.1 vers 203.0.113.1.
3. R-FAI ne voit que ce paquet externe : il ne peut **ni lire ni modifier** le contenu.
4. R-GENEVE **déchiffre** le paquet et le remet au bon VLAN.

---

## 4. Plan d'adressage détaillé

### 4.1 Règle d'adressage

Une règle simple est appliquée partout : **10.[site].[VLAN].0/24**

| Partie | Signification | Exemple pour 10.2.20.11 |
|---|---|---|
| 10 | Réseau privé de classe A (RFC 1918) | Réseau privé |
| [site] | 1 = Genève, 2 = Lausanne | Site 2 = Lausanne |
| [VLAN] | Numéro du VLAN | VLAN 20 = Employés |
| Dernier octet | Numéro de l'équipement | Poste n°11 |

Avantages :
- on sait **tout de suite** à quel site et à quel service appartient une adresse ;
- chaque site tient dans un seul bloc : **10.1.0.0/16** pour Genève et **10.2.0.0/16** pour Lausanne. Une **seule règle** suffit dans le VPN ;
- un futur site prendra simplement **10.3.0.0/16**.

### 4.2 Répartition des adresses dans chaque VLAN

| Plage | Utilisation |
|---|---|
| .1 | **Passerelle** (sous-interface du routeur) |
| .2 à .10 | Réservées (serveurs, imprimantes, équipements réseau) |
| .11 à .254 | **Postes utilisateurs** |
| .255 | Adresse de diffusion (broadcast) |

### 4.3 Réseaux des VLAN

| Site | VLAN | Nom | Réseau | Masque | Passerelle | Plage utilisable | Postes prévus | Capacité | Marge |
|---|---|---|---|---|---|---|---|---|---|
| Genève | 10 | DIRECTION | 10.1.10.0/24 | 255.255.255.0 | 10.1.10.1 | 10.1.10.1 – .254 | 5 | 254 | 249 |
| Genève | 20 | EMPLOYES | 10.1.20.0/24 | 255.255.255.0 | 10.1.20.1 | 10.1.20.1 – .254 | 15 | 254 | 239 |
| Genève | 30 | SERVEURS | 10.1.30.0/24 | 255.255.255.0 | 10.1.30.1 | 10.1.30.1 – .254 | 3 | 254 | 251 |
| Lausanne | 10 | DIRECTION | 10.2.10.0/24 | 255.255.255.0 | 10.2.10.1 | 10.2.10.1 – .254 | 2 | 254 | 252 |
| Lausanne | 20 | EMPLOYES | 10.2.20.0/24 | 255.255.255.0 | 10.2.20.1 | 10.2.20.1 – .254 | 8 | 254 | 246 |

Un /24 donne **254 adresses utilisables** (256 − l'adresse réseau − le broadcast). C'est largement plus que les besoins, ce qui laisse une **grosse marge d'évolution**. Comme on utilise des adresses privées, il n'y a aucune raison d'économiser des adresses.

### 4.4 Liaisons WAN (Internet simulé)

| Liaison | Réseau | Masque | Adresse réseau | Côté site | Côté FAI | Broadcast |
|---|---|---|---|---|---|---|
| R-GENEVE ↔ R-FAI | 203.0.113.0/30 | 255.255.255.252 | 203.0.113.0 | 203.0.113.1 (R-GENEVE) | 203.0.113.2 | 203.0.113.3 |
| R-LAUSANNE ↔ R-FAI | 198.51.100.0/30 | 255.255.255.252 | 198.51.100.0 | 198.51.100.1 (R-LAUSANNE) | 198.51.100.2 | 198.51.100.3 |

- Un **/30** donne exactement **2 adresses utilisables**, pile ce qu'il faut pour une liaison entre 2 routeurs. Aucune adresse n'est gaspillée.
- Les plages **203.0.113.0/24** et **198.51.100.0/24** sont réservées à la **documentation** (RFC 5737). Elles simulent des adresses publiques sans risquer d'utiliser l'adresse de quelqu'un.

### 4.5 Tableau complet par équipement

| Équipement | Interface | Adresse IP | Masque | Passerelle | VLAN | Branché à |
|---|---|---|---|---|---|---|
| R-GENEVE | Gi1/0 | 203.0.113.1 | /30 | 203.0.113.2 (route par défaut) | – | R-FAI Gi1/0 |
| R-GENEVE | Gi0/0 | aucune (trunk) | – | – | 10, 20, 30 | SW-GNV e1 |
| R-GENEVE | Gi0/0.10 | 10.1.10.1 | /24 | – | 10 | – |
| R-GENEVE | Gi0/0.20 | 10.1.20.1 | /24 | – | 20 | – |
| R-GENEVE | Gi0/0.30 | 10.1.30.1 | /24 | – | 30 | – |
| R-FAI | Gi1/0 | 203.0.113.2 | /30 | – | – | R-GENEVE Gi1/0 |
| R-FAI | Gi2/0 | 198.51.100.2 | /30 | – | – | R-LAUSANNE Gi1/0 |
| R-LAUSANNE | Gi1/0 | 198.51.100.1 | /30 | 198.51.100.2 (route par défaut) | – | R-FAI Gi2/0 |
| R-LAUSANNE | Gi0/0 | aucune (trunk) | – | – | 10, 20 | SW-LSN e1 |
| R-LAUSANNE | Gi0/0.10 | 10.2.10.1 | /24 | – | 10 | – |
| R-LAUSANNE | Gi0/0.20 | 10.2.20.1 | /24 | – | 20 | – |
| PC-DIR-A | e0 | 10.1.10.11 | /24 | 10.1.10.1 | 10 | SW-GNV e2 |
| PC-EMP-A | e0 | 10.1.20.11 | /24 | 10.1.20.1 | 20 | SW-GNV e3 |
| SRV-FICHIERS | e0 | 10.1.30.10 | /24 | 10.1.30.1 | 30 | SW-GNV e4 |
| SRV-AD-DNS *(prévu)* | – | 10.1.30.11 | /24 | 10.1.30.1 | 30 | – |
| Imprimante *(prévue)* | – | 10.1.30.20 | /24 | 10.1.30.1 | 30 | – |
| PC-DIR-B | e0 | 10.2.10.11 | /24 | 10.2.10.1 | 10 | SW-LSN e2 |
| PC-EMP-B | e0 | 10.2.20.11 | /24 | 10.2.20.1 | 20 | SW-LSN e3 |

### 4.6 Résumé des réseaux par site

| Site | Réseau résumé | Wildcard | Contient |
|---|---|---|---|
| Genève | 10.1.0.0/16 | 0.0.255.255 | 10.1.10.0, 10.1.20.0, 10.1.30.0 |
| Lausanne | 10.2.0.0/16 | 0.0.255.255 | 10.2.10.0, 10.2.20.0 |

---

## 5. Rapport de justification technique

### 5.1 Pourquoi des VLAN ?

| Avantage | Explication |
|---|---|
| **Sécurité** | Les postes de la Direction sont isolés des Employés au niveau 2. Un employé ne voit pas directement le trafic de la Direction. |
| **Performance** | Chaque VLAN est un **domaine de diffusion** séparé : les broadcasts (ARP, etc.) restent dans leur VLAN et ne saturent pas tout le réseau. |
| **Organisation** | Un réseau par service, donc plus facile à gérer, à filtrer et à dépanner. |
| **Évolutivité** | Ajouter un service (ex. Wi-Fi invités) = ajouter un VLAN, sans recâbler. |

Les numéros de VLAN sont **identiques sur les 2 sites** (10 = Direction, 20 = Employés) pour garder une logique commune.

### 5.2 Pourquoi le router-on-a-stick ?

Des VLAN différents ne peuvent pas communiquer sans routeur (vu au Projet 2 : « No gateway found »). Plusieurs solutions existaient :

| Solution | Avantages | Inconvénients | Retenue ? |
|---|---|---|---|
| **Router-on-a-stick** (1 câble trunk + sous-interfaces) | 1 seul port routeur, 1 seul câble, pas de matériel en plus | Tout le trafic inter-VLAN passe par un seul lien | **Oui** |
| Une interface physique du routeur par VLAN | Simple à comprendre | 1 port et 1 câble par VLAN : ne passe pas à l'échelle | Non |
| Switch de niveau 3 | Très performant | Plus cher, et pas disponible dans la maquette | Non |

Pour une PME de 30 postes, le trafic inter-VLAN est faible : le router-on-a-stick est **suffisant et économique**.

### 5.3 Pourquoi cet adressage ?

- **Adresses privées 10.0.0.0/8** : gratuites, non routées sur Internet, avec énormément d'espace disponible.
- **/24 par VLAN** : simple à calculer, grosse marge d'évolution (voir 4.3).
- **Règle 10.[site].[VLAN]** : lisible, et elle permet de **résumer** chaque site en /16, ce qui simplifie le VPN et les futures règles de pare-feu.
- **/30 sur le WAN** : 2 adresses utilisables, exactement ce qu'il faut pour une liaison point à point.

### 5.4 Pourquoi des routes statiques par défaut ?

Chaque routeur de site n'a **qu'une seule sortie** : le FAI. Une **route par défaut statique** (`ip route 0.0.0.0 0.0.0.0 ...`) suffit.

Un protocole de routage dynamique comme OSPF serait **inutilement complexe** pour 2 sites avec une seule sortie chacun. Il deviendrait utile avec plus de sites ou plusieurs liaisons.

### 5.5 Méthode d'interconnexion : VPN IPsec ou ligne dédiée ?

| Critère | VPN IPsec site-à-site | Ligne dédiée (louée) |
|---|---|---|
| **Coût** | Faible : utilise l'abonnement Internet déjà existant | Élevé : abonnement mensuel spécifique chez l'opérateur |
| **Sécurité** | Données **chiffrées** (AES 256) : illisibles même si elles sont interceptées | Réseau privé de l'opérateur, mais données **non chiffrées** par défaut |
| **Débit / qualité** | Dépend de la qualité d'Internet (non garantie) | Débit et délai **garantis** par contrat (SLA) |
| **Mise en place** | Rapide : quelques commandes sur les 2 routeurs | Lente : plusieurs semaines d'installation par l'opérateur |
| **Évolutivité** | Facile : ajouter un site = ajouter un tunnel | Chaque nouveau site demande une nouvelle ligne |
| **Adapté à** | PME, peu de sites, budget limité | Grandes entreprises, besoins critiques (voix, temps réel) |

**Choix retenu : VPN IPsec site-à-site.** Pour une PME de 30 postes dont l'agence accède surtout à des fichiers, le VPN offre le **meilleur rapport sécurité / coût**. La ligne dédiée serait justifiée seulement avec des besoins de débit garanti, ce qui n'est pas le cas ici.

### 5.6 Choix des paramètres de sécurité IPsec

| Paramètre | Valeur | Justification |
|---|---|---|
| Chiffrement | **AES 256** | Standard actuel, très robuste |
| Intégrité | **SHA (HMAC-SHA1)** | Vérifie que les données n'ont pas été modifiées |
| Authentification | **Clé pré-partagée** | Simple à mettre en place pour 2 sites |
| Échange de clés | **Diffie-Hellman groupe 5** (1536 bits) | Crée une clé secrète commune sans l'envoyer sur le réseau |
| Durée de vie phase 1 | **86 400 s (24 h)** | Les clés sont renouvelées chaque jour |
| Mode | **Tunnel** | Le paquet entier est chiffré, y compris les adresses privées |


**Limite assumée :** SHA-1 et le groupe DH 5 sont aujourd'hui considérés comme vieillissants. Ils ont été choisis pour être compatibles avec l'image IOS du c7200 de la maquette. En production, on utiliserait **SHA-256** et le **groupe 14 ou plus** (voir section 16).


### 5.7 Choix du matériel de la maquette

| Élément réel | Simulé par | Pourquoi |
|---|---|---|
| Routeurs de site | Cisco c7200 | Seul routeur Cisco disponible dans GNS3, il gère VLAN, sous-interfaces et IPsec |
| Internet / FAI | Un 3ᵉ c7200 (R-FAI) | Permet de simuler un réseau public qui ne connaît pas les adresses privées |
| Switchs | Ethernet switch GNS3 | Gère les ports access et trunk 802.1Q |
| Postes et serveur | VPCS | Léger, suffisant pour les tests de ping |
| Analyse du trafic | Wireshark | Permet de voir ce qui passe réellement sur les câbles |

---

## 6. Maquette GNS3 : topologie et switchs

### 6.1 Topologie

![Capture d'écran 2026-10-15 205759](https://hackmd.io/_uploads/B1XAaSJnzl.png)


### 6.2 Câblage

| N° | De | Port | Vers | Port | Type de lien |
|---|---|---|---|---|---|
| 1 | R-GENEVE | Gi1/0 | R-FAI | Gi1/0 | WAN |
| 2 | R-FAI | Gi2/0 | R-LAUSANNE | Gi1/0 | WAN |
| 3 | R-GENEVE | Gi0/0 | SW-GNV | e1 | Trunk 802.1Q |
| 4 | SW-GNV | e2 | PC-DIR-A | e0 | Access VLAN 10 |
| 5 | SW-GNV | e3 | PC-EMP-A | e0 | Access VLAN 20 |
| 6 | SW-GNV | e4 | SRV-FICHIERS | e0 | Access VLAN 30 |
| 7 | R-LAUSANNE | Gi0/0 | SW-LSN | e1 | Trunk 802.1Q |
| 8 | SW-LSN | e2 | PC-DIR-B | e0 | Access VLAN 10 |
| 9 | SW-LSN | e3 | PC-EMP-B | e0 | Access VLAN 20 |

### 6.3 Configuration des switchs

Les Ethernet switch de GNS3 se configurent avec la souris : clic droit → **Configure**. Pour chaque port, on choisit le **Port**, le **VLAN** et le **Type**, puis on clique sur **Add**, **Apply** et **OK**.

| Type de port | Explication |
|---|---|
| **access** | Le port appartient à **un seul VLAN**. Utilisé pour les PC : ils ne savent pas qu'ils sont dans un VLAN. |
| **dot1q** (trunk) | Le port transporte **plusieurs VLAN** sur un seul câble. Chaque trame reçoit une **étiquette** (tag 802.1Q) avec son numéro de VLAN. Utilisé vers le routeur. |

**SW-GNV (Genève) :**

| Port | VLAN | Type | Branché vers |
|---|---|---|---|
| 1 | 1 | **dot1q** | R-GENEVE Gi0/0 |
| 2 | 10 | access | PC-DIR-A |
| 3 | 20 | access | PC-EMP-A |
| 4 | 30 | access | SRV-FICHIERS |

![Capture d'écran 2026-10-15 183316](https://hackmd.io/_uploads/rkE-AS12Gx.png)


**SW-LSN (Lausanne) :**

| Port | VLAN | Type | Branché vers |
|---|---|---|---|
| 1 | 1 | **dot1q** | R-LAUSANNE Gi0/0 |
| 2 | 10 | access | PC-DIR-B |
| 3 | 20 | access | PC-EMP-B |

![Capture d'écran 2026-10-15 183310](https://hackmd.io/_uploads/r1OfRSJ2Mx.png)

---

## 7. Configuration de R-FAI (Internet simulé)

```
R-FAI#enable
R-FAI#configure terminal
R-FAI(config)#hostname R-FAI
R-FAI(config)#interface GigabitEthernet1/0
R-FAI(config-if)#description Vers R-GENEVE
R-FAI(config-if)#ip address 203.0.113.2 255.255.255.252
R-FAI(config-if)#no shutdown
R-FAI(config-if)#exit
R-FAI(config)#interface GigabitEthernet2/0
R-FAI(config-if)#description Vers R-LAUSANNE
R-FAI(config-if)#ip address 198.51.100.2 255.255.255.252
R-FAI(config-if)#no shutdown
R-FAI(config-if)#exit
R-FAI(config)#end
R-FAI#write memory
```
![Capture d'écran 2026-10-15 180953](https://hackmd.io/_uploads/SkdOCH1hzl.png)

| Commande | Explication |
|---|---|
| `enable` | Passe en mode privilégié (`R-FAI#`). |
| `configure terminal` | Entre en mode de configuration globale (`R-FAI(config)#`). |
| `hostname R-FAI` | Donne son nom au routeur. |
| `interface GigabitEthernet1/0` | Sélectionne l'interface reliée à R-GENEVE. |
| `description Vers R-GENEVE` | Commentaire qui indique où va l'interface. |
| `ip address 203.0.113.2 255.255.255.252` | Adresse du FAI côté Genève. Le masque **/30** ne donne que 2 adresses utilisables (.1 et .2). |
| `no shutdown` | Allume l'interface. Les messages `%LINK-3-UPDOWN` et `%LINEPROTO-5-UPDOWN ... changed state to up` le confirment. |
| `interface GigabitEthernet2/0` | Sélectionne l'interface reliée à R-LAUSANNE. |
| `ip address 198.51.100.2 255.255.255.252` | Adresse du FAI côté Lausanne, en /30. |
| `end` | Revient en mode privilégié. |
| `write memory` | Sauvegarde la configuration. |

R-FAI n'a **aucune route** vers les réseaux 10.x.x.x. C'est voulu : sur le vrai Internet, les adresses privées ne sont pas routées. C'est ce qui rend le **VPN obligatoire**.



**Vérification :**

```
R-FAI#show ip interface brief
```

Résultat : **Gi1/0 = 203.0.113.2** et **Gi2/0 = 198.51.100.2**, toutes les deux en **up / up** (méthode `manual`).

![Capture d'écran 2026-10-15 181050](https://hackmd.io/_uploads/r15oRHynze.png)


---

## 8. Configuration de R-GENEVE (siège)

### 8.1 Interface WAN et route par défaut

```
R-GENEVE#enable
R-GENEVE#configure terminal
R-GENEVE(config)#hostname R-GENEVE
R-GENEVE(config)#interface GigabitEthernet1/0
R-GENEVE(config-if)#description WAN - Vers FAI
R-GENEVE(config-if)#ip address 203.0.113.1 255.255.255.252
R-GENEVE(config-if)#no shutdown
R-GENEVE(config-if)#exit
R-GENEVE(config)#ip route 0.0.0.0 0.0.0.0 203.0.113.2
```
![Capture d'écran 2026-10-15 181406](https://hackmd.io/_uploads/r1D60HJhzl.png)

| Commande | Explication |
|---|---|
| `hostname R-GENEVE` | Nom du routeur du siège. |
| `interface GigabitEthernet1/0` | Interface WAN, vers le FAI. |
| `description WAN - Vers FAI` | Commentaire sur le rôle de l'interface. |
| `ip address 203.0.113.1 255.255.255.252` | Adresse « publique » du siège sur le réseau du FAI. |
| `no shutdown` | Allume l'interface. |
| `ip route 0.0.0.0 0.0.0.0 203.0.113.2` | **Route par défaut** : tout ce que R-GENEVE ne connaît pas est envoyé au FAI (203.0.113.2). `0.0.0.0 0.0.0.0` veut dire « toutes les destinations ». |



### 8.2 Sous-interfaces VLAN (router-on-a-stick)

```
R-GENEVE(config)#interface GigabitEthernet0/0
R-GENEVE(config-if)#description LAN - Trunk vers SW-GNV
R-GENEVE(config-if)#no ip address
R-GENEVE(config-if)#no shutdown
R-GENEVE(config-if)#exit
R-GENEVE(config)#interface GigabitEthernet0/0.10
R-GENEVE(config-subif)#description VLAN 10 - DIRECTION
R-GENEVE(config-subif)#encapsulation dot1Q 10
R-GENEVE(config-subif)#ip address 10.1.10.1 255.255.255.0
R-GENEVE(config-subif)#exit
R-GENEVE(config)#interface GigabitEthernet0/0.20
R-GENEVE(config-subif)#description VLAN 20 - EMPLOYES
R-GENEVE(config-subif)#encapsulation dot1Q 20
R-GENEVE(config-subif)#ip address 10.1.20.1 255.255.255.0
R-GENEVE(config-subif)#exit
R-GENEVE(config)#interface GigabitEthernet0/0.30
R-GENEVE(config-subif)#description VLAN 30 - SERVEURS
R-GENEVE(config-subif)#encapsulation dot1Q 30
R-GENEVE(config-subif)#ip address 10.1.30.1 255.255.255.0
R-GENEVE(config-subif)#exit
R-GENEVE(config)#end
R-GENEVE#write memory
```
![Capture d'écran 2026-10-15 181911](https://hackmd.io/_uploads/rkdmkIJ2ze.png)

| Commande | Explication |
|---|---|
| `interface GigabitEthernet0/0` | Interface physique reliée au trunk de SW-GNV. |
| `no ip address` | L'interface physique n'a **pas d'IP** : elle sert seulement de « tuyau » pour le trunk. Les IP sont sur les sous-interfaces. |
| `no shutdown` | Allume l'interface physique. Les sous-interfaces s'allument avec elle. |
| `interface GigabitEthernet0/0.10` | Crée une **sous-interface** : une interface virtuelle sur le même câble. Le numéro `.10` est choisi pour correspondre au VLAN 10 (plus lisible). L'invite devient `(config-subif)#`. |
| `description VLAN 10 - DIRECTION` | Commentaire sur le VLAN géré. |
| `encapsulation dot1Q 10` | La sous-interface traite les trames marquées **VLAN 10** (norme 802.1Q). Cette commande doit être tapée **avant** l'adresse IP. |
| `ip address 10.1.10.1 255.255.255.0` | **Passerelle** du VLAN 10 : les PC de la Direction l'utilisent comme *gateway*. |
| Même chose pour `.20` et `.30` | Passerelles des VLAN 20 (10.1.20.1) et 30 (10.1.30.1). |
| `write memory` | Sauvegarde la configuration. |

**Fonctionnement :** PC-DIR-A (VLAN 10) envoie un paquet vers PC-EMP-A (VLAN 20). Le paquet monte par le trunk avec l'étiquette 10, arrive sur Gi0/0.10, est routé vers Gi0/0.20, puis redescend par le même câble avec l'étiquette 20. Un seul câble, plusieurs VLAN : c'est le **router-on-a-stick**.



**Message `Warning: Attempting to overwrite an NVRAM configuration previously written by a different version of the system image`**
La NVRAM contenait une ancienne sauvegarde faite avec une autre version d'IOS. Il suffit de confirmer avec **Entrée** : ce n'est pas une erreur.


### 8.3 Vérifications

```
R-GENEVE#show ip interface brief
R-GENEVE#show ip route
R-GENEVE#ping 203.0.113.2
R-GENEVE#ping 198.51.100.1
```
![Capture d'écran 2026-10-15 181945](https://hackmd.io/_uploads/BJ_K1Ly3Me.png)
![Capture d'écran 2026-10-15 181957](https://hackmd.io/_uploads/BJOK1Ik3Mg.png)
![Capture d'écran 2026-10-15 182052](https://hackmd.io/_uploads/B1DK1I13fl.png)



**`show ip interface brief`** :

| Interface | Adresse | Status / Protocol | Commentaire |
|---|---|---|---|
| Gi0/0 | unassigned | up / up | Normal : pas d'IP sur l'interface physique du trunk |
| Gi0/0.10 | 10.1.10.1 | up / up | Passerelle VLAN 10 |
| Gi0/0.20 | 10.1.20.1 | up / up | Passerelle VLAN 20 |
| Gi0/0.30 | 10.1.30.1 | up / up | Passerelle VLAN 30 |
| Gi1/0 | 203.0.113.1 | up / up | WAN |



**`show ip route`** :

| Ligne | Signification |
|---|---|
| `Gateway of last resort is 203.0.113.2` | La passerelle par défaut est le FAI. |
| `S* 0.0.0.0/0 [1/0] via 203.0.113.2` | Route statique par défaut. **[1/0]** = distance administrative **1** (route statique tapée à la main, très prioritaire). Au Projet 3, elle valait 254 car elle venait du DHCP. |
| `C 10.1.10.0/24 ... Gi0/0.10` (et .20, .30) | Les 3 VLAN sont **directement connectés** via leurs sous-interfaces. |
| `L 10.1.10.1/32` ... | Adresses propres des sous-interfaces. |
| `C 203.0.113.0/30 ... Gi1/0` | Réseau WAN connecté. |



**Pings :**

| Test | Résultat | Explication |
|---|---|---|
| `ping 203.0.113.2` | `!!!!!` 100 % | Le FAI répond : la liaison WAN marche. |
| `ping 198.51.100.1` | `.....` 0 % | **Normal à ce moment-là** : R-LAUSANNE n'était pas encore configuré. |



---

## 9. Configuration de R-LAUSANNE (agence)

C'est la même configuration que R-GENEVE, avec les adresses du site 2 et seulement 2 VLAN.

```
R-LAUSANNE#enable
R-LAUSANNE#configure terminal
R-LAUSANNE(config)#hostname R-LAUSANNE
R-LAUSANNE(config)#interface GigabitEthernet1/0
R-LAUSANNE(config-if)#description WAN - Vers FAI
R-LAUSANNE(config-if)#ip address 198.51.100.1 255.255.255.252
R-LAUSANNE(config-if)#no shutdown
R-LAUSANNE(config-if)#exit
R-LAUSANNE(config)#ip route 0.0.0.0 0.0.0.0 198.51.100.2
R-LAUSANNE(config)#interface GigabitEthernet0/0
R-LAUSANNE(config-if)#description LAN - Trunk vers SW-LSN
R-LAUSANNE(config-if)#no ip address
R-LAUSANNE(config-if)#no shutdown
R-LAUSANNE(config-if)#exit
R-LAUSANNE(config)#interface GigabitEthernet0/0.10
R-LAUSANNE(config-subif)#description VLAN 10 - DIRECTION
R-LAUSANNE(config-subif)#encapsulation dot1Q 10
R-LAUSANNE(config-subif)#ip address 10.2.10.1 255.255.255.0
R-LAUSANNE(config-subif)#exit
R-LAUSANNE(config)#interface GigabitEthernet0/0.20
R-LAUSANNE(config-subif)#description VLAN 20 - EMPLOYES
R-LAUSANNE(config-subif)#encapsulation dot1Q 20
R-LAUSANNE(config-subif)#ip address 10.2.20.1 255.255.255.0
R-LAUSANNE(config-subif)#exit
R-LAUSANNE(config)#end
R-LAUSANNE#write memory
```
![Capture d'écran 2026-10-15 182338](https://hackmd.io/_uploads/BJUGgIJhMl.png)




| Commande | Explication |
|---|---|
| `ip address 198.51.100.1 255.255.255.252` | Adresse « publique » de l'agence sur le réseau du FAI. |
| `ip route 0.0.0.0 0.0.0.0 198.51.100.2` | Route par défaut vers le FAI, côté Lausanne. |
| `interface GigabitEthernet0/0` + `no ip address` | Interface physique du trunk vers SW-LSN, sans IP. |
| `interface GigabitEthernet0/0.10` + `encapsulation dot1Q 10` + `ip address 10.2.10.1 ...` | Passerelle du VLAN 10 (Direction) de Lausanne. |
| `interface GigabitEthernet0/0.20` + `encapsulation dot1Q 20` + `ip address 10.2.20.1 ...` | Passerelle du VLAN 20 (Employés) de Lausanne. |



### 9.1 Vérifications

```
R-LAUSANNE#show ip interface brief
R-LAUSANNE#show ip route
R-LAUSANNE#ping 198.51.100.2
R-LAUSANNE#ping 203.0.113.1
```

**`show ip interface brief`** : Gi0/0 (unassigned), Gi0/0.10 = 10.2.10.1, Gi0/0.20 = 10.2.20.1 et Gi1/0 = 198.51.100.1, toutes en **up / up**.

![Capture d'écran 2026-10-15 182410](https://hackmd.io/_uploads/Sk3Dx8y3zl.png)


**`show ip route`** : `S* 0.0.0.0/0 [1/0] via 198.51.100.2`, les réseaux `C` 10.2.10.0/24 et 10.2.20.0/24, et le WAN 198.51.100.0/30.


![Capture d'écran 2026-10-15 182417](https://hackmd.io/_uploads/r1OKeUy2Gl.png)


**Pings :**

| Test | Résultat | Explication |
|---|---|---|
| `ping 198.51.100.2` | `!!!!!` 100 % | Le FAI répond côté Lausanne. |
| `ping 203.0.113.1` | `!!!!!` 100 % | **R-GENEVE répond** : les 2 routeurs se joignent à travers le FAI. Le FAI connaît les 2 réseaux /30, qui sont directement connectés à lui. |

![Capture d'écran 2026-10-15 182444](https://hackmd.io/_uploads/HyqilUkhfe.png)



---

## 10. Configuration des PC (VPCS)

| Commande | Explication |
|---|---|
| `ip 10.1.10.11/24 10.1.10.1` | Donne au PC son **adresse IP**, son **masque** (/24 = 255.255.255.0) et sa **passerelle** (la sous-interface du routeur pour son VLAN). `Checking for duplicate address...` vérifie que l'adresse n'est pas déjà utilisée. |
| `save` | Sauvegarde la configuration du PC dans `startup.vpc`. |
| `show ip` | Affiche la configuration : nom, IP/masque, passerelle, DNS, adresse MAC. |

Aucun DNS n'est configuré : la maquette n'a pas d'accès Internet et tous les tests se font par adresse IP.

| PC | Commande | VLAN | Capture |
|---|---|---|---|
| PC-DIR-A | `ip 10.1.10.11/24 10.1.10.1` | 10 | 16 |
| PC-EMP-A | `ip 10.1.20.11/24 10.1.20.1` | 20 | 17 |
| SRV-FICHIERS | `ip 10.1.30.10/24 10.1.30.1` | 30 | 18 |
| PC-DIR-B | `ip 10.2.10.11/24 10.2.10.1` | 10 | 19 |
| PC-EMP-B | `ip 10.2.20.11/24 10.2.20.1` | 20 | 20 |

![Capture d'écran 2026-10-15 182656](https://hackmd.io/_uploads/SJdJ-LJhzl.png)
![Capture d'écran 2026-10-15 182635](https://hackmd.io/_uploads/S1uyWUJ3Mx.png)
![Capture d'écran 2026-10-15 182611](https://hackmd.io/_uploads/B1dyZIy2Gg.png)
![Capture d'écran 2026-10-15 182552](https://hackmd.io/_uploads/r1HuJ-LJnGx.png)
![Capture d'écran 2026-10-15 182529](https://hackmd.io/_uploads/Hy_J-8k3Gx.png)





La ligne de confirmation affiche toujours `PC1 : ...`, quel que soit le PC. C'est un affichage interne de VPCS (numéro d'instance) : le vrai nom apparaît bien dans `show ip` (ex. `NAME : PC-EMP-B[1]`).


---

## 11. Tests AVANT le VPN

### 11.1 Routage inter-VLAN au siège (depuis PC-DIR-A)

```
PC-DIR-A> ping 10.1.10.1
PC-DIR-A> ping 10.1.20.11
PC-DIR-A> ping 10.1.30.10
```

| Test | Résultat | TTL | Explication |
|---|---|---|---|
| Passerelle 10.1.10.1 | 1 timeout puis réponses | 255 | Le premier paquet est perdu le temps de la **résolution ARP** (le PC cherche l'adresse MAC de la passerelle). Ensuite, ça marche. TTL 255 = réponse du routeur lui-même. |
| PC-EMP-A 10.1.20.11 (VLAN 20) | Réponses | 63 | **VLAN 10 → VLAN 20 OK** : le paquet a traversé **1 routeur** (TTL 64 − 1 = 63). |
| SRV-FICHIERS 10.1.30.10 (VLAN 30) | Réponse | 63 | **VLAN 10 → VLAN 30 OK** : la Direction accède au serveur. |

La commande `ping.10.1.30.10` a donné `Bad command` à cause d'une faute de frappe (un point à la place de l'espace). Elle a été retapée correctement juste après.


![Capture d'écran 2026-10-15 183442](https://hackmd.io/_uploads/r1jvbIy3zx.png)


### 11.2 Routage inter-VLAN à l'agence (depuis PC-DIR-B)

```
PC-DIR-B> ping 10.2.20.11
```

Résultat : réponse avec **TTL 63** → **VLAN 10 → VLAN 20 OK** à Lausanne aussi.

![Capture d'écran 2026-10-15 183541](https://hackmd.io/_uploads/Hy2cZLJhzx.png)


### 11.3 Communication entre les sites sans VPN

```
PC-DIR-B> ping 10.1.30.10
```

Résultat : **timeout** sur toutes les requêtes.

**Pourquoi ?** Le paquet part de PC-DIR-B, passe par R-LAUSANNE (route par défaut) et arrive à R-FAI. Mais **R-FAI ne connaît pas le réseau 10.1.30.0** (adresse privée), donc il **jette** le paquet. Sans VPN, l'agence ne peut pas joindre le serveur du siège.

![Capture d'écran 2026-10-15 183546](https://hackmd.io/_uploads/BJqA-812zg.png)


---

## 12. Configuration du VPN IPsec site-à-site

### 12.1 Principe : 2 phases

| Phase | Nom | Rôle |
|---|---|---|
| **Phase 1** | ISAKMP / IKE | Les 2 routeurs **s'authentifient** (clé partagée) et créent un **canal sécurisé** pour négocier. |
| **Phase 2** | IPsec | Les routeurs créent le **vrai tunnel** qui chiffre les données des PC. |

### 12.2 Configuration de R-GENEVE

```
R-GENEVE#configure terminal
R-GENEVE(config)#crypto isakmp policy 10
R-GENEVE(config-isakmp)#encryption aes 256
R-GENEVE(config-isakmp)#hash sha
R-GENEVE(config-isakmp)#authentication pre-share
R-GENEVE(config-isakmp)#group 5
R-GENEVE(config-isakmp)#lifetime 86400
R-GENEVE(config-isakmp)#exit
R-GENEVE(config)#crypto isakmp key <REDACTED_PSK> address 198.51.100.1
R-GENEVE(config)#crypto ipsec transform-set TS-VPN esp-aes 256 esp-sha-hmac
R-GENEVE(cfg-crypto-trans)#mode tunnel
R-GENEVE(cfg-crypto-trans)#exit
R-GENEVE(config)#ip access-list extended VPN-TRAFIC
R-GENEVE(config-ext-nacl)#permit ip 10.1.0.0 0.0.255.255 10.2.0.0 0.0.255.255
R-GENEVE(config-ext-nacl)#exit
R-GENEVE(config)#crypto map VPN-MAP 10 ipsec-isakmp
R-GENEVE(config-crypto-map)#set peer 198.51.100.1
R-GENEVE(config-crypto-map)#set transform-set TS-VPN
R-GENEVE(config-crypto-map)#match address VPN-TRAFIC
R-GENEVE(config-crypto-map)#exit
R-GENEVE(config)#interface GigabitEthernet1/0
R-GENEVE(config-if)#crypto map VPN-MAP
R-GENEVE(config-if)#end
R-GENEVE#write memory
```

![Capture d'écran 2026-10-15 183849](https://hackmd.io/_uploads/BkngGLJ3Gl.png)


### 12.3 Configuration de R-LAUSANNE

Mêmes commandes, avec **le peer et l'ACL inversés** :

```
R-LAUSANNE#configure terminal
R-LAUSANNE(config)#crypto isakmp policy 10
R-LAUSANNE(config-isakmp)#encryption aes 256
R-LAUSANNE(config-isakmp)#hash sha
R-LAUSANNE(config-isakmp)#authentication pre-share
R-LAUSANNE(config-isakmp)#group 5
R-LAUSANNE(config-isakmp)#lifetime 86400
R-LAUSANNE(config-isakmp)#exit
R-LAUSANNE(config)#crypto isakmp key <REDACTED_PSK> address 203.0.113.1
R-LAUSANNE(config)#crypto ipsec transform-set TS-VPN esp-aes 256 esp-sha-hmac
R-LAUSANNE(cfg-crypto-trans)#mode tunnel
R-LAUSANNE(cfg-crypto-trans)#exit
R-LAUSANNE(config)#ip access-list extended VPN-TRAFIC
R-LAUSANNE(config-ext-nacl)#permit ip 10.2.0.0 0.0.255.255 10.1.0.0 0.0.255.255
R-LAUSANNE(config-ext-nacl)#exit
R-LAUSANNE(config)#crypto map VPN-MAP 10 ipsec-isakmp
R-LAUSANNE(config-crypto-map)#set peer 203.0.113.1
R-LAUSANNE(config-crypto-map)#set transform-set TS-VPN
R-LAUSANNE(config-crypto-map)#match address VPN-TRAFIC
R-LAUSANNE(config-crypto-map)#exit
R-LAUSANNE(config)#interface GigabitEthernet1/0
R-LAUSANNE(config-if)#crypto map VPN-MAP
R-LAUSANNE(config-if)#end
R-LAUSANNE#write memory
```

![Capture d'écran 2026-10-15 184240](https://hackmd.io/_uploads/HJ6zfIk2zg.png)



Sur la capture 25, la ligne de l'ACL s'affiche `$0.2.0.0 0.0.255.255 10.1.0.0 0.0.255.255`. Le `$` n'est pas une erreur : la ligne était trop longue pour l'écran, donc IOS a **caché le début** (`permit ip 1`). La commande enregistrée est bien `permit ip 10.2.0.0 0.0.255.255 10.1.0.0 0.0.255.255`.

### 12.4 Explication des commandes

**Phase 1 – ISAKMP :**

| Commande | Explication |
|---|---|
| `crypto isakmp policy 10` | Crée la politique de **phase 1**. Le numéro 10 est une priorité : s'il y a plusieurs politiques, la plus petite est essayée en premier. |
| `encryption aes 256` | Algorithme de chiffrement **AES** avec une clé de **256 bits**. |
| `hash sha` | Algorithme de contrôle d'**intégrité SHA** : garantit que les messages n'ont pas été modifiés. |
| `authentication pre-share` | Les routeurs se reconnaissent grâce à une **clé pré-partagée** (un mot de passe commun). |
| `group 5` | **Diffie-Hellman groupe 5** (1536 bits) : les 2 routeurs fabriquent une clé secrète commune **sans jamais l'envoyer** sur le réseau. |
| `lifetime 86400` | Durée de vie de la phase 1 : **86 400 secondes = 24 heures**. Ensuite, les clés sont renégociées. |
| `crypto isakmp key <REDACTED_PSK> address 198.51.100.1` | Définit la **clé partagée** et l'**adresse du routeur d'en face**. La clé doit être **identique** sur les 2 routeurs. |

**Phase 2 – IPsec :**

| Commande | Explication |
|---|---|
| `crypto ipsec transform-set TS-VPN esp-aes 256 esp-sha-hmac` | Crée un « transform-set » nommé TS-VPN : les règles de protection des données. **ESP** (*Encapsulating Security Payload*) est le protocole IPsec qui **chiffre** (esp-aes 256) et **vérifie l'intégrité** (esp-sha-hmac). |
| `mode tunnel` | Le **paquet entier** du PC (avec ses adresses 10.x privées) est chiffré et placé dans un nouveau paquet entre les 2 routeurs. |

**Trafic à protéger :**

| Commande | Explication |
|---|---|
| `ip access-list extended VPN-TRAFIC` | Crée une ACL étendue nommée VPN-TRAFIC. Ici, elle ne **bloque rien** : elle sert à **désigner le trafic à chiffrer**. |
| `permit ip 10.1.0.0 0.0.255.255 10.2.0.0 0.0.255.255` | Tout le trafic IP de **tout le site A** (10.1.0.0/16) vers **tout le site B** (10.2.0.0/16) passe dans le tunnel. Le wildcard `0.0.255.255` veut dire « les 2 premiers octets doivent correspondre ». Grâce au plan d'adressage, **une seule ligne** couvre tous les VLAN. Sur R-LAUSANNE, l'ACL est **inversée** (10.2 → 10.1). |

**Crypto map (assemblage) :**

| Commande | Explication |
|---|---|
| `crypto map VPN-MAP 10 ipsec-isakmp` | Crée la **carte VPN** qui rassemble tous les réglages. `ipsec-isakmp` = les clés sont négociées automatiquement par ISAKMP. |
| `% NOTE: This new crypto map will remain disabled until a peer and a valid access list have been configured.` | **Message normal** : la carte reste inactive tant que le peer et l'ACL ne sont pas définis (ce qui est fait juste après). |
| `set peer 198.51.100.1` | L'**autre extrémité** du tunnel (le routeur d'en face). |
| `set transform-set TS-VPN` | Utilise les paramètres de phase 2 définis plus haut. |
| `match address VPN-TRAFIC` | Chiffre le trafic qui correspond à l'ACL VPN-TRAFIC. |
| `interface GigabitEthernet1/0` + `crypto map VPN-MAP` | **Applique le VPN** sur l'interface WAN (celle qui sort vers le FAI). |
| `%CRYPTO-6-ISAKMP_ON_OFF: ISAKMP is ON` | Confirmation : le service VPN est **activé** sur le routeur. |

---

## 13. Tests APRÈS le VPN

### 13.1 Ping entre les sites

```
PC-DIR-A> ping 10.2.20.11
PC-DIR-B> ping 10.1.30.10
PC-DIR-B> ping 10.1.10.11
```

| Source → Destination | Résultat | TTL | Ce que ça prouve |
|---|---|---|---|
| PC-DIR-A (Genève, VLAN 10) → PC-EMP-B (Lausanne, VLAN 20) | 2 timeouts puis réponses | **62** | Le siège joint l'agence. |
| PC-DIR-B (Lausanne) → SRV-FICHIERS (Genève, VLAN 30) | Réponses | **62** | **L'agence accède au serveur de fichiers** : c'est le besoin principal. |
| PC-DIR-B (Lausanne) → PC-DIR-A (Genève, VLAN 10) | 2 timeouts puis réponses | **62** | Communication entre les Directions des 2 sites. |

**Pourquoi les premiers timeouts ?**
- La toute première fois, les 2 routeurs doivent **négocier le tunnel** (phases 1 et 2) avant de faire passer les données.
- Ensuite, le routeur d'arrivée doit faire une **résolution ARP** pour trouver l'adresse MAC du PC de destination.

Une fois ces étapes faites, tous les pings passent.

**Pourquoi TTL = 62 ?** Le PC envoie avec un TTL de 64. Le paquet traverse **R-LAUSANNE (−1)** et **R-GENEVE (−1)** = **62**. Le routeur **R-FAI n'est pas compté** : il ne voit que le paquet externe chiffré, et c'est le TTL de ce paquet-là qu'il diminue, pas celui du paquet du PC. C'est la preuve que le trafic passe **dans le tunnel**.


![Capture d'écran 2026-10-15 190231](https://hackmd.io/_uploads/rkfYGUk3fl.png)
![Capture d'écran 2026-10-15 190207](https://hackmd.io/_uploads/BkGYfU1hfg.png)




### 13.2 Vérification de la phase 1 : `show crypto isakmp sa`

```
R-GENEVE#show crypto isakmp sa
```
![Capture d'écran 2026-10-15 190252](https://hackmd.io/_uploads/Hklbm81hfx.png)

| Colonne | Valeur | Signification |
|---|---|---|
| dst | 203.0.113.1 | Destination de la négociation (R-GENEVE) |
| src | 198.51.100.1 | Source de la négociation (R-LAUSANNE) : c'est lui qui a **lancé** le tunnel |
| state | **QM_IDLE** | *Quick Mode Idle* : la phase 1 est **terminée** et le tunnel est **prêt** |
| conn-id | 1001 | Numéro interne de la connexion |
| status | **ACTIVE** | La connexion est **active** |



### 13.3 Vérification de la phase 2 : `show crypto ipsec sa`

```
R-GENEVE#show crypto ipsec sa
```
![Capture d'écran 2026-10-15 190304](https://hackmd.io/_uploads/SyxZ7Uk2fe.png)


| Ligne | Valeur sur R-GENEVE | Signification |
|---|---|---|
| `interface` | GigabitEthernet1/0 | Le VPN est bien appliqué sur l'interface WAN |
| `Crypto map tag` | VPN-MAP, local addr 203.0.113.1 | Nom de la crypto map et adresse locale |
| `local ident` | 10.1.0.0/255.255.0.0 | Réseau protégé de **ce côté** (tout le site A) |
| `remote ident` | 10.2.0.0/255.255.0.0 | Réseau protégé **en face** (tout le site B) |
| `current_peer` | 198.51.100.1 port 500 | Le routeur d'en face. Le **port UDP 500** est celui d'ISAKMP/IKE. |
| `#pkts encaps / encrypt / digest` | **16** | 16 paquets **encapsulés, chiffrés et signés** à l'envoi |
| `#pkts decaps / decrypt / verify` | **16** | 16 paquets **reçus, déchiffrés et vérifiés** |
| `#send errors / #recv errors` | **0 / 0** | Aucune erreur |
| `local / remote crypto endpt.` | 203.0.113.1 / 198.51.100.1 | Les 2 extrémités du tunnel |
| `path mtu` | 1500 | Taille maximale des paquets sur le lien |
| `current outbound spi` | 0x53518BCA | Identifiant du tunnel dans le sens **sortant** |
| `inbound esp sas spi` | 0x4F650D74 | Identifiant du tunnel dans le sens **entrant** |
| `PFS (Y/N)` | N | *Perfect Forward Secrecy* non activé (voir les améliorations) |



### 13.4 Vérification sur R-LAUSANNE

```
R-LAUSANNE#show crypto isakmp sa
R-LAUSANNE#show crypto ipsec sa
```

Résultats : **QM_IDLE / ACTIVE**, `local ident` = **10.2.0.0** et `remote ident` = **10.1.0.0** (inversé, c'est normal), **16 paquets encaps / 16 decaps**, 0 erreur.

**Correspondance des SPI entre les 2 routeurs :**

| SPI | Sur R-GENEVE | Sur R-LAUSANNE |
|---|---|---|
| 0x53518BCA | **outbound** (sortant) | **inbound** (entrant) |
| 0x4F650D74 | **inbound** (entrant) | **outbound** (sortant) |

Ce que R-GENEVE envoie avec le SPI 0x53518BCA est reçu par R-LAUSANNE avec ce même SPI, et inversement. Les **2 sens du tunnel** sont donc bien établis et cohérents.

![Capture d'écran 2026-10-15 190338](https://hackmd.io/_uploads/SyGU7Uy2ze.png)

---

## 14. Preuve du chiffrement avec Wireshark

### 14.1 Objectif

Les commandes `show crypto` montrent que les routeurs **disent** chiffrer. **Wireshark** montre ce qui passe **réellement sur les câbles**. On compare 2 endroits :

| Où on capture | Ce qu'on doit voir |
|---|---|
| **Câble WAN** (entre un routeur de site et R-FAI) = « Internet » | Seulement du trafic **chiffré** entre les 2 routeurs, aucune adresse 10.x |
| **Câble trunk** (R-GENEVE ↔ SW-GNV) = intérieur du siège | Les pings **en clair**, avec l'étiquette VLAN |

### 14.2 Méthode

| Étape | Action | Explication |
|---|---|---|
| 1 | Clic droit sur le câble WAN dans GNS3 → **Start capture** | GNS3 lance Wireshark sur ce câble |
| 2 | Sur R-GENEVE et R-LAUSANNE : `clear crypto sa` puis `clear crypto isakmp` | **Supprime le tunnel** (phases 2 et 1) pour pouvoir observer sa **renégociation** |
| 3 | Sur R-LAUSANNE : `show crypto isakmp sa` | Vérifie que le tunnel est bien coupé (liste vide) |
| 4 | Sur PC-DIR-B : `ping 10.1.30.10` | Le trafic « intéressant » **déclenche** la recréation du tunnel |
| 5 | Dans Wireshark, appliquer les filtres `isakmp`, `esp` puis `icmp` | Chaque filtre n'affiche qu'un type de paquet |

![Capture d'écran 2026-10-15 195912](https://hackmd.io/_uploads/BkucX8J2fe.png)


| Filtre Wireshark | Ce qu'il affiche |
|---|---|
| `isakmp` | Les paquets de **négociation** du VPN (port **UDP 500**) |
| `esp` | Les paquets **chiffrés** par IPsec (protocole IP n°**50**) |
| `icmp` | Les **pings** (s'ils sont visibles en clair) |

### 14.3 Filtre `isakmp` : la négociation du tunnel

Résultat : une série de paquets **ISAKMP** échangés entre **198.51.100.1** et **203.0.113.1** :

| Paquets | Mode affiché dans Wireshark | Phase | Rôle |
|---|---|---|---|
| 1 et 2 | Identity Protection (Main Mode) | Phase 1 | Les routeurs s'échangent leurs **propositions** (AES 256, SHA, groupe 5…) et se mettent d'accord |
| 3 et 4 | Identity Protection (Main Mode) | Phase 1 | Échange **Diffie-Hellman** : fabrication de la clé secrète commune |
| 5 et 6 | Identity Protection (Main Mode) | Phase 1 | **Authentification** avec la clé partagée (messages déjà chiffrés) |
| 7, 8, 9 | Quick Mode | Phase 2 | Création du **tunnel IPsec** et des SPI (messages chiffrés) |

Ces paquets n'apparaissent **qu'à la création du tunnel**. C'est pour ça qu'il a fallu couper le tunnel avec `clear crypto` **pendant** la capture.

![Capture d'écran 2026-10-15 201450](https://hackmd.io/_uploads/BJtJE8k3Gx.png)
![Capture d'écran 2026-10-15 201505](https://hackmd.io/_uploads/HJ9b481hzg.png)

### 14.4 Filtre `esp` : les données chiffrées

Résultat (capture sur le câble R-GENEVE ↔ R-FAI) :

| Source | Destination | Protocole | Info |
|---|---|---|---|
| 198.51.100.1 | 203.0.113.1 | ESP | SPI=0x64b3cd9b |
| 203.0.113.1 | 198.51.100.1 | ESP | SPI=0xa02a7fb4 |
| … (alternance aller / retour) | | | |

Analyse :

| Constat | Ce que ça prouve |
|---|---|
| Source et destination = **les 2 routeurs** uniquement | Sur Internet, on ne voit **jamais** les adresses des PC (10.x) : elles sont cachées dans le paquet chiffré |
| Protocole **ESP** | Les données sont protégées par **IPsec** |
| 2 SPI différents | **Un SPI par sens** : 0x64b3cd9b pour Lausanne → Genève, 0xa02a7fb4 pour Genève → Lausanne |
| Paquets qui alternent aller / retour | Ce sont les **pings** (requête → réponse), mais on ne peut pas le lire |
| Détail **Encapsulating Security Payload** : ESP SPI + ESP Sequence | Le numéro de séquence augmente à chaque paquet : il protège contre le **rejeu** (un pirate ne peut pas renvoyer un vieux paquet) |
| Partie hexadécimale illisible | Le contenu est **chiffré en AES 256** |

![Capture d'écran 2026-10-15 201529](https://hackmd.io/_uploads/BJ9ZVUynfe.png)



### 14.5 Filtre `icmp` : aucun ping visible sur Internet

Résultat : **aucun paquet**. La liste est vide.

Pourtant, des pings ont bien circulé entre PC-DIR-B et SRV-FICHIERS. Ils sont **invisibles** sur le câble WAN parce qu'ils voyagent **à l'intérieur des paquets ESP chiffrés**. Un pirate qui écouterait Internet ne verrait pas qu'il s'agit de pings, ni entre quels PC.

![Capture d'écran 2026-10-15 201701](https://hackmd.io/_uploads/Hk4PE8y3fg.png)


### 14.6 Comparaison : capture sur le trunk du siège

On lance une 2ᵉ capture sur le câble **R-GENEVE Gi0/0 ↔ SW-GNV e1**, puis on refait `ping 10.1.30.10` depuis PC-DIR-B. Filtre : `icmp`.

Résultat :

| Constat | Ce que ça prouve |
|---|---|
| Paquets **ICMP Echo (ping) request / reply** visibles | À l'intérieur du site, après déchiffrement par R-GENEVE, le trafic circule **en clair** (normal : c'est le réseau interne) |
| Source **10.2.10.11** (PC-DIR-B) → destination **10.1.30.10** (SRV-FICHIERS) | On retrouve les **vraies adresses des PC** |
| Ligne **802.1Q Virtual LAN, ID: 30** | La trame porte l'**étiquette du VLAN 30** (Serveurs) : preuve que le **trunk** et le **router-on-a-stick** fonctionnent |

![image](https://hackmd.io/_uploads/S1WPI8yhGg.png)


### 14.7 Correspondance des SPI entre Wireshark et le routeur

```
R-GENEVE#show crypto ipsec sa
```

| SPI vu dans Wireshark | Sens | Sur R-GENEVE |
|---|---|---|
| 0xa02a7fb4 (203.0.113.1 → 198.51.100.1) | Genève → Lausanne | **current outbound spi** |
| 0x64b3cd9b (198.51.100.1 → 203.0.113.1) | Lausanne → Genève | **inbound esp sas spi** |

Les SPI vus sur le câble sont **exactement ceux** du tunnel créé par le routeur. Les paquets ESP capturés appartiennent donc bien à **notre VPN**.

Les SPI sont différents de ceux de la section 13 (0x53518BCA / 0x4F650D74) : c'est normal, car le tunnel a été **recréé** avec `clear crypto`. À chaque nouvelle négociation, de **nouveaux SPI et de nouvelles clés** sont générés.

![Capture d'écran 2026-10-16 095438](https://hackmd.io/_uploads/HkFw_UJ3zl.png)
![Capture d'écran 2026-10-16 095308](https://hackmd.io/_uploads/r1KDuI13Gx.png)


### 14.8 Bilan Wireshark

| Câble | Ce qu'on voit | Conclusion |
|---|---|---|
| WAN (Internet) | ISAKMP + ESP entre 198.51.100.1 et 203.0.113.1, **aucun ICMP** | Le trafic entre les sites est **chiffré et confidentiel** |
| Trunk du siège | ICMP en clair, 10.2.10.11 → 10.1.30.10, **VLAN 30** | Le trafic est **déchiffré** à l'entrée du site et remis dans le bon VLAN |

---

## 15. Problèmes rencontrés

### 15.1 Port 1 du switch resté en mode access

**Symptôme :** depuis PC-DIR-A, `ping 10.1.10.1` répond `host (10.1.10.1) not reachable`. Le PC n'arrive même pas à joindre sa passerelle.

![Capture d'écran 2026-10-15 182756](https://hackmd.io/_uploads/r1vF_Uy3Gl.png)

**Diagnostic :** dans la configuration de SW-GNV, le **port 1** (vers R-GENEVE) était en **access VLAN 1** au lieu de **dot1q**.

![Capture d'écran 2026-10-15 183310](https://hackmd.io/_uploads/BJs9O8J2Gx.png)
![Capture d'écran 2026-10-15 182943](https://hackmd.io/_uploads/rkjcdLk2fg.png)


**Cause :** un port access ne laisse passer qu'**un seul VLAN** (ici le VLAN 1) et **sans étiquette**. Les trames du VLAN 10 n'arrivaient donc jamais à la sous-interface Gi0/0.10 du routeur, qui attend des trames **étiquetées 10**. La requête ARP du PC restait sans réponse, d'où le message « not reachable ».

**Solution :**
1. Supprimer le câble R-GENEVE Gi0/0 ↔ SW-GNV e1 (GNS3 ne permet pas toujours de modifier un port qui a un câble branché).
2. Mettre le port 1 en **VLAN 1 / dot1q**, puis **Add**, **Apply** et **OK**.
3. Rebrancher le câble.

Résultat : le ping vers la passerelle fonctionne (capture 21). La même vérification a été faite sur SW-LSN (capture 4).

### 15.2 Capture Wireshark impossible (accents dans le chemin)

**Symptôme :** au lancement de la capture, GNS3 affiche `The capture file path "...\P5 Conception réseau complète pour une PME multi-sites\..." must only contain ASCII (English) characters`.

![Capture d'écran 2026-10-15 195848](https://hackmd.io/_uploads/H1isuUknzl.png)


**Cause :** le dossier du projet contenait des **accents** (« réseau », « complète »). GNS3 n'accepte que des caractères **ASCII** (sans accent) dans le chemin des fichiers de capture. Renommer seulement le fichier `.pcap` ne suffit pas : c'est le **dossier du projet** qui pose problème.

**Solution :**
1. Fermer le projet et GNS3.
2. Renommer le dossier du projet en **`P5-Multisites`** (et le fichier `.gns3` s'il contient aussi des accents).
3. Rouvrir le projet avec **File → Open project**.

### 15.3 Filtre `isakmp` vide dans Wireshark

**Cause :** le tunnel était **déjà établi** avant le début de la capture. Les paquets ISAKMP ne passent **qu'au moment de la négociation**.

**Solution :** pendant que Wireshark capture, couper le tunnel sur les 2 routeurs (`clear crypto sa` et `clear crypto isakmp`), puis relancer un ping pour qu'il se recrée.

### 15.4 Autres points rencontrés

| Situation | Explication | Solution |
|---|---|---|
| `ping 198.51.100.1` depuis R-GENEVE à 0 % | R-LAUSANNE n'était **pas encore configuré** | Normal. Après la config de R-LAUSANNE, le ping inverse fonctionne (capture 15). |
| `Bad command: "ping.10.1.30.10"` | Faute de frappe : un point à la place de l'espace | Commande retapée correctement. |
| Premiers pings en timeout entre les sites | Négociation du tunnel puis résolution ARP | Normal : les pings suivants passent. |
| `$` au début de la ligne ACL de R-LAUSANNE | Ligne trop longue, IOS cache le début | Pas d'impact, la commande est correcte. |
| Avertissement NVRAM au `write memory` | Ancienne sauvegarde faite avec une autre version d'IOS | Confirmer avec Entrée. |

---

## 16. Pistes d'amélioration

| Amélioration | Intérêt | Comment |
|---|---|---|
| **Renforcer le VPN** | Algorithmes plus récents | `hash sha256`, `group 14`, et activer le **PFS** (`set pfs group14` dans la crypto map) |
| **Accès Internet pour les 2 sites** | Les postes peuvent naviguer | NAT overload sur chaque routeur, en **excluant** le trafic VPN de la NAT (`deny ip 10.1.0.0 0.0.255.255 10.2.0.0 0.0.255.255` en tête de l'ACL NAT) |
| **Serveur DHCP** | Plus besoin de configurer les IP à la main | `ip dhcp pool` par VLAN sur les routeurs, en excluant les adresses .1 à .10 |
| **Filtrage inter-VLAN** | Les Employés n'accèdent pas aux postes de la Direction | ACL étendue sur les sous-interfaces (comme au Projet 3) |
| **VLAN de gestion (VLAN 99)** | Administrer les équipements dans un réseau séparé | Nouvelle sous-interface Gi0/0.99 |
| **VLAN invités / Wi-Fi** | Visiteurs isolés du réseau interne | Nouveau VLAN qui n'a accès qu'à Internet |
| **Redondance** | Le site reste connecté si Internet tombe | 2ᵉ accès Internet (ex. 4G) avec route de secours |
| **Sécuriser l'administration** | Éviter le Telnet en clair | SSH, mots de passe `enable secret`, bannière |
| **Sauvegarde des configs** | Restauration rapide en cas de panne | Copie régulière vers un serveur TFTP |

---

## 17. Conclusion

L'architecture conçue pour **Alpes Services** répond à tous les besoins :

1. **Séparation des services** avec des VLAN (Direction, Employés, Serveurs) sur chaque site.
2. **Communication entre les VLAN** d'un même site grâce au **router-on-a-stick** (un trunk 802.1Q et des sous-interfaces).
3. **Plan d'adressage clair et évolutif** (10.[site].[VLAN].0/24), qui résume chaque site en /16.
4. **Interconnexion sécurisée** des 2 sites par un **VPN IPsec site-à-site** (AES 256), choisi plutôt qu'une ligne dédiée pour son **coût réduit** et son **chiffrement**.

La maquette GNS3 le prouve :
- **avant le VPN**, l'agence ne peut pas joindre le siège : le FAI ne route pas les adresses privées ;
- **après le VPN**, tous les sites communiquent : TTL 62, FAI invisible ;
- le tunnel est **actif** (QM_IDLE / ACTIVE), avec **16 paquets chiffrés et 16 déchiffrés** sans aucune erreur, des deux côtés ;
- **Wireshark** confirme que sur Internet, on ne voit que des paquets **ESP chiffrés** entre les 2 routeurs (aucun ping visible), alors qu'à l'intérieur du siège le trafic est en clair et étiqueté dans le bon VLAN.

---

## 18. Annexe – Commandes par équipement

### 18.1 R-FAI

```
enable
configure terminal
hostname R-FAI
interface GigabitEthernet1/0
 description Vers R-GENEVE
 ip address 203.0.113.2 255.255.255.252
 no shutdown
 exit
interface GigabitEthernet2/0
 description Vers R-LAUSANNE
 ip address 198.51.100.2 255.255.255.252
 no shutdown
 exit
end
write memory
```

Vérification :

```
show ip interface brief
```

### 18.2 R-GENEVE

```
enable
configure terminal
hostname R-GENEVE
interface GigabitEthernet1/0
 description WAN - Vers FAI
 ip address 203.0.113.1 255.255.255.252
 no shutdown
 exit
ip route 0.0.0.0 0.0.0.0 203.0.113.2
interface GigabitEthernet0/0
 description LAN - Trunk vers SW-GNV
 no ip address
 no shutdown
 exit
interface GigabitEthernet0/0.10
 description VLAN 10 - DIRECTION
 encapsulation dot1Q 10
 ip address 10.1.10.1 255.255.255.0
 exit
interface GigabitEthernet0/0.20
 description VLAN 20 - EMPLOYES
 encapsulation dot1Q 20
 ip address 10.1.20.1 255.255.255.0
 exit
interface GigabitEthernet0/0.30
 description VLAN 30 - SERVEURS
 encapsulation dot1Q 30
 ip address 10.1.30.1 255.255.255.0
 exit
crypto isakmp policy 10
 encryption aes 256
 hash sha
 authentication pre-share
 group 5
 lifetime 86400
 exit
crypto isakmp key <REDACTED_PSK> address 198.51.100.1
crypto ipsec transform-set TS-VPN esp-aes 256 esp-sha-hmac
 mode tunnel
 exit
ip access-list extended VPN-TRAFIC
 permit ip 10.1.0.0 0.0.255.255 10.2.0.0 0.0.255.255
 exit
crypto map VPN-MAP 10 ipsec-isakmp
 set peer 198.51.100.1
 set transform-set TS-VPN
 match address VPN-TRAFIC
 exit
interface GigabitEthernet1/0
 crypto map VPN-MAP
 end
write memory
```

Vérification et tests :

```
show ip interface brief
show ip route
ping 203.0.113.2
ping 198.51.100.1
show crypto isakmp sa
show crypto ipsec sa
clear crypto sa
clear crypto isakmp
```

### 18.3 R-LAUSANNE

```
enable
configure terminal
hostname R-LAUSANNE
interface GigabitEthernet1/0
 description WAN - Vers FAI
 ip address 198.51.100.1 255.255.255.252
 no shutdown
 exit
ip route 0.0.0.0 0.0.0.0 198.51.100.2
interface GigabitEthernet0/0
 description LAN - Trunk vers SW-LSN
 no ip address
 no shutdown
 exit
interface GigabitEthernet0/0.10
 description VLAN 10 - DIRECTION
 encapsulation dot1Q 10
 ip address 10.2.10.1 255.255.255.0
 exit
interface GigabitEthernet0/0.20
 description VLAN 20 - EMPLOYES
 encapsulation dot1Q 20
 ip address 10.2.20.1 255.255.255.0
 exit
crypto isakmp policy 10
 encryption aes 256
 hash sha
 authentication pre-share
 group 5
 lifetime 86400
 exit
crypto isakmp key <REDACTED_PSK> address 203.0.113.1
crypto ipsec transform-set TS-VPN esp-aes 256 esp-sha-hmac
 mode tunnel
 exit
ip access-list extended VPN-TRAFIC
 permit ip 10.2.0.0 0.0.255.255 10.1.0.0 0.0.255.255
 exit
crypto map VPN-MAP 10 ipsec-isakmp
 set peer 203.0.113.1
 set transform-set TS-VPN
 match address VPN-TRAFIC
 exit
interface GigabitEthernet1/0
 crypto map VPN-MAP
 end
write memory
```

Vérification et tests :

```
show ip interface brief
show ip route
ping 198.51.100.2
ping 203.0.113.1
show crypto isakmp sa
show crypto ipsec sa
clear crypto sa
clear crypto isakmp
```

### 18.4 SW-GNV (pas de ligne de commande, configuration à la souris)

| Port | VLAN | Type |
|---|---|---|
| 1 | 1 | dot1q |
| 2 | 10 | access |
| 3 | 20 | access |
| 4 | 30 | access |

### 18.5 SW-LSN (pas de ligne de commande, configuration à la souris)

| Port | VLAN | Type |
|---|---|---|
| 1 | 1 | dot1q |
| 2 | 10 | access |
| 3 | 20 | access |

### 18.6 PC-DIR-A

```
ip 10.1.10.11/24 10.1.10.1
save
show ip
ping 10.1.10.1
ping 10.1.20.11
ping 10.1.30.10
ping 10.2.20.11
```

### 18.7 PC-EMP-A

```
ip 10.1.20.11/24 10.1.20.1
save
show ip
```

### 18.8 SRV-FICHIERS

```
ip 10.1.30.10/24 10.1.30.1
save
show ip
```

### 18.9 PC-DIR-B

```
ip 10.2.10.11/24 10.2.10.1
save
show ip
ping 10.2.20.11
ping 10.1.30.10
ping 10.1.10.11
```

### 18.10 PC-EMP-B

```
ip 10.2.20.11/24 10.2.20.1
save
show ip
```

### 18.11 Wireshark (filtres utilisés)

| Câble capturé | Filtre | Résultat attendu |
|---|---|---|
| WAN (routeur de site ↔ R-FAI) | `isakmp` | Négociation du tunnel (Main Mode + Quick Mode) |
| WAN (routeur de site ↔ R-FAI) | `esp` | Paquets chiffrés entre 198.51.100.1 et 203.0.113.1 |
| WAN (routeur de site ↔ R-FAI) | `icmp` | Aucun paquet |
| Trunk R-GENEVE ↔ SW-GNV | `icmp` | Pings en clair avec 802.1Q VLAN ID 30 |

---

## 19. Liste des captures

| N° | Section | Contenu | Comment la reconnaître |
|---|---|---|---|
| 1 | 3 | Schéma d'architecture | Image NE-P5-Architecture.png en couleur |
| 2 | 6.1 | Topologie GNS3 | Schéma GNS3 avec les 3 routeurs, 2 switchs, 5 VPCS |
| 3 | 6.3 | Ports de SW-GNV (corrigés) | Port 1 en **dot1q**, ports 2-3-4 en VLAN 10-20-30 |
| 4 | 6.3 | Ports de SW-LSN | Port 1 en **dot1q**, ports 2-3 en VLAN 10-20 |
| 5 | 7 | Configuration de R-FAI | `R-FAI#enable` … Gi1/0 et Gi2/0 |
| 6 | 7 | `show ip interface brief` de R-FAI | Gi1/0 203.0.113.2 et Gi2/0 198.51.100.2 up |
| 7 | 8.1 | WAN + route par défaut de R-GENEVE | `R-GENEVE#enable` … `ip route 0.0.0.0 ...` |
| 8 | 8.2 | Sous-interfaces de R-GENEVE | Gi0/0.10, .20, .30 |
| 9 | 8.3 | `show ip interface brief` de R-GENEVE | Sous-interfaces + Gi1/0 en up |
| 10 | 8.3 | `show ip route` de R-GENEVE | `Gateway of last resort is 203.0.113.2` |
| 11 | 8.3 | Pings depuis R-GENEVE | `!!!!!` vers 203.0.113.2, `.....` vers 198.51.100.1 |
| 12 | 9 | Configuration de R-LAUSANNE | `R-LAUSANNE#enable` … `[OK]` |
| 13 | 9.1 | `show ip interface brief` de R-LAUSANNE | Gi0/0.10, .20 + Gi1/0 en up |
| 14 | 9.1 | `show ip route` de R-LAUSANNE | `Gateway of last resort is 198.51.100.2` |
| 15 | 9.1 | Pings depuis R-LAUSANNE | 2 fois `!!!!!` (100 percent) |
| 16 | 10 | PC-DIR-A | `ip 10.1.10.11/24 ...` + `show ip` |
| 17 | 10 | PC-EMP-A | `ip 10.1.20.11/24 ...` + `show ip` |
| 18 | 10 | SRV-FICHIERS | `ip 10.1.30.10/24 ...` + `show ip` |
| 19 | 10 | PC-DIR-B | `ip 10.2.10.11/24 ...` + `show ip` |
| 20 | 10 | PC-EMP-B | `ip 10.2.20.11/24 ...` + `show ip` |
| 21 | 11.1 | Inter-VLAN depuis PC-DIR-A | ping .10.1, .20.11, .30.10 (avec `Bad command`) |
| 22 | 11.2 | Inter-VLAN depuis PC-DIR-B | `ping 10.2.20.11` → ttl=63 |
| 23 | 11.3 | Ping entre les sites **avant** VPN | `PC-DIR-B> ping 10.1.30.10` → 3 timeouts |
| 24 | 12.2 | Configuration VPN de R-GENEVE | `crypto isakmp policy 10` … `[OK]` |
| 25 | 12.3 | Configuration VPN de R-LAUSANNE | `crypto isakmp policy 10` … ligne `$0.2.0.0` |
| 26 | 13.1 | Ping Genève → Lausanne | `PC-DIR-A> ping 10.2.20.11` → ttl=62 |
| 27 | 13.1 | Ping Lausanne → Genève | `PC-DIR-B> ping 10.1.30.10` + `10.1.10.11` → ttl=62 |
| 28 | 13.2 | `show crypto isakmp sa` R-GENEVE | Petite capture QM_IDLE ACTIVE |
| 29 | 13.3 | `show crypto ipsec sa` R-GENEVE | `local ident 10.1.0.0`, encaps 16 |
| 30 | 13.4 | `show crypto` R-LAUSANNE | isakmp + ipsec, `local ident 10.2.0.0` |
| 31 | 14.3 | Wireshark, filtre `isakmp` | Paquets ISAKMP Main Mode / Quick Mode |
| 32 | 14.4 | Wireshark, filtre `esp` | Lignes ESP avec SPI + détail ESP déplié |
| 33 | 14.5 | Wireshark, filtre `icmp` sur le WAN | Liste **vide** |
| 34 | 14.6 | Wireshark sur le trunk, filtre `icmp` | Pings 10.2.10.11 ↔ 10.1.30.10 + 802.1Q ID 30 |
| 35 | 14.7 | `show crypto ipsec sa` après renégociation | Nouveaux SPI = ceux de Wireshark |
| 36 | 15.1 | Erreur `host not reachable` | `PC-DIR-A> ping 10.1.10.1` → not reachable |
| 37 | 15.1 | SW-GNV avec le port 1 en access | Port 1 en **access** (l'erreur) |
| 38 | 15.2 | Erreur de chemin avec accents | `must only contain ASCII (English) characters` |