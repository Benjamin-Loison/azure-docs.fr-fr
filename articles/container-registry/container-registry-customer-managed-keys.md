---
title: Chiffrer un registre avec une clé gérée par le client
description: Apprenez-en davantage sur le chiffrement au repos de votre registre de conteneurs Azure et sur la façon de chiffrer votre registre Premium avec une clé gérée par le client, stockée dans Azure Key Vault
ms.topic: how-to
ms.date: 09/13/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: cd15a42585f5722432d32d9cffdfa89e2c17f8a2
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130046427"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Chiffrer un registre à l’aide d’une clé gérée par le client

Lorsque vous stockez des images et d’autres artefacts dans un registre de conteneurs Azure, Azure chiffre automatiquement le contenu du registre au repos avec des [clés gérées par le service](../security/fundamentals/encryption-models.md). Vous pouvez compléter le chiffrement par défaut avec une couche de chiffrement supplémentaire à l’aide d’une clé que vous créez et gérez dans Azure Key Vault (une clé gérée par le client). Cet article vous guide à chaque étape de l'utilisation de l'interface de ligne de commande Azure (Azure CLI), du portail Azure ou d'un modèle Resource Manager.

Le chiffrement côté serveur avec des clés gérées par le client est pris en charge par le biais de l’intégration à [Azure Key Vault](../key-vault/general/overview.md) : 

* Vous pouvez créer vos propres clés de chiffrement et les stocker dans un coffre de clés ou utiliser les API d’Azure Key Vault pour générer des clés. 
* Avec Azure Key Vault, vous pouvez également auditer l’utilisation des clés.
* Azure Container Registry prend en charge la permutation automatique des clés de chiffrement du Registre quand une nouvelle version de clé est disponible dans Azure Key Vault. Vous pouvez également effectuer une permutation manuelle des clés de chiffrement du Registre.

Cette fonctionnalité est disponible uniquement au niveau de service **Premium** de registre de conteneurs. Pour plus d’informations sur les niveaux de service et les limites de registre, consultez [Niveaux de service d’Azure Container Registry](container-registry-skus.md).


## <a name="things-to-know"></a>À savoir

* Vous pouvez actuellement activer une clé gérée par le client uniquement lorsque vous créez un registre. Quand vous activez la clé, vous configurez une identité managée *affectée par l’utilisateur* pour accéder au coffre de clés. Plus tard, vous pouvez activer l’identité gérée par le système du registre pour l’accès au coffre de clés, si nécessaire.
* Après avoir activé le chiffrement avec une clé gérée par le client sur un registre, vous ne pouvez pas le désactiver.  
* Azure Container Registry prend en charge uniquement les clés RSA ou RSA-HSM. Les clés à courbe elliptique ne sont actuellement pas prises en charge.
* L’[approbation de contenu](container-registry-content-trust.md) n’est actuellement pas prise en charge dans un registre chiffré avec une clé gérée par le client.
* Dans un registre chiffré avec une clé gérée par le client, les journaux d’exécution pour les [tâches ACR](container-registry-tasks-overview.md) sont actuellement conservés pendant 24 heures uniquement. Si vous devez conserver les journaux plus longtemps, consultez les instructions pour [exporter et stocker les journaux d’exécution des tâches](container-registry-tasks-logs.md#alternative-log-storage).

## <a name="automatic-or-manual-update-of-key-versions"></a>Mise à jour automatique ou manuelle des versions de clé

La fréquence à laquelle vous mettez à jour (permutez) la clé de chiffrement constitue une considération importante pour la sécurité d’un registre chiffré avec une clé gérée par le client. Votre organisation peut avoir des stratégies de conformité qui nécessitent une mise à jour régulière des [versions](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) de clé stockées dans Azure Key Vault quand elles sont utilisées comme clés gérées par le client. 

Quand vous configurez le chiffrement du Registre avec une clé gérée par le client, vous disposez de deux options pour mettre à jour la version de la clé utilisée pour le chiffrement :

* **Mettre à jour automatiquement la version de la clé** : pour mettre à jour automatiquement une clé gérée par le client quand une nouvelle version est disponible dans Azure Key Vault, omettez la version de la clé quand vous activez le chiffrement du Registre avec une clé gérée par le client. Quand un registre est chiffré avec une clé sans version, Azure Container Registry vérifie régulièrement la nouvelle version de clé dans le coffre de clés et met à jour la clé gérée par le client dans un délai d’1 heure. Azure Container Registry utilise automatiquement la dernière version de la clé.

* **Mettre à jour manuellement la version de la clé** : pour utiliser une version spécifique d’une clé pour le chiffrement du Registre, spécifiez cette version de clé quand vous activez le chiffrement du Registre avec une clé gérée par le client. Quand un registre est chiffré avec une version de clé spécifique, Azure Container Registry utilise cette version pour le chiffrement tant que vous n’avez pas manuellement permuté la clé gérée par le client.

Pour plus d’informations, consultez [Choisir un ID de clé avec ou sans version de clé](#choose-key-id-with-or-without-key-version) et [Mettre à jour la version de la clé](#update-key-version), plus loin dans cet article.

## <a name="prerequisites"></a>Prérequis

Pour utiliser les étapes Azure CLI décrites dans cet article, vous devez disposer d’Azure CLI version 2.2.0 ou ultérieure, ou d’Azure Cloud Shell. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Activer une clé gérée par le client – interface de ligne de commande

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Si nécessaire, exécutez la commande [az group create][az-group-create] pour créer un groupe de ressources pour la création du coffre de clés, du registre de conteneurs et d’autres ressources requises.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Créer une identité managée attribuée par l’utilisateur

Créez une [identité managée attribuée par l’utilisateur pour des ressources Azure](../active-directory/managed-identities-azure-resources/overview.md) avec la commande [az identity create][az-identity-create]. Cette identité sera utilisée par votre registre pour accéder au service Key Vault.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

Dans la sortie de la commande, prenez note des valeurs suivantes : `id` et `principalId`. Vous aurez besoin de ces valeurs dans les étapes ultérieures pour configurer l’accès du registre au coffre de clés.

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Par souci pratique, stockez ces valeurs dans des variables d’environnement :

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Création d’un coffre de clés

Créez un coffre de clés avec [az keyvault create][az-keyvault-create] pour stocker une clé gérée par le client pour le chiffrement du registre. 

Par défaut, le paramètre de **suppression réversible** est automatiquement activé dans un nouveau coffre de clés. Pour éviter toute perte de données provoquée par la suppression accidentelle d'une clé ou d'un coffre de clés, activez également le paramètre de **protection contre la suppression définitive**.

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-purge-protection
```

Pour une utilisation dans les étapes ultérieures, récupérez l’ID de ressource du coffre de clés :

```azurecli
keyvaultID=$(az keyvault show --resource-group <resource-group-name> --name <key-vault-name> --query 'id' --output tsv)
```

### <a name="enable-key-vault-access-by-trusted-services"></a>Activer l’accès au coffre de clés par les services approuvés

Si le coffre de clés est protégé par un pare-feu ou un réseau virtuel (point de terminaison privé), activez le paramètre réseau pour autoriser l’accès par les [services approuvés Azure](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services). 

Pour plus d’informations, consultez [Configurer les paramètres de mise en réseau du Coffre de clés Azure](../key-vault/general/how-to-azure-key-vault-network-security.md?tabs=azure-cli).


### <a name="enable-key-vault-access-by-managed-identity"></a>Activer l’accès au coffre de clés par l’identité managée

#### <a name="enable-key-vault-access-policy"></a>Activer la stratégie d’accès au coffre de clés

L’une des options est de configurer une stratégie pour le coffre de clés afin que l’identité puisse y accéder. Dans la commande [az keyvault set-policy][az-keyvault-set-policy] suivante, vous transmettez l’ID du principal de l’identité managée que vous avez créée, précédemment stocké dans une variable d’environnement. Définissez les autorisations de clé sur **get**, **unwrapKey** et **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey

```
#### <a name="assign-rbac-role"></a>Attribuer un rôle RBAC

Vous pouvez également utiliser [Azure RBAC for Key Vault](../key-vault/general/rbac-guide.md) pour attribuer des autorisations à l’identité afin d’accéder au coffre de clés. Par exemple, attribuez le rôle Service de chiffrement de Key Vault à l’identité à l’aide de la commande [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) :

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption User" \
  --scope $keyvaultID
```

### <a name="create-key-and-get-key-id"></a>Créer une clé et obtenir un ID de clé

Exécutez la commande [az keyvault key create][az-keyvault-key-create] pour créer une clé dans le coffre de clés.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Dans la sortie de la commande, prenez note de l’ID de la clé, `kid`. Vous utiliserez cet ID dans l’étape suivante :

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/<version>",
    "kty": "RSA",
[...]
```

### <a name="choose-key-id-with-or-without-key-version"></a>Choisir un ID de clé avec ou sans version de clé

Pour des raisons pratiques, stockez le format que vous choisissez pour l’ID de clé dans la variable d’environnement $keyID. Vous pouvez utiliser un ID de clé avec version ou une clé sans version.

#### <a name="manual-key-rotation---key-id-with-version"></a>Permutation de clé manuelle - ID de clé avec version

Quand elle est utilisée pour chiffrer un registre avec une clé gérée par le client, cette clé autorise uniquement la permutation de clé manuelle dans Azure Container Registry.

Cet exemple stocke la propriété `kid` de la clé :

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

#### <a name="automatic-key-rotation---key-id-omitting-version"></a>Permutation de clé automatique - ID de clé omettant la version 

Quand elle est utilisée pour chiffrer un registre avec une clé gérée par le client, cette clé active la permutation de clé automatique en cas de détection d’une nouvelle version de clé est détectée dans Azure Key Vault.

Cet exemple supprime la version de la propriété `kid` de la clé :

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)

keyID=$(echo $keyID | sed -e "s/\/[^/]*$//")
```

### <a name="create-a-registry-with-customer-managed-key"></a>Créer un registre avec une clé gérée par le client

Exécutez la commande [az acr create][az-acr-create] pour créer un registre dans le niveau de service Premium et activer la clé gérée par le client. Transmettez l’ID d’identité managée et l’ID de clé, stockés précédemment dans des variables d’environnement :

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Afficher l’état de chiffrement

Pour indiquer si le chiffrement du registre avec une clé gérée par le client est activé, exécutez la commande [az acr encryption show][az-acr-encryption-show] :

```azurecli
az acr encryption show --name <registry-name>
```

En fonction de la clé utilisée pour chiffrer le Registre, la sortie est similaire à ce qui suit :

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "keyRotationEnabled": true,
    "lastKeyRotationTimestamp": xxxxxxxx
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
  },
  "status&quot;: &quot;enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>Activer une clé gérée par le client – portail

### <a name="create-a-managed-identity"></a>Créer une identité managée

Créez une [identité managée attribuée par l’utilisateur pour des ressources Azure](../active-directory/managed-identities-azure-resources/overview.md) dans le portail Azure. Pour connaître les étapes, consultez [Créer une identité managée attribuée par l’utilisateur](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Vous utilisez le nom de l’identité dans des étapes ultérieures.

:::image type="content" source="media/container-registry-customer-managed-keys/create-managed-identity.png" alt-text="Créer une identité affectée par l’utilisateur dans le portail Azure":::

### <a name="create-a-key-vault"></a>Création d’un coffre de clés

Pour connaître la procédure de création d’un coffre de clés, consultez [Démarrage rapide : Créer un coffre de clés avec le portail Azure](../key-vault/general/quick-create-portal.md).

Quand vous créez un coffre de clés pour une clé gérée par le client, sous l’onglet **Informations de base**, activez le paramètre **Protection contre la suppression définitive**. Ce paramètre permet d’éviter toute perte de données provoquée par la suppression accidentelle d’une clé ou d’un coffre de clés.

:::image type="content" source="media/container-registry-customer-managed-keys/create-key-vault.png" alt-text="Créer un coffre de clés dans le portail Azure":::

### <a name="enable-key-vault-access-by-trusted-services"></a>Activer l’accès au coffre de clés par les services approuvés

Si le coffre de clés est protégé par un pare-feu ou un réseau virtuel (point de terminaison privé), activez le paramètre réseau pour autoriser l’accès par les [services approuvés Azure](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services). 

Pour plus d’informations, consultez [Configurer les paramètres de mise en réseau du Coffre de clés Azure](../key-vault/general/how-to-azure-key-vault-network-security.md?tabs=azure-portal).

### <a name="enable-key-vault-access-by-managed-identity"></a>Activer l’accès au coffre de clés par l’identité managée

#### <a name="enable-key-vault-access-policy"></a>Activer la stratégie d’accès au coffre de clés

L’une des options est de configurer une stratégie pour le coffre de clés afin que l’identité puisse y accéder.

1. Accédez à votre coffre de clés.
1. Sélectionnez **Paramètres** > **Stratégies d’accès > +Ajouter une stratégie d’accès**.
1. Sélectionnez **Autorisations de clé**, puis **Obtenir**, **Ne pas inclure la clé** et **Inclure la clé**.
1. Dans **Sélectionner le principal**, sélectionnez le nom de ressource de votre identité managée affectée par l’utilisateur.  
1. Sélectionnez **Ajouter**, puis **Enregistrer**.

:::image type="content" source="media/container-registry-customer-managed-keys/add-key-vault-access-policy.png" alt-text="Créer une stratégie d’accès au coffre de clés":::

#### <a name="assign-rbac-role"></a>Attribuer un rôle RBAC    

Vous pouvez également affecter le rôle Utilisateur du Chiffrement du service du coffre de clés à l’identité managée affectée par l’utilisateur au niveau de l’étendue du coffre de clés.

Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="create-key-optional"></a>Créer une clé (facultatif)

Vous pouvez éventuellement créer une clé dans le coffre de clés pour chiffrer le registre. Procédez comme suit si vous souhaitez sélectionner une version de clé spécifique comme clé gérée par le client. Vous devrez peut-être également créer une clé avant de créer le registre si l’accès au coffre de clés est limité à un point de terminaison privé ou à des réseaux sélectionnés. 

1. Accédez à votre coffre de clés.
1. Sélectionnez **Paramètres** > **Clés**.
1. Sélectionnez **+Générer/Importer** et entrez un nom unique pour la clé.
1. Acceptez les valeurs par défaut restantes et sélectionnez **Créer**.
1. Après la création, sélectionnez la clé, puis sélectionnez la version actuelle. Copiez l’**identificateur de clé** pour la version de clé.

### <a name="create-azure-container-registry"></a>Créer un registre de conteneurs Azure

1. Sélectionnez **Créer une ressource** > **Conteneurs** > **Container Registry**.
1. Sous l’onglet **Informations de base**, sélectionnez ou créez un groupe de ressources, puis entrez un nom de registre. Dans **Référence (SKU)** , sélectionnez **Premium**.
1. Sous l’onglet **Chiffrement**, dans **Clé gérée par le client**, sélectionnez **Activée**.
1. Dans **Identité**, sélectionnez l’identité managée que vous avez créée.
1. Dans **Chiffrement**, choisissez l’une des options suivantes :
    * Sélectionnez **Sélectionner dans Key Vault**, puis sélectionnez un coffre de clés et une clé existants, ou **créez-en**. La clé que vous sélectionnez est sans version et active la rotation automatique des clés.
    * Sélectionnez **Entrez l’URI de clé**, et fournissez une clé existante à l’identificateur. Vous pouvez fournir un URI de clé avec version (pour une clé qui doit être renouvelée manuellement) ou un URI de clé sans version (qui active la rotation automatique des clés). Consultez la section précédente pour connaître les étapes de création d’une clé.
1. Sous l’onglet **Chiffrement**, sélectionnez **Vérifier + créer**.
1. Sélectionnez **Créer** pour déployer l’instance du registre.

:::image type="content" source="media/container-registry-customer-managed-keys/create-encrypted-registry.png" alt-text="Créer un registre chiffré dans le portail Azure":::

Pour visualiser l’état de chiffrement de votre registre dans le portail, accédez à votre registre. Sous **Paramètres**, sélectionnez **Chiffrement**.

## <a name="enable-customer-managed-key---template"></a>Activer une clé gérée par le client – modèle

Vous pouvez également utiliser un modèle Resource Manager pour créer un registre et activer le chiffrement avec une clé gérée par le client.

Le modèle suivant crée un registre de conteneurs et une identité managée attribuée par l’utilisateur. Copiez le contenu suivant dans un nouveau fichier et enregistrez-le à l’aide d’un nom de fichier tel que `CMKtemplate.json`.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}
```

Suivez les étapes décrites dans les sections précédentes pour créer les ressources suivantes :

* Coffre de clés, identifié par un nom
* Clé de coffre de clés, identifiée par un ID de clé

Exécutez la commande [az deployment group create][az-deployment-group-create] suivante pour créer le Registre à l’aide du fichier de modèle précédent. Lorsque cela est indiqué, fournissez un nouveau nom de registre et un nom d’identité managée, ainsi que le nom et l’ID de clé du coffre de clés que vous avez créé.

```bash
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>Afficher l’état de chiffrement

Pour afficher l’état du chiffrement du registre, exécutez la commande[az acr encryption show][az-acr-encryption-show] :

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>Utiliser le registre

Après avoir activé une clé gérée par le client dans un registre, vous pouvez effectuer les mêmes opérations de registre que celles que vous effectuez dans un registre qui n’est pas chiffré avec une clé gérée par le client. Par exemple, vous pouvez vous authentifier à l’aide du registre et envoyer (push) des images Docker. Consultez les exemples de commandes dans [Envoyer (push) et extraire (pull) une image](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Rotation de clé

Mettez à jour la version de la clé dans Azure Key Vault ou créez une autre clé, puis mettez à jour le Registre pour chiffrer les données à l’aide de la clé. Vous pouvez également effectuer ces étapes à l’aide d’Azure CLI ou dans le portail Azure.

Lorsque vous faites pivoter une clé, vous spécifiez généralement la même identité que celle utilisée lors de la création du registre. Si vous le souhaitez, configurez une nouvelle identité affectée par l’utilisateur pour l’accès à la clé, ou activez et spécifiez l’identité affectée par le système du registre.

> [!NOTE]
> * Pour activer l’identité affectée par le système du registre dans le portail, sélectionnez **Paramètres** > **Identité** et définissez le statut de l’identité affectée par le système sur **Activé**.
> * Vérifiez que l’[accès au coffre de clés](#enable-key-vault-access-by-managed-identity) requis est défini pour l’identité que vous configurez pour l’accès à la clé.

### <a name="update-key-version"></a>Mettre à jour la version de la clé

Un scénario courant consiste à mettre à jour la version de la clé utilisée comme clé gérée par le client. En fonction de la configuration du chiffrement du Registre, la clé gérée par le client dans Azure Container Registry est mise à jour automatiquement ou doit l’être manuellement.

### <a name="azure-cli"></a>Azure CLI

Pour créer ou gérer vos clés de coffre de clés, utilisez des commandes [az keyvault key][az-keyvault-key]. Pour créer une version de clé, exécutez la commande [az keyvault key create][az-keyvault-key-create] :

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>
```

L’étape suivante dépend de la configuration du chiffrement du Registre :

* Si le Registre est configuré pour détecter les mises à jour de la version de clé, la clé gérée par le client est mise à jour automatiquement dans un délai d’1 heure.

* Si le Registre est configuré de façon à exiger une mise à jour manuelle pour une nouvelle version de la clé, exécutez la commande [az acr encryption rotate-key][az-acr-encryption-rotate-key], en passant l’ID de la nouvelle clé et l’identité que vous voulez configurer :

Pour mettre à jour manuellement la version de la clé gérée par le client :

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

> [!TIP]
> Quand vous exécutez `az acr encryption rotate-key`, vous pouvez passer un ID de clé avec version ou un ID de clé sans version. Si vous utilisez un ID de clé sans version, le Registre est alors configuré pour détecter automatiquement les mises à jour de version de clé ultérieures.

### <a name="portal"></a>Portail

Utilisez les paramètres **Chiffrement** du Registre pour mettre à jour les paramètres de coffre de clés, de clé ou d’identité utilisés pour la clé gérée par le client.

Par exemple, pour configurer une nouvelle clé :

1. Dans le portail, accédez à votre registre.
1. Sous **Paramètres**, sélectionnez **Chiffrement** > **Modifier la clé**.

    :::image type="content" source="media/container-registry-customer-managed-keys/rotate-key.png" alt-text="Faites pivoter la clé dans le portail Azure.":::
1. Dans **Chiffrement**, choisissez l’une des options suivantes :
    * Sélectionnez **Sélectionner dans Key Vault**, puis sélectionnez un coffre de clés et une clé existants, ou **créez-en**. La clé que vous sélectionnez est sans version et active la rotation automatique des clés.
    * Sélectionnez **Entrer l’URI de la clé** et fournissez directement un identificateur de clé. Vous pouvez fournir un URI de clé avec version (pour une clé qui doit être renouvelée manuellement) ou un URI de clé sans version (qui active la rotation automatique des clés).
1. Terminez la sélection de la clé et sélectionnez **Enregistrer**.

## <a name="revoke-key"></a>Révoquer la clé

Révoquez la clé de chiffrement gérée par le client en modifiant la stratégie ou les autorisations d’accès sur le coffre de clés ou en supprimant la clé. Par exemple, utilisez la commande [az keyvault delete-policy][az-keyvault-delete-policy] pour modifier la stratégie d’accès de l’identité managée utilisée par votre registre :

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

La révocation de la clé bloque efficacement l’accès à toutes les données du registre, car le registre ne peut pas accéder à la clé de chiffrement. Si l’accès à la clé est activé ou si la clé supprimée est restaurée, votre registre choisira la clé afin de vous permettre d’accéder de nouveau aux données de registre chiffrées. 

## <a name="troubleshoot"></a>Dépanner

### <a name="removing-managed-identity"></a>Suppression de l’identité managée


Si vous tentez de supprimer d’un registre une identité managée affectée par l’utilisateur ou par le système qui est utilisée pour configurer le chiffrement, un message d’erreur semblable à celui-ci peut s’afficher :
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
Vous ne pouvez pas non plus modifier (faire pivoter) la clé de chiffrement. Les étapes de résolution varient selon le type d’identité utilisé pour le chiffrement.

**Identité affectée par l’utilisateur**

Si ce problème se produit avec une identité affectée par l’utilisateur, réaffectez d’abord l’identité à l’aide de la commande [az acr identity assign](/cli/azure/acr/identity/#az_acr_identity_assign). Transmettez l’ID de ressource de l’identité, ou utilisez le nom de l’identité lorsqu’il se trouve dans le même groupe de ressources que le registre. Par exemple :

```azurecli
az acr identity assign -n myRegistry \
    --identities "/subscriptions/mysubscription/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentity"
```
        
Ensuite, après avoir modifié la clé et affecté une autre identité, vous pouvez supprimer l’identité affectée par l’utilisateur d’origine.

**Identité affectée par le système**

Si ce problème se produit avec une identité affectée par le système, veuillez [créer un ticket de support Azure](https://azure.microsoft.com/support/create-ticket/) pour obtenir de l’aide afin de restaurer l’identité.

### <a name="enabling-key-vault-firewall"></a>Activation du pare-feu de coffre de clés

Si vous activez un pare-feu de coffre de clés ou un réseau virtuel après avoir créé un registre chiffré, vous pouvez voir HTTP 403 ou d’autres erreurs avec l’importation d’image ou la rotation de clé automatisée. Pour corriger ce problème, reconfigurez l’identité et la clé managées que vous avez utilisées initialement pour le chiffrement. Consultez les étapes dans [Rotation de clé](#rotate-key). 

Si le problème persiste, veuillez contacter le support Azure.

### <a name="accidental-deletion-of-key-vault-or-key"></a>Suppression accidentelle du coffre à clés ou de la clé

La suppression de la chambre forte, ou de la clé, utilisée pour chiffrer un registre avec une clé gérée par le client rendra le contenu du registre inaccessible. Si la [suppression douce](../key-vault/general/soft-delete-overview.md) est activée dans le coffre-fort de clés (option par défaut), vous pouvez récupérer un objet de coffre-fort ou de coffre-fort de clés supprimé et reprendre les opérations de registre.

Pour les scénarios de suppression et de récupération des coffres à clés, voir [Gestion de la récupération des coffres à clés Azure avec protection contre la suppression douce et la purge](../key-vault/general/key-vault-recovery.md).

## <a name="next-steps"></a>Étapes suivantes

* Apprenez-en davantage sur le [chiffrement au repos dans Azure](../security/fundamentals/encryption-atrest.md).
* Apprenez-en davantage sur les stratégies d’accès et sur la façon de [sécuriser l’accès à un coffre de clés](../key-vault/general/security-features.md).


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-show]: /cli/azure/feature#az_feature_show
[az-group-create]: /cli/azure/group#az_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-keyvault-create]: /cli/azure/keyvault#az_keyvault_create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az_keyvault_key_create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az_keyvault_set_policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az_keyvault_delete_policy
[az-resource-show]: /cli/azure/resource#az_resource_show
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az_acr_encryption_rotate_key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az_acr_encryption_show
