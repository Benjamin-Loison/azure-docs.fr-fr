---
title: Se connecter à une machine virtuelle Linux avec SSH
titleSuffix: Azure Bastion
description: Découvrez comment utiliser Azure Bastion pour vous connecter à une machine virtuelle Linux avec SSH.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/12/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: d0c852cf6df97619f7a319da4e13a406c572d4e7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069289"
---
# <a name="create-an-ssh-connection-to-a-linux-vm-using-azure-bastion"></a>Créer une connexion SSH à une machine virtuelle Linux avec Azure Bastion

Cet article vous montre comment créer de manière sécurisée et fluide une connexion SSH à vos machines virtuelles Linux situées dans un réseau virtuel Azure, directement par le biais du portail Azure. Quand vous utilisez Azure Bastion, vos machines virtuelles n’ont pas besoin de client, d’agent ni de logiciel supplémentaire. Vous pouvez également vous connecter à une machine virtuelle Linux avec RDP. Pour plus d’informations, consultez [Créer une connexion RDP à une machine virtuelle Linux](bastion-connect-vm-rdp-linux.md).

Azure Bastion fournit une connectivité sécurisée à toutes les machines virtuelles du réseau virtuel dans lequel il est provisionné. Azure Bastion protège vos machines virtuelles contre l’exposition des ports RDP/SSH au monde extérieur, tout en fournissant un accès sécurisé à l’aide de RDP/SSH. Pour plus d’informations, consultez la page [Présentation d’Azure Bastion](bastion-overview.md).

Quand vous vous connectez à une machine virtuelle Linux avec SSH, vous pouvez utiliser une paire nom d’utilisateur-mot de passe et des clés SSH pour l’authentification. Vous pouvez vous connecter à votre machine virtuelle avec des clés SSH en utilisant :

* Une clé privée que vous entrez manuellement
* Un fichier qui contient les informations relatives à la clé privée

La clé privée SSH doit être dans un format qui commence par `"-----BEGIN RSA PRIVATE KEY-----"` et se termine par `"-----END RSA PRIVATE KEY-----"`.

## <a name="prerequisites"></a>Prérequis

Assurez-vous que vous avez configuré un hôte Azure Bastion pour le réseau virtuel dans lequel réside la machine virtuelle. Pour plus d’informations, consultez [Créer un hôte Azure Bastion](./tutorial-create-host-portal.md). Une fois que le service Bastion est approvisionné et déployé dans votre réseau virtuel, vous pouvez l’utiliser pour vous connecter à toutes les machines virtuelles dans ce réseau virtuel. 

### <a name="required-roles"></a>Rôles nécessaires

Pour établir une connexion, les rôles suivants sont nécessaires :

* Rôle de lecteur sur la machine virtuelle
* Rôle de lecteur sur la carte réseau avec adresse IP privée de la machine virtuelle
* Rôle de lecteur sur la ressource Azure Bastion

### <a name="ports"></a>Ports

Pour vous connecter à la machine virtuelle Linux par le biais du protocole SSH, les ports suivants doivent être ouverts sur votre machine virtuelle :

* Port d’entrée : SSH (22) ***ou***
* Port d’entrée : valeur personnalisée (vous devez spécifier ce port personnalisé quand vous vous connectez à la machine virtuelle avec Azure Bastion)

   > [!NOTE]
   > Si vous souhaitez spécifier une valeur de port personnalisée, vous devez configurer Azure Bastion avec le SKU Standard. Le SKU De base ne permet pas de spécifier des ports personnalisés.
   >

## <a name="connect-using-username-and-password"></a><a name="username"></a>Connexion : Utilisation du nom d’utilisateur et du mot de passe

1. Ouvrez le [portail Azure](https://portal.azure.com). Accédez à la machine virtuelle à laquelle vous souhaitez vous connecter, puis cliquez sur **Se connecter** et sélectionnez **Bastion** dans la liste déroulante.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/connect.png" alt-text="Capture d’écran montrant la vue d’ensemble d’une machine virtuelle dans le Portail Azure avec l’option Se connecter sélectionnée" lightbox="./media/bastion-connect-vm-ssh-linux/connect.png":::

1. Après avoir sélectionné Bastion, cliquez sur **Utiliser Bastion**. Si vous n’avez pas configuré Bastion pour le réseau virtuel, consultez [Configure Bastion (Configurer Bastion)](./quickstart-host-portal.md).
1. Dans la page **Se connecter à l’aide d’Azure Bastion**, entrez le **Nom d’utilisateur** et le **Mot de passe**.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/password.png" alt-text="Capture d’écran montrant Authentification par mot de passe.":::
1. Sélectionnez **Se connecter** pour vous connecter à la machine virtuelle.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Connexion : Entrez manuellement une clé privée

1. Ouvrez le [portail Azure](https://portal.azure.com). Accédez à la machine virtuelle à laquelle vous souhaitez vous connecter, puis cliquez sur **Se connecter** et sélectionnez **Bastion** dans la liste déroulante.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/connect.png" alt-text="Capture d’écran de la vue d’ensemble d’une machine virtuelle dans Portail Azure avec l’option Se connecter sélectionnée." lightbox="./media/bastion-connect-vm-ssh-linux/connect.png":::
1. Après avoir sélectionné Bastion, cliquez sur **Utiliser Bastion**. Si vous n’avez pas configuré Bastion pour le réseau virtuel, consultez [Configure Bastion (Configurer Bastion)](./quickstart-host-portal.md).
1. Dans la page **Se connecter à l’aide d’Azure Bastion**, entrez le **Nom d’utilisateur** et la **Clé privée SSH**.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/ssh-private-key.png" alt-text="Capture d’écran de l’authentification par clé privée SSH.":::
1. Entrez votre clé privée dans la zone de texte **Clé privée SSH** (ou collez-la directement).
1. Sélectionnez **Se connecter** pour vous connecter à la machine virtuelle.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Connexion : Utilisation d’un fichier de clé privée

1. Ouvrez le [portail Azure](https://portal.azure.com). Accédez à la machine virtuelle à laquelle vous souhaitez vous connecter, puis cliquez sur **Se connecter** et sélectionnez **Bastion** dans la liste déroulante.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/connect.png" alt-text="Capture d’écran montrant la vue d’ensemble d’une machine virtuelle dans Portail Azure avec l’option Se connecter sélectionnée." lightbox="./media/bastion-connect-vm-ssh-linux/connect.png":::
1. Après avoir sélectionné Bastion, cliquez sur **Utiliser Bastion**. Si vous n’avez pas configuré Bastion pour le réseau virtuel, consultez [Configure Bastion (Configurer Bastion)](./quickstart-host-portal.md).
1. Dans la page **Se connecter à l’aide d’Azure Bastion**, entrez le **Nom d’utilisateur** et la **Clé privée SSH du fichier local**.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/private-key-file.png" alt-text="Capture d’écran montrant le fichier Clé privée SSH.":::

1. Recherchez le fichier, puis sélectionnez **Ouvrir**.
1. Sélectionnez **Se connecter** pour vous connecter à la machine virtuelle. Une fois que vous avez cliqué sur Se connecter, le protocole SSH vers cette machine virtuelle s’ouvre directement dans le portail Azure. La connexion s’effectue au moyen du protocole HTML5 sur le port 443 du service Bastion, sur l’adresse IP privée de votre machine virtuelle.

## <a name="connect-using-a-private-key-stored-in-azure-key-vault"></a><a name="akv"></a>Connexion : utilisation d’une clé privée stockée dans Azure Key Vault

1. Ouvrez le [portail Azure](https://portal.azure.com). Accédez à la machine virtuelle à laquelle vous souhaitez vous connecter, puis cliquez sur **Se connecter** et sélectionnez **Bastion** dans la liste déroulante.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/connect.png" alt-text="Capture d’écran montrant la vue d’ensemble d’une machine virtuelle dans Portail Azure avec l’option Se connecter sélectionnée" lightbox="./media/bastion-connect-vm-ssh-linux/connect.png":::
1. Après avoir sélectionné Bastion, cliquez sur **Utiliser Bastion**. Si vous n’avez pas configuré Bastion pour le réseau virtuel, consultez [Configure Bastion (Configurer Bastion)](./quickstart-host-portal.md).
1. Dans la page **Se connecter à l’aide d’Azure Bastion**, entrez le **Nom d’utilisateur** et sélectionnez **Clé privée SSH d’Azure Key Vault**.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/ssh-key-vault.png" alt-text="Capture d’écran montrant l’option Clé privée SSH d’Azure Key Vault.":::
1. Sélectionnez la liste déroulante **Azure Key Vault**, puis la ressource dans laquelle vous avez stocké votre clé privée SSH. 

   * Si vous n’avez pas configuré de ressource Azure Key Vault, consultez [Création d’un coffre de clés](../key-vault/secrets/quick-create-powershell.md), puis stockez votre clé privée SSH comme valeur d’un nouveau secret Key Vault.

   * Assurez-vous que vous disposez des accès **List** et **Get** aux secrets stockés dans la ressource Key Vault. Pour affecter et modifier des stratégies d’accès pour votre ressource Key Vault, consultez [Attribuer une stratégie d’accès Key Vault](../key-vault/general/assign-access-policy-portal.md).

     > [!NOTE]
     > Veuillez stocker votre clé privée SSH comme un secret dans Azure Key Vault, à l’aide de l’expérience **PowerShell** ou **Azure CLI**. Le stockage de votre clé privée via l’expérience du portail Azure Key Vault interfère avec la mise en forme et entraîne l’échec de la connexion. Si vous avez stocké votre clé privée en tant que secret à l’aide de l’expérience du portail et que vous n’avez plus accès au fichier de la clé privée d’origine, consultez [Mettre à jour la clé SSH](../virtual-machines/extensions/vmaccess.md#update-ssh-key) pour mettre à jour l’accès à votre machine virtuelle cible avec une nouvelle paire de clés SSH.
     >

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/private-key-stored.png" alt-text="Capture d’écran montrant Azure Key Vault." lightbox="./media/bastion-connect-vm-ssh-linux/private-key-stored.png":::

1. Sélectionnez la liste déroulante **Secret Azure Key Vault**, puis le secret Key Vault contenant la valeur de votre clé privée SSH.
1. Sélectionnez **Se connecter** pour vous connecter à la machine virtuelle. Une fois que vous avez cliqué sur **Se connecter**, le protocole SSH vers cette machine virtuelle s’ouvre directement dans le portail Azure. La connexion s’effectue au moyen du protocole HTML5 sur le port 443 du service Bastion, sur l’adresse IP privée de votre machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure Bastion, consultez les [Questions fréquentes (FAQ) sur Bastion](bastion-faq.md).
