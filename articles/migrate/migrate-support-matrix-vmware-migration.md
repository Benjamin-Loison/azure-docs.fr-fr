---
title: Prise en charge de la migration VMware dans Azure Migrate
description: Découvrez la prise en charge de la migration VMware dans Azure Migrate.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 1217acb5911366b8e3943d414aeeeaf8a9e5e1e1
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357244"
---
# <a name="support-matrix-for-vmware-migration"></a>Tableau de prise en charge pour la migration VMware

Cet article résume les paramètres et les limites de la migration de machines virtuelles VMware avec [Azure Migrate : Migration de serveur](migrate-services-overview.md#azure-migrate-server-migration-tool). Si vous recherchez des informations sur l’évaluation de machines virtuelles VMware pour la migration vers Azure, consultez le [tableau de prise en charge de l’évaluation](migrate-support-matrix-vmware.md).


## <a name="migration-options"></a>Options de migration

Vous pouvez migrer des machines virtuelles VMware de deux manières différentes :

- **À l’aide de la migration sans agent** : Migrez des machines virtuelles sans avoir à installer quoi que ce soit. Vous déployez l’[appliance Azure Migrate](migrate-appliance.md) pour la migration sans agent.
- **À l’aide de la migration avec agent** : Installez un agent sur la machine virtuelle pour la réplication. Vous déployez une [appliance de réplication](migrate-replication-appliance.md).

Consultez [cet article](server-migrate-overview.md) pour déterminer la méthode que vous souhaitez utiliser.

## <a name="agentless-migration"></a>Migration sans agent

Cette section résume les exigences relatives à la migration de machine virtuelle VMware sans agent vers Azure.

### <a name="vmware-requirements-agentless"></a>Exigences liées à VMware (sans agent)

Le tableau récapitule les exigences de l’hyperviseur VMware.

**VMware** | **Détails**
--- | ---
**VMware vCenter Server** | Versions 5.5, 6.0, 6.5, 6.7, 7.0.
**Hôte ESXI VMware vSphere** | Versions 5.5, 6.0, 6.5, 6.7, 7.0.
**Autorisations vCenter Server** | La migration sans agent utilise l’[appliance Migrate](migrate-appliance.md). L’appliance a besoin des autorisations suivantes dans vCenter Server :<br/><br/> - **Datastore.Browse** (Magasin de données -> Parcourir le magasin de données) : Autoriser l’exploration des fichiers journaux des machines virtuelles pour résoudre les problèmes de création et de suppression des instantanés.<br/><br/> - **Datastore.FileManagement** (Magasin de données ->Opérations sur les fichiers de bas niveau) : Autoriser les opérations de lecture/écriture/suppression/renommage dans l’explorateur de magasin de données pour résoudre les problèmes de création et de suppression des instantanés.<br/><br/> - **VirtualMachine.Config.ChangeTracking** (Machine virtuelle -> Suivi des modifications du disque) : Autoriser l’activation ou la désactivation du suivi des modifications des disques de machine virtuelle pour extraire les blocs de données modifiés entre les instantanés.<br/><br/> - **VirtualMachine.Config.DiskLease** (Machine virtuelle -> Bail du disque) : Autoriser les opérations de bail de disque pour une machine virtuelle, à lire le disque en utilisant VDDK (vSphere Virtual Disk Development Kit) VMware vSphere.<br/><br/> - **VirtualMachine.Provisioning.DiskRandomRead** (Machine virtuelle -> Approvisionnement -> Autoriser l’accès au disque en lecture seule) : Autoriser l’ouverture d’un disque sur une machine virtuelle pour lire le disque avec VDDK.<br/><br/> - **VirtualMachine.Provisioning.DiskRandomAccess** (Machine virtuelle -> Approvisionnement -> Autoriser l’accès au disque) : Autoriser l’ouverture d’un disque sur une machine virtuelle pour lire le disque avec VDDK.<br/><br/> - **VirtualMachine.Provisioning.GetVmFiles** (Machine virtuelle -> Approvisionnement -> Autoriser le téléchargement de la machine virtuelle) : Autorise les opérations de lecture sur les fichiers associés à une machine virtuelle, à télécharger les journaux et à résoudre les problèmes en cas d’échec.<br/><br/> - **VirtualMachine.State.\* *_ (Machine virtuelle -> Gestion des instantanés) : Autoriser la création et la gestion des instantanés de machines virtuelles pour la réplication.<br/><br/> - _* VirtualMachine.Interact.PowerOff** (Machine virtuelle -> Interaction -> Mise hors tension) : Autoriser la mise hors tension de la machine virtuelle pendant la migration vers Azure.



### <a name="vm-requirements-agentless"></a>Exigences liées aux machines virtuelles (sans agent)

Le tableau récapitule les exigences de la migration sans agent concernant les machines virtuelles VMware.

**Support** | **Détails**
--- | ---
**Systèmes d’exploitation pris en charge** | Il est possible de migrer les systèmes d’exploitation [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) et [Linux](../virtual-machines/linux/endorsed-distros.md) pris en charge par Azure.
**Machines virtuelles Windows dans Azure** | [Quelques modifications](prepare-for-migration.md#verify-required-changes-before-migrating) pourront se révéler nécessaires sur les machines virtuelles avant la migration.
**Machines virtuelles Linux dans Azure** | Certaines machines virtuelles peuvent nécessiter des modifications pour fonctionner dans Azure.<br/><br/> Azure Migrate effectue automatiquement les modifications pour les systèmes d’exploitation Linux suivants :<br/> - Red Hat Enterprise Linux 8, 7.9, 7.8, 7.7, 7.6, 7.5, 7.4, 7.0, 6.x <br/> - CentOS 8, 7.7, 7.6, 7.5, 7.4, 6.x</br> - SUSE Linux Enterprise Server 11, 12, 15 SP0, 15 SP1 <br/>- Ubuntu 20.04, 19.04, 19.10, 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7, 8, 9 <br/> Oracle Linux 6, 7.7, 7.7-CI<br/> Pour les autres systèmes d’exploitation, les [modifications requises](prepare-for-migration.md#verify-required-changes-before-migrating) sont à effectuer manuellement.
**Configuration requise pour le démarrage** | Si /boot se trouve sur une partition dédiée, il doit être le disque du système d’exploitation et ne pas être réparti sur plusieurs disques.<br/> Si /boot fait partie de la partition racine (/), la partition « / » doit se trouver sur le disque du système d’exploitation et ne pas s’étendre sur d’autres disques.
**Démarrage UEFI** | Pris en charge. Les machines virtuelles UEFI seront migrées vers des machines virtuelles Azure de 2e génération.
**Taille du disque** | Jusqu’à 2 To de disque de système d’exploitation pour les machines virtuelles de 1re génération et de 2e génération ; 32 To pour les disques de données.
**Limites du disque** |  Jusqu'à 60 disques par machine virtuelle.
**Disques/volumes chiffrés** | Les machines virtuelles avec des disques/volumes chiffrés ne sont pas prises en charge pour la migration.
**Cluster de disque partagé** | Non pris en charge.
**Disques indépendants** | Non pris en charge.
**RDM/disques directs** | Si les machines virtuelles ont des disques RDM ou directs, ces disques ne seront pas répliqués dans Azure.
**NFS** | Les volumes NFS montés en tant que volumes sur les machines virtuelles ne sont pas répliqués.
**Cibles iSCSI** | Les machines virtuelles avec des cibles iSCSI ne sont pas prises en charge pour la migration sans agent.
**Multipath I/O** | Non pris en charge.
**Storage vMotion** | Non pris en charge. La réplication ne fonctionne pas si une machine virtuelle utilise Storage vMotion.
**Cartes réseau associées** | Non pris en charge.
**IPv6** | Non pris en charge.
**Disque cible** | Les machines virtuelles peuvent uniquement être migrées vers des disques managés (disques HDD standard, disques SSD standard, disques SSD premium) dans Azure.
**Réplication simultanée** | Jusqu’à 300 machines virtuelles répliquant simultanément par vCenter Server avec 1 appliance. Jusqu’à 500 machines virtuelles répliquant simultanément par vCenter Server quand une [appliance de scale-out](./how-to-scale-out-for-migration.md) supplémentaire est déployée.
**Installation automatique de l’agent de machine virtuelle Azure (agent Windows et Linux)** | Prise en charge pour Windows Server 2008 R2 et versions ultérieures. <br/> Prise en charge pour RHEL6, RHEL7, CentOS7, Ubuntu 14.04, Ubuntu 16.04, Ubuntu18.04, Ubuntu 19.04, Ubuntu 19.10, Ubuntu 20.04.

> [!Note]
> En plus de la connectivité Internet, pour les machines virtuelles Linux, assurez-vous que les packages suivants sont installés pour réussir l’installation de l’agent Linux Microsoft Azure (waagent) :
>- Python 2.6+
>- OpenSSL 1.0+
>- OpenSSH 5.3+
>- Utilitaires de système de fichiers : sfdisk, fdisk, mkfs, séparés
>- Outils de mot de passe : chpasswd, sudo
>- Outils de traitement de texte : sed, grep
>- Outils réseau : ip-route

> [!TIP]
>  Le portail Azure vous permet de sélectionner jusqu’à 10 machines virtuelles à la fois pour configurer la réplication. Pour répliquer davantage de machines virtuelles, vous pouvez utiliser le portail pour ajouter les machines virtuelles à répliquer dans plusieurs lots de 10 machines virtuelles, ou utiliser l’interface Azure Migrate PowerShell pour configurer la réplication. Veillez à ne pas configurer une réplication simultanée sur plus que le nombre maximal de machines virtuelles prises en charge pour des réplications simultanées.

### <a name="appliance-requirements-agentless"></a>Exigences liées aux appliances (sans agent)

La migration sans agent utilise l’[appliance Azure Migrate](migrate-appliance.md). Vous pouvez déployer l’appliance en tant que machine virtuelle VMware à l’aide d’un modèle OVA, importé dans vCenter Server, ou à l’aide d’un [script PowerShell](deploy-appliance-script.md).

- En savoir plus sur les [conditions requises de l’appliance](migrate-appliance.md#appliance---vmware) pour VMware.
- Découvrez les URL auxquelles l’appliance doit accéder dans les clouds [publics](migrate-appliance.md#public-cloud-urls) et du [secteur public](migrate-appliance.md#government-cloud-urls).
- Dans Azure Government, vous devez déployer l’appliance [avec ce script](deploy-appliance-script-government.md).

### <a name="port-requirements-agentless"></a>Exigences liées aux ports (sans agent)

**Appareil** | **Connection**
--- | ---
Appliance | Connexions sortantes sur le port 443 pour charger les données répliquées sur Azure et pour communiquer avec les services Azure Migrate effectuant la réplication et la migration.
Serveur vCenter | Connexions entrantes sur le port 443 pour permettre à l’appliance d’orchestrer la réplication : créer des instantanés, copier des données, publier des instantanés.
Hôte vSphere/ESXI | Trafic entrant sur le port TCP 902 pour que l’appliance réplique des données à partir d’instantanés. Port de sortie 902 de l’hôte ESXi.

## <a name="agent-based-migration"></a>Migration basée sur agent


Cette section récapitule les exigences de la migration avec agent.


### <a name="vmware-requirements-agent-based"></a>Exigences liées à VMware (avec agent)

Ce tableau récapitule la prise en charge et les limitations de l’évaluation pour les serveurs de virtualisation VMware.

**Conditions requises VMware** | **Détails**
--- | ---
**VMware vCenter Server** | Version 5.5, 6.0, 6.5 ou 6.7.
**Hôte ESXI VMware vSphere** | Version 5.5, 6.0, 6.5 ou 6.7.
**Autorisations vCenter Server** | Un compte en lecture seule pour vCenter Server.

### <a name="vm-requirements-agent-based"></a>Exigences liées aux machines virtuelles (avec agent)

Le tableau résume la prise en charge de machines virtuelles VMware pour les machines virtuelles VMware que vous souhaitez migrer à l’aide de la migration basée sur un agent.

**Support** | **Détails**
--- | ---
**Charge de travail des machines** | Azure Migrate prend en charge la migration de toutes les charges de travail (par exemple Active Directory, SQL Server, etc.) exécutées sur une machine prise en charge.
**Systèmes d’exploitation** | Pour obtenir les informations les plus récentes, consultez [Prise en charge des systèmes d’exploitation](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) pour Site Recovery. Azure Migrate fournit une prise en charge identique des systèmes d’exploitation des machines virtuelles.
**Stockage invité/système de fichiers Linux** | Pour obtenir les informations les plus récentes, consultez [Prise en charge des systèmes de fichiers Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) pour Site Recovery. Azure Migrate a une prise en charge identique des systèmes de fichiers Linux.
**Réseau/Stockage** | Pour obtenir les informations les plus récentes, consultez les prérequis en matière de [réseau](../site-recovery/vmware-physical-azure-support-matrix.md#network) et de [stockage](../site-recovery/vmware-physical-azure-support-matrix.md#storage) pour Site Recovery. Azure Migrate fournit des exigences identiques pour le réseau/stockage.
**Conditions requises pour Azure** | Pour obtenir les informations les plus récentes, consultez les exigences d’Azure en matière de [réseau](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), de [stockage](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage) et de [calcul](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) pour Site Recovery. Azure Migrate a des exigences identiques pour la migration de VMware.
**Service de mobilité** | L’agent du service Mobilité doit être installé sur chaque machine virtuelle que vous voulez migrer.
**Démarrage UEFI** | Pris en charge. Les machines virtuelles UEFI seront migrées vers des machines virtuelles Azure de 2e génération.
**UEFI – Démarrage sécurisé**         | Non pris en charge pour la migration.
**Disque cible** | Les machines virtuelles peuvent uniquement être migrées vers des disques managés (disques HDD standard, disques SSD standard, disques SSD premium) dans Azure.
**Taille du disque** | Jusqu’à 2 To de disque de système d’exploitation pour les machines virtuelles de 1re génération ; jusqu’à 4 To de disque de système d’exploitation pour les machines virtuelles de 2e génération ; 32 To pour les disques de données.
**Limites du disque** |  Jusqu'à 63 disques par machine virtuelle.
**Disques/volumes chiffrés** | Les machines virtuelles avec des disques/volumes chiffrés ne sont pas prises en charge pour la migration.
**Cluster de disque partagé** | Non pris en charge.
**Disques indépendants** | Pris en charge.
**Disques directs** | Pris en charge.
**NFS** | Les volumes NFS montés en tant que volumes sur les machines virtuelles ne sont pas répliqués.
**Cibles iSCSI** | Pris en charge.
**Multipath I/O** | Non pris en charge.
**Storage vMotion** | Prise en charge
**Cartes réseau associées** | Non pris en charge.
**IPv6** | Non pris en charge.




### <a name="appliance-requirements-agent-based"></a>Exigences liées aux appliances (avec agent)

Quand vous configurez l’appliance de réplication en utilisant le modèle OVA fourni dans le hub Azure Migrate, l’appliance exécute Windows Server 2016 et est conforme aux exigences de la prise en charge. Si vous configurez manuellement l’appliance de réplication sur un serveur physique, vérifiez qu’elle est conforme aux exigences.

- En savoir plus sur les [conditions requises de l’appliance de réplication](migrate-replication-appliance.md#appliance-requirements) pour VMware.
- MySQL doit être installé sur l’appliance. En savoir plus sur les [options d’installation](migrate-replication-appliance.md#mysql-installation).
- Découvrez les URL auxquelles l’appliance de réplication doit accéder dans les clouds [publics](migrate-replication-appliance.md#url-access) et du [secteur public](migrate-replication-appliance.md#azure-government-url-access).
- Passez en revue les [ports](migrate-replication-appliance.md#port-access) auxquels l’appliance de réplication doit accéder.

### <a name="port-requirements-agent-based"></a>Exigences liées aux ports (avec agent)

**Appareil** | **Connection**
--- | ---
Machines virtuelles | Le service Mobility en cours d’exécution sur des machines virtuelles communique avec l'appliance de réplication locale (serveur de configuration) via le port HTTPS 443 entrant, pour la gestion de la réplication.<br/><br/> Les machines virtuelles envoient des données de réplication au serveur de traitement (s’exécutant sur l’ordinateur du serveur de configuration) sur le port HTTPS 9443 entrant. Ce port peut être modifié.
Appliance de réplication | L’appliance de réplication orchestre la réplication avec Azure sur le port HTTPS 443 sortant.
Serveur de traitement | Le serveur de traitement reçoit les données de réplication, les optimise et les chiffre, puis les envoie au stockage Azure via le port 443 sortant.<br/> Le serveur de processus s’exécute par défaut sur l’appliance de réplication.

## <a name="azure-vm-requirements"></a>Exigences des machines virtuelles Azure

Toutes les machines virtuelles locales répliquées sur Azure (migration avec ou sans agent) doivent respecter les exigences liées aux machines virtuelles Azure récapitulées dans ce tableau.

**Composant** | **Configuration requise**
--- | --- | ---
Système d’exploitation invité | Vérifie les systèmes d’exploitation de machines virtuelles VMware pris en charge pour la migration.<br/> Vous pouvez migrer les charges de travail s’exécutant sur un système d’exploitation pris en charge.
Architecture du système d’exploitation invité | 64 bits.
Taille du disque du système d’exploitation | Jusqu’à 2 048 Go.
Nombre de disques du système d’exploitation | 1
Nombre de disques de données | 64 ou moins.
Taille de disque de données | Jusqu’à 32 To
Adaptateurs réseau | Prise en charge de plusieurs adaptateurs réseau.
Disque dur virtuel partagé | Non pris en charge.
Disque FC | Non pris en charge.
BitLocker | Non pris en charge.<br/><br/> BitLocker doit être désactivé avant la migration de la machine.
nom de la machine virtuelle | De 1 et 63 caractères.<br/><br/> Uniquement des lettres, des chiffres et des traits d’union.<br/><br/> Le nom de la machine doit commencer et se terminer par une lettre ou un chiffre.
Se connecter après la migration - Windows | Pour vous connecter à des machines virtuelles Azure exécutant Windows après la migration :<br/><br/> - Avant la migration, activez le protocole RDP sur la machine virtuelle locale.<br/><br/> Vérifiez que des règles TCP et UDP sont ajoutées pour le profil **Public** et que RDP est autorisé dans **Pare-feu Windows** > **Applications autorisées** pour tous les profils.<br/><br/> Pour l’accès VPN site à site, activez RDP et autorisez RDP dans **Pare-feu Windows** -> **Applications et fonctionnalités autorisées** pour les réseaux **Domaine et Privé**.<br/><br/> Vérifiez aussi que la stratégie SAN du système d’exploitation est définie sur **OnlineAll**. [Plus d’informations](prepare-for-migration.md)
Se connecter après la migration - Linux | Pour vous connecter à des machines virtuelles Azure après la migration avec SSH :<br/><br/> Avant la migration, sur la machine locale, vérifiez que le service Secure Shell est défini sur Démarrer et que les règles de pare-feu autorisent une connexion SSH.<br/><br/> Après le basculement, sur la machine virtuelle Azure, autorisez les connexions entrantes au port SSH pour les règles du groupe de sécurité réseau sur la machine virtuelle basculée, et pour le sous-réseau Azure auquel elle est connectée.<br/><br/> Ajoutez aussi une adresse IP publique pour la machine virtuelle.  


## <a name="next-steps"></a>Étapes suivantes

[Sélectionnez](server-migrate-overview.md) une option de migration VMware.
