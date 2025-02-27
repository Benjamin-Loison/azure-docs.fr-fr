---
title: Tutoriel - Créer un domaine managé Azure Active Directory Domain Services | Microsoft Docs
description: Dans ce tutoriel, vous allez découvrir comment créer et configurer un domaine managé Azure Active Directory Domain Services avec le portail Azure.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 09/15/2021
ms.author: justinha
ms.openlocfilehash: 2080cf50a5837b2b4347f03a77496f5d6215e958
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128605389"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-managed-domain"></a>Tutoriel : Créer et configurer un domaine managé Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) fournit des services de domaine managés, comme la jonction de domaine, la stratégie de groupe, le protocole LDAP, et l’authentification Kerberos/NTLM entièrement compatible avec Windows Server Active Directory. Vous consommez ces services de domaine sans déployer, gérer et mettre à jour avec des correctifs les contrôleurs de domaine vous-même. Azure AD DS s’intègre à votre locataire Azure AD existant. Cette intégration permet aux utilisateurs de se connecter en utilisant leurs informations d’identification d’entreprise, et vous pouvez utiliser des groupes et des comptes d’utilisateur existants pour sécuriser l’accès aux ressources.

Vous pouvez créer un domaine managé avec des options de configuration par défaut pour le réseau et la synchronisation, ou [définir ces paramètres manuellement][tutorial-create-instance-advanced]. Ce tutoriel montre comment utiliser les options par défaut pour créer et configurer un domaine managé Azure AD DS avec le portail Azure.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Comprendre les exigences DNS pour un domaine managé
> * Créer un domaine managé
> * Activer la synchronisation de hachage de mot de passe

Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce tutoriel, vous avez besoin des ressources et des privilèges suivants :

* Un abonnement Azure actif.
    * Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un locataire Azure Active Directory associé à votre abonnement, synchronisé avec un annuaire local ou un annuaire cloud uniquement.
    * Si nécessaire, [créez un locataire Azure Active Directory][create-azure-ad-tenant] ou [associez un abonnement Azure à votre compte][associate-azure-ad-tenant].
* Vous devez disposer des privilèges d’*Administrateur global* dans votre locataire Azure AD pour activer Azure AD DS.
* Vous avez besoin de privilèges de *Contributeur* dans votre abonnement Azure pour créer les ressources Azure AD DS nécessaires.
* Vous avez besoin d’un réseau virtuel avec des serveurs DNS qui peuvent interroger l’infrastructure nécessaire, comme le stockage. Des serveurs DNS qui ne peuvent pas effectuer de requêtes Internet générales peuvent empêcher de créer un domaine managé. 

Bien que ce ne soit pas obligatoire pour Azure AD DS, nous vous recommandons de [configurer la réinitialisation de mot de passe en libre-service (SSPR)][configure-sspr] pour le locataire Azure AD. Les utilisateurs peuvent changer leur mot de passe sans SSPR, mais SSPR aide s’ils oublient leur mot de passe et doivent les réinitialiser.

> [!IMPORTANT]
> Vous ne pouvez pas déplacer le domaine managé vers un autre abonnement, un autre groupe de ressources, une autre région, un autre réseau virtuel ou sous-réseau après l’avoir créé. Veillez à sélectionner l’abonnement, le groupe de ressources, la région, le réseau virtuel et le sous-réseau les plus appropriés quand vous déployez le domaine managé.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Dans ce tutoriel, vous créez et configurez le domaine managé avec le portail Azure. Pour commencer, connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-a-managed-domain"></a>Créer un domaine managé

Pour lancer l’Assistant **Activer Azure AD Domain Services**, procédez comme suit :

1. Dans le menu du Portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**.
1. Entrez *Domain Services* dans la barre de recherche, puis choisissez *Azure AD Domain Services* dans les suggestions de recherche.
1. Dans la page Azure AD Domain Services, cliquez sur le bouton **Créer**. L’Assistant **Activer Azure AD Domain Services** est lancé.
1. Sélectionnez l’**Abonnement** Azure dans lequel vous souhaitez créer le domaine managé.
1. Sélectionnez le **Groupe de ressources** auquel le domaine managé doit appartenir. Choisissez de **Créer** ou de sélectionner un groupe de ressources existant.

Quand vous créez un domaine managé, vous spécifiez un nom DNS. Voici quelques considérations liées au choix de ce nom DNS :

* **Nom de domaine intégré :** Par défaut, le nom de domaine intégré de l’annuaire est utilisé (un suffixe *.onmicrosoft.com*). Si vous voulez activer l’accès LDAP sécurisé au domaine managé via Internet, vous ne pouvez pas créer un certificat numérique pour sécuriser la connexion avec ce domaine par défaut. Microsoft détient le domaine *.onmicrosoft.com* : une autorité de certification n’émettra donc pas de certificat.
* **Noms de domaine personnalisés :** L’approche la plus courante consiste à spécifier un nom de domaine personnalisé, en général celui que vous possédez déjà et qui est routable. Quand vous utilisez un domaine personnalisé routable, le trafic peut s’écouler correctement en fonction des besoins pour prendre en charge vos applications.
* **Suffixes de domaine non routables :** D’une façon générale, nous vous recommandons d’éviter un suffixe de nom de domaine non routable, comme *contoso.local*. Le suffixe *.local* n’est pas routable et peut entraîner des problèmes de résolution DNS.

> [!TIP]
> Si vous créez un nom de domaine personnalisé, faites attention aux espaces de noms DNS existants. Il est recommandé d’utiliser un nom de domaine distinct de tout espace de noms DNS local ou Azure existant.
>
> Par exemple, si vous disposez de l’espace de noms DNS existant *contoso.com*, créez un domaine managé avec le nom de domaine personnalisé *aaddscontoso.com*. Si vous devez utiliser le protocole LDAP sécurisé, vous devez inscrire et avoir ce nom de domaine personnalisé pour générer les certificats requis.
>
> Vous devrez peut-être créer des enregistrements DNS supplémentaires pour d’autres services dans votre environnement, ou des redirecteurs DNS conditionnels entre les espaces de noms DNS existants dans votre environnement. Par exemple, si vous exécutez un serveur web qui héberge un site à l’aide du nom DNS racine, il peut y avoir des conflits de nommage qui nécessitent des entrées DNS supplémentaires.
>
> Dans ces tutoriels et articles de guide pratique, le domaine personnalisé *aaddscontoso.com* est utilisé comme exemple simple. Dans toutes les commandes, spécifiez votre propre nom de domaine.

Les restrictions de nom DNS suivantes s’appliquent également :

* **Restrictions de préfixe de domaine :** Vous ne pouvez pas créer de domaine managé avec un préfixe de plus de 15 caractères. Le préfixe du nom de domaine spécifié (par exemple, *aaddscontoso* dans le nom de domaine *aaddscontoso.com*) doit contenir au maximum 15 caractères.
* **Conflits de noms de réseau :** Le nom de domaine DNS de votre domaine managé ne doit pas déjà exister dans le réseau virtuel. En particulier, recherchez les scénarios suivants, qui aboutiraient à un conflit de noms :
    * Si vous avec un domaine Active Directory avec le même nom de domaine DNS sur le réseau virtuel Azure.
    * Si le réseau virtuel dans lequel vous envisagez d’activer le domaine managé a une connexion VPN avec votre réseau local. Dans ce scénario, veillez à ne pas avoir de domaine portant le même nom de domaine DNS sur votre réseau local.
    * Si vous avez un service cloud Azure avec ce nom sur le réseau virtuel Azure.

Renseignez les champs de la fenêtre *De base* du portail Azure pour créer un domaine managé :

1. Entrez un **Nom de domaine DNS** pour votre domaine managé, en tenant compte des points précédents.
1. Choisissez l’**Emplacement** Azure dans lequel créer le domaine managé. Si vous choisissez une région qui prend en charge les Zones de disponibilité Azure, les ressources Azure AD DS sont réparties entre les zones pour assurer une redondance supplémentaire.

    > [!TIP]
    > Les Zones de disponibilité sont des emplacements physiques uniques au sein d’une région Azure. Chaque zone de disponibilité est composée d’un ou de plusieurs centres de données équipés d’une alimentation, d’un système de refroidissement et d’un réseau indépendants. Pour garantir la résilience, un minimum de trois zones distinctes sont activées dans toutes les régions.
    >
    > Vous ne devez rien configurer pour la répartition d’Azure AD DS entre les zones. La plateforme Azure gère automatiquement la répartition de zone des ressources. Pour plus d’informations et pour connaître la disponibilité régionale, consultez [Que sont les zones de disponibilité dans Azure ?][availability-zones]

1. La **référence SKU** détermine le niveau de performance et la fréquence de sauvegarde. Vous pouvez changer de référence SKU après la création du domaine managé en cas de changement des besoins métier. Pour plus d’informations, consultez [Concepts relatifs aux références SKU dans Azure AD DS][concepts-sku].

    Pour ce tutoriel, sélectionnez la référence SKU *Standard*.
1. Une *forêt* est une construction logique utilisée par Active Directory Domain Services pour regrouper un ou plusieurs domaines. Par défaut, un domaine managé est créé en tant que forêt d’*utilisateurs*. Ce type de forêt synchronise tous les objets d’Azure AD, notamment les comptes d’utilisateur créés dans un environnement AD DS local.

    Une forêt de *ressources* synchronise uniquement les utilisateurs et les groupes créés directement dans Azure AD. Pour plus d’informations sur les forêts de *ressources*, notamment sur la raison pour laquelle vous pouvez en utiliser une et comment créer des approbations de forêts avec des domaines AD DS locaux, consultez [Vue d’ensemble des forêts de ressources Azure AD DS][resource-forests].

    Pour ce tutoriel, choisissez de créer une forêt d’*utilisateurs*.

    ![Configurer les paramètres de base pour un domaine managé Azure AD Domain Services](./media/tutorial-create-instance/basics-window.png)

Pour créer rapidement un domaine managé, vous pouvez sélectionner **Vérifier + créer** afin d’accepter d’autres options de configuration par défaut. Quand vous choisissez cette option de création, les paramètres par défaut suivants sont configurés :

* Crée un réseau virtuel nommé *aadds-vnet* qui utilise la plage d’adresses IP *10.0.2.0/24*.
* Crée un sous-réseau appelé *aadds-subnet* qui utilise la plage d’adresses IP *10.0.2.0/24*.
* Synchronise *tous* les utilisateurs entre Azure AD et le domaine managé.

Sélectionnez **Vérifier + créer** pour accepter ces options de configuration par défaut.

## <a name="deploy-the-managed-domain"></a>Déployer le domaine managé

Dans la page **Résumé** de l’Assistant, examinez les paramètres de configuration du domaine managé. Vous pouvez revenir à n’importe quelle étape de l’Assistant pour faire des modifications. Pour redéployer un domaine managé sur un autre client Azure AD en utilisant ces mêmes options de configuration, vous pouvez également **télécharger un modèle pour l’automatisation**.

1. Pour créer le domaine managé, sélectionnez **Créer**. Une remarque s’affiche pour signaler que certaines options de configuration, telles que le nom DNS ou le réseau virtuel, ne sont plus modifiables une fois que le domaine managé Azure AD DS a été créé. Pour continuer, sélectionnez **OK**.
1. Le processus d’approvisionnement de votre domaine managé peut prendre jusqu’à une heure. Vous voyez une notification dans le portail indiquant la progression de votre déploiement Azure AD DS. Sélectionnez la notification pour voir la progression détaillée du déploiement.

    ![Notification dans le portail Azure du déploiement en cours](./media/tutorial-create-instance/deployment-in-progress.png)

1. La page se charge avec les mises à jour du processus de déploiement, y compris la création de ressources dans votre annuaire.
1. Sélectionnez votre groupe de ressources, tel que *myResourceGroup*, puis choisissez votre domaine managé dans la liste des ressources Azure, par exemple *aaddscontoso.com*. L’onglet **Vue d’ensemble** montre que le domaine managé est actuellement en cours de *Déploiement*. Vous ne pouvez pas configurer le domaine managé tant qu’il n’est pas entièrement provisionné.

    ![État des services de domaine pendant l’état Provisionnement](./media/tutorial-create-instance/provisioning-in-progress.png)

1. Lorsque le domaine managé est entièrement approvisionné, l’onglet **Vue d’ensemble** affiche l’état du domaine comme *En cours d’exécution*.

    ![État des services de domaine une fois le provisionnement terminé](./media/tutorial-create-instance/successfully-provisioned.png)

> [!IMPORTANT]
> Le domaine managé est associé à votre locataire Azure AD. Pendant le processus d’approvisionnement, Azure AD DS crée deux applications d’entreprise nommées *Services de contrôleur de domaine* et *AzureActiveDirectoryDomainControllerServices* dans le locataire Azure AD. Ces applications d’entreprise sont nécessaires pour entretenir votre domaine géré. Ne supprimez pas ces applications.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Mettre à jour les paramètres DNS pour le réseau virtuel Azure

Avec Azure AD DS correctement déployé, configurez maintenant le réseau virtuel pour permettre à d’autres machines virtuelles et applications connectées d’utiliser le domaine managé. Pour fournir cette connectivité, mettez à jour les paramètres du serveur DNS pour que votre réseau virtuel pointe vers les deux adresses IP où le domaine managé est déployé.

1. L’onglet **Vue d’ensemble** pour votre domaine managé montre quelques **Étapes de configuration obligatoires**. La première étape de configuration est de mettre à jour les paramètres du serveur DNS pour votre réseau virtuel. Une fois les paramètres DNS correctement configurés, cette étape n’apparaît plus.

    Les adresses listées sont les contrôleurs de domaine à utiliser dans le réseau virtuel. Dans cet exemple, ces adresses sont *10.0.2.4* et *10.0.2.5*. Vous pouvez trouver ultérieurement ces adresses IP sous l’onglet **Propriétés**.

    ![Configurer les paramètres DNS pour votre réseau virtuel avec les adresses IP d’Azure AD Domain Services](./media/tutorial-create-instance/configure-dns.png)

1. Pour mettre à jour les paramètres du serveur DNS pour le réseau virtuel, sélectionnez le bouton **Configurer**. Les paramètres DNS sont configurés automatiquement pour votre réseau virtuel.

> [!TIP]
> Si vous avez sélectionné un réseau virtuel existant au cours des étapes précédentes, les machines virtuelles connectées au réseau obtiennent les nouveaux paramètres DNS seulement après un redémarrage. Vous pouvez redémarrer les machines virtuelles en utilisant le portail Azure, Azure PowerShell ou Azure CLI.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Activer les comptes d’utilisateur pour Azure AD DS

Pour authentifier les utilisateurs sur le domaine managé, Azure AD DS nécessite les hachages de mot de passe dans un format adapté à l’authentification NTLM (NT LAN Manager) et Kerberos. Azure AD ne génère pas et ne stocke pas les hachages de mot de passe au format nécessaire pour l’authentification NTLM ou Kerberos tant que vous n’activez pas Azure AD DS pour votre locataire. Pour des raisons de sécurité, Azure AD ne stocke pas non plus d’informations d’identification de mot de passe sous forme de texte en clair. Par conséquent, Azure AD ne peut pas générer automatiquement ces hachages de mot de passe NTLM ou Kerberos en fonction des informations d’identification existantes des utilisateurs.

> [!NOTE]
> Une fois configurés de façon appropriée, les hachages de mot de passe utilisables sont stockés dans le domaine managé. Si vous supprimez le domaine managé, tout hachage de mot de passe stocké à ce stade est également supprimé.
>
> Les informations d’identification synchronisées dans Azure AD ne peuvent pas être réutilisées si vous créez par la suite un domaine managé : vous devez reconfigurer la synchronisation de hachage de mot de passe pour stocker à nouveau les hachages de mot de passe. Les machines virtuelles ou les utilisateurs auparavant joints à un domaine ne pourront pas s’authentifier immédiatement : Azure AD doit générer et stocker les hachages de mot de passe dans le nouveau domaine managé.
>
> [Azure AD Connect Cloud Sync n’est pas pris en charge avec Azure AD DS][/azure/active-directory/cloud-sync/what-is-cloud-sync#comparison-between-azure-ad-connect-and-cloud-sync]. Les utilisateurs locaux doivent être synchronisés à l’aide d’Azure AD Connect pour pouvoir accéder aux machines virtuelles jointes à un domaine. Pour plus d’informations, consultez [Processus de synchronisation du hachage de mot de passe pour Azure AD DS et Azure AD Connect][password-hash-sync-process].

Les étapes de génération et de stockage de ces hachages de mot de passe sont différentes pour les comptes d’utilisateur cloud uniquement créés dans Azure AD et pour les comptes d’utilisateur qui sont synchronisés à partir de votre annuaire local avec Azure AD Connect.

Un compte d’utilisateur uniquement dans le cloud est un compte qui a été créé dans votre répertoire Azure AD à l’aide du portail Azure ou d’applets de commande PowerShell Azure AD. Ces comptes d’utilisateurs ne sont pas synchronisés à partir d’un annuaire local.

> Dans ce tutoriel, nous utilisons un compte d’utilisateur de base cloud uniquement. Pour plus d’informations sur les étapes supplémentaires nécessaires pour utiliser Azure AD Connect, consultez [Synchroniser les hachages de mot de passe pour les comptes d’utilisateur synchronisés à partir de votre annuaire Active Directory local vers votre domaine managé][on-prem-sync].

> [!TIP]
> Si votre locataire Azure AD utilise une combinaison d’utilisateurs cloud uniquement et d’utilisateurs provenant de votre annuaire Active Directory local, vous devez effectuer ces deux ensembles d’étapes.

Pour les comptes d’utilisateurs cloud uniquement, les utilisateurs doivent changer leur mot de passe avant de pouvoir utiliser Azure AD DS. Ce processus de changement du mot de passe entraîne la génération et le stockage dans Azure AD des hachages de mot de passe pour l’authentification Kerberos et NTLM. Le compte n’est pas synchronisé entre Azure AD et Azure AD DS tant que le mot de passe n’a pas été changé. Vous pouvez faire expirer les mots de passe de tous les utilisateurs cloud du locataire qui doivent utiliser Azure AD DS, ce qui force le changement de mot de passe à la connexion suivante, ou demander aux utilisateurs cloud de changer manuellement leur mot de passe. Pour ce tutoriel, nous changeons manuellement le mot de passe d’un utilisateur.

Pour qu’un utilisateur puisse réinitialiser son mot de passe, le locataire Azure AD doit être [configuré pour la réinitialisation du mot de passe en libre-service][configure-sspr].

Pour changer le mot de passe d’un utilisateur cloud uniquement, l’utilisateur doit effectuer les étapes suivantes :

1. Accédez à la page Panneau d’accès Azure AD à l’adresse [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. En haut à droite, sélectionnez votre nom, puis choisissez **Profil** dans le menu déroulant.

    ![Sélectionner un profil](./media/tutorial-create-instance/select-profile.png)

1. Dans la page **Profil**, sélectionnez **Changer le mot de passe**.
1. Dans la page **Changer le mot de passe**, entrez votre mot de passe existant (l’ancien), puis entrez un nouveau mot de passe et confirmez-le.
1. Sélectionnez **Envoyer**.

Une fois que vous avez changé votre mot de passe, quelques minutes sont nécessaires pour que le nouveau mot de passe soit utilisable dans Azure AD DS et que vous puissiez vous connecter aux ordinateurs joints au domaine managé.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Comprendre les exigences DNS pour un domaine managé
> * Créer un domaine managé
> * Ajouter des utilisateurs d’administration à la gestion du domaine
> * Activer les comptes d’utilisateur pour Azure AD DS et générer les hachages de mot de passe

Avant de joindre des machines virtuelles au domaine et de déployer des applications qui utilisent le domaine managé, configurez un réseau virtuel Azure pour les charges de travail des applications.

> [!div class="nextstepaction"]
> [Configurer un réseau virtuel Azure pour que les charges de travail des applications utilisent votre domaine managé](tutorial-configure-networking.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/tutorial-enable-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[skus]: overview.md
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain#selecting-a-prefix
