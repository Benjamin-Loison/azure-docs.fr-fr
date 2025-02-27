---
title: Créer un pool d’hôtes Azure Virtual Desktop (classique) via PowerShell – Azure
description: Comment créer un pool d’hôtes dans Azure Virtual Desktop (classique) avec des applets de commande PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: eabf679d02c0e542176bf1f6e37f1ccc674d8eb0
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111749882"
---
# <a name="create-a-host-pool-in-azure-virtual-desktop-classic-with-powershell"></a>Créer un pool d’hôtes dans Azure Virtual Desktop (classique) avec PowerShell

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop (classique), qui ne prend pas en charge les objets Azure Virtual Desktop pour Azure Resource Manager. Si vous essayez de gérer des objets Azure Virtual Desktop Azure Resource Manager, consultez [cet article](../create-host-pools-powershell.md).

Les pools d’hôtes sont des ensembles d’une ou de plusieurs machines virtuelles identiques dans des environnements de locataires Azure Virtual Desktop. Chaque pool d’hôtes peut contenir un groupe d’applications avec lequel les utilisateurs peuvent interagir comme ils le feraient sur un ordinateur de bureau physique.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Utilisez votre client PowerShell pour créer un pool d’hôtes

Si ce n’est déjà fait, commencez par télécharger et importer le [module PowerShell Azure Virtual Desktop](/powershell/windows-virtual-desktop/overview/) à utiliser dans votre session PowerShell.

Exécutez l’applet de commande suivante pour vous connecter à l’environnement Azure Virtual Desktop

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Exécutez ensuite cette applet de commande pour créer un nouveau pool d’hôtes dans votre locataire Azure Virtual Desktop :

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Exécutez la cmdlet suivante pour créer un jeton d’inscription et permettre à un hôte de session de rejoindre le pool d'hôtes et de le sauvegarder dans un nouveau fichier sur votre ordinateur local. Vous pouvez spécifier la durée de validité du jeton d’inscription à l'aide du paramètre -ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

Exécutez la commande suivante pour ajouter des utilisateurs Azure Active Directory au groupe d’applications de bureau par défaut du pool d’hôtes.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

La cmdlet **Add-RdsAppGroupUser** ne prend pas en charge l'ajout de groupes de sécurité et n'ajoute qu'un seul utilisateur à la fois au groupe d’applications. Pour ajouter plusieurs utilisateurs au groupe d'applications, réexécutez la cmdlet avec les noms des utilisateurs principaux.

Exécutez l’applet de commande suivante pour exporter le jeton d’inscription vers une variable, que vous utiliserez ultérieurement dans [Inscrire les machines virtuelles dans le pool d'hôtes Azure Virtual Desktop](#register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Créer les machines virtuelles pour le pool d'hôtes

Vous pouvez maintenant créer une machine virtuelle Azure à joindre à votre pool d’hôtes Azure Virtual Desktop.

Vous pouvez créer une machine virtuelle de plusieurs façons :

- [Créer une machine virtuelle à partir d’une image Azure Gallery](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Créer une machine virtuelle à partir d’une image managée](../../virtual-machines/windows/create-vm-generalized-managed.md)
- [Créer une machine virtuelle à partir d’une image non managée](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-from-user-image)

>[!NOTE]
>Si vous déployez une machine virtuelle à l’aide de Windows 7 en tant que système d’exploitation hôte, le processus de création et de déploiement sera un peu différent. Pour plus d’informations, consultez [Déployer une machine virtuelle Windows 7 sur Azure Virtual Desktop](deploy-windows-7-virtual-machine.md).

Une fois que vous avez créé vos machines virtuelles hôtes de session, [appliquez une licence Windows à une machine virtuelle hôte de session](../apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) pour exécuter vos machines virtuelles Windows ou Windows Server sans payer une autre licence.

## <a name="prepare-the-virtual-machines-for-azure-virtual-desktop-agent-installations"></a>Préparer les machines virtuelles pour l’installation des agents Azure Virtual Desktop

Avant d’installer les agents Azure Virtual Desktop et d'inscrire les machines virtuelles dans votre pool d’hôtes Azure Virtual Desktop, les opérations suivantes sont requises :

- Vous devez joindre le domaine à la machine. Ainsi, le compte Azure Active Directory des utilisateurs Azure Virtual Desktop entrants est mappé à leur compte Active Directory pour leur permettre d’accéder à la machine virtuelle.
- Vous devez installer le rôle Hôte de session Bureau à distance (RDSH) si la machine virtuelle exécute un système d’exploitation Windows Server. Le rôle RDSH permet la bonne installation des agents Azure Virtual Desktop.

À des fins de jonction de domaine, procédez comme suit sur chaque machine virtuelle :

1. [Connectez-vous à la machine virtuelle](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) avec les informations d’identification que vous avez indiquées lors de la création de la machine virtuelle.
2. Sur la machine virtuelle, lancez le **Panneau de configuration** et sélectionnez **Système**.
3. Sélectionnez successivement **Nom de l’ordinateur**, **Modifier les paramètres** et **Modifier…**
4. Sélectionnez **Domaine**, puis entrez le domaine Active Directory sur le réseau virtuel.
5. Authentifiez-vous avec un compte de domaine qui dispose de privilèges d’accès sur les machines de jonction de domaine.

    >[!NOTE]
    > Si vous joignez vos machines virtuelles à un environnement Azure Active Directory Domain Services (Azure AD DS), vérifiez que votre utilisateur de jonction de domaine est également membre du [groupe Administrateurs AAD DC](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

## <a name="register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool"></a>Inscrire les machines virtuelles auprès du pool d’hôtes Azure Virtual Desktop

L’inscription de machines virtuelles auprès d’un pool d’hôtes Azure Virtual Desktop est aussi simple que l’installation des agents Azure Virtual Desktop.

Pour inscrire les agents Azure Virtual Desktop, procédez comme suit sur chaque machine virtuelle :

1. [Connectez-vous à la machine virtuelle](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) avec les informations d’identification que vous avez indiquées lors de la création de la machine virtuelle.
2. Téléchargez et installez l’agent Azure Virtual Desktop.
   - Téléchargez l’[agent Azure Virtual Desktop](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Cliquez sur le programme d’installation téléchargé, sélectionnez **Propriétés**, **Débloquer**, puis **OK**. Votre système sera ainsi en mesure d’approuver le programme d’installation.
   - Exécutez le programme d’installation. Lorsque le programme d’installation vous demande le jeton d’inscription, entrez la valeur obtenue à partir de la cmdlet **Export-RdsRegistrationInfo**.
3. Téléchargez et installez le chargeur de démarrage de l’agent Azure Virtual Desktop.
   - Téléchargez le [chargeur de démarrage de l’agent Azure Virtual Desktop](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Cliquez sur le programme d’installation téléchargé, sélectionnez **Propriétés**, **Débloquer**, puis **OK**. Votre système sera ainsi en mesure d’approuver le programme d’installation.
   - Exécutez le programme d’installation.

>[!IMPORTANT]
>Pour contribuer à sécuriser votre environnement Azure Virtual Desktop dans Azure, nous vous recommandons de ne pas ouvrir le port entrant 3389 sur vos machines virtuelles. Azure Virtual Desktop ne nécessite pas l’ouverture du port entrant 3389 pour permettre aux utilisateurs d’accéder aux machines virtuelles du pool d’hôtes. Si vous devez ouvrir le port 3389 pour résoudre des problèmes, nous vous recommandons d’utiliser un [accès à la machine virtuelle juste-à-temps](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé un pool d'hôtes, vous pouvez le remplir avec RemoteApps. Pour en savoir plus sur la façon de gérer des applications dans Azure Virtual Desktop, consultez le tutoriel Gérer les groupes d’applications.

> [!div class="nextstepaction"]
> [Gérer les groupes d’applications](../manage-app-groups.md)
