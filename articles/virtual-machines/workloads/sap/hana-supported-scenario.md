---
title: Scénarios pris en charge pour SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Découvrez les scénarios pris en charge pour SAP HANA sur Azure (grandes instances) ainsi que les détails architecturaux correspondants.
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/19/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9ebd38cbff7d28515a7f60554a2ef755bdf6d04a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114467755"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Scénario pris en charge pour des grandes instances HANA
Cet article décrit les scénarios pris en charge pour SAP HANA sur Azure (grandes instances) ainsi que les détails architecturaux correspondants.

>[!NOTE]
>Si votre scénario n’est pas mentionné dans cet article, contactez l’équipe de management des services Microsoft pour évaluer vos besoins.
Avant de configurer l’unité HLI, validez la conception avec SAP ou avec votre partenaire d’implémentation du service.

## <a name="terms-and-definitions"></a>Termes et définitions
Découvrons les termes et définitions utilisés dans cet article :

- **SID** : identificateur système du système HANA
- **HLI** : HANA grandes instances.
- **DR** : récupération d'urgence.
- **DR normale** : configuration système utilisant des ressources dédiées à des fins de récupération d'urgence uniquement.
- **DR polyvalente** : système de site de récupération d'urgence configuré pour utiliser un environnement de non-production avec une instance de production configurée pour un événement de récupération d'urgence 
- **SID unique** : système sur lequel une seule instance est installée.
- **Multi-SID** : système sur lequel plusieurs instances sont configurées ; également appelé environnement MCOS.
- **HSR** : réplication du système SAP HANA.

## <a name="overview"></a>Vue d’ensemble
Les grandes instances HANA prennent en charge diverses architectures pour vous aider à répondre aux besoins de votre entreprise. Les sections suivantes couvrent les scénarios architecturaux et les détails de leur configuration. 

Les conceptions architecturales dérivées sont purement du point de vue de l'infrastructure. Consultez SAP ou vos partenaires d'implémentation pour le déploiement de HANA. Si vos scénarios ne sont pas répertoriés dans cet article, contactez l'équipe des comptes Microsoft pour examiner l'architecture et trouver une solution qui vous convienne.

> [!NOTE]
> Ces architectures sont entièrement conformes à la conception Tailored Data Integration (TDI) et sont prises en charge par SAP.

Cet article détaille les deux composants dans chaque architecture prise en charge :

- Ethernet
- Stockage

### <a name="ethernet"></a>Ethernet

Chaque serveur approvisionné est préconfiguré avec des jeux d’interfaces Ethernet. Les interfaces Ethernet configurées sur chaque unité HLI sont classées en quatre types :

- **R** : Utilisé pour ou par l’accès client.
- **B** : Utilisé pour la communication nœud à nœud. Cette interface est configurée sur tous les serveurs, quelle que soit la topologie que vous demandez. Toutefois, elle est uniquement utilisée pour les scénarios de scale-out.
- **C** : Utilisé pour la connectivité de nœud à stockage.
- **D** : Utilisé pour la connexion nœud à appareil ISCSI pour l’installation de STONITH. Cette interface est configurée uniquement lorsque une configuration HSR est demandée.  

| Interface logique de la carte réseau | Type de référence SKU | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| Un | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Nœud à nœud|
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | STONITH |
| Un | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Nœud à nœud|
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH |

Vous choisissez l’interface en fonction de la topologie configurée sur l’unité HLI. Par exemple, l’interface « B » est configurée pour la communication nœud à nœud, ce qui est utile lorsque vous avez une topologie Scale-out configurée. Cette interface n'est pas utilisée pour les configurations de scale-up sur un nœud unique. Pour obtenir plus d’informations sur l’utilisation de l’interface, étudiez les scénarios dont vous avez besoin (plus loin dans ce document). 

Le cas échéant, vous pouvez définir vous-même d'autres cartes réseau. Toutefois, les configurations des cartes réseau existantes *ne peuvent pas* être modifiées.

>[!NOTE]
>Vous pouvez trouver des interfaces supplémentaires qui sont des interfaces ou des liaisons physiques. Considérez uniquement les interfaces mentionnées précédemment correspondant à votre cas d’usage. Ignorez les autres.

Pour les unités auxquelles deux adresses IP ont été attribuées, distribution doit se présenter comme suit :

- L’adresse IP attribuée à « Ethernet A » doit se trouver dans la plage d’adresses du pool d’adresses IP du serveur que vous avez envoyée à Microsoft. Cette adresse IP doit être conservée dans le répertoire */etc/hosts* du système d'exploitation.

- L’adresse IP attribuée à « Ethernet C » doit être utilisée pour la communication à NFS. Cette adresse n’a *pas* besoin d’être conservée dans le répertoire *etc/hosts* pour autoriser un trafic interinstance au sein de l’abonné.

Une configuration à deux adresses IP ne convient pas aux déploiements Scale-out HANA ni aux réplications de système HANA. Si vous n’avez que deux adresses IP attribuées et que vous souhaitez déployer une telle configuration, contactez SAP HANA sur le management des services Azure. SAP HANA peut vous attribuer une troisième adresse IP dans un troisième VLAN. Pour les grandes instances HANA avec trois adresses IP attribuées à trois ports de carte réseau, les règles d’utilisation suivantes s’appliquent :

- L’adresse IP attribuée à « Ethernet A » doit se trouver hors de la plage d’adresses du pool d’adresses IP du serveur que vous avez envoyée à Microsoft. Cette adresse IP ne doit pas être conservée dans le répertoire *etc/hosts* du système d'exploitation.

- « Ethernet B » doit être conservé exclusivement dans le répertoire *etc/hosts* pour la communication entre les différentes instances. Conservez ces adresses IP dans des configurations HANA de scale-out comme adresses IP utilisées par HANA pour la configuration inter-nœuds.

- L’adresse IP attribuée à « Ethernet C » doit être utilisée pour la communication à NFS. Ce type d'adresse ne doit pas être conservé dans le répertoire *etc/hosts*.

- « Ethernet D » doit être utilisé exclusivement pour l’accès aux appareils STONITH pour stimulateurs cardiaques. Cette interface est nécessaire lorsque vous configurez une réplication de système HANA et que vous souhaitez parvenir au basculement automatique du système d'exploitation à l'aide d'un appareil SBD.


### <a name="storage"></a>Stockage
Le stockage est préconfiguré en fonction de la topologie demandée. Les tailles de volume et les points de montage varient en fonction du nombre de serveurs et de références SKU ainsi que de la topologie configurée. Pour plus d’informations, étudiez les scénarios dont vous avez besoin (plus loin dans cet article). Si vous avez besoin de davantage de stockage, vous pouvez l’acheter par incréments de 1 To.

>[!NOTE]
>Le point de montage /usr/sap/\<SID> est un lien symbolique vers le point de montage /hana/shared.


## <a name="supported-scenarios"></a>Scénarios pris en charge

Les diagrammes d'architecture des sections suivantes utilisent les notations ci-dessous :

[ ![Tableau des diagrammes d’architecture](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

Voici les scénarios pris en charge :

* Nœud unique avec un SID
* Nœud unique avec MCOS
* Nœud unique avec récupération d’urgence (normale)
* Nœud unique avec récupération d’urgence (polyvalente)
* HSR avec STONITH
* HSR avec récupération d’urgence (normale/polyvalente) 
* Basculement automatique avec hôte (1+1) 
* Scale-out avec nœud de secours
* Scale-out sans nœud de secours
* Scale-out avec reprise d’activité après sinistre

## <a name="single-node-with-one-sid"></a>Nœud unique avec un SID

Cette topologie prend en charge un seul nœud dans une configuration de montée en charge avec un SID.

### <a name="architecture-diagram"></a>Diagramme de l'architecture  

![Nœud unique avec un SID](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| Interface logique de la carte réseau | Type de référence SKU | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| Un | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Configuré mais non utilisé |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| Un | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Configuré mais non utilisé |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|/Hana/Shared/SID | Installation de HANA | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux | 
|/hana/logbackups/SID | Journaux d’activité de rétablissement |

### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.

## <a name="single-node-mcos"></a>Nœud unique avec MCOS

Cette topologie prend en charge un seul nœud dans une configuration de montée en charge avec plusieurs SID.

### <a name="architecture-diagram"></a>Diagramme de l'architecture  

![Nœud unique avec MCOS](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| Interface logique de la carte réseau | Type de référence SKU | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| Un | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Configuré mais non utilisé |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| Un | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Configuré mais non utilisé |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|/hana/shared/SID1 | Installation de HANA pour SID1 | 
|/hana/data/SID1/mnt00001 | Installation des fichiers de données pour SID1 | 
|/hana/log/SID1/mnt00001 | Installation des fichiers journaux pour SID1 | 
|/hana/logbackups/SID1 | Journaux d’activité de rétablissement pour SID1 |
|/hana/shared/SID2 | Installation de HANA pour SID2 | 
|/hana/data/SID2/mnt00001 | Installation des fichiers de données pour SID2 | 
|/hana/log/SID2/mnt00001 | Installation des fichiers journaux pour SID2 | 
|/hana/logbackups/SID2 | Journaux d’activité de rétablissement pour SID2 |

### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- La répartition de la taille des volumes est basée sur la taille de la base de données en mémoire. Pour découvrir quelles tailles de base de données en mémoire sont prises en charge dans un environnement multi-SID, consultez la section [Vue d’ensemble et architecture](./hana-overview-architecture.md).

## <a name="single-node-with-dr-using-storage-replication"></a>Nœud unique avec reprise d’activité à l’aide de la réplication de stockage
 
Cette topologie prend en charge un seul nœud dans une configuration de scale-out avec plusieurs SID. La réplication basée sur stockage vers le site de récupération d’urgence est utilisée pour un SID principal. Dans le diagramme, seul un système à SID unique est représenté sur le site principal, mais les systèmes MCOS sont également pris en charge.

### <a name="architecture-diagram"></a>Diagramme de l'architecture  

![Nœud unique avec reprise d’activité à l’aide de la réplication de stockage](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| Interface logique de la carte réseau | Type de référence SKU | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| Un | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Configuré mais non utilisé |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| Un | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Configuré mais non utilisé |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|/Hana/Shared/SID | Installation de HANA pour SID | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID | 
|/hana/logbackups/SID | Journaux d’activité de rétablissement pour SID |


### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- Pour MCOS : La répartition de la taille des volumes est basée sur la taille de la base de données en mémoire. Pour découvrir quelles tailles de base de données en mémoire sont prises en charge dans un environnement multi-SID, consultez la section [Vue d’ensemble et architecture](./hana-overview-architecture.md).
- Sur le site de récupération d’urgence : Les volumes et les points de montage sont configurés, marqués comme « Required for HANA installation » (nécessaires pour l’installation de HANA), en vue de l’installation de l’instance HANA de production sur l’unité HLI de récupération d’urgence. 
- Sur le site de récupération d’urgence : Les volumes partagés, de sauvegarde de fichiers journaux et de données, marqués comme « Storage Replication » (Réplication de stockage), sont répliqués par le biais de la capture instantanée depuis le site de production. Ces volumes sont montés pendant le basculement uniquement. Pour plus d’informations, consultez [Procédure de basculement en cas de récupération d’urgence](./hana-overview-high-availability-disaster-recovery.md).
- Le volume de démarrage pour la *classe de type I des références SKU* est répliqué sur le nœud de récupération d’urgence.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>Nœud unique avec récupération d’urgence (polyvalente) à l’aide de la réplication de stockage
 
Cette topologie prend en charge un seul nœud dans une configuration de scale-out avec plusieurs SID. La réplication basée sur stockage vers le site de récupération d’urgence est utilisée pour un SID principal. 

Dans le diagramme, seul un système à SID unique est représenté sur le site principal, mais les systèmes multi-SID (MCOS) sont également pris en charge. Sur le site DR, l’unité HLI est utilisée pour l’instance d’assurance qualité. Les opérations de production s’exécutent à partir du site principal. Durant le basculement de la récupération d’urgence (ou le test de basculement), l’instance AQ sur le site de récupération d’urgence est arrêté.

### <a name="architecture-diagram"></a>Diagramme de l'architecture  

![Nœud unique avec récupération d’urgence (polyvalente) à l’aide de la réplication de stockage](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| Interface logique de la carte réseau | Type de référence SKU | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| Un | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Configuré mais non utilisé |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| Un | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Configuré mais non utilisé |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur le site principal**|
|/Hana/Shared/SID | Installation de HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |
|**Sur le site de reprise après sinistre**|
|/Hana/Shared/SID | Installation de HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/shared/QA-SID | Installation de HANA pour SID d’assurance qualité | 
|/hana/data/QA-SID/mnt00001 | Installation des fichiers de données pour SID d’assurance qualité | 
|/hana/log/QA-SID/mnt00001 | Installation des fichiers journaux pour SID d’assurance qualité |
|/hana/logbackups/QA-SID | Journaux d’activité de rétablissement pour SID AQ |

### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- Pour MCOS : La répartition de la taille des volumes est basée sur la taille de la base de données en mémoire. Pour découvrir quelles tailles de base de données en mémoire sont prises en charge dans un environnement multi-SID, consultez la section [Vue d’ensemble et architecture](./hana-overview-architecture.md).
- Sur le site de récupération d’urgence : Les volumes et les points de montage sont configurés, marqués comme « Required for HANA installation » (nécessaires pour l’installation de HANA), en vue de l’installation de l’instance HANA de production sur l’unité HLI de récupération d’urgence. 
- Sur le site de récupération d’urgence : Les volumes partagés, de sauvegarde de fichiers journaux et de données, marqués comme « Storage Replication » (Réplication de stockage), sont répliqués par le biais de la capture instantanée depuis le site de production. Ces volumes sont montés pendant le basculement uniquement. Pour plus d’informations, consultez [Procédure de basculement en cas de récupération d’urgence](./hana-overview-high-availability-disaster-recovery.md). 
- Sur le site de récupération d’urgence : Les volumes partagés, de données, de journaux et de sauvegardes de fichiers journaux pour l’assurance qualité, marqués comme « QA Instance installation » (Installation de l’instance AQ), sont configurés pour l’installation de l’instance AQ.
- Le volume de démarrage pour la *classe de type I des références SKU* est répliqué sur le nœud de récupération d’urgence.

## <a name="hsr-with-stonith-for-high-availability"></a>HSR avec STONITH pour une haute disponibilité
 
Cette topologie prend en charge deux nœuds pour la configuration de la réplication du système HANA. Cette configuration est prise en charge uniquement pour les instances HANA uniques sur un nœud. Les scénarios MCOS *ne sont pas* pris en charge.

> [!NOTE]
> Depuis décembre 2019, cette architecture est uniquement prise en charge pour le système d’exploitation SUSE.


### <a name="architecture-diagram"></a>Diagramme de l'architecture  

![HSR avec STONITH pour une haute disponibilité](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| Interface logique de la carte réseau | Type de référence SKU | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| Un | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Configuré mais non utilisé |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Utilisé pour STONITH |
| Un | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Configuré mais non utilisé |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Utilisé pour STONITH |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur le nœud principal**|
|/Hana/Shared/SID | Installation de HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |
|**Sur le nœud secondaire**|
|/Hana/Shared/SID | Installation de HANA pour SID secondaire | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID secondaire | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID secondaire | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID secondaire |

### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- Pour MCOS : La répartition de la taille des volumes est basée sur la taille de la base de données en mémoire. Pour découvrir quelles tailles de base de données en mémoire sont prises en charge dans un environnement multi-SID, consultez la section [Vue d’ensemble et architecture](./hana-overview-architecture.md).
- STONITH : Un SBD est configuré pour l’installation STONITH. L’utilisation de STONITH est néanmoins facultative.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>Haute disponibilité avec HSR et reprise d’activité avec réplication de stockage
 
Cette topologie prend en charge deux nœuds pour la configuration de la réplication du système HANA. Les deux possibilités de récupération d’urgence, normale et polyvalente, sont prises en charge. Ces configurations sont prises en charge uniquement pour les instances HANA uniques sur un nœud. Les scénarios MCOS *ne sont pas* pris en charge avec ces configurations.

Dans le diagramme, un scénario polyvalent est affiché au niveau du site DR, où l’unité HLMI est utilisée pour l’instance d’assurance qualité. Les opérations de production s’exécutent à partir du site principal. Durant le basculement de la récupération d’urgence (ou le test de basculement), l’instance AQ sur le site de récupération d’urgence est arrêté. 

### <a name="architecture-diagram"></a>Diagramme de l'architecture  

![Haute disponibilité avec HSR et reprise d’activité avec réplication de stockage](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| Interface logique de la carte réseau | Type de référence SKU | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| Un | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Configuré mais non utilisé |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Utilisé pour STONITH |
| Un | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Configuré mais non utilisé |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Utilisé pour STONITH |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur le nœud principal du site principal**|
|/Hana/Shared/SID | Installation de HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |
|**Sur le nœud secondaire du site principal**|
|/Hana/Shared/SID | Installation de HANA pour SID secondaire | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID secondaire | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID secondaire | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID secondaire |
|**Sur le site de reprise après sinistre**|
|/Hana/Shared/SID | Installation de HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/shared/QA-SID | Installation de HANA pour SID d’assurance qualité | 
|/hana/data/QA-SID/mnt00001 | Installation des fichiers de données pour SID d’assurance qualité | 
|/hana/log/QA-SID/mnt00001 | Installation des fichiers journaux pour SID d’assurance qualité |
|/hana/logbackups/QA-SID | Journaux d’activité de rétablissement pour SID AQ |

### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- Pour MCOS : La répartition de la taille des volumes est basée sur la taille de la base de données en mémoire. Pour découvrir quelles tailles de base de données en mémoire sont prises en charge dans un environnement multi-SID, consultez la section [Vue d’ensemble et architecture](./hana-overview-architecture.md).
- STONITH : Un SBD est configuré pour l’installation STONITH. L’utilisation de STONITH est néanmoins facultative.
- Sur le site de récupération d’urgence : *Deux jeux de volumes de stockage sont nécessaires* pour la réplication du nœud principal et du nœud secondaire.
- Sur le site de récupération d’urgence : Les volumes et les points de montage sont configurés, marqués comme « Required for HANA installation » (nécessaires pour l’installation de HANA), en vue de l’installation de l’instance HANA de production sur l’unité HLI de récupération d’urgence. 
- Sur le site de récupération d’urgence : Les volumes partagés, de sauvegarde de fichiers journaux et de données, marqués comme « Storage Replication » (Réplication de stockage), sont répliqués par le biais de la capture instantanée depuis le site de production. Ces volumes sont montés pendant le basculement uniquement. Pour plus d’informations, consultez [Procédure de basculement en cas de récupération d’urgence](./hana-overview-high-availability-disaster-recovery.md). 
- Sur le site de récupération d’urgence : Les volumes partagés, de données, de journaux et de sauvegardes de fichiers journaux pour l’assurance qualité, marqués comme « QA Instance installation » (Installation de l’instance AQ), sont configurés pour l’installation de l’instance AQ.
- Le volume de démarrage pour la *classe de type I des références SKU* est répliqué sur le nœud de récupération d’urgence.


## <a name="host-auto-failover-11"></a>Basculement automatique avec hôte (1+1)
 
Cette topologie prend en charge deux nœuds dans une configuration de basculement automatique avec hôte. Un nœud dispose du rôle principal/de travail, l’autre étant un nœud de secours. *SAP prend en charge ce scénario uniquement pour S/4 HANA.* Pour plus d’informations, reportez-vous à la note OSS [2408419 – SAP S/4HANA – Multi-Node Support](https://launchpad.support.sap.com/#/notes/2408419) (2408419 – SAP S/4HANA – Prise en charge de plusieurs nœuds).



### <a name="architecture-diagram"></a>Diagramme de l'architecture  

![Basculement automatique avec hôte (1+1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| Interface logique de la carte réseau | Type de référence SKU | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| Un | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Communication nœud à nœud |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| Un | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Communication nœud à nœud |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur les nœuds principal et de secours**|
|/hana/shared | Installation de HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |



### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- Sur le nœud de secours : Les volumes et les points de montage sont configurés, marqués comme « Required for HANA installation » (nécessaires pour l’installation de HANA), en vue de l’installation de l’instance HANA sur l’unité de secours.
 

## <a name="scale-out-with-standby"></a>Scale-out avec nœud de secours
 
Cette topologie prend en charge plusieurs nœuds dans une configuration de scale-out. Elle est composée d’un nœud doté du rôle principal, d’un ou de plusieurs nœuds dotés d’un rôle de travail et d’un ou plusieurs nœuds de secours. Toutefois, il ne peut exister qu'un seul nœud principal à un moment donné.


### <a name="architecture-diagram"></a>Diagramme de l'architecture  

![Scale-out avec nœud de secours](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| Interface logique de la carte réseau | Type de référence SKU | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| Un | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Communication nœud à nœud |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| Un | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Communication nœud à nœud |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur les nœuds principal, de travail et de secours**|
|/hana/shared | Installation de HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |


## <a name="scale-out-without-standby"></a>Scale-out sans nœud de secours
 
Cette topologie prend en charge plusieurs nœuds dans une configuration de scale-out. Elle est composée d’un nœud doté du rôle principal et d’un ou de plusieurs nœuds dotés d’un rôle de travail. Toutefois, il ne peut exister qu'un seul nœud principal à un moment donné.


### <a name="architecture-diagram"></a>Diagramme de l'architecture  

![Scale-out sans nœud de secours](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| Interface logique de la carte réseau | Type de référence SKU | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| Un | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Communication nœud à nœud |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| Un | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Communication nœud à nœud |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur les nœuds principal et de travail**|
|/hana/shared | Installation de HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |


### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.

## <a name="scale-out-with-dr-using-storage-replication"></a>Scale-out avec reprise d’activité à l’aide de la réplication de stockage
 
Cette topologie prend en charge plusieurs nœuds dans une configuration de scale-out avec reprise d’activité après sinistre. Les deux possibilités de récupération d’urgence, normale et polyvalente, sont prises en charge. Dans le diagramme, seul la récupération d'urgence à objectif unique est affichée. Vous pouvez demander cette topologie avec ou sans nœud de secours.


### <a name="architecture-diagram"></a>Diagramme de l'architecture  

![Scale-out avec reprise d’activité à l’aide de la réplication de stockage](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| Interface logique de la carte réseau | Type de référence SKU | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| Un | TYPE I | eth0.tenant | eno1.tenant | Client à HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | Communication nœud à nœud |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| Un | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Communication nœud à nœud |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur le nœud principal**|
|/hana/shared | Installation de HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |
|**Sur le nœud du site de reprise après sinistre**|
|/hana/shared | Installation de HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 


### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
-  Sur le site de récupération d’urgence : Les volumes et les points de montage sont configurés, marqués comme « Required for HANA installation » (nécessaires pour l’installation de HANA), en vue de l’installation de l’instance HANA de production sur l’unité HLI de récupération d’urgence. 
- Sur le site de récupération d’urgence : Les volumes partagés, de sauvegarde de fichiers journaux et de données, marqués comme « Storage Replication » (Réplication de stockage), sont répliqués par le biais de la capture instantanée depuis le site de production. Ces volumes sont montés pendant le basculement uniquement. Pour plus d’informations, consultez [Procédure de basculement en cas de récupération d’urgence](./hana-overview-high-availability-disaster-recovery.md). 
- Le volume de démarrage pour la *classe de type I des références SKU* est répliqué sur le nœud de récupération d’urgence.


## <a name="single-node-with-dr-using-hsr"></a>Nœud unique avec reprise d’activité à l’aide de HSR
 
Cette topologie prend en charge un seul nœud dans une configuration de scale-up avec un seul SID, avec la réplication du système HANA vers le site de récupération d'urgence pour un SID principal. Dans le diagramme, seul un système à SID unique est représenté sur le site principal, mais les systèmes multi-SID (MCOS) sont également pris en charge.

### <a name="architecture-diagram"></a>Diagramme de l'architecture  

![Nœud unique avec reprise d’activité à l’aide de HSR](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| Interface logique de la carte réseau | Type de référence SKU | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| Un | TYPE I | eth0.tenant | eno1.tenant | Client à HLI/HSR |
| B | TYPE I | eth2.tenant | eno3.tenant | Configuré mais non utilisé |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| Un | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI/HSR |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Configuré mais non utilisé |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés sur les deux unités HLI (unité principale et unité de récupération d'urgence) :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|/Hana/Shared/SID | Installation de HANA pour SID | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID | 
|/hana/logbackups/SID | Journaux d’activité de rétablissement pour SID |


### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- Pour MCOS : La répartition de la taille des volumes est basée sur la taille de la base de données en mémoire. Pour découvrir quelles tailles de base de données en mémoire sont prises en charge dans un environnement multi-SID, consultez la section [Vue d’ensemble et architecture](./hana-overview-architecture.md).
- Le nœud principal se synchronise avec le nœud de récupération d'urgence à l'aide de la réplication du système HANA. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) permet d’associer des circuits ExpressRoute afin de constituer un réseau privé entre vos réseaux régionaux.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>HSR à nœud unique vers site de récupération d’urgence (optimisation des coûts) 
 
 Cette topologie prend en charge un seul nœud dans une configuration de montée en charge avec un SID. La réplication du système HANA vers le site de récupération d’urgence est utilisée pour un SID principal. Dans le diagramme, seul un système à SID unique est représenté sur le site principal, mais les systèmes multi-SID (MCOS) sont également pris en charge. Sur le site DR, une unité HLI est utilisée pour l’instance d’assurance qualité. Les opérations de production s’exécutent à partir du site principal. Durant le basculement de la récupération d’urgence (ou le test de basculement), l’instance AQ sur le site de récupération d’urgence est arrêté.

### <a name="architecture-diagram"></a>Diagramme de l'architecture  

![HSR à nœud unique vers site de récupération d’urgence (optimisation des coûts)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| Interface logique de la carte réseau | Type de référence SKU | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| Un | TYPE I | eth0.tenant | eno1.tenant | Client à HLI/HSR |
| B | TYPE I | eth2.tenant | eno3.tenant | Configuré mais non utilisé |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| Un | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI/HSR |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Configuré mais non utilisé |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur le site principal**|
|/Hana/Shared/SID | Installation de HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |
|**Sur le site de reprise après sinistre**|
|/Hana/Shared/SID | Installation de HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |
|/hana/shared/QA-SID | Installation de HANA pour SID d’assurance qualité | 
|/hana/data/QA-SID/mnt00001 | Installation des fichiers de données pour SID d’assurance qualité | 
|/hana/log/QA-SID/mnt00001 | Installation des fichiers journaux pour SID d’assurance qualité |
|/hana/logbackups/QA-SID | Journaux d’activité de rétablissement pour SID AQ |

### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- Pour MCOS : La répartition de la taille des volumes est basée sur la taille de la base de données en mémoire. Pour découvrir quelles tailles de base de données en mémoire sont prises en charge dans un environnement multi-SID, consultez la section [Vue d’ensemble et architecture](./hana-overview-architecture.md).
- Sur le site de récupération d’urgence : Les volumes et les points de montage sont configurés, marqués comme « PROD Instance at DR site » (Instance PROD sur le site de récupération d’urgence), en vue de l’installation de l’instance HANA de production sur l’unité HLI de récupération d’urgence. 
- Sur le site de récupération d’urgence : Les volumes partagés, de données, de journaux et de sauvegardes de fichiers journaux pour l’assurance qualité, marqués comme « QA Instance installation » (Installation de l’instance AQ), sont configurés pour l’installation de l’instance AQ.
- Le nœud principal se synchronise avec le nœud de récupération d'urgence à l'aide de la réplication du système HANA. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) permet d’associer des circuits ExpressRoute afin de constituer un réseau privé entre vos réseaux régionaux.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>Haute disponibilité et reprise d’activité avec HSR 
 
 Cette topologie prend en charge deux nœuds dans le contexte de la configuration de la réplication du système HANA pour la haute disponibilité des régions locales. Pour la récupération d’urgence, le troisième nœud de la région de récupération d’urgence se synchronise avec le site principal par le biais de la HSR (mode asynchrone). 

### <a name="architecture-diagram"></a>Diagramme de l'architecture  

![Haute disponibilité et reprise d’activité avec HSR](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| Interface logique de la carte réseau | Type de référence SKU | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| Un | TYPE I | eth0.tenant | eno1.tenant | Client à HLI/HSR |
| B | TYPE I | eth2.tenant | eno3.tenant | Configuré mais non utilisé |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| Un | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI/HSR |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Configuré mais non utilisé |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur le site principal**|
|/Hana/Shared/SID | Installation de HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |
|**Sur le site de reprise après sinistre**|
|/Hana/Shared/SID | Installation de HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |


### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- Sur le site de récupération d’urgence : Les volumes et les points de montage sont configurés, marqués comme « PROD DR Instance » (Instance de récupération d’urgence PROD), en vue de l’installation de l’instance HANA de production sur l’unité HLI de récupération d’urgence. 
- Le nœud du site principal se synchronise avec le nœud de récupération d'urgence à l'aide de la réplication du système HANA. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) permet d’associer des circuits ExpressRoute afin de constituer un réseau privé entre vos réseaux régionaux.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>Haute disponibilité et reprise d’activité avec HSR (optimisation des coûts)
 
 Cette topologie prend en charge deux nœuds dans le contexte de la configuration de la réplication du système HANA pour la haute disponibilité des régions locales. Pour la récupération d’urgence, le troisième nœud de la région de récupération d’urgence se synchronise avec le site principal par le biais de la HSR (mode asynchrone), tandis qu’une autre instance (par exemple, AQ) est déjà en cours d’exécution à partir du nœud de récupération d’urgence. 

### <a name="architecture-diagram"></a>Diagramme de l'architecture  

![Haute disponibilité et reprise d’activité avec HSR (optimisation des coûts)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| Interface logique de la carte réseau | Type de référence SKU | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| Un | TYPE I | eth0.tenant | eno1.tenant | Client à HLI/HSR |
| B | TYPE I | eth2.tenant | eno3.tenant | Configuré mais non utilisé |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| Un | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI/HSR |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Configuré mais non utilisé |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur le site principal**|
|/Hana/Shared/SID | Installation de HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |
|**Sur le site de reprise après sinistre**|
|/Hana/Shared/SID | Installation de HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |
|/hana/shared/QA-SID | Installation de HANA pour SID d’assurance qualité | 
|/hana/data/QA-SID/mnt00001 | Installation des fichiers de données pour SID d’assurance qualité | 
|/hana/log/QA-SID/mnt00001 | Installation des fichiers journaux pour SID d’assurance qualité |
|/hana/logbackups/QA-SID | Journaux d’activité de rétablissement pour SID AQ |

### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- Sur le site de récupération d’urgence : Les volumes et les points de montage sont configurés, marqués comme « PROD DR Instance » (Instance de récupération d’urgence PROD), en vue de l’installation de l’instance HANA de production sur l’unité HLI de récupération d’urgence. 
- Sur le site de récupération d’urgence : Les volumes partagés, de données, de journaux et de sauvegardes de fichiers journaux pour l’assurance qualité, marqués comme « QA Instance installation » (Installation de l’instance AQ), sont configurés pour l’installation de l’instance AQ.
- Le nœud du site principal se synchronise avec le nœud de récupération d'urgence à l'aide de la réplication du système HANA. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) permet d’associer des circuits ExpressRoute afin de constituer un réseau privé entre vos réseaux régionaux.

## <a name="scale-out-with-dr-using-hsr"></a>Scale-out avec reprise d’activité à l’aide de HSR
 
Cette topologie prend en charge plusieurs nœuds dans une configuration de scale-out avec reprise d’activité après sinistre. Vous pouvez demander cette topologie avec ou sans nœud de secours. Le nœud du site principal se synchronise avec le nœud du site de récupération d'urgence à l'aide de la réplication du système HANA (mode asynchrone).


### <a name="architecture-diagram"></a>Diagramme de l'architecture  

[ ![Scale-out avec récupération d’urgence à l’aide de la HSR](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Ethernet
Les interfaces réseau suivantes sont préconfigurées :

| Interface logique de la carte réseau | Type de référence SKU | Nom avec le système d’exploitation SUSE | Nom avec le système d’exploitation RHEL | Cas d’utilisation|
| --- | --- | --- | --- | --- |
| Un | TYPE I | eth0.tenant | eno1.tenant | Client à HLI/HSR |
| B | TYPE I | eth2.tenant | eno3.tenant | Communication nœud à nœud |
| C | TYPE I | eth1.tenant | eno2.tenant | Nœud à stockage |
| D | TYPE I | eth4.tenant | eno4.tenant | Configuré mais non utilisé |
| Un | TYPE II | vlan\<tenantNo> | team0.tenant | Client à HLI/HSR |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | Communication nœud à nœud |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | Nœud à stockage |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | Configuré mais non utilisé |

### <a name="storage"></a>Stockage
Les points de montage suivants sont préconfigurés :

| Point de montage | Cas d’utilisation | 
| --- | --- |
|**Sur le nœud principal**|
|/hana/shared | Installation de HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |
|**Sur le nœud du site de reprise après sinistre**|
|/hana/shared | Installation de HANA pour SID de production | 
|/hana/data/SID/mnt00001 | Installation des fichiers de données pour SID de production | 
|/hana/log/SID/mnt00001 | Installation des fichiers journaux pour SID de production | 
|/hana/logbackups/SID | Journaux de rétablissement pour SID de production |


### <a name="key-considerations"></a>Considérations relatives aux clés
- /usr/sap/SID est un lien symbolique vers /hana/shared/SID.
- Sur le site de récupération d’urgence : Les volumes et les points de montage sont configurés en vue de l’installation de l’instance HANA de production sur l’unité HLI de récupération d’urgence. 
- Le nœud du site principal se synchronise avec le nœud de récupération d'urgence à l'aide de la réplication du système HANA. 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) permet d’associer des circuits ExpressRoute afin de constituer un réseau privé entre vos réseaux régionaux.


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le déploiement de grandes instances HANA.

> [!div class="nextstepaction"]
> [Déploiement de SAP HANA (grandes instances)](./hana-overview-infrastructure-connectivity.md)
