---
title: Exemple de script Azure CLI - Utiliser des clés-valeurs dans un magasin Azure App Configuration
titleSuffix: Azure App Configuration
description: Utiliser un script Azure CLI pour créer, afficher, mettre à jour et supprimer des valeurs de clés dans le magasin App Configuration
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: 144b324dcde0c0bdcbaf0a64840b56c6c618a19e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441582"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Utiliser des clés-valeurs dans un magasin Azure App Configuration

Cet exemple de script montre comment :
* Créer une paire clé-valeur
* Lister toutes les paires clé-valeur existantes
* Mettre à jour la valeur d’une clé nouvellement créée
* Supprimer la nouvelle paire clé-valeur

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ce tutoriel nécessite l’interface Azure CLI version 2.0 ou ultérieure. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.
## <a name="sample-script"></a>Exemple de script

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"
refKey="KeyVaultReferenceTestKey"
uri="[URL to value stored in Key Vault]"
uri2="[URL to another value stored in Key Vault]"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --key $newKey --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Create a new key-value referencing a value stored in Azure Key Vault
az appconfig kv set-keyvault  --name $appConfigName --key $refKey --secret-identifier $uri

# List current key-values
az appconfig kv list --name $appConfigName

# Update Key Vault reference
az appconfig kv set-keyvault --name $appConfigName --key $refKey --secret-identifier $uri2

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# Delete Key Vault reference
az appconfig kv delete --name $appConfigName --key $refKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce tableau liste les commandes utilisées dans notre exemple de script. 

| Commande | Notes |
|---|---|
| [az appconfig kv set](/cli/azure/appconfig/kv#az_appconfig_kv_set) | Crée ou met à jour une paire clé-valeur. |
| [az appconfig kv list](/cli/azure/appconfig/kv#az_appconfig_kv_list) | Liste les paires clé-valeur dans un magasin App Configuration. |
| [az appconfig kv delete](/cli/azure/appconfig/kv#az_appconfig_kv_delete) | Supprime une paire clé-valeur. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous trouverez des exemples supplémentaires de scripts d’interface de ligne de commande App Configuration dans les [exemples d’interface de ligne de commande Azure App Configuration](../cli-samples.md).
