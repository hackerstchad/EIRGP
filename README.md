# EIGRP – Enhanced Interior Gateway Routing Protocol
## Guide complet, avancé et pratique

---

## Table des matières

1. [Introduction](#1-introduction)
2. [Historique et créateurs](#2-historique-et-créateurs)
3. [Définition et description officielle](#3-définition-et-description-officielle)
4. [Positionnement dans la suite TCP/IP](#4-positionnement-dans-la-suite-tcpip)
5. [Caractéristiques fondamentales](#5-caractéristiques-fondamentales)
6. [Comparaison EIGRP vs OSPF vs IS-IS vs RIP](#6-comparaison-eigrp-vs-ospf-vs-is-is-vs-rip)
7. [Algorithmes et mécanismes internes](#7-algorithmes-et-mécanismes-internes)
8. [Messages et paquets EIGRP](#8-messages-et-paquets-eigrp)
9. [Métrique composite EIGRP](#9-métrique-composite-eigrp)
10. [Diffusion et encapsulation](#10-diffusion-et-encapsulation)
11. [Types de réseaux et modes de fonctionnement](#11-types-de-réseaux-et-modes-de-fonctionnement)
12. [Topologies classiques](#12-topologies-classiques)
13. [Configuration EIGRP avancée](#13-configuration-eigrp-avancée)
14. [Commandes de vérification et monitoring](#14-commandes-de-vérification-et-monitoring)
15. [Configuration sur différentes plateformes](#15-configuration-sur-différentes-plateformes)
16. [Outils, simulateurs et émulateurs](#16-outils-simulateurs-et-émulateurs)
17. [Dépannage et troubleshooting](#17-dépannage-et-troubleshooting)
18. [Sécurisation d'EIGRP](#18-sécurisation-deigrp)
19. [Bonnes pratiques](#19-bonnes-pratiques)
20. [Cas d'usage et déploiements réels](#20-cas-dusage-et-déploiements-réels)
21. [RFCs et documentation Cisco](#21-rfcs-et-documentation-cisco)
22. [Ressources, livres et liens](#22-ressources-livres-et-liens)
23. [Glossaire](#23-glossaire)
24. [FAQ](#24-faq)

---

## 1. Introduction

**EIGRP** (Enhanced Interior Gateway Routing Protocol) est un protocole de routage à vecteur de distance avancé développé par Cisco Systems. Contrairement aux protocoles de routage classiques à vecteur de distance tels que RIP, EIGRP intègre des fonctionnalités algorithmiques provenant des protocoles à état de lien. Cette combinaison le place dans la catégorie des protocoles **hybrides** ou **vecteur de distance avancés**.

EIGRP est conçu pour offrir une convergence rapide, une utilisation efficace de la bande passante, une prise en charge de plusieurs protocoles routés (IPv4, IPv6, IPX et anciennement AppleTalk) et une configuration relativement simple. Il est particulièrement populaire dans les réseaux d'entreprise Cisco, les campus, les succursales et les environnements WAN.

Bien qu'il ait longtemps été propriétaire à Cisco, Cisco a publié les informations de base d'EIGRP en 2013 sous la forme d'une **RFC d'information (RFC 7868)**, ce qui a permis à des tiers de mieux comprendre le protocole et de développer des implémentations limitées.

---

## 2. Historique et créateurs

### 2.1 Création et contexte

- **Année de création** : 1992
- **Créateur** : **Cisco Systems**, plus précisément développé par l'équipe de routage de Cisco dans le but d'améliorer le protocole IGRP (Interior Gateway Routing Protocol).
- **Première apparition** : EIGRP a été introduit avec **IOS 9.21** sur les routeurs Cisco.
- **Motivation** : Remplacer IGRP, qui souffrait de limitations importantes : métrique 24 bits, convergence lente, boucles de routage possibles et manque de scalabilité.

### 2.2 Évolution chronologique

| Année | Événement |
|-------|-----------|
| 1987 | Création d'IGRP par Cisco |
| 1992 | Introduction d'EIGRP comme amélioration d'IGRP |
| 1998 | Ajout du support IPv6 (EIGRP for IPv6) |
| 2000+ | Adoption massive dans les entreprises et les campus |
| 2013 | Publication de la RFC 7868 par Cisco |
| 2013+ | Ajout de fonctionnalités avancées : EIGRP Over the Top (OT), wide metrics, authentication SHA-256 |
| 2020+ | Continuation du support dans Cisco IOS XE, IOS XR, NX-OS et les équipements tiers limités |

### 2.3 Créateurs clés

Bien que les noms individuels des ingénieurs ne soient pas toujours publiquement détaillés, EIGRP est une création collective de l'équipe de développement de protocoles de routage de **Cisco Systems**, menée par **Dr. J.J. Garcia-Luna-Aceves** et ses collaborateurs. Le protocole repose sur l'algorithme **DUAL (Diffusing Update Algorithm)** développé par Garcia-Luna-Aceves.

---

## 3. Définition et description officielle

### 3.1 Définition formelle

> EIGRP est un protocole de routage intérieur (IGP) à vecteur de distance avancé, utilisant l'algorithme DUAL pour garantir une topologie sans boucles et une convergence rapide.

### 3.2 Description technique

EIGRP est caractérisé par :

- **Protocole de couche 3.5** : utilise le protocole de transport **RTP (Reliable Transport Protocol)** directement au-dessus d'IP, sans TCP ni UDP.
- **Numéro de protocole IP** : **88**
- **Adresse de multicast** : **224.0.0.10** pour IPv4
- **Adresse de multicast IPv6** : **FF02::A**
- **Métrique composite** : basée sur la bande passante, le délai, la charge, la fiabilité et la MTU
- **Algorithmes** : DUAL pour le calcul sans boucle, PDM (Protocol Dependent Modules) pour la prise en charge multi-protocole

### 3.3 Caractéristiques principales

| Caractéristique | Description |
|-----------------|-------------|
| Type de protocole | Hybride (vecteur de distance avancé) |
| Algorithme | DUAL (Diffusing Update Algorithm) |
| Convergence | Très rapide grâce à la table de topologie et aux successeurs de secours |
| Mise à jour | Incrémentielle, déclenchée par les changements de topologie |
| Évolutivité | Jusqu'à 255 sauts (pratique recommandée : moins de 50) |
| Métrique | Composite (bande passante, délai, charge, fiabilité, MTU) |
| Authentification | MD5 et SHA-256 |
| VLSM/CIDR | Support complet |
| Routage sans classe | Oui (classless) |

---

## 4. Positionnement dans la suite TCP/IP

EIGRP ne repose pas sur TCP ou UDP. Il utilise son propre mécanisme de transport appelé **RTP (Reliable Transport Protocol)**.

```
+----------------------------------+
|        Application               |
+----------------------------------+
|        Transport (RTP)           |  <- Reliable Transport Protocol
+----------------------------------+
|        Réseau (IP)               |  <- Protocole 88
+----------------------------------+
|        Liaison de données        |  <- Ethernet, PPP, HDLC, Frame Relay...
+----------------------------------+
|        Physique                  |
+----------------------------------+
```

### 4.1 Encapsulation EIGRP

| Couche | Champ |
|--------|-------|
| IP Header | Protocol = 88 |
| EIGRP Header | Version, Opcode, Checksum, Flags, Sequence, Acknowledgment |
| EIGRP TLVs | Type/Length/Value contenant les routes, métriques, paramètres |

---

## 5. Caractéristiques fondamentales

### 5.1 Tables EIGRP

Un routeur EIGRP maintient trois tables principales :

#### 5.1.1 Table de voisinage (Neighbor Table)

- Contient la liste des routeurs voisins EIGRP directement connectés.
- Identifiée par l'adresse IP ou l'adresse link-local (IPv6) du voisin.
- Stocke le temps de maintien (Hold Time), le temps de lissage (Smooth Round Trip Time - SRTT), le compteur de retransmission (RTO) et la file d'attente de retransmission.

#### 5.1.2 Table de topologie (Topology Table)

- Contient toutes les destinations apprises par EIGRP.
- Pour chaque destination, stocke le successeur (meilleure route) et les successeurs de secours (feasible successors).
- Stocke les valeurs de distance rapportée (Reported Distance - RD) et de distance calculée (Feasible Distance - FD).

#### 5.1.3 Table de routage (Routing Table)

- Contient uniquement les meilleures routes (successeurs) sélectionnées dans la table de topologie.
- Installe les routes avec la métrique la plus faible.

### 5.2 Concepts clés

| Concept | Description |
|---------|-------------|
| **Successeur (Successor)** | Route voisine utilisée pour atteindre une destination avec la meilleure métrique. |
| **Successeur de secours (Feasible Successor)** | Route de backup précalculée sans boucle. |
| **Feasible Distance (FD)** | Meilleure métrique calculée pour atteindre une destination. |
| **Reported Distance (RD)** | Métrique annoncée par un voisin pour atteindre une destination. |
| **Condition de faisabilité (FC)** | RD d'un voisin < FD actuel pour qu'il soit un successeur de secours. |
| **DUAL** | Algorithme garantissant une convergence sans boucle. |

---

## 6. Comparaison EIGRP vs OSPF vs IS-IS vs RIP

| Critère | EIGRP | OSPF | IS-IS | RIPv2 |
|---------|-------|------|-------|-------|
| Type | Hybride / Vecteur de distance avancé | État de lien | État de lien | Vecteur de distance |
| Algorithme | DUAL | SPF/Dijkstra | SPF/Dijkstra | Bellman-Ford |
| Convergence | Très rapide | Rapide | Très rapide | Lente |
| Métrique | Composite (bande passante, délai...) | Coût basé sur la bande passante | Coût basé sur la bande passante | Nombre de sauts |
| Utilisation CPU/Mémoire | Modérée | Élevée | Élevée | Faible |
| Scalabilité | Très bonne | Très bonne | Excellente | Faible |
| Standard | Propriétaire Cisco (RFC 7868 info) | Standard ouvert (RFC 2328) | Standard ouvert (ISO 10589, RFC 1195) | Standard ouvert |
| IPv6 | Oui (EIGRP for IPv6) | Oui (OSPFv3) | Oui (Multi-Topology) | RIPng |
| Configuration | Simple à modérée | Modérée à complexe | Complexe | Simple |
| Authentification | MD5, SHA-256 | MD5, SHA, IPsec | MD5, HMAC-MD5 | Texte clair, MD5 |

---

## 7. Algorithmes et mécanismes internes

### 7.1 DUAL – Diffusing Update Algorithm

DUAL est l'algorithme central d'EIGRP. Il a été conçu par **J.J. Garcia-Luna-Aceves** pour fournir une convergence rapide sans boucles.

#### 7.1.1 Principes de DUAL

- Chaque routeur connaît la métrique de ses voisins vers une destination (RD).
- Un routeur accepte un voisin comme successeur de secours uniquement si la RD de ce voisin est strictement inférieure à la FD actuelle (condition de faisabilité).
- Cette condition garantit que le voisin n'est pas passé par le routeur local pour atteindre la destination, évitant ainsi les boucles.
- Si le successeur principal tombe en panne et qu'un successeur de secours existe, la route de secours est immédiatement activée sans recalcul.
- Si aucun successeur de secours n'existe, le routeur envoie des requêtes à ses voisins et passe en état **Active**.

#### 7.1.2 États DUAL

| État | Description |
|------|-------------|
| **Passive** | Route stable, aucun recalcul en cours. |
| **Active** | Route en cours de recalcul après perte du successeur sans successeur de secours. |
| **Update** | Mise à jour en cours d'envoi. |
| **Query** | Requête envoyée aux voisins. |
| **Reply** | Réponse reçue des voisins. |

#### 7.1.3 Condition de faisabilité

```
Reported Distance (RD) < Feasible Distance (FD)
```

Si cette condition est respectée, le voisin est un **successeur de secours valide**.

### 7.2 RTP – Reliable Transport Protocol

RTP assure la livraison fiable ou non fiable des paquets EIGRP.

| Type de paquet | Mode RTP |
|----------------|----------|
| Hello | Non fiable (unicast/multicast) |
| Update | Fiable (unicast/multicast) |
| Query | Fiable (unicast/multicast) |
| Reply | Fiable (unicast) |
| Ack | Non fiable (unicast) |

### 7.3 PDM – Protocol Dependent Modules

EIGRP utilise des modules dépendants du protocole pour gérer différentes familles d'adresses :

- **IPv4 PDM**
- **IPv6 PDM**
- Anciennement : IPX PDM et AppleTalk PDM

Chaque PDM gère ses propres tables de voisinage, topologie et routage.

---

## 8. Messages et paquets EIGRP

### 8.1 Types de paquets EIGRP

| Opcode | Type de paquet | Description |
|--------|----------------|-------------|
| 1 | Update | Annonce des routes et métriques |
| 2 | Request | Demande d'informations spécifiques |
| 3 | Query | Demande de recalcul en cas de perte de route |
| 4 | Reply | Réponse à une requête |
| 5 | Hello | Découverte et maintien des voisins |
| 6 | Ack | Accusé de réception |
| 7 | SIA-Query | Requête Stuck-in-Active |
| 8 | SIA-Reply | Réponse Stuck-in-Active |

### 8.2 Format du paquet EIGRP

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|   Version     |    Opcode     |           Checksum            |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                             Flags                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                           Sequence                              |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                         Acknowledgment                          |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           Virtual Router ID     |      Autonomous System Number |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                         TLVs ...                                |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

### 8.3 Types de TLV

| TLV | Description |
|-----|-------------|
| 0x0001 | EIGRP Parameters (K-values, hold time) |
| 0x0102 | IPv4 Internal Routes |
| 0x0103 | IPv4 External Routes |
| 0x0402 | IPv6 Internal Routes |
| 0x0403 | IPv6 External Routes |
| 0x0602 | Wide Metrics (bande passante, délai étendus) |
| 0x0603 | External Routes avec wide metrics |

---

## 9. Métrique composite EIGRP

### 9.1 Formule classique (legacy / narrow metrics)

```
Metric = [K1 * bandwidth + (K2 * bandwidth) / (256 - load) + K3 * delay] * [K5 / (reliability + K4)]
```

Par défaut, K1 = 1, K3 = 1, les autres K = 0.

```
Metric par défaut = (K1 * bandwidth + K3 * delay)
                   = bandwidth + delay
```

- **bande passante** = (10^7 / bande passante minimale en Kbps) * 256
- **délai** = somme des délais en dizaines de microsecondes * 256

### 9.2 Wide Metrics (métriques étendues)

Introduites pour supporter des liaisons à très haut débit (10 Gbps, 40 Gbps, 100 Gbps).

```
Metric = [(K1 * Throughput + (K2 * Throughput) / (256 - Load) + K3 * Latency + K6 * Extended) * K5 / (Reliability + K4)]
```

- **Throughput** : débit calculé sur 64 bits
- **Latency** : latence calculée sur 64 bits
- **Extended** : utilisé pour la consommation d'énergie ou d'autres attributs

### 9.3 Valeurs K par défaut

```
K1 = 1  (bande passante / throughput)
K2 = 0  (charge)
K3 = 1  (délai / latence)
K4 = 0  (fiabilité)
K5 = 0  (fiabilité)
K6 = 0  (attribut étendu)
```

**Important** : Les valeurs K doivent être identiques entre voisins EIGRP, sinon l'adjacence ne se forme pas.

---

## 10. Diffusion et encapsulation

### 10.1 Adresses et ports

| Famille | Adresse de multicast | Protocole IP |
|---------|----------------------|--------------|
| IPv4 | 224.0.0.10 | 88 |
| IPv6 | FF02::A | 88 |

### 10.2 Hello Timer par défaut

| Type de réseau | Hello Timer | Hold Timer |
|----------------|-------------|------------|
| Broadcast (Ethernet) | 5 secondes | 15 secondes |
| Non-broadcast (NBMA) | 60 secondes | 180 secondes |
| Point-to-point | 5 secondes | 15 secondes |

Ces timers peuvent être modifiés avec les commandes `ip hello-interval eigrp` et `ip hold-time eigrp`.

---

## 11. Types de réseaux et modes de fonctionnement

### 11.1 Réseaux broadcast

- Ethernet, FastEthernet, GigabitEthernet
- EIGRP envoie des Hello en multicast 224.0.0.10
- Formation automatique des voisinages

### 11.2 Réseaux point-to-point

- Liaisons série, PPP, HDLC, GRE tunnels
- Pas d'élection de DR/BDR (contrairement à OSPF)
- Envoi de Hello en multicast

### 11.3 Réseaux NBMA (Non-Broadcast Multi-Access)

- Frame Relay, ATM, X.25, DMVPN phase 1/2/3
- Nécessite une configuration spécifique avec des voisins statiques ou l'utilisation du mode multipoint
- EIGRP peut utiliser des voisins unicast avec la commande `neighbor`

### 11.4 EIGRP Over the Top (OT)

Permet d'établir une adjacence EIGRP entre deux routeurs à travers un réseau non-EIGRP (par exemple Internet ou un WAN tiers) en encapsulant les paquets EIGRP dans des tunnels GRE, LISP ou directement sur UDP.

---

## 12. Topologies classiques

### 12.1 Réseau d'entreprise simple

```
[Internet]
    |
[R1] -- EIGRP AS 100 -- [R2] -- [R3]
                        |        |
                       [SW1]    [SW2]
                        |        |
                      [LAN A]  [LAN B]
```

### 12.2 Réseau hub-and-spoke (WAN)

```
        [Hub]
       /  |  \
    [Spoke1] [Spoke2] [Spoke3]
```

EIGRP fonctionne bien dans cette topologie avec le contrôle de la bande passante et le split horizon.

### 12.3 Datacenter / campus à trois niveaux

```
[Core] -- [Core]
   |         |
[Distribution] -- [Distribution]
   |         |
[Access]   [Access]
```

EIGRP est souvent utilisé entre Core et Distribution, avec des routes résumées pour limiter la taille de la table de topologie.

---

## 13. Configuration EIGRP avancée

### 13.1 Configuration de base EIGRP IPv4

```cisco
! Activer EIGRP avec le numéro d'AS 100
R1(config)# router eigrp 100
R1(config-router)# network 192.168.1.0 0.0.0.255
R1(config-router)# network 10.0.0.0 0.0.0.3
R1(config-router)# no auto-summary
R1(config-router)# passive-interface default
R1(config-router)# no passive-interface GigabitEthernet0/0
R1(config-router)# eigrp router-id 1.1.1.1
```

### 13.2 Configuration EIGRP nommé (mode moderne)

```cisco
R1(config)# router eigrp NAMED_MODE
R1(config-router)# address-family ipv4 unicast autonomous-system 100
R1(config-router-af)# af-interface GigabitEthernet0/0
R1(config-router-af-interface)# hello-interval 5
R1(config-router-af-interface)# hold-time 15
R1(config-router-af-interface)# exit
R1(config-router-af)# network 192.168.1.0 0.0.0.255
R1(config-router-af)# eigrp router-id 1.1.1.1
R1(config-router-af)# topology base
R1(config-router-af-topology)# variance 2
R1(config-router-af-topology)# exit
```

### 13.3 Configuration EIGRP pour IPv6

```cisco
R1(config)# ipv6 unicast-routing
R1(config)# router eigrp NAMED_MODE
R1(config-router)# address-family ipv6 autonomous-system 100
R1(config-router-af)# af-interface GigabitEthernet0/0
R1(config-router-af-interface)# no shutdown
R1(config-router-af-interface)# exit
R1(config-router-af)# topology base
R1(config-router-af-topology)# exit
```

### 13.4 Résumé de route manuel

```cisco
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip summary-address eigrp 100 192.168.0.0 255.255.0.0
```

### 13.5 Résumé automatique

```cisco
R1(config)# router eigrp 100
R1(config-router)# auto-summary
```

**Recommandation** : Désactiver l'auto-summary dans les réseaux modernes avec `no auto-summary`.

### 13.6 Load balancing

#### 13.6.1 Equal-cost load balancing

Par défaut, EIGRP effectue du load balancing jusqu'à 4 chemins de coût égal.

```cisco
R1(config-router)# maximum-paths 6
```

#### 13.6.2 Unequal-cost load balancing (variance)

```cisco
R1(config-router)# variance 3
```

La variance permet d'utiliser des chemins dont la métrique est jusqu'à 3 fois supérieure à celle du successeur, à condition qu'ils respectent la condition de faisabilité.

### 13.7 Authentification EIGRP

#### 13.7.1 Authentification MD5

```cisco
R1(config)# key chain MY_KEY_CHAIN
R1(config-keychain)# key 1
R1(config-keychain-key)# key-string MY_SECRET_KEY
R1(config-keychain-key)# accept-lifetime 00:00:00 Jan 1 2024 00:00:00 Jan 1 2025
R1(config-keychain-key)# send-lifetime 00:00:00 Jan 1 2024 00:00:00 Jan 1 2025

R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip authentication mode eigrp 100 md5
R1(config-if)# ip authentication key-chain eigrp 100 MY_KEY_CHAIN
```

#### 13.7.2 Authentification SHA-256 (mode nommé)

```cisco
R1(config)# router eigrp NAMED_MODE
R1(config-router)# address-family ipv4 unicast autonomous-system 100
R1(config-router-af)# af-interface GigabitEthernet0/0
R1(config-router-af-interface)# authentication mode hmac-sha-256
R1(config-router-af-interface)# authentication key-chain MY_KEY_CHAIN
```

### 13.8 Stub routing

```cisco
R1(config)# router eigrp 100
R1(config-router)# eigrp stub connected summary
```

Le stub routing limite les requêtes EIGRP et améliore la stabilité dans les topologies hub-and-spoke.

### 13.9 Redistribution

#### 13.9.1 Redistribution OSPF vers EIGRP

```cisco
R1(config)# router eigrp 100
R1(config-router)# redistribute ospf 1 metric 10000 100 255 1 1500
```

Les paramètres de métrique sont : bande passante (Kbps), délai (dizaines de µs), fiabilité, charge, MTU.

#### 13.9.2 Redistribution statique vers EIGRP

```cisco
R1(config)# router eigrp 100
R1(config-router)# redistribute static metric 100000 10 255 1 1500
```

### 13.10 Contrôle de bande passante sur les interfaces

```cisco
R1(config)# interface Serial0/0/0
R1(config-if)# ip bandwidth-percent eigrp 100 50
```

Cela limite EIGRP à 50% de la bande passante configurée de l'interface.

### 13.11 Modification des timers Hello et Hold

```cisco
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip hello-interval eigrp 100 3
R1(config-if)# ip hold-time eigrp 100 10
```

### 13.12 EIGRP Over the Top (OT)

```cisco
R1(config)# router eigrp NAMED_MODE
R1(config-router)# address-family ipv4 unicast autonomous-system 100
R1(config-router-af)# neighbor 203.0.113.2 GigabitEthernet0/0 remote 10 lisp-encap
```

---

## 14. Commandes de vérification et monitoring

### 14.1 Vérification des voisins

```cisco
R1# show ip eigrp neighbors
R1# show ipv6 eigrp neighbors
```

### 14.2 Vérification de la table de topologie

```cisco
R1# show ip eigrp topology
R1# show ip eigrp topology all-links
R1# show ip eigrp topology 10.0.0.0/24
```

### 14.3 Vérification de la table de routage

```cisco
R1# show ip route eigrp
R1# show ipv6 route eigrp
```

### 14.4 Vérification des interfaces EIGRP

```cisco
R1# show ip eigrp interfaces
R1# show ip eigrp interfaces detail
```

### 14.5 Vérification du trafic EIGRP

```cisco
R1# show ip eigrp traffic
```

### 14.6 Dépannage des événements EIGRP

```cisco
R1# debug eigrp packets
R1# debug eigrp neighbors
R1# debug ip eigrp
R1# debug ip eigrp summary
```

### 14.7 Vérification de la configuration

```cisco
R1# show running-config | section router eigrp
R1# show ip protocols
```

---

## 15. Configuration sur différentes plateformes

### 15.1 Cisco IOS

Cisco IOS utilise la syntaxe classique ou nommée présentée ci-dessus.

### 15.2 Cisco IOS XE

IOS XE supporte EIGRP classique et nommé, avec des améliorations de performances et de scalabilité.

```cisco
router eigrp NAMED_MODE
 address-family ipv4 unicast autonomous-system 100
  network 10.0.0.0/8
  eigrp router-id 1.1.1.1
  topology base
   variance 3
  exit-af-topology
```

### 15.3 Cisco NX-OS

NX-OS supporte EIGRP pour les switchs Catalyst et Nexus.

```cisco
feature eigrp
router eigrp 100
  router-id 1.1.1.1
  default-metric 10000 100 255 1 1500

interface Ethernet1/1
  ip router eigrp 100
  ip eigrp hello-interval 5
```

### 15.4 FRRouting (FRR)

FRRouting est un fork de Quagga supportant EIGRP via le daemon `eigrpd`.

```bash
! /etc/frr/frr.conf
router eigrp 100
 network 192.168.1.0/24
 network 10.0.0.0/30
 eigrp router-id 1.1.1.1
```

Lien : https://frrouting.org/

### 15.5 BIRD

BIRD ne supporte pas nativement EIGRP car c'est un protocole propriétaire Cisco.

### 15.6 GNS3 / EVE-NG / Containerlab

Ces plateformes d'émulation utilisent des images Cisco IOS, IOSv, IOS-XE ou des routeurs virtuels FRRouting pour pratiquer EIGRP.

---

## 16. Outils, simulateurs et émulateurs

| Outil | Type | Support EIGRP | Lien |
|-------|------|---------------|------|
| **Cisco Packet Tracer** | Simulateur | Limité (EIGRP de base) | https://www.netacad.com/courses/packet-tracer |
| **GNS3** | Émulateur | Complet avec images Cisco | https://www.gns3.com |
| **EVE-NG** | Émulateur | Complet | https://www.eve-ng.net |
| **Containerlab** | Émulateur basé conteneurs | Via FRRouting | https://containerlab.dev |
| **FRRouting** | Routeur logiciel open source | EIGRP via eigrpd | https://frrouting.org |
| **Cisco Modeling Labs (CML)** | Émulateur officiel Cisco | Complet | https://developer.cisco.com/modeling-labs/ |
| **VIRL / CML Personal** | Émulateur Cisco | Complet | https://learningnetworkstore.cisco.com |
| **Boson NetSim** | Simulateur | EIGRP supporté | https://www.boson.com |

### 16.1 Recommandations par niveau

| Niveau | Outils recommandés |
|--------|--------------------|
| Débutant | Cisco Packet Tracer, Boson NetSim |
| Intermédiaire | GNS3, EVE-NG Community |
| Avancé | EVE-NG Professional, Cisco CML, Containerlab + FRRouting |

---

## 17. Dépannage et troubleshooting

### 17.1 Problèmes d'adjacence

| Symptôme | Cause probable | Solution |
|----------|---------------|----------|
| Voisin non détecté | AS différent | Vérifier `router eigrp X` |
| Voisin en attente | Valeurs K différentes | Vérifier `metric weights` |
| Voisin qui saute | Timers hello/hold incompatibles | Harmoniser les timers |
| Authentification échoue | Clé MD5/SHA incorrecte | Vérifier key-chain et mode |
| Subnet mismatch | Masque différent | Vérifier les adresses IP |
| Passive interface | Interface en passive | `no passive-interface` |

### 17.2 Routes manquantes

```cisco
R1# show ip eigrp topology all-links
R1# show ip protocols
R1# show ip interface brief
```

### 17.3 Stuck-In-Active (SIA)

L'état SIA se produit quand une requête EIGRP reste sans réponse pendant trop longtemps.

```cisco
R1# show ip eigrp topology active
```

Solutions :
- Utiliser le stub routing
- Limiter la profondeur de la topologie
- Augmenter les timers SIA si nécessaire
- Améliorer la stabilité des liaisons WAN

### 17.4 Commandes de debug avancées

```cisco
R1# debug eigrp packets hello
R1# debug eigrp packets update
R1# debug eigrp packets query
R1# debug eigrp packets reply
R1# debug ip eigrp notifications
R1# debug ip eigrp summary
```

---

## 18. Sécurisation d'EIGRP

### 18.1 Authentification

- Toujours utiliser l'authentification MD5 ou SHA-256 entre voisins.
- Utiliser des key-chains avec rotation des clés.

### 18.2 Passive interface

Désactiver EIGRP sur les interfaces inutiles pour réduire la surface d'attaque.

```cisco
R1(config-router)# passive-interface default
R1(config-router)# no passive-interface GigabitEthernet0/0
```

### 18.3 Stub routing

Limiter les annonces sur les routeurs de périphérie.

### 18.4 ACL et filtrage

Bloquer le protocole IP 88 sur les interfaces exposées à Internet.

---

## 19. Bonnes pratiques

1. **Désactiver l'auto-summary** avec `no auto-summary`.
2. **Configurer un router-id statique** stable.
3. **Utiliser le mode EIGRP nommé** pour les nouveaux déploiements.
4. **Activer l'authentification** MD5 ou SHA-256.
5. **Utiliser le résumé de routes manuel** pour limiter la taille des tables.
6. **Déployer le stub routing** dans les topologies hub-and-spoke.
7. **Ajuster les timers** en fonction des caractéristiques du réseau.
8. **Surveiller les états Active** pour éviter les SIA.
9. **Documenter les numéros d'AS** et les politiques de redistribution.
10. **Limiter le pourcentage de bande passante** sur les liaisons lentes.

---

## 20. Cas d'usage et déploiements réels

### 20.1 Réseaux d'entreprise Cisco

EIGRP est historiquement le protocole IGP par défaut dans les réseaux d'entreprise équipés de routeurs Cisco.

### 20.2 Réseaux de succursales

Les topologies hub-and-spoke avec des routeurs de périphérie stub sont idéales pour EIGRP.

### 20.3 Campus et datacenters

EIGRP peut être utilisé comme protocole de routage interne dans les campus et datacenters Cisco.

### 20.4 Réseaux hybrides cloud

Avec EIGRP Over the Top (OT), il est possible d'étendre EIGRP à travers des réseaux cloud ou Internet.

### 20.5 Opérateurs et FAI

Moins courant que IS-IS ou OSPF chez les opérateurs, EIGRP peut néanmoins être utilisé dans les réseaux d'accès ou de peering limités.

---

## 21. RFCs et documentation Cisco

### 21.1 RFCs

| RFC | Titre |
|-----|-------|
| RFC 7868 | Cisco's Enhanced Interior Gateway Routing Protocol (EIGRP) |

### 21.2 Documentation Cisco officielle

- Cisco EIGRP Configuration Guide : https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/iproute_eigrp/configuration/xe-3s/ire-xe-3s-book.html
- Cisco EIGRP Technology White Paper : https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/enhanced-interior-gateway-routing-protocol-eigrp/whitepaper_c11-720525.html
- EIGRP Design Guide : https://www.cisco.com/c/en/us/support/docs/ip/enhanced-interior-gateway-routing-protocol-eigrp/16406-eigrp-toc.html

---

## 22. Ressources, livres et liens

### 22.1 Livres recommandés

| Titre | Auteur(s) | Description |
|-------|-----------|-------------|
| Routing TCP/IP, Volume I | Jeff Doyle | Chapitres détaillés sur EIGRP |
| CCIE Routing and Switching v5.0 Official Cert Guide | Wendell Odom | Configuration et troubleshooting EIGRP |
| EIGRP for IP: Basic Operation and Configuration | Russ White, Alvaro Retana, Don Slice | Livre de référence sur EIGRP |
| IP Routing on Cisco IOS, IOS XE, and IOS XR | Brad Edgeworth | EIGRP sur différentes plateformes |
| CCDE Study Guide | Marwan Al-shawi | Conception réseau incluant EIGRP |

### 22.2 Liens pratiques

- FRRouting EIGRP : https://docs.frrouting.org/protocol-eigrp.html
- EVE-NG : https://www.eve-ng.net
- GNS3 : https://www.gns3.com
- Containerlab : https://containerlab.dev
- Cisco Learning Network : https://learningnetwork.cisco.com

### 22.3 Vidéos et cours

- CBT Nuggets EIGRP
- INE CCIE Routing & Switching
- NetworkChuck sur YouTube
- Cisco Networking Academy

---

## 23. Glossaire

| Terme | Définition |
|-------|------------|
| **AS (Autonomous System)** | Groupe de réseaux géré par une seule entité administrative. |
| **DUAL** | Diffusing Update Algorithm, algorithme sans boucle d'EIGRP. |
| **FD** | Feasible Distance, meilleure métrique vers une destination. |
| **RD** | Reported Distance, métrique annoncée par un voisin. |
| **Successor** | Meilleur chemin vers une destination. |
| **Feasible Successor** | Chemin de backup valide selon la condition de faisabilité. |
| **SIA** | Stuck-In-Active, état de requête sans réponse. |
| **RTP** | Reliable Transport Protocol, protocole de transport interne. |
| **PDM** | Protocol Dependent Module, module pour IPv4/IPv6. |
| **Variance** | Paramètre de load balancing inégal. |

---

## 24. FAQ

### Q1 : EIGRP est-il un protocole ouvert ?

EIGRP est principalement propriétaire Cisco. Cependant, Cisco a publié la RFC 7868 à titre informatif en 2013. Quelques implémentations tierces limitées existent (FRRouting).

### Q2 : Puis-je utiliser EIGRP dans un réseau multi-fabricants ?

Non, à moins d'utiliser FRRouting ou des implémentations très limitées. Pour le multi-fabricant, OSPF ou IS-IS sont recommandés.

### Q3 : Quelle est la différence entre EIGRP classique et nommé ?

Le mode nommé (EIGRP Named Configuration) regroupe la configuration IPv4 et IPv6 sous un seul processus et offre plus de flexibilité.

### Q4 : Pourquoi mon voisin EIGRP ne se forme-t-il pas ?

Vérifiez l'AS, les valeurs K, les timers, l'authentification, les masques de sous-réseau et les interfaces passives.

### Q5 : Comment EIGRP gère-t-il le load balancing ?

EIGRP effectue du load balancing à coût égal par défaut et peut aussi faire du load balancing à coût inégal avec la commande `variance`.

### Q6 : Quelle est la métrique maximale d'EIGRP ?

Avec les métriques classiques, la métrique maximale est 4 294 967 295 (32 bits). Les wide metrics utilisent une métrique 64 bits.

### Q7 : EIGRP supporte-t-il IPv6 ?

Oui, via EIGRP for IPv6 ou le mode nommé avec address-family ipv6.

### Q8 : Quels outils utiliser pour apprendre EIGRP ?

Cisco Packet Tracer, GNS3, EVE-NG, Cisco CML, Boson NetSim et FRRouting.

---

## Conclusion

EIGRP est un protocole de routage robuste, rapide et relativement simple à configurer, particulièrement adapté aux environnements Cisco. Sa compréhension approfondie est essentielle pour tout ingénieur réseau travaillant sur des infrastructures d'entreprise, des succursales ou des campus. Avec la RFC 7868 et les implémentations comme FRRouting, EIGRP reste pertinent même si OSPF et IS-IS dominent les réseaux multi-fabricants et opérateurs.

---

*Document généré pour un usage éducatif et professionnel. Les commandes et liens sont fournis à titre indicatif ; adaptez-les à votre environnement et vos politiques de sécurité.*
