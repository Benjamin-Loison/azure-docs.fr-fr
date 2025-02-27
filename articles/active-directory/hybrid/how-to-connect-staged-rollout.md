---
title: 'Azure AD Connect : Authentification cloud par lancement intermédiaire | Microsoft Docs'
description: Cet article explique comment migrer de l’authentification fédérée vers l’authentification cloud à l’aide d’un lancement intermédiaire.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/03/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35dcb1125451e378d79aa7bab4c4e066ccab9acb
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130044305"
---
# <a name="migrate-to-cloud-authentication-using-staged-rollout"></a>Migrer vers l’authentification cloud avec un lancement intermédiaire

Le lancement intermédiaire vous permet de tester des groupes d'utilisateurs de manière sélective avec des fonctionnalités d'authentification cloud comme Azure AD Multi-Factor Authentication (MFA), l'accès conditionnel, Identity Protection pour les informations d'identification divulguées ou Identity Governance avant le basculement de vos domaines.  Cet article explique comment procéder. Toutefois, avant d’effectuer le lancement intermédiaire, vous devez prendre en compte les implications si une ou plusieurs des conditions suivantes sont remplies :
    
-  Vous utilisez actuellement un serveur Multi-Factor Authentication local. 
-  Vous utilisez des cartes à puce pour l’authentification. 
-  Votre serveur actuel offre certaines fonctionnalités de fédération uniquement.

Avant d’essayer cette fonctionnalité, nous vous suggérons de consulter notre guide sur le choix de la méthode d’authentification appropriée. Pour plus d’informations, consultez la table « Comparaison des méthodes dans [Choisir la méthode d’authentification adaptée à votre solution d’identité hybride Azure Active Directory](./choose-ad-authn.md#comparing-methods).

Pour obtenir une vue d’ensemble de ces fonctionnalités, consultez « Azure Active Directory : qu’est ce que le lancement intermédiaire ? » vidéo :

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Prérequis

-   Vous disposez d’un locataire Azure Active Directory (Azure AD) avec des domaines fédérés.

-   Vous avez décidé de passer à l’une des deux options suivantes :
    - **Option A** - *synchronisation de hachage de mot de passe (sync)* .  Pour plus d'informations, consultez [Qu'est-ce que la synchronisation de hachage de mot de passe ?](whatis-phs.md). 
    - **Option B** - *authentification directe*.  Pour plus d’informations, consultez [Qu’est-ce que l’authentification directe ?](how-to-connect-pta.md).  
    
    Pour ces deux options, nous vous recommandons d’activer l’authentification unique (SSO) pour une expérience de connexion silencieuse. 
    Pour les appareils Windows 7 ou 8.1 joints à un domaine, nous vous recommandons d’utiliser l’authentification unique transparente. Pour plus d'informations, consultez [Qu'est-ce que l’authentification unique transparente ?](how-to-connect-sso.md). 
    Pour Windows 10, Windows Server 2016 et versions ultérieures, il est recommandé d’utiliser l’authentification unique via le [jeton d’actualisation principal (PRT)](../devices/concept-primary-refresh-token.md) avec les [appareils joints Azure AD](../devices/concept-azure-ad-join.md), les [appareils joints Azure AD hybride](../devices/concept-azure-ad-join-hybrid.md) ou les appareils personnels inscrits via Ajouter un compte professionnel ou scolaire.

-   Vous avez configuré toutes les stratégies appropriées d’accès conditionnel et de marque de locataire dont vous avez besoin pour les utilisateurs migrés vers l’authentification cloud.

-   Si vous envisagez d'utiliser Azure AD Multi-Factor Authentication, nous vous recommandons une [inscription combinée à la réinitialisation de mot de passe en libre-service (SSPR) et à l'authentification multifacteur](../authentication/concept-registration-mfa-sspr-combined.md) pour permettre à vos utilisateurs d'inscrire leurs méthodes d'authentification en une seule fois. Remarque : Lors de l’utilisation de SSPR pour réinitialiser le mot de passe ou changer le mot de passe en utilisant la page MyProfile dans la phase de déploiement intermédiaire, Azure AD Connect doit synchroniser le hachage du nouveau mot de passe, ce qui peut prendre jusqu’à 2 minutes après la réinitialisation.

-   Pour utiliser la fonctionnalité de lancement intermédiaire, vous devez être l’administrateur général de votre locataire.

-   Pour activer *l’authentification unique transparente* sur une forêt Active Directory particulière, vous devez être l’administrateur de domaine.

-  Si vous déployez la jonction Azure AD ou Azure AD Hybride, vous devez installer la mise à jour Windows 10 1903.


## <a name="supported-scenarios"></a>Scénarios pris en charge

Les scénarios suivants sont pris en charge pour le lancement intermédiaire. Cette fonction convient uniquement aux :

- utilisateurs configurés pour Azure AD à l’aide de Azure AD Connect. Elle ne s’applique pas aux utilisateurs cloud uniquement.

- Trafic de connexion utilisateur sur les navigateurs et les clients *d’authentification modernes*. Les applications ou les services cloud utilisant l’authentification héritée sont redirigés vers des flux d’authentification fédérés. Par exemple, Exchange Online avec l’authentification moderne désactivée ou Outlook 2010, qui ne prend pas en charge l’authentification moderne.

- La taille du groupe est actuellement limitée à 50 000 utilisateurs.  Si vos groupes dépassent 50 000 utilisateurs, nous vous recommandons de les fractionner en plusieurs groupes pour le lancement intermédiaire.

- Acquisition du jeton d’actualisation principal de jonction Azure AD ou de jonction hybride pour Windows 10 sans ligne de mire sur le serveur de fédération pour Windows 10 version 1903 et versions ultérieures lorsque l’UPN de l’utilisateur est routable et que le suffixe de domaine est vérifié dans Azure AD.

## <a name="unsupported-scenarios"></a>Scénarios non pris en charge

Les scénarios suivants ne sont pas pris en charge pour le lancement intermédiaire :

- L’authentification héritée telle que POP3 et SMTP n’est pas prise en charge.

- Certaines applications envoient le paramètre de requête « domain_hint » à Azure AD pendant l’authentification. Ces flux se poursuivront et les utilisateurs activés pour le lancement intermédiaire continueront d’utiliser la fédération pour l’authentification.

<!-- -->

- Les administrateurs peuvent déployer l’authentification cloud à l’aide de groupes de sécurité. Pour éviter toute latence de synchronisation lorsque vous utilisez des groupes de sécurité Active Directory locaux, nous vous recommandons d’utiliser des groupes de sécurité cloud. Les conditions suivantes s’appliquent :

    - Vous pouvez utiliser 10 groupes maximum par fonctionnalité. Autrement dit, vous pouvez utiliser 10 groupes pour la *synchronisation du hachage de mot de passe*, *l’authentification directe* et *l’authentification unique transparente*.
    - Les groupes imbriqués *ne sont pas pris en charge*. 
    - Les groupes dynamiques *ne sont pas pris en charge* pour le lancement intermédiaire.
    - Les objets contact du groupe bloquent l’ajout du groupe.

- Lorsque vous ajoutez pour la première fois un groupe de sécurité au lancement intermédiaire, vous êtes limité à 200 utilisateurs pour éviter que l’expérience utilisateur n’expire. Une fois que vous avez ajouté le groupe, vous pouvez y ajouter directement d’autres utilisateurs, selon les besoins.

- Quand des utilisateurs sont sélectionnés pour le lancement intermédiaire, la stratégie d’expiration de mot de passe est définie sur 90 jours sans option de personnalisation. 

- Acquisition du jeton d’actualisation principal de jonction Azure AD ou de jonction hybride pour Windows 10 pour les versions de Windows 10 antérieures à la version 1903. Ce scénario revient au point de terminaison WS-Trust du serveur de fédération, même si l’utilisateur qui se connecte figure dans l’étendue du déploiement par étapes.

- Acquisition du jeton d’actualisation principal de jonction Azure AD ou de jonction hybride pour Windows 10 pour toutes les versions lorsque l’UPN local de l’utilisateur n’est pas routable. Ce scénario revient au point de terminaison WS-Trust en mode de déploiement par étapes, mais il cesse de fonctionner lorsque la migration par étapes est terminée et que l’authentification de l’utilisateur ne dépend plus du serveur de fédération.

- Si vous disposez d’une configuration VDI non persistante avec la version 1903 ou une version ultérieure de Windows 10, vous devez rester sur un domaine fédéré. Le passage à un domaine managé n’est pas pris en charge sur une infrastructure VDI non persistante. Pour plus d’informations, consultez [Identité d’appareil et virtualisation des postes de travail](../devices/howto-device-identity-virtual-desktop-infrastructure.md).

- Si vous disposez d’un certificat de confiance hybride Windows Hello Entreprise avec des certificats émis par le biais de votre serveur de fédération agissant en tant qu’autorité d’inscription ou utilisateur de carte à puce, le scénario n’est pas pris en charge dans un déploiement par étapes. 

- L’inscription Autopilot n’est pas prise en charge dans le déploiement par étapes. Les utilisateurs activés pour le déploiement par étapes continuent d’utiliser l’authentification fédérée lors de l’inscription Autopilot. Si Windows 10 version 1903 ou ultérieure est installé sur votre appareil, après l’inscription Autopilot, l’ensemble des demandes d’authentification passent par le déploiement par étapes. 

  >[!NOTE]
  >Vous devez encore effectuer le basculement final de l’authentification fédérée à l’authentification cloud à l’aide d’Azure AD Connect ou de PowerShell. Le lancement intermédiaire ne fait pas basculer les domaines d’un état fédéré à managé.  Pour plus d’informations sur le basculement de domaine, consultez [Migrer de la fédération à la synchronisation de hachage de mot de passe](./migrate-from-federation-to-cloud-authentication.md) et [Migrer de la fédération à l’authentification directe](./migrate-from-federation-to-cloud-authentication.md).
  
## <a name="get-started-with-staged-rollout"></a>Prise en main du lancement intermédiaire

Pour tester la connexion de *synchronisation de hachage de mot de passe* à l’aide du lancement intermédiaire, suivez les instructions de travail préalable de la section suivante.

Pour savoir quel applet de commande PowerShell utiliser, consultez [la version préliminaire Azure AD 2.0](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged_rollout).

## <a name="pre-work-for-password-hash-sync"></a>Travail préalable pour la synchronisation de hachage de mot de passe

1. Activez la *synchronisation de hachage de mot de passe* dans la page [Fonctionnalités facultatives](how-to-connect-install-custom.md#optional-features) d’Azure AD Connect. 

   ![Capture d’écran de la page Fonctionnalités facultatives d’Azure Active Directory Connect](media/how-to-connect-staged-rollout/staged-1.png)

1. Assurez-vous qu’un cycle complet de *synchronisation de hachage de mot de passe* a été exécuté pour que tous les hachages de mot de passe des utilisateurs aient été synchronisés avec Azure AD. Pour vérifier l’état de la *synchronisation de hachage de mot de passe*, vous pouvez utiliser les diagnostics PowerShell dans [Détecter un problème de synchronisation de hachage de mot de passe avec Azure AD Connect](tshoot-connect-password-hash-synchronization.md).

   ![Capture d’écran du journal de résolution des problèmes AADConnect](./media/how-to-connect-staged-rollout/staged-2.png)

Si vous souhaitez tester *l’authentification directe* à l’aide du lancement intermédiaire, activez-la en suivant les instructions de travail préalable de la section suivante.

## <a name="pre-work-for-pass-through-authentication"></a>Travail préalable pour l’authentification directe

1. Reconnaitre un serveur exécutant Windows Server 2012 R2 (ou une version ultérieure) sur lequel vous souhaitez que l’agent *d’authentification directe* s’exécute. 

   *Ne choisissez pas* le serveur Azure AD Connect.  Assurez-vous que le serveur est joint à un domaine, qu’il peut authentifier les utilisateurs sélectionnés à l’aide d’Active Directory et qu’il peut communiquer avec Azure AD sur les ports de sortie ou les URL. Pour plus d’informations, voir la section « Étape 1 : vérifier les prérequis » du [Démarrage rapide : authentification unique transparente Azure AD](how-to-connect-sso-quick-start.md).

1. [Téléchargez l’agent d’authentification Azure AD Connect](https://aka.ms/getauthagent) et installez-le sur le serveur. 

1. Pour activer la [haute disponibilité](how-to-connect-sso-quick-start.md), installez des agents d’authentification supplémentaires sur d’autres serveurs.

1. Vérifiez que vous avez configuré vos [paramètres de verrouillage intelligent](../authentication/howto-password-smart-lockout.md) de manière appropriée. Cela permet de garantir que les comptes Active Directory locaux de vos utilisateurs ne sont pas verrouillés par les mauvais intervenants.

Nous vous recommandons d’activer *l’authentification unique transparente*, quelle que soit la méthode de connexion (*synchronisation de hachage de mot de passe* ou *authentification directe*) que vous sélectionnez pour le lancement intermédiaire. Pour activer *l’authentification unique transparente*, suivez les instructions de travail préalable de la section suivante.

## <a name="pre-work-for-seamless-sso"></a>Travail préalable pour l’authentification unique transparente

Activez l’*authentification unique fluide* sur les forêts Active Directory en utilisant PowerShell. Si vous avez plusieurs forêts Active Directory, activez-la pour chaque forêt individuellement. L’*authentification unique fluide* est déclenchée seulement pour les utilisateurs sélectionnés pour le déploiement intermédiaire. Cela n’a aucun impact sur votre configuration de fédération existante.

Pour activer *l’authentification unique transparente* procédez comme suit :

1. Connectez-vous au serveur Azure AD Connect.

2. Accédez au dossier *%programfiles%\\Microsoft Azure Active Directory Connect*.

3. Importez le module PowerShell de *l’authentification unique transparente* en exécutant la commande suivante : 

   `Import-Module .\AzureADSSO.psd1`

4. Exécutez PowerShell ISE en tant qu’administrateur. Dans PowerShell, appelez `New-AzureADSSOAuthenticationContext`. Cette commande permet d’ouvrir un volet dans lequel vous pouvez entrer les informations d’identification d’administrateur général de votre locataire.

5. Appelez `Get-AzureADSSOStatus | ConvertFrom-Json`. Cette commande permet d’afficher la liste des forêts Azure Directory (voir la liste « Domaines ») dans lesquelles cette fonctionnalité a été activée. Par défaut, elle est définie sur False au niveau du locataire.

   ![Exemple de la sortie Windows PowerShell](./media/how-to-connect-staged-rollout/staged-3.png)

6. Appelez `$creds = Get-Credential`. Quand vous y êtes invité, entrez les informations d’identification d’administrateur de domaine pour la forêt Azure Directory souhaitée.

7. Appelez `Enable-AzureADSSOForest -OnPremCredentials $creds`. Cette commande permet de créer le compte d’ordinateur AZUREADSSOACC à partir du contrôleur de domaine local pour la forêt Azure Directory requise pour *l’authentification unique transparente*.

8. *L’authentification unique transparente* requiert que les URL soient dans la zone intranet. Pour déployer ces URL à l’aide de stratégies de groupe, consultez le [Démarrage rapide : authentification unique transparente Azure AD](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature).

9. Pour obtenir une procédure pas à pas complète, vous pouvez également télécharger nos [plans de déploiement](https://aka.ms/SeamlessSSODPDownload) pour une *authentification unique transparente*.

## <a name="enable-staged-rollout"></a>Activer le lancement intermédiaire

Pour déployer une fonctionnalité spécifique (*authentification directe*, *synchronisation de hachage de mot de passe* ou *authentification unique transparente*) à un certain ensemble d’utilisateurs dans un groupe, suivez les instructions indiquées aux sections suivantes :

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Activer le lancement intermédiaire d’une fonctionnalité spécifique sur votre locataire

Vous pouvez déployer l’une de ces options :

- **Option A** - *synchronisation de hachage de mot de passe* + *authentification unique transparente*
- **Option B** - *authentification directe* + *authentification unique transparente*
- **Non prise en charge** - *synchronisation de hachage de mot de passe* + *authentification directe* + *authentification unique transparente*

Effectuez les actions suivantes :

1. Pour accéder à l’expérience utilisateur, connectez-vous au [portail Azure AD](https://aka.ms/stagedrolloutux).

2. Sélectionnez le lien **Activer le lancement intermédiaire pour la connexion utilisateur managée**.

   Par exemple, si vous souhaitez activer *l’option A*, assurez-vous que les contrôles **synchronisation de hachage de mot de passe** et **authentification unique transparente** sont sur **Activer**, comme indiqué dans les images suivantes.

   

  

3. Ajoutez les groupes à la fonctionnalité pour activer *l’authentification directe* et *l’authentification unique transparente*. Assurez-vous que les groupes de sécurité n’ont pas plus de 200 membres au départ pour éviter l’expiration de l’expérience utilisateur.

   

   >[!NOTE]
   >Les membres d’un groupe sont automatiquement activés pour le lancement intermédiaire. Les groupes dynamiques et imbriqués ne sont pas pris en charge pour le lancement intermédiaire.
   >Quand vous ajoutez un nouveau groupe, les utilisateurs du groupe (jusqu’à 200 utilisateurs pour un nouveau groupe) sont mis à jour pour utiliser l’authentification managée immédiatement. La prise en compte de la modification d’un groupe (ajout ou suppression d’utilisateurs) peut prendre jusqu’à 24 heures.
   >L’authentification unique fluide s’applique seulement si les utilisateurs se trouvent dans le groupe Authentification unique fluide et également dans un groupe PTA ou PHS.

## <a name="auditing"></a>Audit

Nous avons activé les événements d’audit pour les différentes actions que nous effectuons pour le lancement intermédiaire :

- Vérifiez l’événement lorsque vous activez le lancement intermédiaire pour la *synchronisation de hachage de mot de passe*, *l’authentification directe* ou *l’authentification unique transparente*.

  >[!NOTE]
  >Un événement est consigné lorsque *l’authentification unique transparente* est activée à l’aide du lancement intermédiaire.

  ![Le volet « Créer une stratégie de déploiement pour la fonctionnalité » – Onglet Activités](./media/how-to-connect-staged-rollout/staged-7.png)

  ![Le volet « Créer une stratégie de déploiement pour la fonctionnalité » – Onglet Propriétés modifiées](./media/how-to-connect-staged-rollout/staged-8.png)

- Vérifiez l’événement lorsqu’un groupe est ajouté à la *synchronisation de hachage de mot de passe*, *l’authentification directe* ou *l’authentification unique transparente*.

  >[!NOTE]
  >Un événement est consigné lorsqu’un groupe est ajouté à *l’authentification unique transparente* pour le lancement intermédiaire.

  ![Le volet « Ajouter un groupe au lancement de fonctionnalités » – Onglet Activités](./media/how-to-connect-staged-rollout/staged-9.png)

  ![Le volet « Ajouter un groupe au lancement de fonctionnalités » – Onglet Propriétés modifiées](./media/how-to-connect-staged-rollout/staged-10.png)

- Vérifiez l’événement lors de l’activation d’un utilisateur ajouté au groupe pour le lancement intermédiaire.

  ![Le volet « Ajouter un utilisateur au lancement de fonctionnalités » – Onglet Activités](media/how-to-connect-staged-rollout/staged-11.png)

  ![Le volet « Ajouter un utilisateur au lancement de fonctionnalités » – Onglet Cible(s)](./media/how-to-connect-staged-rollout/staged-12.png)

## <a name="validation"></a>Validation

Pour tester la connexion avec la *synchronisation de hachage de mot de passe* ou *l’authentification directe* (connexion par nom d’utilisateur/mot de passe), procédez comme suit :

1. À partir de l’extranet, accédez à la[page Applications](https://myapps.microsoft.com) dans une session de navigation privée et entrez le nom d’utilisateur principal (UPN) du compte d’utilisateur sélectionné pour le lancement intermédiaire.

   Les utilisateurs qui ont été sélectionnés pour le lancement intermédiaire ne sont pas redirigés vers votre page de connexion fédérée. Ils sont en effet invités à se connecter à la page de connexion Azure AD à la marque du locataire.

1. Vérifiez que la connexion s’affiche bien dans le [rapport d’activité de connexion Azure AD](../reports-monitoring/concept-sign-ins.md) en filtrant par UserPrincipalName.

Pour tester la connexion avec *l’authentification unique transparente* :

1. À partir de l’intranet, accédez à la[page Applications](https://myapps.microsoft.com) dans une session de navigation privée et entrez le nom d’utilisateur principal (UPN) du compte d’utilisateur sélectionné pour le lancement intermédiaire.

   Les utilisateurs ayant été ciblés pour le lancement intermédiaire de *l’authentification unique transparente* verront un message « Tentative de connexion… » avant la connexion silencieuse.

1. Vérifiez que la connexion s’affiche bien dans le [rapport d’activité de connexion Azure AD](../reports-monitoring/concept-sign-ins.md) en filtrant par UserPrincipalName.

   Pour suivre les connexions utilisateur qui se produisent toujours sur les services de fédération Active Directory (AD FS) pour les utilisateurs sélectionnés pour le lancement intermédiaire, suivez les instructions fournies dans [Résolution des problèmes AD FS : Événements et journalisation](/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Consultez la documentation du fournisseur pour savoir comment le vérifier sur les fournisseurs de fédération tiers.

## <a name="monitoring"></a>Surveillance
Grâce aux nouveaux workbooks d’authentification hybride du portail Azure, vous pouvez superviser les utilisateurs et groupes ajoutés ou supprimés dans un déploiement par étapes ainsi que les connexions des utilisateurs lors du déploiement par étapes.

 ![Workbooks d’authentification hybride](./media/how-to-connect-staged-rollout/staged-13.png)

## <a name="remove-a-user-from-staged-rollout"></a>Supprimer un utilisateur du lancement intermédiaire

La suppression de l’utilisateur du groupe désactive le lancement intermédiaire pour l’utilisateur. Pour désactiver la fonctionnalité de déploiement intermédiaire, assurez-vous que le contrôle est sur **DÉSACTIVER**.

## <a name="frequently-asked-questions"></a>Forum aux questions

**Q : Puis-je utiliser cette fonctionnalité en production ?**

A : Oui, vous pouvez utiliser cette fonctionnalité dans votre locataire de production, mais nous vous recommandons de l’essayer d’abord dans votre locataire de test.

**Q : Cette fonctionnalité peut-elle être utilisée pour maintenir une « coexistence » permanente, où certains utilisateurs utilisent l’authentification fédérée et d’autres l’authentification cloud ?**

A : Non, cette fonctionnalité est conçue pour tester l’authentification cloud. Après avoir testé avec succès quelques groupes d’utilisateurs, vous devez passer à l’authentification cloud. Nous déconseillons un état mixte permanent, car cela peut entraîner des flux d’authentification inattendus.

**Q : Puis-je utiliser PowerShell pour effectuer un lancement intermédiaire ?**

A : Oui. Pour découvrir comment utiliser PowerShell pour effectuer un lancement intermédiaire, consultez la [version préliminaire Azure AD](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged_rollout).

## <a name="next-steps"></a>Étapes suivantes
- [Version préliminaire Azure AD 2.0](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged_rollout )
- [Changer la méthode de connexion pour la synchronisation de hachage de mot de passe](./migrate-from-federation-to-cloud-authentication.md)
- [Changer la méthode de connexion pour l’authentification directe](./migrate-from-federation-to-cloud-authentication.md)
- [Guide interactif du déploiement par étapes](https://mslearn.cloudguides.com/en-us/guides/Test%20migration%20to%20cloud%20authentication%20using%20staged%20rollout%20in%20Azure%20AD)
