---
title: Résoudre les problèmes liés à Azure Data Share
description: Découvrez comment résoudre les problèmes avec les invitations et les erreurs rencontrées lorsque vous créez ou recevez des partages de données dans Azure Data Share.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 09/10/2021
ms.openlocfilehash: da746d14d891c875c9bed49c94e8b408d956e29e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124811251"
---
# <a name="troubleshoot-common-problems-in-azure-data-share"></a>Résoudre les problèmes courants dans Azure Data Share 

Cet article explique comment résoudre les problèmes courants dans Azure Data Share. 

## <a name="azure-data-share-invitations"></a>Invitations Azure Data Share 

Dans certains cas, lorsque de nouveaux utilisateurs sélectionnent **Accepter l’invitation** dans une invitation par e-mail, il est possible qu’une liste vide d’invitations s’affiche. Ce problème peut avoir l’une des causes suivantes :

* **Le service Azure Data Share n’est pas inscrit en tant que fournisseur de ressources d’un abonnement Azure dans le locataire Azure.** Ce problème se produit lorsque votre locataire Azure n’a pas de ressource Data Share. 

    Lorsque vous créez une ressource Azure Data Share, celle-ci inscrit automatiquement le fournisseur de ressources dans votre abonnement Azure. Vous pouvez inscrire manuellement le service Data Share en procédant comme suit. Pour effectuer ces étapes, vous devez disposer du [rôle Contributeur](../role-based-access-control/built-in-roles.md#contributor) pour l’abonnement Azure. 

    1. Dans le portail Azure, accédez à **Abonnements**.
    1. Sélectionnez l’abonnement à utiliser pour créer la ressource Azure Data Share.
    1. Sélectionner les **Fournisseurs de ressources**
    1. Recherchez **Microsoft.DataShare**.
    1. Sélectionnez **Inscription**.

* **L’invitation est envoyée à votre alias de messagerie au lieu de votre e-mail de connexion à Azure.** Si vous avez déjà enregistré le service Azure Data Share ou créé une ressource Data Share dans le locataire Azure, mais que vous ne voyez toujours pas l’invitation, votre alias de messagerie est peut être répertorié comme destinataire. Contactez votre fournisseur de données et assurez-vous que l’invitation sera envoyée à votre adresse e-mail de connexion à Azure et non à votre alias de messagerie.

* **L’invitation est déjà acceptée.** Le lien inclut dans l’e-mail vous amène à la page **Invitations Data Share** dans le portail Azure. Cette page répertorie uniquement les invitations en attente. Les invitations acceptées n’apparaissent pas sur la page. Pour afficher les partages reçus et configurer votre paramètre de cluster Azure Data Explorer cible, accédez à la ressource Data Share utilisée pour accepter l’invitation.

* **Vous êtes un utilisateur invité du locataire.** Si vous êtes un utilisateur invité du locataire, vous devez vérifier votre adresse de messagerie pour le locataire avant d’afficher l’invitation. Une fois la vérification effectuée, elle est valide pendant 12 mois. 

## <a name="creating-and-receiving-shares"></a>Création et réception de partages

Les erreurs suivantes peuvent s’afficher lorsque vous créez un nouveau partage, ajoutez des jeux de données ou mappez des jeux de données :

* Échec de l’ajout des jeux de données.
* Échec du mappage des jeux de données.
* Impossible d’accorder à la ressource Data Share x l’accès à y.
* Vous ne disposez pas des autorisations appropriées sur x.
* Nous n’avons pas pu ajouter d’autorisations d’écriture pour le compte Azure Data Share à une ou plusieurs de vos ressources sélectionnées.

L’une de ces erreurs peut s’afficher si vous ne disposez pas d’autorisations suffisantes sur le magasin de données Azure. Pour plus d’informations, consultez [Rôles et exigences](concepts-roles-permissions.md). 

Vous avez besoin de l’autorisation d’écriture pour partager ou recevoir des données à partir d’un magasin de données Azure. En règle générale, cette autorisation existe dans le rôle Contributeur. 

Si vous partagez ou recevez des données à partir du magasin de données Azure pour la première fois, vous devez également obtenir l’autorisation *Microsoft.Authorization/role assignments/write*. En règle générale, cette autorisation existe dans le rôle Propriétaire. Même si vous avez créé la ressource de magasin de données Azure, cela ne fait pas automatiquement de vous le propriétaire de la ressource. 

Une fois que vous avez les autorisations appropriées, le service Azure Data Share autorise automatiquement l’identité managée de la ressource de partage de données à accéder au magasin de données. Ce processus peut prendre quelques minutes. Si vous rencontrez un problème en raison de ce délai, réessayez après quelques minutes.

Le partage basé sur SQL nécessite des autorisations supplémentaires. Pour plus d’informations sur les prérequis, consultez [Partager à partir de sources SQL](how-to-share-from-sql.md).

## <a name="snapshots"></a>Instantanés
Un instantané peut échouer pour diverses raisons. Ouvrez un message d’erreur détaillé en sélectionnant l’heure de début de l’instantané, puis l’état de chaque jeu de données. 

Les instantanés échouent généralement pour les raisons suivantes :

* Data Share n’est pas autorisé à lire à partir du magasin de données source ou à écrire dans le magasin de données cible. Pour plus d’informations, consultez [Rôles et exigences](concepts-roles-permissions.md). Si vous prenez un instantané pour la première fois, la ressource Data Share peut nécessiter quelques minutes pour accéder au magasin de données Azure. Patientez quelques minutes, puis réessayez.
* La connexion de Data Share au magasin de données source ou cible est bloquée par un pare-feu.
* Un jeu de données partagé, un magasin de données source ou un magasin de données cible a été supprimé.

Pour les comptes de stockage, un instantané peut échouer parce qu’un fichier est en cours de mise à jour à la source pendant la prise de l’instantané. Par conséquent, un fichier de 0 octet peut apparaître dans la cible. Une fois la mise à jour à la source effectuée, les instantanés devraient s’effectuer correctement.

Pour les sources SQL, un instantané peut échouer pour les raisons suivantes :

* Le script SQL source ou le script SQL cible qui accorde l’autorisation Data Share n’a pas été exécuté. Ou, pour Azure SQL Database ou Azure Synapse Analytics (anciennement Azure SQL Data Warehouse), le script est exécuté à l’aide de l’authentification SQL au lieu de l’authentification Azure Active Directory. Vous pouvez exécuter la requête ci-dessous pour vérifier si le compte de partage de données dispose des autorisations appropriées sur la base de données SQL. Pour la base de données SQL source, le résultat de la requête doit indiquer que le compte de partage de données a le rôle *db_datareader*. Pour la base de données SQL cible, le résultat de la requête doit indiquer que le compte de partage de données possède des rôles *db_datareader*, *db_datawriter* et *db_dlladmin*.

    ```sql
        SELECT DP1.name AS DatabaseRoleName,
        isnull (DP2.name, 'No members') AS DatabaseUserName
        FROM sys.database_role_members AS DRM
        RIGHT OUTER JOIN sys.database_principals AS DP1
        ON DRM.role_principal_id = DP1.principal_id
        LEFT OUTER JOIN sys.database_principals AS DP2
        ON DRM.member_principal_id = DP2.principal_id
        WHERE DP1.type = 'R'
        ORDER BY DP1.name; 
     ``` 

* Le magasin de données source ou le magasin de données SQL cible est interrompu.
* Les types de données SQL ne sont pas pris en charge par le processus d’instantané ou le magasin de données cible. Pour plus d’informations, consultez [Partager à partir de sources SQL](how-to-share-from-sql.md#supported-data-types).
* Le magasin de données source ou le magasin de données SQL cible est verrouillé par d’autres processus. Azure Data Share ne verrouille pas ces magasins de données. Toutefois, les verrous existants sur ces magasins de données peuvent causer l’échec d’un instantané.
* La table SQL cible est référencée par une contrainte de clé étrangère. Pendant la prise d’un instantané, si une table cible porte le même nom qu’une table dans les données sources, Azure Data Share supprime la table et en crée une nouvelle. Si la table SQL cible est référencée par une contrainte de clé étrangère, elle ne peut pas être supprimée.
* Un fichier CSV cible est généré, mais les données ne peuvent pas être lues dans Excel. Ce problème peut apparaître lorsque la table SQL source contient des données qui incluent des caractères n’appartenant pas à l’alphabet latin. Dans Excel, sélectionnez l’onglet **Obtenir les données** et choisissez le fichier CSV. Sélectionnez l’origine du fichier **65001: Unicode (UTF-8)** , puis chargez les données.

## <a name="update-snapshot-schedule"></a>Mettre à jour la planification d’instantané
Une fois que le fournisseur de données a mis à jour la planification d’instantanés pour le partage envoyé, le consommateur de données doit désactiver la planification d’instantanés précédente et réactiver la planification d’instantanés mise à jour pour le partage reçu. La planification d’instantanés est stockée au format UTC et affichée dans l’interface utilisateur comme heure locale de l’ordinateur. Elle ne s’ajuste pas automatiquement à l’heure d’été.  

## <a name="in-place-sharing"></a>Partage en place
Le mappage de jeu de données peut échouer pour les clusters Explorateur de données Azure pour les raisons suivantes :

* L’utilisateur n’a pas d’autorisation en *écriture* sur le cluster Explorateur de données Azure. En règle générale, cette autorisation existe dans le rôle Contributeur. 
* Le cluster Explorateur de données Azure source ou cible est suspendu.
* Le cluster Explorateur de données Azure source est EngineV2 et la cible est EngineV3, ou vice versa. Le partage entre les clusters Explorateur de données Azure de différentes versions de moteur n’est pas pris en charge.

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment commencer à partager des données, passez au tutoriel [Partager des données](share-your-data.md). 

Pour découvrir comment recevoir des données, passez au tutoriel [Accepter et recevoir des données](subscribe-to-data-share.md).