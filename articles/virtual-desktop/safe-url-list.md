---
title: Liste des URL requises pour Azure Virtual Desktop – Azure
description: Liste des URL que vous devez débloquer pour garantir que votre déploiement d’Azure Virtual Desktop fonctionne comme prévu.
author: Heidilohr
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: ea5cd9dffeb8efdeb9dc29fa9d38696973285e47
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128549233"
---
# <a name="required-url-list"></a>Liste des URL requises

Pour déployer et utiliser Azure Virtual Desktop, vous devez débloquer certaines URL afin que vos machines virtuelles puissent y accéder à tout moment. Cet article répertorie les URL requises que vous devez débloquer pour que Azure Virtual Desktop fonctionne correctement. 

>[!IMPORTANT]
>Azure Virtual Desktop ne prend pas en charge les déploiements qui bloquent les URL mentionnées dans cet article.

## <a name="required-url-check-tool"></a>Outil de vérification d’URL obligatoire

L’outil de vérification d’URL obligatoire valide les URL et indique si les URL nécessaires au fonctionnement de la machine virtuelle sont accessibles. Si ce n’est pas le cas, l’outil répertorie les URL inaccessibles afin que vous puissiez les débloquer, si nécessaire.

Il est important de garder à l’esprit les éléments suivants :

- Vous ne pouvez utiliser l’outil de vérification d’URL obligatoire que pour les déploiements dans des clouds commerciaux.
- L’outil de vérification d’URL obligatoire ne peut pas vérifier les URL avec des caractères génériques. Veillez donc à débloquer ces URL en premier.

### <a name="requirements"></a>Spécifications

Pour utiliser l’outil de vérification d’URL obligatoire, vous avez besoin des éléments suivants :

- Votre machine virtuelle doit disposer de .NET Framework 4.6.2
- RDAgent version 1.0.2944.400 ou ultérieure
- Le fichier WVDAgentUrlTool.exe doit se trouver dans le même dossier que le fichier WVDAgentUrlTool.config

### <a name="how-to-use-the-required-url-check-tool"></a>Utilisation de l’outil de vérification d’URL obligatoire

Pour utiliser l’outil de vérification d’URL obligatoire :

1. Ouvrez une invite de commandes en tant qu’administrateur sur votre machine virtuelle.
2. Exécutez la commande suivante pour définir le répertoire sur le même dossier que l’agent de build actuel (RDAgent_1.0.2944.1200 dans cet exemple) :

    ```console
    cd C:\Program Files\Microsoft RDInfra\RDAgent_1.0.2944.1200
    ```

3. Exécutez la commande suivante :

    ```console
    WVDAgentUrlTool.exe
    ```
 
4. Une fois que vous avez exécuté le fichier, vous voyez une liste d’URL accessibles et inaccessibles.

    Par exemple, la capture d’écran suivante montre un scénario dans lequel vous devez débloquer deux URL non génériques obligatoires :

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la sortie des URL non accessibles.](media/noaccess.png)
    
    Voici à quoi ressemble la sortie une fois que vous avez débloqué toutes les URL non génériques requises :

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la sortie des URL non accessibles.](media/access.png)

## <a name="virtual-machines"></a>Machines virtuelles

Les machines virtuelles Azure que vous créez pour Azure Virtual Desktop doivent avoir accès aux URL suivantes dans le cloud commercial Azure :

|Adresse|Port TCP sortant|Objectif|Balise du service|
|---|---|---|---|
|*.wvd.microsoft.com|443|Trafic de service|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|Trafic de l’agent|AzureCloud|
|production.diagnostics.monitoring.core.windows.net|443|Trafic de l’agent|AzureCloud|
|*xt.blob.core.windows.net|443|Trafic de l’agent|AzureCloud|
|*eh.servicebus.windows.net|443|Trafic de l’agent|AzureCloud|
|*xt.table.core.windows.net|443|Trafic de l’agent|AzureCloud|
|*xt.queue.core.windows.net|443|Trafic de l’agent|AzureCloud|
|catalogartifact.azureedge.net|443|Place de marché Azure|AzureCloud|
|kms.core.windows.net|1688|Activation de Windows|Internet|
|mrsglobalsteus2prod.blob.core.windows.net|443|Agent et mises à jour de pile SXS|AzureCloud|
|wvdportalstorageblob.blob.core.windows.net|443|Prise en charge du portail Azure|AzureCloud|
| 169.254.169.254 | 80 | [Point de terminaison Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md) | N/A |
| 168.63.129.16 | 80 | [Surveillance de l’intégrité de l’hôte de la session](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | N/A |

>[!IMPORTANT]
>Azure Virtual Desktop prend désormais en charge l’étiquette FQDN. Pour plus d’informations, consultez [Utiliser le pare-feu Azure pour protéger les déploiements de Windows Virtual Desktop](../firewall/protect-azure-virtual-desktop.md).
>
>Nous vous recommandons d’utiliser des étiquettes de nom de domaine complet ou de service à la place des URL pour éviter tout problème lié aux services. Les étiquettes et URL répertoriées correspondent uniquement aux sites et ressources Azure Virtual Desktop. Elles n’incluent pas les URL d’autres services comme Azure Active Directory.

Les machines virtuelles Azure que vous créez pour Azure Virtual Desktop doivent avoir accès aux URL suivantes dans le cloud Azure Government :

|Adresse|Port TCP sortant|Objectif|Étiquette du service|
|---|---|---|---|
|*.wvd.azure.us|443|Trafic de service|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|Trafic de l’agent|AzureCloud|
|monitoring.core.usgovcloudapi.net|443|Trafic de l’agent|AzureCloud|
|fairfax.warmpath.usgovcloudapi.net|443|Trafic de l’agent|AzureCloud|
|*xt.blob.core.usgovcloudapi.net|443|Trafic de l’agent|AzureCloud|
|*.servicebus.usgovcloudapi.net|443|Trafic de l’agent|AzureCloud|
|*xt.table.core.usgovcloudapi.net|443|Trafic de l’agent|AzureCloud|
|Kms.core.usgovcloudapi.net|1688|Activation de Windows|Internet|
|mrsglobalstugviffx.blob.core.usgovcloudapi.net|443|Agent et mises à jour de pile SXS|AzureCloud|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Prise en charge du portail Azure|AzureCloud|
| 169.254.169.254 | 80 | [Point de terminaison Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md) | N/A |
| 168.63.129.16 | 80 | [Surveillance de l’intégrité de l’hôte de la session](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | N/A |

Le tableau suivant liste les URL facultatives auxquelles vos machines virtuelles Azure peuvent accéder :

|Adresse|Port TCP sortant|Objectif|Azure Gov|
|---|---|---|---|
|*.microsoftonline.com|443|Authentification auprès de Microsoft Online Services|login.microsoftonline.us|
|*.events.data.microsoft.com|443|Service de télémétrie|None|
|www.msftconnecttest.com|443|Détecte si l’interface est connectée à internet|None|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|None|
|login.windows.net|443|Se connecter à Microsoft Online Services, Microsoft 365|login.microsoftonline.us|
|*.sfx.ms|443|Mises à jour pour le logiciel client OneDrive|oneclient.sfx.ms|
|*.digicert.com|443|Vérification de la révocation de certificat|None|
|*.azure-dns.com|443|Résolution d’Azure DNS|None|
|*.azure-dns.net|443|Résolution d’Azure DNS|Aucun|

>[!NOTE]
>Actuellement, Azure Virtual Desktop ne dispose pas d’une liste de plages d’adresses IP que vous pouvez débloquer pour autoriser le trafic réseau. Pour le moment, seules certaines URL spécifiques peuvent être débloquées.
>
>Si vous utilisez un pare-feu de nouvelle génération, vous devez utiliser une liste dynamique spécialement conçue pour les adresses IP Azure pour vous assurer que vous pouvez vous connecter.
>
>Pour obtenir la liste des URL sécurisées liées à Office, notamment les URL liées à Azure Active Directory obligatoires, consultez [URL et plages d’adresses IP Office 365](/office365/enterprise/urls-and-ip-address-ranges).
>
>Vous devez utiliser le caractère générique (*) pour les URL impliquant du trafic de service. Si vous préférez ne pas utiliser * pour le trafic lié à l’agent, voici comment trouver les URL sans caractères génériques :
>
>1. Inscrivez vos machines virtuelles dans le pool d’hôtes Azure Virtual Desktop.
>2. Ouvrez **Observateur d’événements**, accédez à **Journaux Windows** > **Application** > **WVD-Agent**, puis recherchez l’ID d’événement 3701.
>3. Débloquez les URL que vous trouvez sous l’ID d’événement 3701. Les URL sous l’ID d’événement 3701 sont spécifiques à la région. Vous devez répéter le processus de déblocage avec les URL appropriées pour chaque région où vous souhaitez déployer vos machines virtuelles.

## <a name="remote-desktop-clients"></a>Clients Bureau à distance

Les clients Bureau à distance que vous utilisez doivent avoir accès aux URL suivantes :

|Adresse|Port TCP sortant|Objectif|Client(s)|Azure Gov|
|---|---|---|---|---|
|*.wvd.microsoft.com|443|Trafic de service|Tous|*.wvd.microsoft.us|
|*.servicebus.windows.net|443|Résolution des problèmes de données|Tous|*.servicebus.usgovcloudapi.net|
|go.microsoft.com|443|Microsoft FWLinks|Tous|None|
|aka.ms|443|Réducteur d’URL Microsoft|Tous|None|
|docs.microsoft.com|443|Documentation|Tous|None|
|privacy.microsoft.com|443|Déclaration de confidentialité|Tous|None|
|query.prod.cms.rt.microsoft.com|443|Mises à jour de client|Windows Desktop|Aucun|

>[!IMPORTANT]
>L’ouverture de ces URL est essentielle pour une expérience client fiable. Il n’est pas possible de bloquer l’accès à ces URL, car cela affecterait le fonctionnement du service.
>
>Ces URL correspondent uniquement aux sites client et aux ressources. Cette liste n’inclut pas les URL d’autres services comme Azure Active Directory. Les URL Azure Active Directory figurent sous l’ID 56, 59 et 125 sur les [URL et les plages d’adresses IP Office 365](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online).
