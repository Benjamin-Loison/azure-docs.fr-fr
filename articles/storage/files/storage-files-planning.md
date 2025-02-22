---
title: Planification d’un déploiement Azure Files | Microsoft Docs
description: Comprendre la planification d’un déploiement Azure Files. Vous pouvez soit directement monter un partage de fichiers Azure, soit mettre en cache un partage de fichiers Azure local avec Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 407d3c8d14cec2a55a9a33d58dfa1af77b8266b7
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522837"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planification d’un déploiement Azure Files
Le service [Azure Files](storage-files-introduction.md) peut être déployé principalement de deux façons : en montant directement les partages de fichiers Azure serverless, ou en mettant en cache les partages de fichiers Azure en local avec Azure File Sync. L'option de déploiement que vous choisissez détermine les éléments à prendre en compte lors de la planification de votre déploiement. 

- **Montage direct d'un partage de fichiers Azure** : Étant donné qu’Azure Files fournit un accès SMB (Server Message Block) ou NFS (Network File System), vous pouvez monter des partages de fichiers Azure localement ou dans le cloud à l’aide des clients SMB ou NFS standard disponibles dans votre système d’exploitation. Dans la mesure où les partages de fichiers Azure sont serverless, vous n'avez aucun serveur de fichiers ou appareil NAS à gérer lors des déploiements liés à des scénarios de production. Concrètement, cela signifie que vous n'avez aucun correctif logiciel à appliquer ni aucun disque physique à remplacer. 

- **Mise en cache d'un partage de fichiers Azure localement à l'aide d'Azure File Sync** : Azure File Sync vous permet de centraliser les partages de fichiers de votre organisation dans Azure Files, tout en conservant la flexibilité, le niveau de performance et la compatibilité d'un serveur de fichiers local. Azure File Sync transforme une instance Windows Server locale (ou cloud) en un cache rapide de votre partage de fichiers SMB Azure. 

Cet article traite principalement de considérations relatives au déploiement, afin de déployer un partage de fichiers Azure en vue de son montage directement par un client local ou un client cloud. Pour planifier un déploiement d’Azure File Sync, consultez [Planification d’un déploiement Azure File Sync](../file-sync/file-sync-planning.md).

## <a name="available-protocols"></a>Protocoles disponibles
Azure Files propose deux protocoles standard pour le montage d’un partage de fichiers Azure : le protocole [SMB](files-smb-protocol.md) et le protocole [NFS](files-nfs-protocol.md). Azure Files vous permet de choisir le protocole de système de fichiers le mieux adapté à votre charge de travail. Vous ne pouvez pas utiliser à la fois le protocole SMB et le protocole NFS dans un même partage de fichiers Azure. Cependant, vous pouvez créer des partages de fichiers SMB et NFS au sein d’un même compte de stockage. NFS 4.1 n’est pris en charge que par le nouveau type de compte de stockage **FileStorage** (partages de fichiers Premium uniquement).

Avec les partages de fichiers SMB et NFS, Azure Files propose des partages de fichiers d’entreprise qui peuvent être mis à l’échelle pour répondre à vos besoins de stockage et qui permettent l’accès simultané de milliers de clients.

| Fonctionnalité | SMB | NFS |
|---------|-----|---------------|
| Versions des protocoles prises en charge | SMB 3.1.1, SMB 3.0, SMB 2.1 | NFS 4.1 |
| Système d’exploitation recommandé | <ul><li>Windows 10, version 21H1+</li><li>Windows Server 2019+</li><li>Noyau Linux version 5.3+</li></ul> | Noyau Linux version 4.3+ |
| [Niveaux disponibles](storage-files-planning.md#storage-tiers)  | Premium, transaction optimisée, niveaux d’accès chaud et froid | Premium |
| Modèle de facturation | <ul><li>[Capacité provisionnée pour les partages de fichiers Premium](./understanding-billing.md#provisioned-model)</li><li>[Paiement à l’utilisation pour les partages de fichiers standard](./understanding-billing.md#pay-as-you-go-model)</li></ul> | [Capacité allouée](./understanding-billing.md#provisioned-model) |
| [Redondance](storage-files-planning.md#redundancy) | LRS, ZRS, GRS, GZRS | LRS, ZRS |
| Sémantique du système de fichiers | Win32 | POSIX |
| Authentification | Authentification basée sur l’identité (Kerberos), authentification par clé partagée (NTLMv2) | Authentification basée sur l’hôte |
| Autorisation | Listes de contrôle d’accès de type Win32 | Autorisations de style UNIX |
| Respect de la casse | Non sensible à la casse, casse conservée | Respect de la casse |
| Suppression ou modification de fichiers ouverts | Avec verrou uniquement | Yes |
| Partage de fichiers | [Mode de partage Windows](/windows/win32/fileio/creating-and-opening-files) | Gestionnaire conseil de verrouillage réseau de plage d’octets |
| Prise en charge des liens physiques | Non prise en charge | Pris en charge |
| Prise en charge des liens symboliques | Non prise en charge | Pris en charge |
| Accessible par Internet (facultatif) | Oui (SMB 3.0+ uniquement) | No |
| Prend en charge FileREST | Yes | Sous-ensemble : <br /><ul><li>[Opérations sur `FileService`](/rest/api/storageservices/operations-on-the-account--file-service-)</li><li>[Opérations sur `FileShares`](/rest/api/storageservices/operations-on-shares--file-service-)</li><li>[Opérations sur `Directories`](/rest/api/storageservices/operations-on-directories)</li><li>[Opérations sur `Files`](/rest/api/storageservices/operations-on-files)</li></ul> |

## <a name="management-concepts"></a>Concepts de gestion
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Lorsque vous déployez des partages de fichiers Azure dans des comptes de stockage, tenez compte des recommandations suivantes :

- Déployez uniquement des partages de fichiers Azure dans des comptes de stockage ayant d’autres partages de fichiers Azure. Bien que les comptes de stockage GPv2 vous permettent de disposer de comptes de stockage mixte, à partir du moment où les ressources de stockage (telles que les partages de fichiers Azure et les conteneurs d’objets BLOB) partagent les limites du compte de stockage, la combinaison des ressources peut compliquer la résolution des problèmes de performances par la suite. 

- Faites attention aux limites d’IOPS d’un compte de stockage lors du déploiement des partages de fichiers Azure. Dans l'idéal, une correspondance 1:1 doit être respectée entre les partages de fichiers et les comptes de stockage, mais cela n'est pas toujours possible en raison des différentes limites et restrictions imposées par votre organisation et Azure. S’il n’est pas possible d’avoir un seul partage de fichiers déployé dans un compte de stockage, tenez compte des partages qui seront très actifs et des partages qui le seront moins, afin de garantir que les partages de fichiers les plus sollicités ne soient pas mis ensemble dans le même compte de stockage.

- Déployez uniquement des comptes GPv2 et FileStorage, et mettez à niveau les comptes de stockage GPv1 et Classic lorsque vous les trouvez dans votre environnement. 

## <a name="identity"></a>Identité
Pour accéder à un partage de fichiers Azure, l’utilisateur du partage de fichiers doit être authentifié et disposer de l’autorisation d’accéder au partage. Cette opération s’effectue en fonction de l’identité de l’utilisateur accédant au partage de fichiers. Azure Files intègre trois fournisseurs d’identité principaux :
- **Active Directory Domain Services en local (AD DS ou AD DS en local)**  : Les comptes de stockage Azure peuvent être joints à un domaine sur une instance Active Directory Domain Services appartenant à un client, exactement comme un serveur de fichiers Windows Server ou un appareil NAS. Votre contrôleur de domaine peut être déployé localement, dans une machine virtuelle Azure, ou même en tant que machine virtuelle dans un autre fournisseur de cloud. Azure Files ne dépend pas de l’emplacement où votre contrôleur de domaine est hébergé. Dès lors qu’un compte de stockage est joint à un domaine, l’utilisateur final peut monter un partage de fichiers avec le compte d’utilisateur dont il s’est servi pour se connecter à son PC. L’authentification basée sur Active Directory utilise le protocole d’authentification Kerberos.
- **Azure Active Directory Domain Services (Azure AD DS)** . Azure AD DS fournit un contrôleur de domaine géré par Microsoft et pouvant être utilisé pour les ressources Azure. Joindre le domaine de votre compte de stockage à Azure AD DS offre des avantages similaires à le joindre à une instance Active Directory détenue par le client. Cette option de déploiement est particulièrement utile pour les scénarios lift-and-shift d’application qui nécessitent des autorisations basées sur AD. Étant donné qu’Azure AD DS fournit une authentification basée sur Active Directory, cette option utilise également le protocole d’authentification Kerberos.
- **Clé du compte de Stockage Azure** : Les partages de fichiers Azure peuvent également être montés à l’aide d’une clé de compte de stockage Azure. Pour monter un partage de fichiers de cette façon, le nom du compte de stockage est utilisé comme nom d’utilisateur, et la clé du compte de stockage comme mot de passe. L’utilisation de la clé de compte de stockage pour monter le partage de fichiers Azure est en fait une opération d’administrateur, car le partage de fichiers monté disposera d’autorisations complètes sur tous les fichiers et dossiers du partage, même s’ils ont des listes de contrôle d’accès. Lors de l’utilisation de la clé de compte de stockage pour le montage sur SMB, le protocole d’authentification NTLMv2 est utilisé.

Pour les clients qui migrent à partir de serveurs de fichiers locaux, ou qui créent dans Azure Files de nouveaux partages de fichiers destinés à se comporter comme des serveurs de fichiers Windows ou des appliances NAS, le fait de joindre le domaine de votre compte de stockage à l’instance **Active Directory appartenant au client** représente l’option recommandée. Pour en savoir plus sur la jonction du domaine de votre compte de stockage à une instance Active Directory détenue par le client, consultez [Vue d’ensemble Azure Files Active Directory](storage-files-active-directory-overview.md).

Si vous envisagez d’utiliser la clé de compte de stockage pour accéder à vos partages de fichiers Azure, nous vous recommandons d’utiliser des points de terminaison de service, comme décrit à la section [Mise en réseau](#networking).

## <a name="networking"></a>Mise en réseau
Les partages de fichiers Azure sont accessibles de n’importe quel endroit via le point de terminaison public du compte de stockage. Ainsi, les demandes authentifiées, comme celles qui sont autorisées par l’identité d’ouverture de session d’un utilisateur, peuvent provenir de manière sécurisée, de l’intérieur comme de l’extérieur d’Azure. Dans de nombreux environnements de clients, un montage initial du partage de fichiers Azure sur votre station de travail locale échouera, alors même que les montages à partir de machines virtuelles Azure réussissent. L’explication tient au fait que de nombreuses organisations et autres fournisseurs de services Internet bloquent le port dont se sert SMB pour communiquer, à savoir le port 445. Pour afficher le récapitulatif des FAI qui autorisent ou interdisent l’accès depuis le port 445, consultez [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Pour débloquer l’accès à votre partage de fichiers Azure, vous avez principalement deux options à votre disposition :

- Débloquer le port 445 pour le réseau local de votre organisation. Les partages de fichiers Azure sont uniquement accessibles en externe via le point de terminaison public utilisant des protocoles sûrs, comme SMB 3.x et l’API FileREST. Il s’agit du moyen le plus simple pour accéder à votre partage de fichiers Azure localement, car il ne nécessite pas de configuration réseau avancée au-delà de la modification des règles du port de sortie de votre organisation. Toutefois, nous vous recommandons de supprimer les versions héritées et dépréciées du protocole SMB, à savoir SMB 1.0. Pour savoir comment faire, consultez [Sécurisation de Windows/Windows Server](/windows-server/storage/file-server/troubleshoot/detect-enable-and-disable-smbv1-v2-v3) et [Sécurisation de Linux](files-remove-smb1-linux.md).

- Accéder aux partages de fichiers Azure via une connexion ExpressRoute ou VPN. Lorsque vous accédez à votre partage de fichiers Azure via un tunnel réseau, vous pouvez monter votre partage de fichiers Azure comme un partage de fichiers en local, car le trafic SMB ne traverse pas les limites de votre organisation.   

Bien que d’un point de vue technique il soit beaucoup plus facile de monter vos partages de fichiers Azure via le point de terminaison public, nous pensons que la plupart des clients choisiront de monter leurs partages de fichiers Azure sur une connexion ExpressRoute ou VPN. Le montage avec ces options est possible avec les partages SMB et NFS. Pour ce faire, vous devez configurer les éléments suivants pour votre environnement :  

- **Tunneling réseau à l’aide d’un VPN ExpressRoute, de site à site ou de point à site**. Le tunneling dans un réseau virtuel permet d’accéder aux partages de fichiers Azure depuis l’environnement local, même si le port 445 est bloqué.
- **Points de terminaison privés**. Les points de terminaison privés attribuent à votre compte de stockage une adresse IP dédiée depuis l’espace d’adressage du réseau virtuel. Le tunneling réseau est ainsi possible sans avoir à ouvrir de réseaux locaux sur la totalité des plages d’adresses IP détenues par les clusters de stockage Azure. 
- **Transfert DNS** : Configurez votre DNS local afin de résoudre le nom de votre compte de stockage (`storageaccount.file.core.windows.net` pour les régions du cloud public) sur l’adresse IP de vos points de terminaison privés.

> [!Important]  
> Azure Files prend en charge plusieurs options de routage réseau. L’option par défaut, routage Microsoft, fonctionne avec toutes les configurations d’Azure Files. L’option de routage Internet ne prend pas en charge les scénarios de jonction de domaine AD ni Azure File Sync.

Pour planifier la mise en réseau associée au déploiement d’un partage de fichiers Azure, consultez [Considérations relatives à la mise en réseau Azure Files](storage-files-networking-overview.md).

## <a name="encryption"></a>Chiffrement
Azure Files prend en charge deux types de chiffrement : le chiffrement en transit, qui se rapporte au chiffrement utilisé lors du montage/de l’accès au partage de fichiers Azure, et le chiffrement au repos, qui a trait à la façon dont les données sont chiffrées lorsqu’elles sont stockées sur le disque. 

### <a name="encryption-in-transit"></a>Chiffrement en transit

> [!IMPORTANT]
> Cette section traite en détail du chiffrement en transit pour les partages SMB. Pour plus d’informations sur le chiffrement en transit avec les partages NFS, consultez [Sécurité et réseau](files-nfs-protocol.md#security-and-networking).

Par défaut, le chiffrement en transit est activé pour tous les comptes de stockage Azure. Cela signifie que, quand vous montez un partage de fichiers sur SMB ou y accédez en utilisant le protocole FileREST (par exemple, via le portail Azure, PowerShell/CLI ou des kits SDK Azure), Azure Files autorise la connexion uniquement si elle est établie à l’aide des protocoles SMB 3.x avec chiffrement ou HTTPS. Les clients qui ne prennent pas en charge le protocole SMB 3.x, ou qui prennent en charge le protocole SMB 3.x mais pas le chiffrement SMB, ne peuvent pas monter le partage de fichiers Azure si le chiffrement en transit est activé. Pour plus d’informations sur les systèmes d’exploitation prenant en charge SMB 3.x avec chiffrement, consultez notre documentation détaillée pour [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) et [Linux](storage-how-to-use-files-linux.md). Toutes les versions actuelles de PowerShell, de CLI et des SDK prennent en charge le protocole HTTPS.  

Vous pouvez désactiver le chiffrement en transit pour un compte de stockage Azure. Quand le chiffrement est désactivé, Azure Files autorise également les protocoles SMB 2.1 et SMB 3.x sans chiffrement, ainsi que les appels d’API FileREST non chiffrés via le protocole HTTP. La principale raison justifiant de désactiver le chiffrement en transit est la nécessité de prendre en charge une application héritée devant être exécutée sur un système d’exploitation plus ancien, tel que Windows Server 2008 R2 ou une distribution Linux non récente. Azure Files n’autorise que les connexions SMB 2.1 au sein de la même région Azure que le partage de fichiers Azure. Ainsi, un client SMB 2.1 situé en dehors de la région Azure dans laquelle se trouve le partage de fichiers Azure, par exemple, localement ou dans une autre région Azure, ne peut pas accéder au partage de fichiers.

Nous recommandons fortement de vérifier que le chiffrement des données en transit est activé.

Pour plus d’informations sur le chiffrement en transit, voir [ Exiger un transfert sécurisé dans Stockage Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Chiffrement au repos
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>Protection de données
Azure Files adopte une approche multicouche pour garantir que vos données sont sauvegardées, récupérables et protégées contre les menaces de sécurité.

### <a name="soft-delete"></a>Suppression réversible
La suppression réversible pour les partages de fichiers (préversion) est un paramètre de niveau de compte de stockage qui vous permet de récupérer votre partage de fichiers en cas de suppression accidentelle. Lors de la suppression d’un partage de fichiers, celui-ci passe par un état transitoire de suppression réversible au lieu d’être supprimé définitivement. Vous pouvez configurer la durée pendant laquelle les données supprimées de manière réversible sont récupérables avant leur suppression définitive, et annuler la suppression du partage à tout moment lors de la période de rétention. 

Nous vous recommandons d’activer la suppression réversible pour la plupart des partages de fichiers. Si vous avez un flux de travail dans lequel la suppression de partage est courante et attendue, vous pouvez décider de configurer une période de rétention brève ou de ne pas activer la suppression réversible.

Pour plus d’informations sur la suppression réversible, consultez [Prévenir les suppressions de données accidentelles](./storage-files-prevent-file-share-deletion.md).

### <a name="backup"></a>Sauvegarde
Vous pouvez sauvegarder votre partage de fichiers Azure via des [captures instantanées de partage](./storage-snapshots-files.md), qui sont des copies en lecture seule de votre partage à un instant dans le passé. Les captures instantanées sont incrémentielles, ce qui signifie qu’elles ne contiennent que les données modifiées depuis la capture instantanée précédente. Vous pouvez avoir jusqu’à 200 captures instantanées par partage de fichiers et les conserver pendant jusqu’à 10 ans. Vous pouvez soit prendre manuellement ces captures instantanées dans le portail Azure, via PowerShell ou l’interface de ligne de commande (CLI), soit d’utiliser [Sauvegarde Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Les captures instantanées sont stockées dans votre partage de fichiers, ce qui signifie que, si vous supprimez votre partage de fichiers, vos captures instantanées sont également supprimées. Pour protéger vos sauvegardes de captures instantanées contre des suppressions accidentelles, assurez-vous que la suppression réversible est activée pour votre partage.

La [Sauvegarde Azure pour les partages de fichiers Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) gère la planification et la rétention des captures instantanées. Ses fonctionnalités grand-père-père-fils vous permettent de prendre des captures instantanées quotidiennes, hebdomadaires, mensuelles et annuelles, dont les périodes de rétention diffèrent. Le service Sauvegarde Azure orchestre également l’activation de la suppression réversible, et pose un verrou de suppression sur un compte de stockage dès qu’un partage de fichiers dans celui-ci est configuré pour la sauvegarde. Enfin, le service Sauvegarde Azure offre certaines fonctionnalités de surveillance et d’alerte clés, qui permettent aux clients d’avoir une vue consolidée de leur parc de sauvegarde.

Sauvegarde Azure vous permet d’effectuer des restaurations au niveau élément et au niveau partage dans le portail Azure. Il vous suffit de choisir le point de restauration (une capture instantanée particulière), le fichier ou répertoire particulier le cas échéant, puis l’emplacement (d’origine ou de remplacement) sur lequel vous souhaitez effectuer la restauration. Le service de sauvegarde gère la copie des données de captures instantanées et affiche la progression de la restauration dans le portail.

Pour plus d’informations sur la sauvegarde, consultez [À propos de la sauvegarde des partages de fichiers Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="protect-azure-files-with-microsoft-defender-for-storage"></a>Protéger Azure Files avec Microsoft defender pour Stockage
Microsoft Defender for Stockage fournit une couche supplémentaire d’intelligence de sécurité qui génère des alertes lorsqu’il détecte une activité anormale sur votre compte de stockage, par exemple des tentatives d’accès inhabituelles. Il exécute également une analyse de réputation du hachage de programme malveillant et déclenche une alerte concernant les programmes malveillants connus. Vous pouvez configurer Microsoft Defender pour Stockage au niveau de l’abonnement ou du compte de stockage via Microsoft Defender pour le Cloud.

Pour plus d’informations, consultez [Présentation d’Azure Defender pour le Stockage](../../security-center/defender-for-storage-introduction.md).

## <a name="storage-tiers"></a>Niveaux de stockage
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

#### <a name="limitations"></a>Limites
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redondance
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migration
Dans de nombreux cas, vous n’établirez pas de nouveau partage de fichiers net pour votre organisation, mais migrerez plutôt un partage de fichiers existant, depuis un serveur de fichiers local ou un appareil NAS, vers Azure Files. Le choix de la stratégie et de l’outil de migration appropriés pour votre scénario est important pour la réussite de votre migration. 

L’[article vue d’ensemble de la migration](storage-files-migration-overview.md) aborde brièvement les bases et contient un tableau qui vous amène à des guides de migration susceptibles de couvrir votre scénario.

## <a name="next-steps"></a>Étapes suivantes
* [Planification d’un déploiement Azure File Sync](../file-sync/file-sync-planning.md)
* [Déploiement d’Azure Files](./storage-how-to-create-file-share.md)
* [Déploiement d’Azure File Sync](../file-sync/file-sync-deployment-guide.md)
* [Consultez l’article de vue d’ensemble de la migration pour trouver le guide de migration de votre scénario](storage-files-migration-overview.md)
