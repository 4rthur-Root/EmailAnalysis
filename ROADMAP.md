# Roadmap Projet Email - Analyse et triage d'emails de phishing



## 1. Objectif

Maîtriser le workflow complet d'analyse d'un email de phishing (détection, analyse, remédiation) : d'abord à la main, ensuite automatisé. Cible : postes de SOC analyst et pentest.

Livrables portfolio :
- un dépôt avec le pipeline Python et sa documentation ;
- des rapports d'analyse manuelle (11 analyses + 3 contrôles légitimes) ;
- un scénario « un utilisateur clique » observé dans des logs ;
- des métriques de détection sur un corpus de test.

## 2. Contraintes

- **Disque** : aucune brique à écritures lourdes et continues. Pas de Wazuh, pas de stack Elasticsearch/OpenSearch/Cassandra. Toute brique candidate est mesurée avant adoption (§ 8).

- **Isolation** : les samples contiennent de vrais liens et de vraies pièces jointes. On les ouvre uniquement dans une VM jetable (overlay qcow2), réseau isolé ou sans sortie. Aucun clic hors VM, tout est défangé dans les rapports.
- **Pas de remédiation réelle** : actions recommandées dans les rapports, simulées en lab.

- **Décisions différées** : SIEM, SOAR et IA se tranchent aux points de décision G1 à G3 (§ 7), pas avant.

- **Scope fermé** : tout ce qui n'est pas listé en § 3 passe par le bonus (§ 6, Phase 5).

## 3. Scope

**Dans le scope**
- Parsing .eml : en-têtes, résultats SPF/DKIM/DMARC, chaîne `Received`, corps texte/HTML, pièces jointes.
- Analyse statique des URLs et pièces jointes (pas d'exécution).
- Extraction, défanging et enrichissement des IOC (VirusTotal, URLhaus, AbuseIPDB).
- Score pondéré et explicable, rapport automatique.
- Remédiation recommandée (blocage, purge, notification, reset d'identifiants).
- Scénario de clic en lab isolé et lecture des logs produits.
- Corpus de test, métriques FP/FN, tests de robustesse.

**Hors scope**
- Intégration à une vraie boîte mail (dossier surveillé à la place).
- Blocage sur une infrastructure réelle, tenant M365, firewall.
- Détonation de malware, sandbox maison.
- Classifieur d'apprentissage automatique entraîné.

## 10. Définition de « terminé »

-  rapports manuels dans le dépôt.
- Pipeline qui traite un lot d'emails et produit un rapport par mail.
- Scénario clic documenté avec les logs observés et au moins 2 règles.
- Métriques publiées sur un corpus séparé.
- README clair : installation, usage, limites connues.

## 4. Flux logique

```
.eml → ingestion → parsing → extraction IOC → enrichissement → score expliqué → rapport
                                                                  ↓
                             Phase 3 : scénario clic → logs → corrélation (outil décidé en G1)
                                                                  ↓
                             Phase 4 : playbooks (G2) et appel d'un modèle IA (G3)
```

## 5. Calendrier



| Phase | Durée | Livrable |
|---|---|---|
| 0. Warm-up | 1 sem. | Module HTB, challenges, lab prêt |
| 1. Analyse manuelle | 1 sem. | 11 rapports + 3 contrôles + gabarit |
| 2. Pipeline Python | 2 sem. | Pipeline qui reproduit tes verdicts manuels |
| 3. Scénario clic et logs | 2 sem. | Tableau événement → log → règle, décision G1 |
| 4. Automatisation et IA | 1 sem. | Playbooks, module IA évalué (G2, G3) |
| 5. Tests et finition | 1 sem. | Métriques, README, rapport final |

## 6. Phases

### Phase 0 - Warm-up 
Début du projet , comprendre e que c'est que cet analyse et préparer le lab .
1. Lecture du blog CyberDefenders sur l'identification du phishing. Garde-le comme aide-mémoire : cycle détection → investigation → confinement → remédiation, et corrélation email + endpoint + réseau.
2. Module HTB Academy « Phishing Email Analysis (LetsDefend) » .
3. Challenge BTLO « The Planet's Prestige » (gratuit, retiré). 
4. +de challenges comme PhishStrike Lab de cyberdefenders ou pas
5. Préparer la VM d'analyse (specs, réseau isolé, outils).
6. Recherches des samples pour la phase suivante .

**Sortie** : modules de test terminés,VM de base prête, samples OK.

### Phase 1 - Analyse manuelle 

Analyse de samples collectés, ici rien n'est guidé.
Samples that demonstrate common indicators of compromise (IOCs) such as header manipulation, authentication failures, and malicious payloads.
* **Critères de choix**

- Vol d'identifiants par lien (service de messagerie ou cloud)
- Faux colis ou banque, lien raccourci ou redirection 
- Spoofing d'expéditeur : SPF/DKIM/DMARC en échec, Reply-To différent 
- Domaine lookalike (typosquat) 
- Pièce jointe archive (zip, rar) 
- Pièce jointe HTML ou PDF qui mène à un lien 
- BEC : facture, virement, carte cadeau, sans lien ni pièce jointe 
- QR code si tu en trouves, sinon un second cas à lien 


Les samples proviennent du dépôt [phishing_pot](https://github.com/rf-peixoto/phishing_pot/) . Ce sont des mails réels collectés par honeypots et anonymisés (adresse remplacée par `phishing@pot`).

* **Méthode par sample** 
1. sha256 du fichier 
2. En-têtes : `From`, `Return-Path`, `Reply-To`, `Message-ID`, chaîne `Received` (de bas en haut), `Authentication-Results`.
3. Corps : ton, urgence, demande faite à la victime.
4. URLs : défange, note le domaine, son âge, les redirections (sans cliquer), la réputation.
5. Pièces jointes : extraction, hash, type réel du fichier, jamais d'ouverture hors VM.
6. Tableau d'IOC.
7. Verdict, niveau de confiance, technique MITRE (T1566.001 pièce jointe, T1566.002 lien).
8. Remédiation recommandée.

**Sortie** : Documents détaillés de la logique d'analyse, des résultats(transparence totale).

### Phase 2 - Pipeline Python (2 semaines)

Automatiser la partie manuelle de l'analyse.

| Module | Rôle | Point d'attention |
|---|---|---|
| ingest | Dossier surveillé, un .eml → un dossier de sortie | Polling simple |
| parse | En-têtes, auth, corps, pièces jointes | Encodages exotiques, multipart cassé |
| ioc | URLs, domaines, IP, hashes, défanging, dédoublonnage | Filtrer le bruit (pieds de page, trackers légitimes) |
| enrich | VT, URLhaus, AbuseIPDB via `requests` | Cache SQLite, limiteur de débit (quotas gratuits), timeouts, mode hors-ligne |
| score | Règles pondérées visibles | Chaque point du score est justifié dans le rapport |
| report | Markdown/HTML (Jinja2) et JSON | Technique séparée du contexte analyste |

Verdicts : Légitime, Suspect, Phishing. Pas de seuil unique sur un compteur VT.

Écritures : logs en niveau WARNING par défaut, un seul fichier de cache SQLite.

**Sortie** : les samples de la phase 1 donnent un verdict cohérent avec l' analyse manuelle, chaque verdict est explicable, aucune entrée cassée ne plante le pipeline.

### Phase 3 - Scénario « un utilisateur clique » 

Voir ce qui se passe dans un soc lorsque un utilisateur clique sur ce genre de mails , corrélation avec comportements ( compromission compte ou logs ?)

**Lab** : Kali crée un mail, site fictif, utilisateur clique.VM et un serveur web local inoffensif qui journalise les accès, plus un résolveur DNS local journalisé. Réseau isolé.

**Événements à provoquer et observer**
1. Email livré (trace de réception).
2. Clic → requête DNS.
3. Requête HTTP vers le domaine.
4. Téléchargement du fichier ou entrée login (wireshark aide ?).

**Sortie** : tableau « événement → source de log → champ clé → règle de détection », 2 à 3 règles écrites, corrélation avec les IOC extraits par le pipeline. Puis **point de décision**.
Pour observer les logs en raison de contraintes de disque voir entre splunk, ou simple grafana ou autre ...

### Phase 4 - Automatisation et IA (1 semaine)

Points de décision G2 et G3 (§ 7), puis implémentation du choix retenu.

**Module IA (si G3 positif)**
---- 
- Rôle : résumé analyste, explication du score, repérage des tactiques d'ingénierie sociale. Il ne décide pas du verdict.
- Le contenu de l'email est une entrée non fiable : une instruction cachée dans le corps peut manipuler le modèle (injection de prompt). Passe le mail comme donnée, exige une sortie structurée (JSON), ne donne au modèle aucun outil ni accès, valide sa sortie.
- Confidentialité : n'envoie que des samples publics ou anonymisés.
- Évaluation : compare verdict score seul, IA seule, combiné sur le corpus ; note coût et latence.
----

### Phase 5 - Tests, métriques, finition 

- **Corpus** : au moins 20 phishing et 10 légitimes, séparés des samples de la phase 1. Prévoir des légitimes piégeux : newsletters, mails avec beaucoup de liens, transferts.
- **Métriques** : précision, rappel, faux positifs, faux négatifs, temps par email, avec les quotas d'API en tête.
- **Robustesse** : liens morts, archive protégée par mot de passe, grosse pièce jointe, HTML cassé, encodages rares.
- **Finition** : README, schéma du flux, rapport final, démonstration.
- **Bonus (seulement si tout est terminé)** : IMAP, VM Windows, analyse du kit derrière un lien (par exemple le lab CyberDefenders « GrabThePhisher »).

## 7. Points de décision

| ID | Quand | Question | Critères | Défaut proposé |
|---|---|---|---|---|
| G1 | Fin phase 3 | Quel outil pour lire et corréler les logs ? | Écritures disque mesurées (§ 8), RAM, temps de mise en place, valeur CV | Splunk Free si la mesure est acceptable, grafana  sinon corrélation Python sur fichiers de logs |
| G2 | Début phase 4 | Orchestration et gestion de cas ? | Même mesure disque, RAM disponible | Playbooks Python et rapports de cas en Markdown |
| G3 | Début phase 4 | Intégrer un modèle d'IA ? API ou local ? | Quota et coût, RAM et disque pour un modèle local, confidentialité | Intégration limitée au rôle décrit en phase 4 |


## 8. Mesurer les écritures disque

1. Relevé de référence : 1 h VM allumée au repos (`iostat -d` ou `iotop -o`).
2. Relevé en charge : 1 h avec le scénario de la phase 3.
3. Noter les Mo écrits dans les deux cas et fixer ton seuil d'acceptation avant de décider.
4. VM de lab sur overlay jetable ; la détruire après chaque session.

## 9. Risques

| Risque | Parade |
|---|---|
| Ouvrir un sample dangereux | VM jetable isolée, jamais de clic hors VM |
| Quotas d'API gratuits | Cache SQLite, limiteur de débit, mode hors-ligne |
| Score trop fragile | Règles pondérées, corpus de légitimes piégeux |
| Scope qui gonfle | Tout ajout passe par le bonus |
| Infra qui dévore le temps | Outils lourds exclus, décisions G1 à G3 sur mesure |
| Injection de prompt via l'email | Entrée non fiable, sortie structurée validée, aucun outil donné au modèle |



## Annexe A - Gabarit de rapport d'analyse

```
Sample : <id>        Source : <...>        sha256 : <...>
Verdict : <Légitime | Suspect | Phishing>   Confiance : <faible | moyenne | élevée>
MITRE : <T1566.00x>

Faits techniques
- Expéditeur : <From / Return-Path / Reply-To>
- Authentification : <SPF / DKIM / DMARC>
- Chaîne Received : <résumé>
- URLs : <défangées>   Pièces jointes : <nom, type réel, hash>

IOC
| Type | Valeur (défangée) | Réputation |

Contexte analyste (séparé des faits)
- Pourquoi ce verdict, ce qui m'a fait hésiter.

Remédiation recommandée
- <bloquer, purger, notifier, reset>
```

## Annexe B - Sources

- Dépôt de samples : `rf-peixoto/phishing_pot` (GitHub).
- Warm-up : module HTB Academy « Phishing Email Analysis (LetsDefend) », challenge BTLO « The Planet's Prestige ».
- Lecture : blog CyberDefenders « How Email Data Helps Identify Phishing ».
- Optionnels, accès à vérifier : HTB Sherlock « PhishNet », corpus Nazario, malware-traffic-analysis.net.
