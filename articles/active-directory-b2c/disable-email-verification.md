---
title: Désactiver la vérification par e-mail lors de l’inscription du client
titleSuffix: Azure AD B2C
description: Découvrez comment désactiver la vérification par e-mail lors de l’inscription de client dans Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 902b7575f7da414abcd34acbfbcd426e0415a948
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130222606"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Désactiver la vérification par e-mail lors de l’inscription de client dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Par défaut, Azure Active Directory B2C (Azure AD B2C) vérifie l’adresse e-mail de votre client pour les comptes locaux (comptes des utilisateurs qui s’inscrivent avec une adresse e-mail ou un nom d’utilisateur). Azure AD B2C vérifie la validité de l’adresse e-mail en demandant au client de la confirmer pendant le processus d’inscription. Il empêche également des acteurs malveillants d’utiliser des processus automatisés pour générer des comptes frauduleux dans vos applications.

Certains développeurs d’applications préfèrent ignorer la vérification par e-mail pendant le processus d’inscription et amener les clients à vérifier l’adresse e-mail ultérieurement. Pour ce faire, Azure Active Directory B2C peut être configuré afin de désactiver la vérification par e-mail. Cette opération crée un processus de connexion plus simple et offre aux développeurs la possibilité de différencier les clients qui ont vérifié leur adresse e-mail de ceux qui ne l’ont pas encore fait.

> [!WARNING]
> La désactivation de la vérification par e-mail dans le processus d’inscription peut entraîner la réception de courrier indésirable. Si vous désactivez la vérification par e-mail par défaut d’Azure AD B2C, nous vous recommandons d’implémenter un système de vérification de remplacement.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]
## <a name="disable-email-verification"></a>Désactiver la vérification e-mail

::: zone pivot="b2c-user-flow"

Pour désactiver la vérification par e-mail, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com)
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Flux d’utilisateurs**.
1. Sélectionnez le flux d’utilisateur pour lequel vous souhaitez désactiver la vérification par e-mail.
1. Sélectionnez **Mises en page**.
1. Sélectionnez **Page d’inscription à un compte Local**.
1. Sous **Attributs utilisateur**, sélectionnez **Adresse e-mail**.
1. Dans la liste déroulante **Requiert une vérification**, sélectionnez **Non**.
1. Sélectionnez **Enregistrer**. La vérification par e-mail est maintenant désactivée pour ce flux d’utilisateur.

::: zone-end

::: zone pivot="b2c-custom-policy"
Le profil technique **LocalAccountSignUpWithLogonEmail** est [autodéclaré](self-asserted-technical-profile.md) ; il est appelé pendant le déroulement de l’inscription. Pour désactiver la vérification par e-mail, définissez les métadonnées `EnforceEmailVerification` sur false. Remplacez les profils techniques LocalAccountSignUpWithLogonEmail dans le fichier d’extension. 

1. Ouvrez le fichier d’extensions de votre stratégie. Par exemple <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`**</em>.
1. Recherchez l’élément `ClaimsProviders`. Si l’élément n’existe pas, ajoutez-le.
1. Ajoutez le fournisseur de revendications suivant à l’élément `ClaimsProviders` :

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-policy"></a>Tester votre stratégie 

1. Connectez-vous au [portail Azure](https://portal.azure.com)
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Dans le menu de gauche, sélectionnez **Azure AD B2C**. Ou sélectionnez **Tous les services**, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Flux d’utilisateurs**.
1. Sélectionnez le flux d’utilisateur pour lequel vous souhaitez désactiver la vérification par e-mail. Par exemple, *B2C_1_signinsignup*.
1. Pour tester votre stratégie, sélectionnez **Exécuter le flux d’utilisateur**.
1. Pour **Application**, sélectionnez l’application web *testapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Cliquez sur **Exécuter le flux d’utilisateur**.
1. Vous devriez pouvoir vous inscrire au moyen d’une adresse mail sans la validation.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Mettre à jour et tester le fichier de partie de confiance

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser le répertoire qui contient votre locataire Azure AD. Sélectionnez l’icône **Répertoires + Abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire Azure AD dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Inscriptions d’applications**.
1. Sélectionnez **Infrastructure d’expérience d’identité**.
1. Sélectionnez **Charger une stratégie personnalisée**, puis chargez les deux fichiers de stratégie que vous avez modifiés.
1. Sélectionnez la stratégie d’inscription et de connexion que vous avez chargée, puis cliquez sur le bouton **Exécuter maintenant**.
1. Vous devriez pouvoir vous inscrire au moyen d’une adresse mail sans la validation.

::: zone-end


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [personnaliser l’interface utilisateur dans Azure Active Directory B2C](customize-ui-with-html.md)

