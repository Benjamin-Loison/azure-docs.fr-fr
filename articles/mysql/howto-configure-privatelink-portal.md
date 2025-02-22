---
title: Private Link - Portail Azure - Azure Database pour MySQL
description: Découvrez comment configurer une instance Private Link pour Azure Database pour MySQL à partir du portail Azure
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 01/09/2020
ms.openlocfilehash: 26b2bc1d2ba1d31aaa7269b3fdb861b69ce70eac
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122641130"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-using-portal"></a>Créer et gérer une liaison privée pour Azure Database pour MySQL en utilisant le portail

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Private Endpoint est le composant fondamental de Private Link dans Azure. Il permet à des ressources Azure, comme des machines virtuelles, de communiquer en privé avec des ressources Private Link. Dans cet article, vous apprendrez à utiliser le portail Azure pour créer une machine virtuelle dans un réseau virtuel Azure et un serveur Azure Database pour MySQL avec un point de terminaison privé Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!NOTE]
> La fonctionnalité de lien privé est disponible uniquement pour les serveurs Azure Database pour MySQL dans les niveaux tarifaires Usage général ou Mémoire optimisée. Vérifiez que le serveur de base de données se trouve dans l’un de ces niveaux tarifaires.

## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Création d’une machine virtuelle Azure

Dans cette section, vous allez créer un réseau virtuel et le sous-réseau pour héberger la machine virtuelle qui est utilisée pour accéder à votre ressource Private Link (un serveur MySQL dans Azure).

### <a name="create-the-virtual-network"></a>Créer un réseau virtuel
Dans cette section, vous allez créer un réseau virtuel et le sous-réseau pour héberger la machine virtuelle qui est utilisée pour accéder à votre ressource de liaison privée.

1. Dans le coin supérieur gauche de l’écran, sélectionnez **Créer une ressource** > **Mise en réseau** > **Réseau virtuel**.
2. Dans **Créer un réseau virtuel**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez *MyVirtualNetwork*. |
    | Espace d’adressage | Entrez *10.1.0.0/16*. |
    | Abonnement | Sélectionnez votre abonnement.|
    | Resource group | Sélectionnez **Créer nouveau**, entrez *myResourceGroup* et sélectionnez **OK**. |
    | Emplacement | Sélectionnez **Europe Ouest**.|
    | Sous-réseau - Nom | Entrez *mySubnet*. |
    | Plage d’adresses du sous-réseau | Entrez *10.1.0.0/24*. |
    |||
3. Conservez les autres valeurs par défaut, puis sélectionnez **Créer**.

### <a name="create-virtual-machine"></a>Créer une machine virtuelle

1. En haut à gauche de l’écran du portail Azure, sélectionnez **Créer une ressource** > **Calcul** > **Machine virtuelle**.

2. Dans **Créer une machine virtuelle - Notions de base**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **DÉTAILS DU PROJET** | |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. Vous avez créé cela dans la section précédente.  |
    | **DÉTAILS DE L’INSTANCE** |  |
    | Nom de la machine virtuelle | Entrez *myVm*. |
    | Région | Sélectionnez **Europe Ouest**. |
    | Options de disponibilité | Conservez la valeur par défaut **Aucune redondance d’infrastructure nécessaire**. |
    | Image | Sélectionnez **Windows Server 2019 Datacenter**. |
    | Taille | Conservez la valeur par défaut **Standard DS1 v2**. |
    | **COMPTE ADMINISTRATEUR** |  |
    | Nom d’utilisateur | Entrez un nom d’utilisateur de votre choix. |
    | Mot de passe | Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.yml?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm-).|
    | Confirmer le mot de passe | Retapez le mot de passe. |
    | **RÈGLES DES PORTS D’ENTRÉE** |  |
    | Aucun port d’entrée public | Conservez la valeur par défaut **Aucun**. |
    | **ÉCONOMISEZ DE L’ARGENT** |  |
    | Vous disposez déjà d’une licence Windows ? | Conservez la valeur par défaut **Non**. |
    |||

1. Sélectionnez **Suivant : Disques**.

1. Dans **Créer une machine virtuelle - Disks**, conservez les valeurs par défaut et sélectionnez **Suivant : Mise en réseau**.

1. Dans **Créer une machine virtuelle - Mise en réseau**, sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Réseau virtuel | Conservez la valeur par défaut, **MyVirtualNetwork**.  |
    | Espace d’adressage | Conservez la valeur par défaut, **10.1.0.0/24**.|
    | Subnet | Conservez la valeur par défaut, **mySubnet (10.1.0.0/24)** .|
    | Adresse IP publique | Conservez la valeur par défaut **(new) myVm-ip**. |
    | Aucun port d’entrée public | Sélectionnez **Autoriser les ports sélectionnés**. |
    | Sélectionner des ports d’entrée | Sélectionnez **HTTP** et **RDP**.|
    |||


1. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page **Vérifier + créer** où Azure valide votre configuration.

1. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer**.

## <a name="create-an-azure-database-for-mysql"></a>Créer une base de données Azure pour MySQL

Dans cette section, vous allez créer un serveur Azure Database pour MySQL dans Azure. 

1. En haut à gauche de l’écran du portail Azure, sélectionnez **Créer une ressource** > **Bases de données** > **Azure Database pour MySQL**.

1. Dans **Azure Database pour MySQL**, fournissez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** | |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. Vous avez créé cela dans la section précédente.|
    | **Détails du serveur** |  |
    |Nom du serveur  | Entrez *myServer*. Si ce nom est utilisé, créez un nom unique.|
    | Nom d’utilisateur administrateur| Entrez le nom d’un administrateur de votre choix. |
    | Mot de passe | Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 8 caractères et satisfaire aux exigences définies. |
    | Emplacement | Sélectionnez une région Azure dans laquelle vous souhaitez que votre serveur MySQL réside. |
    |Version  | Sélectionnez la version de base de données du serveur MySQL qui est requise.|
    | Calcul + Stockage| Sélectionnez le niveau tarifaire requis pour le serveur en fonction de la charge de travail. |
    |||
 
7. Sélectionnez **OK**. 
8. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page **Vérifier + créer** où Azure valide votre configuration. 
9. Quand le message Validation réussie s’affiche, sélectionnez **Créer**. 
10. Quand le message Validation réussie s’affiche, sélectionnez Créer. 

> [!NOTE]
> Il peut arriver que l’instance Azure Database pour MySQL et le sous-réseau de réseau virtuel se trouvent dans des abonnements différents. Dans ce cas, vous devez vérifier les configurations suivantes :
> - Assurez-vous que le fournisseur de ressources **Microsoft.DBforMySQL** est inscrit pour les deux abonnements. Pour plus d’informations, reportez-vous à [resource-manager-registration][resource-manager-portal]

## <a name="create-a-private-endpoint"></a>Créer un Private Endpoint

Dans cette section, vous allez créer un serveur MySQL et lui ajouter un point de terminaison privé. 

1. En haut à gauche de l’écran du portail Azure, sélectionnez **Créer une ressource** > **Mise en réseau** > **Liaison privée**.

2. Dans **Centre de liaisons privées - Vue d’ensemble**, dans l’option permettant de **Générer une connexion privée à un service**, sélectionnez **Démarrer**.

    :::image type="content" source="media/concepts-data-access-and-security-private-link/privatelink-overview.png" alt-text="Présentation de Private Link":::

1. Dans **Créer un point de terminaison privé – Informations de base**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** | |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. Vous avez créé cela dans la section précédente.|
    | **Détails de l’instance** |  |
    | Nom | Entrez *myPrivateEndpoint*. Si ce nom est utilisé, créez un nom unique. |
    |Région|Sélectionnez **Europe Ouest**.|
    |||

5. Sélectionnez **Suivant : Ressource**.
6. Dans **Créer un point de terminaison privé - Ressource**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    |Méthode de connexion  | Sélectionnez Se connecter à une ressource Azure dans mon répertoire.|
    | Abonnement| Sélectionnez votre abonnement. |
    | Type de ressource | Sélectionnez **Microsoft.DBforMySQL/servers**. |
    | Ressource |Sélectionnez *myServer*|
    |Sous-ressource cible |Sélectionnez *mysqlServer*|
    |||
7. Sélectionnez **Suivant : Configuration**.
8. Dans **Créer un point de terminaison privé – Configuration**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    |**MISE EN RÉSEAU**| |
    | Réseau virtuel| Sélectionnez *MyVirtualNetwork*. |
    | Subnet | Sélectionnez *mySubnet*. |
    |**INTÉGRATION À DNS PRIVÉ**||
    |Intégrer à une zone DNS privée |Sélectionnez **Oui**. |
    |Zone DNS privée |Sélectionnez *(New)privatelink.mysql.database.azure.com* |
    |||

    > [!Note] 
    > Utilisez la zone DNS privée prédéfinie pour votre service ou indiquez le nom de la zone DNS de votre choix. Pour plus d’informations, reportez-vous à la [Configuration de la zone DNS des services Azure](../private-link/private-endpoint-dns.md).

1. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page **Vérifier + créer** où Azure valide votre configuration. 
2. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer**. 

    :::image type="content" source="media/concepts-data-access-and-security-private-link/show-mysql-private-link.png" alt-text="Instance Private Link créée":::

    > [!NOTE] 
    > Le FQDN dans le paramètre DNS du client n’est pas résolu en adresse IP privée configurée. Vous devez configurer une zone DNS pour le FQDN configuré, comme indiqué [ici](../dns/dns-operations-recordsets-portal.md).

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Se connecter à une machine virtuelle à l’aide du Bureau à distance (RDP)


Après avoir créé **myVm**, connectez-vous à cette machine virtuelle à partir d’Internet comme suit : 

1. Dans la barre de recherche du portail, entrez *myVm*.

1. Sélectionnez le bouton **Connexion**. Après avoir sélectionné le bouton **Connecter**, **Se connecter à la machine virtuelle** s’ouvre.

1. Sélectionnez **Télécharger le fichier RDP**. Azure crée un fichier de protocole RDP (Remote Desktop Protocol) ( *.rdp*) et le télécharge sur votre ordinateur.

1. Ouvrez le fichier *downloaded.rdp*.

    1. Si vous y êtes invité, sélectionnez **Connexion**.

    1. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle.

        > [!NOTE]
        > Vous devrez peut-être sélectionner **Plus de choix** > **Utiliser un autre compte**, pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle.

1. Sélectionnez **OK**.

1. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Si vous recevez un avertissement de certificat, sélectionnez **Oui** ou **Continuer**.

1. Une fois que le bureau de la machine virtuelle s’affiche, réduisez-le pour revenir à votre poste de travail local.

## <a name="access-the-mysql-server-privately-from-the-vm"></a>Accéder au serveur MySQL en privé à partir de la machine virtuelle

1. Dans le Bureau à distance de *myVM*, ouvrez PowerShell.

2. Entrez `nslookup  myServer.privatelink.mysql.database.azure.com`. 

    Vous recevez un message similaire à celui ci :
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myServer.privatelink.mysql.database.azure.com
    Address:  10.1.3.4
    ```
    > [!NOTE]
    > Si l’accès public est désactivé dans les paramètres du pare-feu dans Azure Database pour MySQL – Serveur unique, ces tests Ping et Telnet réussissent indépendamment des paramètres du pare-feu. Ils garantissent la connectivité réseau.

3. Testez la connexion de liaison privée pour le serveur MySQL à l’aide de tout client disponible. Dans l’exemple ci-dessous, j’ai utilisé [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) pour effectuer l’opération.

4. Dans **Nouvelle connexion**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Type de serveur| Sélectionnez **MySQL**.|
    | Nom du serveur| Sélectionnez *myServer.privatelink.mysql.database.azure.com*. |
    | Nom d'utilisateur | Entrez le nom d’utilisateur au format username@servername qui est fourni lors de la création du serveur MySQL. |
    |Mot de passe |Entrez le mot de passe fourni lors de la création du serveur MySQL. |
    |SSL|Sélectionnez **Obligatoire**.|
    ||

5. Sélectionnez Se connecter.

6. Parcourez les bases de données dans le menu de gauche.

7. (Facultatif) Créez ou interrogez des informations à partir du serveur MySQL.

8. Fermez la connexion Bureau à distance à myVm.

## <a name="clean-up-resources"></a>Nettoyer les ressources
Lorsque vous avez fini d’utiliser le point de terminaison privé, le serveur MySQL et la machine virtuelle, supprimez le groupe de ressources et toutes les ressources qu’il contient :

1. Entrez *myResourceGroup* dans la zone **Recherche** en haut du portail, puis sélectionnez *myResourceGroup* dans les résultats de la recherche.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Entrez myResourceGroup dans **TAPER LE NOM DU GROUPE DE RESSOURCES**, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide pratique, vous avez créé une machine virtuelle sur un réseau virtuel, une instance Azure Database pour MySQL et un point de terminaison privé pour un accès privé. Vous vous êtes connecté à une machine virtuelle à partir d’Internet et avez communiqué de façon sécurisée avec le serveur MySQL via Azure Private Link. Pour plus d’informations sur les points de terminaison privés, consultez [Qu’est-ce qu’Azure Private Endpoint ?](../private-link/private-endpoint-overview.md).

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md