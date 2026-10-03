# Roadmap Projet Email - Analyse et triage d'emails de phishing

## Ressources

**Warm-up**
- Module HTB Academy « Phishing Email Analysis (LetsDefend) ».
- Challenge BTLO « The Planet's Prestige » (gratuit, retiré).
- Blog CyberDefenders, aide-mémoire : [How to identify phishing, a SOC analyst's guide](https://cyberdefenders.org/blog/how-to-identify-phishing-a-soc-analysts/).
- Lab CyberDefenders [PhishStrike](https://cyberdefenders.org/blueteam-ctf-challenges/phishstrike/) (Threat Intel, medium : en-têtes, URLHaus, URLScan.io, VirusTotal, MalwareBazaar, VMRay). 
- Lab CyberDefenders « GrabThePhisher » (analyse d'un kit de phishing). Optionnel, à faire avant la phase 3.

**Samples**
- [phishing_pot](https://github.com/rf-peixoto/phishing_pot/) : vrais mails de phishing collectés par honeypots, adresses remplacées par `phishing@pot`. Aucun classement fourni, d'où l'indexation par script (Phase 0).
- Contrôles légitimes : messages de vérification ou notifications issus de services réels connus, exportés en .eml. Exemples : WhatsApp, Gmail, sécurité de compte, notifications d’application. Ils servent à établir la ligne de base du “normal”.
- Volume légitime pour la phase 5 (optionnel) : corpus publics en .eml (Enron, SpamAssassin ham). Anciens, donc probablement sans en-têtes d'authentification modernes, à vérifier.

**Références (lecture, pas des samples)**
- [SOC Simulator, 15 exemples analysés](https://www.socsimulator.com/blog/phishing-email-examples) : exemples fictifs en texte, sans en-têtes ni fichiers. Utile pour la taxonomie et les 7 signaux d'alerte.
- [SOC Simulator, comment analyser un email de phishing](https://www.socsimulator.com/blog/phishing-email-analysis).
- [MITRE ATT&CK T1566](https://attack.mitre.org/techniques/T1566/) (phishing).

**Optionnels, accès à vérifier** : HTB Sherlock « PhishNet », corpus Nazario, malware-traffic-analysis.net, jeu de données IEEE DataPort « Multi-Source Phishing, Spam, Ham Email Dataset » (CSV de caractéristiques, utilisable comme point de comparaison pour l'extraction).

## 1. Objectif

Maîtriser le workflow complet d'analyse d'un email de phishing (détection, analyse, remédiation) : d'abord à la main, ensuite automatisé. Cible : postes de SOC analyst et pentest.

L'analyse d'un email reste l'objectif principal. Le projet la replace dans un environnement réaliste : que devient le mail une fois livré, que se passe-t-il quand l'utilisateur clique, qu'en disent les logs. Le but est de toucher ou d'effleurer la chaîne complète.

Livrables portfolio :
- un dépôt avec le pipeline Python et sa documentation ;
- des rapports d'analyse manuelle (8 analyses, une par catégorie, + 2 à 3 contrôles légitimes) ;
- un scénario « un utilisateur clique » observé dans des logs ;
- des métriques de détection sur un corpus de test.

## 2. Contraintes

- **Disque** : aucune brique à écritures lourdes et continues. Pas de Wazuh, pas de stack Elasticsearch/OpenSearch/Cassandra. Toute brique candidate est mesurée avant adoption (§ 8).
- **Isolation** : les samples contiennent de vrais liens et de vraies pièces jointes. On les ouvre uniquement dans une VM jetable (overlay qcow2), réseau isolé ou sans sortie. Aucun clic hors VM, tout est défangé dans les rapports.
- **Pas de remédiation réelle** : le projet ne touche à aucun système réel. Le rapport recommande les actions (bloquer le domaine, purger le mail, forcer le reset du compte). En lab, on peut simuler l'effet, par exemple ajouter le domaine au résolveur DNS local isolé.
- **Décisions différées** : SIEM, SOAR et IA se tranchent aux points de décision G1 à G3 (§ 7), pas avant.
- **Scope fermé** : tout ce qui n'est pas listé en § 3 passe par le bonus (§ 6, Phase 5).

## 3. Scope

**Dans le scope**
- Parsing .eml : en-têtes, résultats SPF/DKIM/DMARC, chaîne `Received`, corps texte/HTML, pièces jointes.
- Analyse statique des URLs et pièces jointes (pas d'exécution).
- Extraction, défanging et enrichissement des IOC (VirusTotal, URLhaus, AbuseIPDB).
- Rapport automatique. Verdict et score explicables : un plus, pas l'objectif.
- Remédiation recommandée (blocage, purge, notification, reset d'identifiants).
- Scénario de clic en lab isolé et lecture des logs produits.
- Corpus de test, métriques d'extraction, tests de robustesse.

**Hors scope**
- Intégration à une vraie boîte mail (dossier surveillé à la place).
- Blocage sur une infrastructure réelle, tenant M365, firewall.
- Détonation de malware, sandbox maison.
- Classifieur d'apprentissage automatique entraîné.

## 4. Flux logique

```
.eml → ingestion → parsing → extraction IOC → enrichissement → rapport (+ verdict indicatif)
                                                                  ↓
                             Phase 3 : scénario clic → logs → corrélation (outil décidé en G1)
                                                                  ↓
                             Phase 4 : playbooks (G2) et appel d'un modèle IA (G3)
```

## 5. Ordre et livrables

| Phase | Livrable |
|---|---|
| 0. Warm-up | Modules et labs terminés, VM de base prête, samples indexés et sélectionnés |
| 1. Analyse manuelle | 8 rapports + 2 à 3 contrôles légitimes, tableaux d'IOC |
| 2. Pipeline Python | Extraction qui reproduit tes tableaux d'IOC manuels |
| 3. Scénario clic et logs | Tableau événement → log → règle, décision G1 |
| 4. Automatisation et IA | Playbooks, module IA évalué (G2, G3) |
| 5. Tests et finition | Métriques, README, rapport final |

## 6. Phases

### Phase 0 - Warm-up

Comprendre ce qu'est cette analyse, préparer le lab, préparer les samples. Pas de limite de temps.

1. Lecture du blog CyberDefenders comme aide-mémoire : cycle détection → investigation → confinement → remédiation, et corrélation email + endpoint + réseau. Aussi les 15 exemples SOC Simulator pour la taxonomie et les 7 signaux d'alerte.
2. Module HTB Academy « Phishing Email Analysis (LetsDefend) ».
3. Challenge BTLO « The Planet's Prestige » (gratuit, retiré).
4. Labs CyberDefenders : PhishStrike, puis GrabThePhisher (optionnel, avant la phase 3) pour voir ce qu'il y a derrière un lien : le kit de phishing qui héberge la fausse page et collecte les identifiants.
5. Préparer la VM d'analyse (specs, réseau isolé, outils).
6. Indexer et sélectionner les samples (ci-dessous).

**Indexation des samples (script d'auto-étiquetage)**

`phishing_pot` n'a aucun classement. Plutôt que de fouiller à la main, cloner le dépôt et écrire un script Python (module `email`, `policy=default`) qui sort un CSV pré-étiqueté. Ce script est le germe du module `parse` de la phase 2.

Pour chaque .eml : sha256, domaine de `From`, `Reply-To` et `Return-Path`, présence et valeur de `Authentication-Results` / `Received-SPF`, pièces jointes (nom, extension, type MIME déclaré), nombre d'URLs, nombre d'images embarquées, mots-clés BEC.

L’objectif de ce pré-étiquetage est de trier rapidement les candidats et de guider la vérification manuelle, pas de déterminer le verdict final.

| Catégorie | Règle de pré-étiquetage |
|---|---|
| Vol d'identifiants par lien | Lien(s) dans le corps, thème compte/connexion/mot de passe |
| Faux colis ou banque, lien raccourci ou redirection | Domaine de raccourcisseur ou thème livraison/banque dans le corps |
| Spoofing d'expéditeur | `spf/dkim/dmarc=fail` si présents, ou `From` différent de `Reply-To` / `Return-Path` |
| Domaine lookalike (typosquat) | Distance d'édition faible entre le domaine de `From` et une liste de marques |
| Pièce jointe archive | Extension .zip/.rar/.7z ou type MIME correspondant |
| Pièce jointe HTML ou PDF qui mène à un lien | Extension .html/.htm/.pdf sur une pièce jointe |
| BEC | Aucun lien, aucune pièce jointe, mots-clés facture/virement/gift card |
| QR code | Candidats : images embarquées, peu de texte, pas de lien. Décoder le QR (pyzbar ou OpenCV) uniquement sur ces candidats |

Pièges à éviter :
- Le corps d'un mail est presque toujours `text/html`. La règle « pièce jointe HTML » doit porter sur une pièce jointe (disposition `attachment` ou nom de fichier), pas sur le type du corps.
- Une archive est souvent déclarée `application/octet-stream`. Utilise l'extension du nom de fichier en plus du type MIME.
- Un PNG dans un mail est souvent un logo. Sans décodage, la règle QR sort énormément de faux positifs.
- Mesurer d'abord quelle part des samples contient `Authentication-Results`. Si elle est faible, définir « spoofing » surtout par les écarts d'adresses et la chaîne `Received`.

Étiquettes multiples possibles (un mail peut cumuler lookalike et lien, par exemple). Le script prend un tirage avec graine fixe : 3 candidats par catégorie, retenir 1 après vérification manuelle. Noter la raison du choix dans un `SAMPLES.md`. Le pré-étiquetage n'est pas une vérité : vérifier fait partie de l'exercice. Éviter deux samples de la même campagne.

**Sortie** : modules et labs terminés, VM de base prête, CSV d'indexation, 8 samples retenus.

### Phase 1 - Analyse manuelle

Analyse de samples collectés. Rien n'est guidé. Les samples montrent des indicateurs de compromission courants : manipulation d'en-têtes, échecs d'authentification, charges malveillantes.

**Les 8 catégories** (un sample de `phishing_pot` par catégorie, retenu en phase 0)
1. Vol d'identifiants par lien (service de messagerie ou cloud).
2. Faux colis ou banque, lien raccourci ou redirection.
3. Spoofing d'expéditeur : SPF/DKIM/DMARC en échec, Reply-To différent.
4. Domaine lookalike (typosquat).
5. Pièce jointe archive (zip, rar).
6. Pièce jointe HTML ou PDF qui mène à un lien.
7. BEC : facture, virement, carte cadeau, sans lien ni pièce jointe.
8. QR code si tu en trouves, sinon un second cas à lien.

**Contrôles légitimes (2 à 3)** : messages de vérification ou notifications issus de services réels connus, exportés en .eml depuis Gmail ou d’autres plateformes. Anonymiser l’adresse et les codes avant tout dépôt public. Ils servent à apprendre ce que le “normal” donne : authentification valide, domaine cohérent, comportement attendu. Le verdict attendu est « Légitime », même si le contexte semble suspect. Vérifier les en-têtes d’authentification avant de conclure.

Les échantillons légitimes peuvent provenir de ma boîte mail, de notifications d’application ou de services réels connus, à condition d’être vérifiés comme non douteux. L’objet n’est pas de trouver un mail “parfaitement banal”, mais de disposer d’un point de comparaison fiable pour distinguer les signaux d’authentification et les indicateurs de phishing.

Les samples de phishing viennent de [phishing_pot](https://github.com/rf-peixoto/phishing_pot/) : mails réels collectés par honeypots et anonymisés (adresse remplacée par `phishing@pot`).

**Méthode par sample**
1. sha256 du fichier.
2. En-têtes : `From`, `Return-Path`, `Reply-To`, `Message-ID`, chaîne `Received` (de bas en haut), `Authentication-Results`.
3. Corps : ton, urgence, demande faite à la victime.
4. URLs : défange, note le domaine, son âge, les redirections (sans cliquer), la réputation.
5. Pièces jointes : extraction, hash, type réel du fichier, jamais d'ouverture hors VM.
6. Tableau d'IOC.
7. Verdict, niveau de confiance, technique MITRE (T1566.001 pièce jointe, T1566.002 lien).
8. Remédiation recommandée.

Le but n’est pas de démontrer qu’un mail est “de langue anglaise ou française” ou pas, mais de montrer que le verdict se base sur les signaux techniques et le contexte, pas sur une simple impression visuelle.

**Sortie** : documents détaillés de la logique d'analyse et des résultats (transparence totale), au gabarit de l'Annexe A. Les tableaux d'IOC deviennent la vérité terrain de la phase 2.

### Phase 2 - Pipeline Python

Automatiser la partie manuelle de l'analyse. L'objectif principal est l'extraction des données techniques (headers, URLs, pièces jointes, IOC), avec un verdict indicatif en plus. Le cœur du projet reste l'analyse d'email, tandis que les éléments de corrélation et d'automatisation viennent en appui.

| Module | Rôle | Point d'attention |
|---|---|---|
| ingest | Dossier surveillé, un .eml → un dossier de sortie | Polling simple |
| parse | En-têtes, auth, corps, pièces jointes | Encodages exotiques, multipart cassé. Reprend le script de la phase 0 |
| ioc | URLs, domaines, IP, hashes, défanging, dédoublonnage | Filtrer le bruit (pieds de page, trackers légitimes) |
| enrich | VT, URLhaus, AbuseIPDB via `requests` | Cache SQLite, limiteur de débit (quotas gratuits), timeouts, mode hors-ligne |
| report | Markdown/HTML (Jinja2) et JSON | Faits techniques séparés du contexte analyste |
| score (plus) | Règles pondérées visibles, verdict indicatif Légitime / Suspect / Phishing | Chaque point du score est justifié. Pas de seuil unique sur un compteur VT |

Écritures : logs en niveau WARNING par défaut, un seul fichier de cache SQLite.

**Sortie** : sur les samples de la phase 1, les IOC extraits correspondent à tes tableaux manuels (trouvés, manqués, faux IOC mesurés). Aucune entrée cassée ne plante le pipeline. Le verdict indicatif est cohérent avec l'analyse manuelle, chaque verdict est explicable.

### Phase 3 - Scénario « un utilisateur clique »

Voir ce qui se passe dans un SOC quand un utilisateur clique sur ce genre de mail, et comment la corrélation se manifeste dans les logs.

**Lab** : Kali prépare le mail et un site fictif. Une VM victime clique. Un serveur web local inoffensif journalise les accès, plus un résolveur DNS local journalisé. Réseau isolé, jamais exposé à Internet. Page générique, identifiants factices. Ce scénario reste un bonus utile, pas le cœur de la démonstration.

**Événements à provoquer et observer**
1. Email livré (trace de réception).
2. Clic → requête DNS.
3. Requête HTTP vers le domaine.
4. Téléchargement du fichier de test, ou saisie d'identifiants factices sur la page.

**Profondeur**
- Niveau 1 (obligatoire) : événements 1 à 4 vus dans les logs DNS et HTTP.
- Niveau 2 (à effleurer) : après la saisie d'identifiants factices, une connexion « attaquant » simulée depuis une autre adresse sur une source d'authentification de lab (SSH ou petite appli), et une règle qui la détecte. C'est ce qui permet de parler de compromission de compte.

Wireshark ou tcpdump aident à voir DNS et HTTP en clair sur le site. Leur présence est utile pour la preuve, mais la démonstration reste secondaire dans le cadre de ce projet.

**Sortie** : tableau « événement → source de log → champ clé → règle de détection », 2 à 3 règles écrites, corrélation avec les IOC extraits par le pipeline. Puis point de décision G1.
Pour observer les logs sous la contrainte disque : Splunk Free, ou Grafana + Loki, ou corrélation Python sur fichiers.

### Phase 4 - Automatisation et IA

Points de décision G2 et G3 (§ 7), puis implémentation du choix retenu.

**Module IA (si G3 positif)**
- Intégré au pipeline. Entrée : les champs extraits (JSON) et le corps du mail en texte, délimité comme donnée. Pas de pièces jointes.
- Rôle : assistant qui appuie et explique (résumé analyste, explication des signaux, repérage des tactiques d'ingénierie sociale). Il ne décide pas du verdict.
- Le contenu de l'email est une entrée non fiable : une instruction cachée dans le corps peut manipuler le modèle (injection de prompt). Sortie structurée (JSON), aucun outil ni accès donné au modèle, sortie validée.
- La sortie du modèle est aussi non fiable : échappe-la avant de l'insérer dans le rapport HTML et défange tout lien qu'elle contient.
- Confidentialité : n'envoie que des samples publics ou anonymisés.
- Évaluation : compare avec et sans IA sur le corpus ; note coût et latence.

### Phase 5 - Tests, métriques, finition

- **Corpus** : au moins 20 phishing et 10 légitimes, séparés des samples de la phase 1. Prévoir des légitimes piégeux : newsletters, mails avec beaucoup de liens, transferts.
- **Métriques** : d'abord l'extraction (IOC trouvés, manqués, faux IOC, bruit sur les légitimes). Ensuite, en secondaire, le verdict indicatif (précision, rappel, faux positifs, faux négatifs). Temps par email, avec les quotas d'API en tête.
- **Robustesse** : liens morts, archive protégée par mot de passe, grosse pièce jointe, HTML cassé, encodages rares.
- **Finition** : README, schéma du flux, rapport final, démonstration.
- **Bonus (seulement si tout est terminé)** : IMAP, VM Windows.

## 7. Points de décision

| ID | Quand | Question | Critères | Défaut proposé |
|---|---|---|---|---|
| G1 | Fin phase 3 | Quel outil pour lire et corréler les logs ? | Écritures disque mesurées (§ 8), RAM, temps de mise en place, valeur CV | Splunk Free si la mesure est acceptable ; sinon Grafana + Loki ; sinon corrélation Python sur fichiers de logs |
| G2 | Début phase 4 | Orchestration et gestion de cas ? | Même mesure disque, RAM disponible | Playbooks Python et rapports de cas en Markdown |
| G3 | Début phase 4 | Intégrer un modèle d'IA ? API ou local ? | Quota et coût, RAM et disque pour un modèle local, confidentialité | Intégration limitée au rôle décrit en phase 4 |



## 8. Risques

| Risque | Parade |
|---|---|
| Ouvrir un sample dangereux | VM jetable isolée, jamais de clic hors VM |
| Pré-étiquetage faux | Vérification manuelle de chaque sample retenu |
| Peu d'en-têtes d'authentification dans les samples | Mesure en phase 0, définition du spoofing sur les écarts d'adresses |
| Quotas d'API gratuits | Cache SQLite, limiteur de débit, mode hors-ligne |
| Score trop fragile | Verdict présenté comme indicatif, règles pondérées, corpus de légitimes piégeux |
| Scope qui gonfle | Tout ajout passe par le bonus |
| Infra qui dévore le temps | Outils lourds exclus, décisions G1 à G3 sur mesure ; le cœur du projet reste l'analyse d'email |
| Injection de prompt via l'email | Entrée non fiable, sortie structurée validée et échappée, aucun outil donné au modèle |

## 10. Définition de « terminé »

- 8 rapports manuels (un par catégorie) + 2 à 3 contrôles légitimes dans le dépôt.
- Pipeline qui traite un lot d'emails et produit un rapport par mail.
- Scénario clic documenté avec les logs observés et au moins 2 règles.
- Métriques publiées sur un corpus séparé.
- README clair : installation, usage, limites connues.
- Le cœur du projet reste l'analyse d'email ; les éléments plus avancés restent optionnels et ne doivent pas compromettre la faisabilité du projet.

## Gabarit de rapport d'analyse (à fine tuner plus tard)

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
```Annexe A - 