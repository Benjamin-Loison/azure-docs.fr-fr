---
title: Sauvegarder une application
description: Apprenez à créer des sauvegardes de vos applications Web dans Azure App Service. Exécutez des sauvegardes manuelles ou planifiées. Personnalisez les sauvegardes en incluant la base de données attachée.
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.topic: article
ms.date: 09/02/2021
ms.custom: seodec18
ms.openlocfilehash: f1bd37c1b3557a8106981377f9ed75c50a28c773
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433050"
---
# <a name="back-up-your-app-in-azure"></a>Sauvegarde de votre application dans Azure

La fonctionnalité de sauvegarde et de restauration [d’Azure App Service](overview.md) vous permet de créer facilement des sauvegardes d’applications manuelles ou planifiées. Vous pouvez configurer les sauvegardes pour qu’elles soient conservées pendant une durée indéfinie. Vous pouvez restaurer l’application d’après la capture instantanée d’un état précédent en remplaçant l’application existante ou en restaurant sur une autre application.

Pour plus d’informations sur la restauration d’une application à partir d’une sauvegarde, consultez [Restauration d’une application dans Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Éléments sauvegardés

App Service peut sauvegarder les informations suivantes dans un compte de stockage Azure et un conteneur pour l’utilisation desquels votre application a été configurée.

* la configuration d’une application ;
* le contenu d’un fichier ;
* la base de données connectée à votre application.

Les solutions de base de données suivantes sont prises en charge par la fonctionnalité de sauvegarde :

- [Base de données SQL](https://azure.microsoft.com/services/sql-database/)
- [Azure Database pour MySQL](https://azure.microsoft.com/services/mysql)
- [Base de données Azure pour PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL dans l’application](https://azure.microsoft.com/blog/mysql-in-app-preview-app-service/)

> [!NOTE]
> Chaque sauvegarde représente une copie hors connexion complète de votre application et non une mise à jour incrémentielle.
>

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Exigences et restrictions

* La fonctionnalité de sauvegarde et de restauration nécessite un plan App Service de niveau **Standard**, **Premium** ou **Isolé**. Pour plus d'informations sur la mise à l’échelle de votre plan App Service en vue d'utiliser un niveau plus élevé, consultez [Mise à l’échelle d’une application web dans Microsoft Azure App Service](manage-scale-up.md). Les niveaux **Premium** et **Isolé** permettent un plus grand nombre de sauvegardes quotidiennes que le niveau **Standard**.
* Vous avez besoin d’un compte de stockage Azure et d’un conteneur dans le même abonnement que l’application que vous souhaitez sauvegarder. Pour plus d’informations sur les comptes de stockage Azure, consultez [Vue d’ensemble des comptes de stockage Azure](../storage/common/storage-account-overview.md).
* Les sauvegardes peuvent atteindre jusqu’à 10 Go de contenu d’application et de base de données, jusqu’à 4 Go pouvant être la sauvegarde de base de données. Une erreur se produit si la taille de la sauvegarde dépasse cette limite.
* La sauvegarde [de la Base de données Azure pour MySQL avec TLS activé](../mysql/concepts-ssl-connection-security.md) n’est pas prise en charge. Si une sauvegarde est configurée, vous rencontrerez des échecs de sauvegarde.
* La sauvegarde [de la Base de données Azure pour PostgreSQL avec TLS activé](../postgresql/concepts-ssl-connection-security.md) n’est pas prise en charge. Si une sauvegarde est configurée, vous rencontrerez des échecs de sauvegarde.
* Les bases de données MySQL in-app sont automatiquement sauvegardées sans aucune configuration. Si vous définissez manuellement des paramètres des bases de données MySQL in-app, par exemple l’ajout de chaînes de connexion, il est possible que les sauvegardes ne fonctionnent pas correctement.
* L’utilisation d’un [compte de stockage avec pare-feu](../storage/common/storage-network-security.md)comme destination de vos sauvegardes n’est pas prise en charge. Si une sauvegarde est configurée, vous rencontrerez des échecs de sauvegarde.
* L’utilisation d’un [compte de stockage de point de terminaison privé activé](../storage/common/storage-private-endpoints.md) pour la sauvegarde et la restauration n’est pas prise en charge.

<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Création d’une sauvegarde manuelle

1. Dans le [portail Azure](https://portal.azure.com), accédez à la page de votre application, puis sélectionnez **Sauvegardes**. La page **Sauvegardes** s’affiche.

    ![Page Sauvegardes](./media/manage-backup/access-backup-page.png)

    > [!NOTE]
    > Si le message suivant s’affiche, cliquez dessus pour mettre à niveau votre plan App Service avant de pouvoir poursuivre les sauvegardes.
    > Pour plus d’informations, consultez [Faire monter en puissance une application web dans Azure](manage-scale-up.md).
    > :::image type="content" source="./media/manage-backup/upgrade-plan.png" alt-text="Capture d’écran d’une bannière contenant un message relatif à la mise à niveau du plan App Service afin d’accéder à la fonctionnalité de sauvegarde et de restauration.":::
    >
    >

2. Dans la page **Sauvegarde**, sélectionnez **La sauvegarde n’est pas configurée. Cliquez ici pour configurer la sauvegarde de votre application**.

    ![Cliquez sur Configurer](./media/manage-backup/configure-start.png)

3. Dans la page **Configuration de la sauvegarde**, cliquez sur **Stockage non configuré** pour configurer un compte de stockage.

    :::image type="content" source="./media/manage-backup/configure-storage.png" alt-text="Capture d’écran de la section Stockage de sauvegarde avec le paramètre Stockage non configuré sélectionné.":::

4. Choisissez la destination de sauvegarde en sélectionnant un **Compte de stockage** et un **Conteneur**. Ce compte de stockage doit relever du même abonnement que l’application que vous souhaitez sauvegarder. Si vous le souhaitez, vous pouvez créer un compte de stockage ou un conteneur dans les pages respectives. Quand vous avez terminé, cliquez sur **Sélectionner**.

5. Dans la page **Configuration de la sauvegarde** toujours ouverte, vous pouvez configurer **Base de données de sauvegarde**, sélectionner les bases de données que vous souhaitez inclure dans les sauvegardes (SQL Database ou MySQL), puis cliquer sur **OK**.

    :::image type="content" source="./media/manage-backup/configure-database.png" alt-text="Capture d’écran de la section Sauvegarde de la base de données avec la sélection Inclure dans la sauvegarde.":::

    > [!NOTE]
    > Pour qu’une base de données apparaisse dans cette liste, sa chaîne de connexion doit figurer dans la section **Chaînes de connexion** de la page **Paramètres d’application** de votre application. 
    >
    > Les bases de données MySQL in-app sont automatiquement sauvegardées sans aucune configuration. Si vous définissez manuellement des paramètres des bases de données MySQL in-app, par exemple l’ajout de chaînes de connexion, il est possible que les sauvegardes ne fonctionnent pas correctement.
    >
    >

6. Dans la page **Configuration de la sauvegarde**, cliquez sur **Enregistrer**.
7. Dans la page **Sauvegardes**, cliquez sur **Sauvegarde**.

    ![Bouton Backup Now](./media/manage-backup/manual-backup.png)

    Un message de progression s’affiche au cours du processus de sauvegarde.

Une fois le compte de stockage et le conteneur configurés, vous pouvez lancer une sauvegarde manuelle à tout moment. Les sauvegardes manuelles sont conservées indéfiniment.

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Configuration de sauvegardes automatisées

1. Dans la page **Configuration de la sauvegarde**, **activez** la **Sauvegarde planifiée**.

    ![Activation des sauvegardes automatisées](./media/manage-backup/scheduled-backup.png)

2. Configurez la planification des sauvegardes comme vous le souhaitez et sélectionnez **OK**.

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Configurer des sauvegardes partielles

Parfois, vous ne souhaitez pas sauvegarder tout le contenu de votre application. Voici quelques exemples :

* Vous [configurez une sauvegarde hebdomadaire](#configure-automated-backups) de votre application qui contient du contenu statique qui ne change jamais, comme des anciens billets de blog ou des images.
* Votre application contient plus de 10 Go de données (quantité maximale que vous pouvez sauvegarder à la fois).
* Vous ne souhaitez pas sauvegarder les fichiers journaux.

Les sauvegardes partielles vous permettent de choisir exactement les fichiers à sauvegarder.

> [!NOTE]
> Les bases de données individuelles de la sauvegarde peuvent atteindre 4 Go au maximum. Toutefois, la taille totale maximale de la sauvegarde est de 10 Go

### <a name="exclude-files-from-your-backup"></a>Exclusion de fichiers de votre sauvegarde

Supposons que vous avez une application qui contient des fichiers journaux et des images statiques créés à un moment donné et qui ne seront jamais modifiés. Dans ce cas, vous pouvez exclure ces fichiers et dossiers du stockage lors de vos sauvegardes futures. Pour exclure des fichiers et dossiers de vos sauvegardes, créez un fichier `_backup.filter` dans le dossier `D:\home\site\wwwroot` de votre application. Spécifiez la liste des fichiers et dossiers à exclure de ce fichier.

Vous pouvez accéder à vos fichiers via `https://<app-name>.scm.azurewebsites.net/DebugConsole`. Si vous y êtes invité, connectez-vous à votre compte Azure.

Identifiez les dossiers que vous souhaitez exclure de vos sauvegardes. Par exemple, si vous souhaitez exclure les fichiers et dossiers en surbrillance.

![Dossier images](./media/manage-backup/kudu-images.png)

Créez un fichier sous le nom `_backup.filter` et placez la liste précédente dans le fichier, mais supprimez `D:\home`. Listez un répertoire ou fichier par ligne. Par conséquent, le contenu du fichier doit ressembler à ce qui suit :

```
\site\wwwroot\Images\brand.png
\site\wwwroot\Images\2014
\site\wwwroot\Images\2013
```

Téléchargez le fichier `_backup.filter` vers le répertoire `D:\home\site\wwwroot\` de votre site en utilisant [ftp](deploy-ftp.md) ou toute autre méthode. Si vous le souhaitez, créez le fichier directement à l’aide de Kudu `DebugConsole` et ajoutez-y le contenu.

Exécutez des sauvegardes comme vous le faites normalement, [manuellement](#create-a-manual-backup) ou [automatiquement](#configure-automated-backups). Maintenant, tous les fichiers et dossiers spécifiés dans `_backup.filter` sont exclus des futures sauvegardes planifiées ou lancées manuellement.

> [!NOTE]
> Pour restaurer les sauvegardes partielles de votre site, procédez de la même façon que pour [restaurer une sauvegarde régulière](web-sites-restore.md). Le processus de restauration fait ce qu’il faut.
>
> Lorsqu'une sauvegarde complète est restaurée, tout le contenu sur le site est remplacé par tout ce qui se trouve dans la sauvegarde. Si un fichier se trouve sur le site, mais pas dans la sauvegarde, il est supprimé. Mais lorsqu’une sauvegarde partielle est restaurée, tout contenu qui se trouve dans l’un des répertoires limités, ou n’importe quel fichier limité, est conservé tel quel.
>

<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Mode de stockage des sauvegardes

Dès que vous avez effectué une ou plusieurs sauvegardes de votre application, celles-ci apparaissent dans la page **Conteneurs** de votre compte de stockage et dans votre application. Dans le compte de stockage, chaque sauvegarde se compose d’un fichier `.zip` et d’un fichier `.xml` contenant respectivement les données sauvegardées et un manifeste du contenu du fichier `.zip`. Vous pouvez décompresser et parcourir ces fichiers si vous souhaitez accéder à vos sauvegardes sans réellement effectuer une restauration d'application.

La sauvegarde de base de données pour l'application est stockée dans la racine du fichier .zip. Pour SQL Database, il s’agit d’un fichier BACPAC (pas d’extension de fichier) qui peut être importé. Pour créer une base de données dans Azure SQL Database en fonction de l’exportation de BACPAC, consultez [Importer un fichier BACPAC pour créer une base de données dans Azure SQL Database](../azure-sql/database/database-import.md).

> [!WARNING]
> Toute modification apportée aux fichiers de votre conteneur **websitebackups** peut invalider la sauvegarde et la rendre impossible à restaurer.

## <a name="troubleshooting"></a>Résolution des problèmes

La page **Sauvegardes** vous montre l’état de chaque sauvegarde. Si vous cliquez sur une sauvegarde qui a échoué, vous pouvez obtenir les détails du journal concernant l’échec. Utilisez le tableau suivant pour vous aider à résoudre des problèmes sur votre sauvegarde. Si l’échec n’est pas documenté dans le tableau, ouvrez un ticket de support.

| Erreur | Correction |
| - | - |
| Échec de l'accès au stockage. | Supprimez la planification de sauvegarde et reconfigurez-la. Vous pouvez également reconfigurer le stockage de sauvegarde. |
| La taille du site web et de la base de données dépasse la limite de {0} Go pour les sauvegardes. La taille de votre contenu est de {1} Go. | [Excluez certains fichiers](#configure-partial-backups) de la sauvegarde ou supprimez la partie base de données de la sauvegarde et utilisez plutôt des sauvegardes proposées en externe. |
| Une erreur s’est produite lors de la connexion à la base de données {0} sur le serveur {1}: Échec de l’authentification sur l’hôte '{1}' pour l’utilisateur '\<username>' à l’aide de la méthode 'mysql_native_password' avec le message : Base de données inconnue '\<db-name>' | Mettez à jour la chaîne de connexion à la base de données. |
| Impossible de résoudre {0}. {1} (CannotResolveStorageAccount) | Supprimez la planification de sauvegarde et reconfigurez-la. |
| Échec de la connexion pour l’utilisateur '{0}'. | Mise à jour de la chaîne de connexion de base de données. |
| La création d’une copie de base de données de {0} ({1}) a levé une exception. Impossible de créer une copie de base de données. | Utilisez un utilisateur administrateur dans la chaîne de connexion. |
| Le principal de serveur « \<name> » ne peut pas accéder à la base de données « master » dans le contexte de sécurité actuel. Impossible d’ouvrir la base de données « Master » demandée par la connexion. La connexion a échoué. Échec de la connexion pour l’utilisateur '\<name>'. | Utilisez un utilisateur administrateur dans la chaîne de connexion. |
| Une erreur liée au réseau ou propre à une instance s’est produite lors de l’établissement d’une connexion à SQL Server. Le serveur est introuvable ou inaccessible. Vérifiez que le nom de l’instance est correct et que SQL Server est configuré pour autoriser les connexions à distance. (fournisseur : Fournisseur de canaux nommés, erreur : 40 - Impossible d’ouvrir une connexion à SQL Server). | Vérifiez que la chaîne de connexion est valide. Autorisez les [adresses IP sortantes](overview-inbound-outbound-ips.md) de l’application dans les paramètres du serveur de base de données. |
| Impossible d’ouvrir le serveur « \<name> » demandé par la connexion. La connexion a échoué. | Vérifiez que la chaîne de connexion est valide. |
| Paramètres obligatoires manquants pour la signature d’accès partagé valide. | Supprimez la planification de sauvegarde et reconfigurez-la. |
| Connexion SSL obligatoire. Veuillez spécifier les options SSL puis réessayez. Lors d’une tentative de connexion. | Utilisez plutôt la fonctionnalité de sauvegarde intégrée dans Azure MySQL ou Azure PostgresSQL. |

## <a name="automate-with-scripts"></a>Automatiser des tâches à l’aide de scripts

Vous pouvez automatiser la gestion des sauvegardes à l’aide de scripts, en utilisant [Azure CLI](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/).

Pour obtenir des exemples, consultez :

- [Exemples d’interface de ligne de commande Azure](samples-cli.md)
- [Exemples Azure PowerShell](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la restauration d’une application à partir d’une sauvegarde, consultez [Restauration d’une application dans Azure](web-sites-restore.md).
