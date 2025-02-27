---
title: Modifier la manière dont un compte de stockage est répliqué
titleSuffix: Azure Storage
description: Découvrez comment modifier la façon dont les données d’un compte de stockage existant sont répliquées.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/16/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7b5f2e6e8f883470826343c0aee1103a9a245be4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437540"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Modifier la manière dont un compte de stockage est répliqué

Stockage Azure stocke toujours plusieurs copies de vos données afin qu’elles soient protégées contre les événements planifiés ou non, notamment les défaillances matérielles temporaires, les pannes de réseau ou de courant et les catastrophes naturelles massives. La redondance garantit que votre compte de stockage répond aux exigences du [Contrat de niveau de service (SLA) pour Stockage Azure](https://azure.microsoft.com/support/legal/sla/storage/) même en cas de panne.

Stockage Azure propose les types de réplication suivants :

- Stockage localement redondant (LRS)
- Stockage redondant interzone (ZRS)
- Stockage géoredondant (GRS) ou stockage géographiquement redondant avec accès en lecture (RA-GRS)
- Stockage géoredondant interzone (GZRS) ou stockage géoredondant interzone avec accès en lecture (RA-GZRS)

Pour obtenir une vue d’ensemble de chacune de ces options, consultez la rubrique [Redondance de Stockage Azure](storage-redundancy.md).

## <a name="switch-between-types-of-replication"></a>Basculer entre les types de réplication

Vous pouvez basculer un compte de stockage d’un type de réplication à un autre, mais certains scénarios sont plus simples que d’autres. Si vous souhaitez ajouter ou supprimer la géoréplication ou l’accès en lecture à la région secondaire, vous pouvez utiliser le Portail Azure, PowerShell ou Azure CLI pour mettre à jour le paramètre de réplication. Toutefois, si vous souhaitez modifier la façon dont les données sont répliquées dans la région primaire en passant de LRS à ZRS ou vice versa, vous devez effectuer une migration manuelle.

Le tableau suivant fournit une vue d’ensemble de la façon de passer de chaque type de réplication à un autre :

| Basculement | … vers LRS | … vers GRS/RA-GRS | … vers ZRS | … vers GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>… depuis LRS</b> | N/A | Utiliser le Portail Azure, PowerShell ou l’interface CLI pour modifier le paramètre de réplication<sup>1,2</sup> | Effectuer une migration manuelle <br /><br /> OR <br /><br /> Demander une migration dynamique | Effectuer une migration manuelle <br /><br /> OR <br /><br /> Basculer d’abord sur GRS/RA-GRS, puis demander une migration dynamique<sup>3</sup> |
| <b>… depuis GRS/RA-GRS</b> | Utiliser le Portail Azure, PowerShell ou l’interface CLI pour modifier le paramètre de réplication | N/A | Effectuer une migration manuelle <br /><br /> OR <br /><br /> Basculer d’abord sur LRS, puis demander une migration dynamique<sup>3</sup> | Effectuer une migration manuelle <br /><br /> OR <br /><br /> Demander une migration dynamique<sup>3</sup> |
| <b>… depuis ZRS</b> | Effectuer une migration manuelle | Effectuer une migration manuelle | N/A | Demander une migration dynamique<sup>3</sup> |
| <b>… depuis GZRS/RA-GZRS</b> | Effectuer une migration manuelle | Effectuer une migration manuelle | Utiliser le Portail Azure, PowerShell ou l’interface CLI pour modifier le paramètre de réplication | N/A |

<sup>1</sup> Implique des frais de sortie ponctuels.<br />
<sup>2</sup> La migration de LRS vers GRS n’est pas prise en charge si le compte de stockage contient des objets blob dans le niveau archive.<br />
<sup>3</sup> La migration dynamique est prise en charge pour les comptes de stockage de partage de fichiers Premium et v2 universels Standard. La migration dynamique n’est pas prise en charge pour les comptes de stockage d’objets blob de blocs ou d’objets blob de pages Premium.

> [!CAUTION]
> Si vous avez opéré un [basculement de compte](storage-disaster-recovery-guidance.md) pour votre compte (RA-)GRS ou (RA-)GZRS, le compte est localement redondant (LRS) dans la nouvelle région primaire après le basculement. La migration en direct vers ZRS ou GZRS pour un compte LRS résultant d’un basculement n’est pas prise en charge. Cela est vrai même dans le cas d’opérations de restauration automatique. Par exemple, si vous effectuez un basculement de compte de RA-GZRS vers LRS dans la région secondaire, puis que vous le configurez de nouveau sur RA-GRS et effectuez un autre basculement de compte vers la région principale d’origine, vous ne pouvez pas contacter le support pour la migration dynamique d’origine vers RA-GZRS dans la région primaire. Vous devrez opérer une migration manuelle vers ZRS ou GZRS.

## <a name="change-the-replication-setting"></a>Modifier le paramètre de réplication

Vous pouvez utiliser le Portail Azure, PowerShell ou Azure CLI pour modifier le paramètre de réplication d’un compte de stockage, tant que vous ne modifiez pas la manière dont les données sont répliquées dans la région primaire. Si vous effectuez une migration depuis LRS dans la région primaire vers ZRS dans la région primaire, ou vice versa, vous devez effectuer une migration manuelle ou une migration dynamique.

La modification du mode de réplication de votre compte de stockage n’entraîne pas de temps d’indisponibilité pour vos applications.

# <a name="portal"></a>[Portail](#tab/portal)

Pour modifier l’option de redondance de votre compte de stockage dans le Portail Azure, procédez comme suit :

1. Accédez à votre compte de stockage dans le portail Azure.
1. Sous **Paramètres**, sélectionnez **Configuration**.
1. Mettez à jour le paramètre **Réplication**.

    :::image type="content" source="media/redundancy-migration/change-replication-option.png" alt-text="Capture d’écran montrant comment modifier l’option de réplication sur le portail." lightbox="media/redundancy-migration/change-replication-option.png":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour modifier l’option de redondance de votre compte de stockage avec PowerShell, appelez la commande [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) et spécifiez le paramètre `-SkuName` :

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour modifier l’option de redondance de votre compte de stockage avec Azure CLI, appelez la commande [az storage account update](/cli/azure/storage/account#az_storage_account_update) et spécifiez le paramètre `--sku` :

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs-gzrs-or-ra-gzrs"></a>Effectuer une migration manuelle vers ZRS, GZRS ou RA-GZRS

Si vous souhaitez modifier la façon dont les données de votre compte de stockage sont répliquées dans la région primaire, en passant de LRS à ZRS ou vice versa, vous pouvez choisir d’effectuer une migration manuelle. Une migration manuelle offre plus de souplesse qu’une migration dynamique. Vous contrôlez le moment choisi pour une migration manuelle. Par conséquent, utilisez cette option si vous avez besoin que la migration se termine avant une certaine date.

Lorsque vous effectuez une migration manuelle de LRS vers ZRS dans la région primaire ou vice versa, le compte de stockage de destination peut être géoredondant et peut également être configuré pour un accès en lecture sur la région secondaire. Par exemple, vous pouvez migrer un compte LRS vers un compte GZRS ou RA-GZRS en une seule étape.

Une migration manuelle peut entraîner un temps d’arrêt de l’application. Si votre application requiert une haute disponibilité, Microsoft offre également une option de migration dynamique. Une migration dynamique est une migration sur place sans temps d’arrêt.

Avec une migration manuelle, vous copiez les données de votre compte de stockage existant vers un nouveau compte de stockage qui utilise ZRS dans la région primaire. Pour effectuer une migration manuelle, vous pouvez utiliser l’une des options suivantes :

- Copiez les données en utilisant un outil existant tel qu’AzCopy, une des bibliothèques clientes de Stockage Azure, ou un outil tiers fiable.
- Si vous êtes familiarisé avec Hadoop ou HDInsight, vous pouvez attacher les comptes de stockage source et de destination à votre cluster. Ensuite, vous parallélisez le processus de copie de données avec un outil tel que DistCp.

## <a name="request-a-live-migration-to-zrs-gzrs-or-ra-gzrs"></a>Demander une migration dynamique vers ZRS, GZRS ou RA-GZRS

Si vous devez migrer votre compte de stockage de LRS vers ZRS dans la région primaire sans interruption de service pour les applications, vous pouvez demander une migration dynamique à Microsoft. Pour migrer de LRS vers GZRS ou RA-GZRS, commencez par passer à GRS ou RA-GRS, puis demandez une migration dynamique. De même, vous pouvez demander une migration dynamique de GRS ou RA-GRS vers GZRS ou RA-GZRS. Pour migrer de GRS ou RA-GRS vers ZRS , commencez par passer à LRS, puis demandez une migration dynamique.

Pendant une migration dynamique, vous pouvez accéder aux données de votre compte de stockage sans aucune perte de durabilité ni de disponibilité. Le SLA Stockage Azure est conservé pendant le processus de migration. La migration dynamique n’occasionne aucune perte de données. Les points de terminaison de service, les clés d’accès, les signatures d’accès partagé et les autres options de compte restent inchangés après la migration.

Pour le niveau de performance Standard, le stockage ZRS prend uniquement en charge les comptes v2 universels. Veillez donc à mettre à niveau votre compte de stockage s’il s’agit d’un compte v1 universel avant d’envoyer une demande de migration dynamique au stockage ZRS. Pour obtenir plus d’informations, consultez [Mettre à niveau vers un compte de stockage v2 à usage général](storage-account-upgrade.md). Un compte de stockage doit contenir les données à migrer à l’aide de la migration dynamique.

Pour le niveau de performance Premium, la migration dynamique est prise en charge pour les comptes de partage de fichiers Premium, mais pas pour les comptes d’objets blob de blocs Premium ou d’objets blob de pages Premium.

Si votre compte utilise RA-GRS, vous devez commencer par modifier le type de réplication de votre compte en LRS ou GRS avant de procéder à la migration dynamique. Cette étape intermédiaire supprime le point de terminaison en lecture seule secondaire fourni par RA-GRS.

Lorsque Microsoft gère votre demande de migration dynamique rapidement, il n’existe aucune garantie quant au moment où une migration dynamique s’achève. Si vos données doivent être migrées vers ZRS pour une certaine date, Microsoft recommande d’effectuer plutôt une migration manuelle. En général, plus la quantité de données présentes dans votre compte est élevée, plus la migration des données est longue.

Vous devez effectuer une migration manuelle si :

- Vous souhaitez migrer vos données vers un compte de stockage ZRS situé dans une région différente de celle du compte source.
- Votre compte de stockage est un compte Premium d’objets blob de pages ou d’objets blob de blocs.
- Vous souhaitez migrer les données de ZRS vers LRS, GRS ou RA-GRS.
- Votre compte de stockage comprend des données dans le niveau archive.

Vous pouvez demander une migration dynamique via le [portail du Support Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

> [!IMPORTANT]
> Si vous devez migrer plus d’un compte de stockage, créez un seul ticket de support et spécifiez les noms des comptes à convertir sous l’onglet **Détails**.

Pour demander une migration dynamique, procédez comme suit :

1. Dans le portail Azure, accédez au compte de stockage que vous voulez migrer.
1. Sous **Support et dépannage**, sélectionnez **Nouvelle demande de support**.
1. Renseignez les informations de votre compte dans l’onglet **Informations de base** :
    - **Type de problème** : Sélectionnez **Technique**.
    - **Service** : Sélectionnez **Mes services**, puis **Gestion des comptes de stockage**.
    - **Ressource** : Sélectionnez un compte de stockage à migrer. Si vous devez spécifier plusieurs comptes de stockage, vous pouvez le faire dans la section **Détails**.
    - **Type de problème** : Choisissez **Migration des données**.
    - **Sous-type de problème** : Choisissez **Migrer vers ZRS, GZRS ou RA-GZRS**.

    :::image type="content" source="media/redundancy-migration/request-live-migration-basics-portal.png" alt-text="Capture d’écran montrant comment demander une migration dynamique, onglet Informations de base":::

1. Sélectionnez **Suivant**. Sous l’onglet **Solutions**, vous pouvez vérifier l’éligibilité de vos comptes de stockage pour la migration.
1. Sélectionnez **Suivant**. Si vous avez plus d’un compte de stockage à migrer, sous l’onglet **Détails**, spécifiez le nom de chaque compte, en les séparant par un point-virgule.

    :::image type="content" source="media/redundancy-migration/request-live-migration-details-portal.png" alt-text="Capture d’écran montrant comment demander une migration dynamique, onglet Détails":::

1. Renseignez les informations supplémentaires requises sous l’onglet **Détails**, puis sélectionnez **Vérifier + créer** pour vérifier et envoyer votre ticket de support. Une personne du support vous contactera pour vous apporter l’aide dont vous aurez besoin.

> [!NOTE]
> Les partages de fichiers Premium sont disponibles uniquement pour les stockages LRS et ZRS.
>
> Les comptes de stockage GZRS ne prennent pas en charge le niveau archive pour le moment. Consultez [Niveaux d’accès Chaud, Froid et Archive pour les données d’objet blob](../blobs/access-tiers-overview.md) pour plus de détails.
>
> Les disques managés sont disponibles uniquement pour LRS, et ne peuvent pas être migrés vers un stockage redondant interzone (ZRS). Vous pouvez stocker des images et des instantanés de disques managés SSD Standard sur le stockage HDD Standard et [choisir entre les options LRS et ZRS](https://azure.microsoft.com/pricing/details/managed-disks/). Pour plus d’informations sur l’intégration avec des groupes à haute disponibilité, consultez [Introduction aux disques managés Azure](../../virtual-machines/managed-disks-overview.md#integration-with-availability-sets).

## <a name="switch-from-zrs-classic"></a>Basculer vers ZRS classique

> [!IMPORTANT]
> Le 31 mars 2021, Microsoft abandonnera et migrera les comptes ZRS classiques. Avant l’abandon de cette option, les clients du stockage ZRS classique recevront plus d’informations.
>
> Une fois que le stockage ZRS est mis à la disposition générale dans une région donnée, les clients ne peuvent plus créer de compte ZRS classiques à partir du Portail Azure dans cette région. L’utilisation de Microsoft PowerShell et d’Azure CLI pour créer des comptes ZRS classiques reste une option tant que le stockage ZRS classique n’est pas abandonné. Pour savoir où ZRS est disponible, consultez la rubrique [Redondance de Stockage Azure](storage-redundancy.md).

ZRS classique réplique les données de manière asynchrone entre les centres de données d’une à deux régions. Il se peut que des données répliquées soient indisponibles jusqu’à ce que Microsoft opère le basculement vers la région secondaire. Un compte ZRS classique ne peut pas être converti en ou à partir d’un stockage LRS, GRS ou RA-GRS. De plus, les comptes ZRS classiques ne prennent en charge ni les métriques, ni la journalisation.

ZRS classique est disponible uniquement pour les **objets blob de blocs** dans les comptes de stockage à usage général v1 (GPv1). Pour plus d’informations sur les comptes de stockage, consultez [Vue d’ensemble des comptes de stockage Azure](storage-account-overview.md).

Pour migrer manuellement des données de compte ZRS vers ou depuis un compte LRS, GRS, RA-GRS ou ZRS classique, servez-vous de l’un des outils suivants : AzCopy, Explorateur Stockage Azure, PowerShell ou Azure CLI. Vous pouvez également créer votre propre solution de migration avec l’une des bibliothèques clientes de Stockage Azure.

Vous pouvez également mettre à niveau votre compte de stockage ZRS classique vers ZRS à l’aide du Portail Azure, de PowerShell ou d’Azure CLI dans les régions où ZRS est disponible.

# <a name="portal"></a>[Portail](#tab/portal)

Pour mettre à niveau vers ZRS via le Portail Azure, accédez aux paramètres **Configuration** du compte, puis choisissez **Mettre à niveau** :

![Mettre à niveau ZRS classique vers ZRS via le portail](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour mettre à niveau vers ZRS à l’aide de PowerShell, appelez la commande suivante :

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour mettre à niveau vers ZRS à l’aide d’Azure CLI, appelez la commande suivante :

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Coûts associés à la modification du mode de réplication des données

Les coûts associés à la modification du mode de réplication des données dépendent de votre chemin de conversion. De la plus économique à la plus chère, les offres de redondance de Stockage Azure comprennent : LRS, ZRS, GRS, RA-GRS, GZRS et RA-GZRS.

Par exemple, une migration *à partir de* LRS vers un autre type de réplication implique des frais supplémentaires, car vous passez à un niveau de redondance plus sophistiqué. La migration *vers* un stockage GRS ou RA-GRS entraînera des frais de bande passante en sortie au moment de la migration, car la totalité de votre compte de stockage est répliquée dans la région secondaire. Toutes les écritures ultérieures dans la région primaire entraînent également des frais de bande passante en sortie pour répliquer l’écriture dans la région secondaire. Pour plus d’informations sur les coûts de bande passante, consultez la [page de tarification de Stockage Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Si vous migrez votre compte de stockage géoredondant en compte de stockage localement redondant, aucun coût supplémentaire ne vous est facturé, mais vos données répliquées sont supprimées de l’emplacement secondaire.

> [!IMPORTANT]
> Si vous migrez votre compte de stockage géographiquement redondant avec accès en lecture en compte de stockage géoredondant ou localement redondant, ce compte est facturé comme stockage géographiquement redondant avec accès en lecture pendant une période supplémentaire de 30 jours après la date de sa conversion.

## <a name="see-also"></a>Voir aussi

- [Redondance de Stockage Azure](storage-redundancy.md)
- [Vérifier la propriété Heure de la dernière synchronisation pour un compte de stockage](last-sync-time-get.md)
- [Utilisez la géo-redondance pour concevoir des applications hautement disponibles](geo-redundant-design.md)
