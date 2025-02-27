---
title: Concepts de base d’Azure Attestation
description: Concepts de base d’Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: bc405fa293eec4f991ca5cab51d3d8f2a4c3c816
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129233255"
---
# <a name="basic-concepts"></a>Concepts de base

Voici quelques concepts de base relatifs à Microsoft Azure Attestation.

## <a name="json-web-token-jwt"></a>JSON Web Token (JWT)

[JSON Web Token](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) (JWT) est une méthode [RFC7519](https://tools.ietf.org/html/rfc7519) de norme ouverte pour la transmission d’informations de manière sécurisée entre des tiers sous la forme d’un objet JSON (JavaScript Object Notation). Ces informations peuvent être vérifiées et approuvées, car elles sont signées numériquement. Les jetons JWT peuvent être signés à l’aide d’un secret ou d’une paire de clés publique/privée.

## <a name="json-web-key-jwk"></a>Clé web JSON (JWK)

[JSON Web Key](https://tools.ietf.org/html/rfc7517) (JWK) est une structure de données JSON qui représente une clé de chiffrement. Cette spécification définit également une structure de données JSON JWK Set qui représente un ensemble de clés JWK.

## <a name="attestation-provider"></a>Fournisseur d’attestations

Le fournisseur d’attestations appartient au fournisseur de ressources Azure nommé Microsoft.Attestation. Le fournisseur de ressources est un point de terminaison de service qui fournit un contrat REST Azure Attestation et qui est déployé à l’aide d’[Azure Resource Manager](../azure-resource-manager/management/overview.md). Chaque fournisseur d’attestations honore une stratégie spécifique et détectable. Les fournisseurs d’attestations sont créés avec une stratégie par défaut pour chaque type d’attestation (notez que l’enclave VBS n’a pas de stratégie par défaut). Pour plus d’informations sur la stratégie par défaut pour SGX, consultez [Exemples de stratégie d’attestation](policy-examples.md).

### <a name="regional-shared-provider"></a>Fournisseur partagé régional

Azure Attestation propose un fournisseur partagé régional dans chaque région disponible. Les clients ont le choix entre utiliser le fournisseur partagé régional pour l’attestation ou créer leurs propres fournisseurs avec des stratégies personnalisées. Les fournisseurs partagés sont accessibles à tous les utilisateurs Azure AD. La stratégie associée ne peut pas être modifiée.

| Région | URI d’attestation | 
|--|--|
| USA Est | `https://sharedeus.eus.attest.azure.net` | 
| USA Ouest | `https://sharedwus.wus.attest.azure.net` | 
| Sud du Royaume-Uni | `https://shareduks.uks.attest.azure.net` | 
| Ouest du Royaume-Uni| `https://sharedukw.ukw.attest.azure.net  ` | 
| Est du Canada | `https://sharedcae.cae.attest.azure.net` | 
| Centre du Canada | `https://sharedcac.cac.attest.azure.net` | 
| Europe Nord | `https://sharedneu.neu.attest.azure.net` | 
| Europe Ouest| `https://sharedweu.weu.attest.azure.net` | 
| USA Est 2 | `https://sharedeus2.eus2.attest.azure.net` | 
| USA Centre | `https://sharedcus.cus.attest.azure.net` |
| Asie Sud-Est | `https://sharedsasia.sasia.attest.azure.net` | 
| Centre-Nord des États-Unis | `https://sharedncus.ncus.attest.azure.net` | 
| États-Unis - partie centrale méridionale | `https://sharedscus.scus.attest.azure.net` | 
| Gouvernement américain - Virginie | `https://sharedugv.ugv.attest.azure.us` | 
| Gouvernement des États-Unis – Arizona | `https://shareduga.uga.attest.azure.us` | 

## <a name="attestation-request"></a>Demande d’attestation

La demande d’attestation est un objet JSON sérialisé envoyé par une application cliente à un fournisseur d’attestations. L’objet de la demande pour l’enclave SGX a deux propriétés : 
- « Quote » : la valeur de la propriété « Quote » est une chaîne contenant une représentation encodée Base64URL de la déclaration d’attestation
- « EnclaveHeldData » : la valeur de la propriété « EnclaveHeldData » est une chaîne contenant une représentation encodée Base64URL des données détenues par l’enclave.

Azure Attestation valide le « Quote » fourni et vérifie ensuite que le hachage SHA256 des données détenues par l’enclave est exprimé dans les 32 premiers octets du champ reportData du Quote. 

## <a name="attestation-policy"></a>Stratégie d’attestation

La stratégie d’attestation est utilisée pour traiter la preuve d’attestation et peut être configurée par les clients. Au cœur d’Azure Attestation se trouve un moteur de stratégie, qui traite les revendications constituant la preuve. Les stratégies sont utilisées pour déterminer si Azure Attestation doit émettre un jeton d’attestation en fonction de la preuve (ou non) et, ainsi, endosser l’attesteur (ou non). En conséquence, si le traitement par toutes les stratégies n’est pas couronné de succès, aucun jeton JWT n’est émis.

Si la stratégie par défaut dans le fournisseur d’attestations ne répond pas aux besoins, les clients peuvent créer des stratégies personnalisées dans l’une des régions prises en charge par Azure Attestation. La gestion des stratégies est une fonctionnalité clé fournie aux clients par Azure Attestation. Les stratégies sont propres au type d’attestation et peuvent être utilisées pour identifier les enclaves, ajouter ou modifier des revendications dans un jeton de sortie. 

Pour obtenir des exemples de stratégies, consultez [Exemples de stratégie d’attestation](policy-examples.md).

## <a name="benefits-of-policy-signing"></a>Avantages de la signature de stratégie

Une stratégie d’attestation est ce qui détermine en dernier lieu si un jeton d’attestation doit être émis par Azure Attestation. La stratégie détermine également les revendications à générer dans le jeton d’attestation. Il est donc primordial que la stratégie évaluée par le service soit de fait la stratégie écrite par l’administrateur et qu’elle n’ait pas été falsifiée ou modifiée par des entités externes. 

Le modèle d’approbation définit le modèle d’autorisation du fournisseur d’attestations pour définir et mettre à jour la stratégie.  Deux modèles sont pris en charge : l’un est basé sur une autorisation Azure AD, tandis que l’autre, appelé « modèle isolé », repose sur la possession de clés de chiffrement gérées par le client.  Le modèle isolé permet à Azure Attestation de s’assurer que la stratégie soumise par le client n’est pas falsifiée.

Dans le modèle isolé, l’administrateur crée un fournisseur d’attestations en spécifiant un ensemble de certificats X.509 de signature approuvés dans un fichier. L’administrateur peut ensuite ajouter une stratégie signée au fournisseur d’attestations. Lors du traitement de la demande d’attestation, Azure Attestation valide la signature de la stratégie à l’aide de la clé publique représentée par le paramètre « jwk » ou « x5c » dans l’en-tête.  Azure Attestation vérifie également si la clé publique dans l’en-tête de la demande figure dans la liste des certificats de signature approuvés associés au fournisseur d’attestations. De cette façon, la partie de confiance (Azure Attestation) peut approuver une stratégie signée à l’aide des certificats X.509 dont elle a connaissance. 

Pour obtenir des exemples, consultez [Exemples de certificat du signataire de stratégie](policy-signer-examples.md).

## <a name="attestation-token"></a>Jeton d’attestation

La réponse d’Azure Attestation est une chaîne JSON dont la valeur contient un jeton JWT. Azure Attestation empaquette les revendications et génère un jeton JWT signé. L’opération de signature est effectuée à l’aide d’un certificat auto-signé dont le nom de l’objet correspond à l’élément AttestUri du fournisseur d’attestations.

L’API de récupération des métadonnées OpenID retourne une réponse de configuration OpenID telle que spécifiée par le [protocole OpenID Connect Discovery](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig). L’API récupère les métadonnées relatives aux certificats de signature utilisés par Azure Attestation.

Exemple de jeton JWT généré pour une enclave SGX :

```
{
  "alg": "RS256",
  "jku": "https://tradewinds.us.attest.azure.net/certs",
  "kid": <self signed certificate reference to perform signature verification of attestation token,
  "typ": "JWT"
}.{
  "aas-ehd": <input enclave held data>,
  "exp": 1568187398,
  "iat": 1568158598,
  "is-debuggable": false,
  "iss": "https://tradewinds.us.attest.azure.net",
  "maa-attestationcollateral": 
    {
      "qeidcertshash": <SHA256 value of QE Identity issuing certs>,
      "qeidcrlhash": <SHA256 value of QE Identity issuing certs CRL list>,
      "qeidhash": <SHA256 value of the QE Identity collateral>,
      "quotehash": <SHA256 value of the evaluated quote>, 
      "tcbinfocertshash": <SHA256 value of the TCB Info issuing certs>, 
      "tcbinfocrlhash": <SHA256 value of the TCB Info issuing certs CRL list>, 
      "tcbinfohash": <SHA256 value of the TCB Info collateral>
     },
  "maa-ehd": <input enclave held data>,
  "nbf": 1568158598,
  "product-id": 4639,
  "sgx-mrenclave": <SGX enclave mrenclave value>,
  "sgx-mrsigner": <SGX enclave msrigner value>,
  "svn": 0,
  "tee": "sgx"
  "x-ms-attestation-type": "sgx", 
  "x-ms-policy-hash": <>,
  "x-ms-sgx-collateral": 
    {
      "qeidcertshash": <SHA256 value of QE Identity issuing certs>,
      "qeidcrlhash": <SHA256 value of QE Identity issuing certs CRL list>,
      "qeidhash": <SHA256 value of the QE Identity collateral>,
      "quotehash": <SHA256 value of the evaluated quote>, 
      "tcbinfocertshash": <SHA256 value of the TCB Info issuing certs>, 
      "tcbinfocrlhash": <SHA256 value of the TCB Info issuing certs CRL list>, 
      "tcbinfohash": <SHA256 value of the TCB Info collateral>
     },
  "x-ms-sgx-ehd": <>, 
  "x-ms-sgx-is-debuggable": true,
  "x-ms-sgx-mrenclave": <SGX enclave mrenclave value>,
  "x-ms-sgx-mrsigner": <SGX enclave msrigner value>, 
  "x-ms-sgx-product-id": 1, 
  "x-ms-sgx-svn": 1,
  "x-ms-ver": "1.0",
  "x-ms-sgx-config-id": "000102030405060708090a0b0c0d8f99000102030405060708090a0b0c860e9a000102030405060708090a0b7d0d0e9b000102030405060708090a740c0d0e9c",
  "x-ms-sgx-config-svn": 3451,
  "x-ms-sgx-isv-extended-product-id": "8765432143211234abcdabcdef123456",
  "x-ms-sgx-isv-family-id": "1234567812344321abcd1234567890ab"
}.[Signature]
```

Certaines des revendications utilisées ci-dessus sont considérées comme dépréciées, mais sont entièrement prises en charge.  Nous vous recommandons de faire en sorte que tous les futurs outils et code utilisent les noms des revendications non dépréciées. Pour plus d’informations, consultez les [revendications émises par Azure Attestation](claim-sets.md).

Les revendications ci-dessous s’affichent uniquement dans le jeton d’attestation généré pour les plateformes de serveurs à processeur évolutif Intel® Xeon®. Les revendications ne s’affichent pas si l’enclave SGX n’est pas configurée avec une [séparation des clés et une prise en charge du partage](https://github.com/openenclave/openenclave/issues/3054).

**x-ms-sgx-config-id**

**x-ms-sgx-config-svn**

**x-ms-sgx-isv-extended-product-id**

**x-ms-sgx-isv-family-id**

## <a name="encryption-of-data-at-rest"></a>Chiffrement des données au repos

Pour protéger les données des clients, Azure Attestation conserve ses données dans Stockage Azure. Stockage Azure assure un chiffrement des données au repos à mesure qu’elles sont écrites dans les centres de données et les déchiffre pour permettre aux clients d’y accéder. Ce chiffrement s'effectue à l'aide d'une clé de chiffrement gérée par Microsoft. 

En plus de protéger les données dans Stockage Azure, Azure Attestation tire également parti d’Azure Disk Encryption (ADE) pour chiffrer les machines virtuelles du service. Pour Azure Attestation s’exécutant dans une enclave au sein d’environnements informatiques confidentiels Azure, l’extension ADE n’est actuellement pas prise en charge. En pareil cas, pour empêcher que les données soient stockées en mémoire, le fichier d’échange est désactivé. 

Les données des clients ne sont en aucun cas conservées sur les lecteurs de disque dur locaux de l’instance Azure Attestation.


## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour créer et signer une stratégie d’attestation](author-sign-policy.md)
- [Configurer Azure Attestation à l’aide de PowerShell](quickstart-powershell.md)
