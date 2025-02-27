---
title: Passer un jeton d’accès de fournisseur d’identité à votre application
titleSuffix: Azure AD B2C
description: Découvrez comment passer un jeton d’accès pour les fournisseurs d’identité OAuth 2.0 en tant que revendication dans un flux utilisateur dans Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 73a4b909e5f6e54923aebd2ddec65f9ce80575b2
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130217522"
---
# <a name="pass-an-identity-provider-access-token-to-your-application-in-azure-active-directory-b2c"></a>Passer un jeton d’accès de fournisseur d’identité à votre application dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Un [flux utilisateur](user-flow-overview.md) dans Azure AD B2C (Azure Active Directory B2C) permet aux utilisateurs de votre application de s’inscrire ou de se connecter à un fournisseur d’identité. Au départ, Azure AD B2C reçoit un [jeton d’accès](tokens-overview.md) du fournisseur d’identité. Azure AD B2C utilise ce jeton pour récupérer des informations sur l’utilisateur. Vous activez une revendication dans votre flux utilisateur pour passer le jeton aux applications que vous inscrivez dans Azure AD B2C.

::: zone pivot="b2c-user-flow"

Azure AD B2C permet uniquement de passer le jeton d’accès de fournisseurs d’identité [OAuth 2.0](add-identity-provider.md) (dont font partie [Facebook](identity-provider-facebook.md) et [Google](identity-provider-google.md)). Pour tous les autres fournisseurs d’identité, la revendication est retournée vide.

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure AD B2C prend en charge la transmission du jeton d'accès des fournisseurs d'identité [OAuth 2.0](authorization-code-flow.md) et [OpenID Connect](openid-connect.md). Pour tous les autres fournisseurs d’identité, la revendication est retournée vide.

::: zone-end

Le diagramme suivant montre comment un jeton de fournisseur d’identité est retourné à votre application : 

![Flux du transfert direct du fournisseur d’identité](./media/idp-pass-through-user-flow/identity-provider-pass-through-flow.png)

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="enable-the-claim"></a>Activer la revendication

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
1. Sélectionnez **Flux utilisateur (stratégies)** , puis votre flux utilisateur. Par exemple, **B2C_1_signupsignin1**.
1. Cliquez sur **Revendications de l’application**.
1. Activez la revendication **Jeton d’accès du fournisseur d’identité**.

    ![Activer la revendication Jeton d’accès du fournisseur d’identité](./media/idp-pass-through-user-flow/identity-provider-pass-through-app-claim.png)

1. Cliquez sur **Enregistrer** pour enregistrer le flux utilisateur.

## <a name="test-the-user-flow"></a>Tester le flux utilisateur

Quand vous testez vos applications dans Azure AD B2C, il peut être utile de retourner le jeton Azure AD B2C à `https://jwt.ms` pour passer en revue les revendications qu’il contient.

1. Dans la page Vue d’ensemble du flux utilisateur, sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application que vous avez précédemment inscrite. Pour voir le jeton dans l’exemple ci-dessous, l’**URL de réponse** doit indiquer `https://jwt.ms`.
1. Cliquez sur **Exécuter le flux d’utilisateur**, puis connectez-vous avec les informations d’identification de votre compte. Vous devez voir le jeton d’accès du fournisseur d’identité dans la revendication **idp_access_token**.

    Vous devriez voir quelque chose de similaire à l’exemple suivant :

    ![Jeton décodé dans jwt.ms avec bloc idp_access_token mis en surbrillance](./media/idp-pass-through-user-flow/identity-provider-pass-through-token.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-the-claim-elements"></a>Ajouter les éléments de la revendication

1. Ouvrez votre fichier *TrustframeworkExtensions.xml* et ajoutez l’élément **ClaimType** avec l’identificateur `identityProviderAccessToken` à l’élément **ClaimsSchema** :

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

1. Ajoutez l’élément **OutputClaim** à l’élément **TechnicalProfile** pour chaque fournisseur d’identité OAuth 2.0 dont vous souhaitez avoir le jeton d’accès. L’exemple suivant montre l’élément ajouté au profil technique de Facebook :

    ```xml
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Enregistrez le fichier *TrustframeworkExtensions.xml*.
1. Ouvrez le fichier de stratégie de votre partie de confiance, par exemple *SignUpOrSignIn.xml*, puis ajoutez l’élément **OutputClaim** à **TechnicalProfile** :

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

1. Enregistrez le fichier de stratégie.

## <a name="test-your-policy"></a>Tester votre stratégie

Quand vous testez vos applications dans Azure AD B2C, il peut être utile de retourner le jeton Azure AD B2C à `https://jwt.ms` pour passer en revue les revendications qu’il contient.

### <a name="upload-the-files"></a>Téléchargement des fichiers

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Infrastructure d’expérience d’identité**.
1. Dans la page Stratégies personnalisées, cliquez sur **Charger une stratégie**.
1. Activez **Remplacer la stratégie si elle existe**, puis recherchez et sélectionnez le fichier *TrustframeworkExtensions.xml*.
1. Sélectionnez **Télécharger**.
1. Répétez les étapes 5 à 7 pour le fichier de la partie de confiance (par exemple, *SignUpOrSignIn.xml*).

### <a name="run-the-policy"></a>Exécuter la stratégie

1. Ouvrez la stratégie que vous avez changée. Par exemple, *B2C_1A_signup_signin*.
1. Pour **Application**, sélectionnez l’application que vous avez précédemment inscrite. Pour voir le jeton dans l’exemple ci-dessous, l’**URL de réponse** doit indiquer `https://jwt.ms`.
1. Sélectionnez **Exécuter maintenant**.

    Vous devriez voir quelque chose de similaire à l’exemple suivant :

    ![Jeton décodé dans jwt.ms avec bloc idp_access_token mis en surbrillance](./media/idp-pass-through-user-flow/identity-provider-pass-through-token-custom.png)

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage dans la [présentation des jetons Azure AD B2C](tokens-overview.md).
