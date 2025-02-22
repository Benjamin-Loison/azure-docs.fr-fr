---
title: 'Tutoriel : Créer et gérer des budgets Azure'
description: Ce tutoriel vous permet de planifier et prendre en compte les coûts de services Azure que vous consommez.
author: bandersmsft
ms.author: banders
ms.date: 06/17/2021
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 1531b6bf591d2fb859dbd680c41d51a5835347a1
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112320727"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Tutoriel : Créer et gérer des budgets Azure

Les budgets dans Cost Management vous aident à planifier et à suivre la comptabilité de l’organisation. Ils vous permettent d’informer les autres utilisateurs de leurs dépenses pour gérer les coûts de manière proactive, ainsi que pour superviser la progression des dépenses. Vous pouvez configurer des alertes en fonction du coût réel ou prévu pour vous assurer que vos dépenses respectent la limite de dépense de votre organisation. En cas de dépassement des seuils budgétaires que vous avez créés, seules des notifications sont déclenchées. Aucune de vos ressources n’est affectée et votre consommation n’est pas arrêtée. Vous pouvez utiliser des budgets pour comparer et suivre les dépenses lors de l’analyse des coûts.

Les données de coûts et d’utilisation sont généralement disponibles dans un délai de 8 à 24 heures, et les budgets sont évalués par rapport à ces coûts toutes les 24 heures. Veillez à vous familiariser avec les détails de [Mises à jour et rétention des données de coût et d’utilisation](./understand-cost-mgt-data.md#cost-and-usage-data-updates-and-retention). Quand un seuil budgétaire est atteint, des notifications par e-mail sont normalement envoyées dans l’heure suivant l’évaluation.

Les budgets sont automatiquement réinitialisés à la fin d’une période (mensuelle, trimestrielle ou annuelle) pour le même montant lorsque vous sélectionnez une date d’expiration ultérieure. Étant donné qu’ils sont réinitialisés avec le même montant de budget, vous devez créer des budgets distincts quand les montants budgétisés diffèrent pour des périodes ultérieures. Un budget qui arrive à expiration est automatiquement supprimé.

Les exemples de ce tutoriel expliquent comment créer et modifier un budget pour un abonnement Azure Contrat Entreprise (EA).

Regardez la vidéo [Appliquer des budgets aux abonnements à l’aide du Portail Azure](https://www.youtube.com/watch?v=UrkHiUx19Po) pour voir comment vous pouvez créer des budgets dans Azure afin de surveiller les dépenses. Pour regarder d’autres vidéos, consultez la [chaîne YouTube relative à Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/UrkHiUx19Po]

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créez un budget dans le portail Azure
> * Créer et modifier des budgets avec PowerShell
> * Créer un budget avec un modèle Azure Resource Manager

## <a name="prerequisites"></a>Prérequis

Les budgets sont pris en charge pour les types suivants d’étendues et de types de comptes Azure :

- Étendues de contrôle d’accès en fonction du rôle Azure (Azure RBAC)
    - Groupes d’administration
    - Abonnement
- Étendues Contrat Entreprise
    - Compte de facturation
    - department
    - Compte d’inscription
- Contrats individuels
    - Compte de facturation
- Étendues Contrat client Microsoft
    - Compte de facturation
    - Profil de facturation
    - Section de facture
    - Customer
- Étendues AWS
    - Compte externe
    - Abonnement externe

Pour afficher les budgets, vous devez au minimum disposer d'un accès en lecture à votre compte Azure.

Si vous disposez d’un nouvel abonnement, vous ne pouvez pas créer un budget ou utiliser les autres fonctionnalités de Cost Management tout de suite. Vous risquez de devoir attendre jusqu’à 48 heures avant de pouvoir utiliser toutes les fonctionnalités de Cost Management.

Dans le cadre des abonnements Azure EA, vous devez disposer d'un accès en lecture pour afficher les budgets. Pour créer et gérer des budgets, vous devez disposer d’une autorisation de contributeur.

Les autorisations, ou étendues, Azure suivantes sont prises en charge par abonnement aux budgets par utilisateur et par groupe.

- Propriétaire : peut créer, modifier ou supprimer des budgets pour un abonnement.
- Contributeur et Contributeur Cost Management : peut créer, modifier ou supprimer ses propres budgets. Peut modifier le montant des budgets créés par d’autres utilisateurs.
- Lecteur et Lecteur Cost Management : peut afficher les budgets pour lesquels ils disposent des autorisations adéquates.

**Pour plus d’informations sur les étendues, notamment sur l’accès nécessaire pour configurer les exportations pour les étendues Contrat Entreprise et Contrat client Microsoft, consultez [Comprendre et utiliser les étendues](understand-work-scopes.md)** . Pour plus d’informations sur l’affectation d’une autorisation d’accès aux données Cost Management, consultez [Affecter une autorisation d’accès aux données Cost Management](./assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

- Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-budget-in-the-azure-portal"></a>Créez un budget dans le portail Azure

Vous pouvez créer un budget d’abonnement Azure pour un mois, un trimestre ou un an.

Pour créer ou voir un budget, ouvrez l’étendue dans le portail Azure et sélectionnez **Budgets** dans le menu. Par exemple, accédez à **Abonnements**, sélectionnez un abonnement dans la liste, puis sélectionnez **Budgets** dans le menu. Utilisez la pastille **Étendue** pour basculer vers une autre étendue, un groupe d’administration par exemple, dans Budgets. Pour plus d’informations sur les étendues, consultez [Comprendre et utiliser les étendues](understand-work-scopes.md).

Une fois des budgets créés, ils affichent une vue simple de vos dépenses actuelles par rapport à ces budgets.

Sélectionnez **Ajouter**.

:::image type="content" source="./media/tutorial-acm-create-budgets/budgets-cost-management.png" alt-text="Capture d’écran montrant une liste de budgets déjà créés." lightbox="./media/tutorial-acm-create-budgets/budgets-cost-management.png" :::

Dans la fenêtre **Créer un budget**, vérifiez que l’étendue définie est correcte. Choisissez les filtres que vous souhaitez ajouter. Les filtres vous permettent de créer des budgets afférents à des coûts spécifiques, tels que des groupes de ressources dans un abonnement ou un service tel que des machines virtuelles. Tout filtre que vous pouvez utiliser dans l’analyse du coût peut également être appliqué à un budget.

Après avoir identifié votre étendue et vos filtres, tapez un nom de budget. Choisissez ensuite une période de réinitialisation mensuelle, trimestrielle ou annuelle pour le budget. La période de réinitialisation détermine la fenêtre de temps analysée par le budget. Le coût évalué par le budget commence à zéro au début de chaque nouvelle période. Quand vous créez un budget trimestriel, il fonctionne de la même façon qu’un budget mensuel. La différence est que le montant du budget pour le trimestre est divisé de manière équitable entre les trois mois du trimestre. Un montant de budget annuel est divisé de manière équitable entre les 12 mois de l’année civile.

Si vous avez un abonnement de paiement à l’utilisation, MSDN ou Visual Studio, votre période de facturation peut ne pas être alignée sur le mois calendaire. Pour ces types d’abonnements et groupes de ressources, vous pouvez créer un budget aligné sur votre période de facturation ou sur les mois calendaires. Pour créer un budget aligné sur votre période de facturation, sélectionnez une période de réinitialisation : **Mois de facturation**, **Trimestre de facturation** ou **Année de facturation**. Pour créer un budget aligné sur le mois calendaire, sélectionnez une période de réinitialisation : **Mensuelle**, **Trimestrielle** ou **Annuelle**.

Ensuite, identifiez la date d’expiration à laquelle le budget ne sera plus valide et cessera d’évaluer vos coûts.

Selon les champs choisis dans le budget jusqu’à présent, un graphique s’affiche pour vous aider à sélectionner un seuil à utiliser pour votre budget. Le budget suggéré est basé sur le coût prévu le plus élevé que vous pourriez rencontrer durant les périodes à venir. Vous pouvez changer le montant du budget.

:::image type="content" source="./media/tutorial-acm-create-budgets/create-monthly-budget.png" alt-text="Capture d’écran montrant la création d’un budget avec des données de coût mensuel." lightbox="./media/tutorial-acm-create-budgets/create-monthly-budget.png" :::

Après avoir configuré le montant du budget, sélectionnez **Suivant** pour configurer des alertes budgétaires basées sur le coût réel et les coûts prévus.

## <a name="configure-actual-costs-budget-alerts"></a>Configurer des alertes budgétaires basées sur les coûts réels

Les budgets nécessitent au moins un seuil de coût (% du budget) et une adresse e-mail correspondante. Si vous le souhaitez, vous pouvez inclure jusqu’à cinq seuils et cinq adresses e-mail dans un seul budget. Quand un seuil budgétaire est atteint, des notifications par e-mail sont normalement envoyées dans l’heure suivant l’évaluation. Les alertes budgétaires basées sur les coûts réels sont générées en fonction des coûts réels que vous avez accumulés par rapport aux seuils budgétaires configurés.

## <a name="configure-forecasted-budget-alerts"></a>Configurer des alertes budgétaires basées sur les coûts prévus

Les alertes basées sur les coûts prévus fournissent une notification anticipée indiquant qu’au regard des tendances, vos dépenses sont susceptibles de dépasser votre budget. Les alertes utilisent des [prédictions relatives aux coûts prévus](quick-acm-cost-analysis.md#understand-forecast). Des alertes sont générées quand la projection des coûts prévus dépasse le seuil défini. Vous pouvez configurer ce seuil en pourcentage du budget. Quand les coûts prévus atteignent le seuil budgétaire, des notifications sont normalement envoyées dans l’heure suivant l’évaluation.

Pour basculer entre une configuration d’alerte basée sur les coûts réels et sur les coûts prévus, utilisez le champ `Type` quand vous configurez l’alerte comme indiqué dans l’image suivante.

Si vous voulez recevoir des e-mails, ajoutez azure-noreply@microsoft.com à votre liste d’expéditeurs approuvés afin que les e-mails ne soient pas placés dans votre dossier de courrier indésirable. Pour plus d'informations sur les notifications, consultez [Utiliser les alertes de coût](./cost-mgt-alerts-monitor-usage-spending.md).

Dans l’exemple suivant, une alerte par e-mail est générée quand 90 % du budget sont atteints. Si vous créez un budget avec l’API Budgets, vous pouvez également attribuer des rôles à des personnes pour qu’elles reçoivent des alertes. L’attribution de rôles à des personnes n’est pas prise en charge dans le Portail Azure. Pour plus d’informations sur l’API Budgets d’Azure, consultez [API Budgets](/rest/api/consumption/budgets). Si vous souhaitez envoyer une alerte par e-mail dans une autre langue, consultez [Paramètres régionaux pris en charge pour les e-mails d’alerte budgétaire](manage-automation.md#supported-locales-for-budget-alert-emails).

Les limites d’alerte prennent en charge une plage de 0,01 à 1 000 % du seuil budgétaire que vous avez spécifié.

:::image type="content" source="./media/tutorial-acm-create-budgets/budget-set-alert.png" alt-text="Capture d’écran montrant les conditions d’alerte." lightbox="./media/tutorial-acm-create-budgets/budget-set-alert.png" :::

Une fois que vous avez créé un budget, il est indiqué dans l’analyse des coûts. Visualiser votre budget par rapport à la tendance de vos dépenses est une des premières étapes quand vous commencez à [analyser vos coûts et vos dépenses](./quick-acm-cost-analysis.md).

:::image type="content" source="./media/tutorial-acm-create-budgets/cost-analysis.png" alt-text="Capture d’écran montrant un exemple de budget avec les dépenses affichées dans l’analyse des coûts." lightbox="./media/tutorial-acm-create-budgets/cost-analysis.png" :::

Dans l’exemple précédent, vous avez créé un budget pour un abonnement. Vous pouvez aussi créer un budget pour un groupe de ressources. Si vous voulez créer un budget pour un groupe de ressources, accédez à **Gestion des coûts + facturation** &gt; **Abonnements** &gt; sélectionnez un abonnement > **Groupes de ressource** > sélectionnez un groupe de ressources > **Budgets** > puis **Ajouter** un budget.

### <a name="create-a-budget-for-combined-azure-and-aws-costs"></a>Créer un budget pour les coûts Azure et AWS combinés

Vous pouvez regrouper vos coûts Azure et AWS en affectant un groupe d’administration à votre connecteur, ainsi que ses comptes consolidés et liés. Attribuez vos abonnements Azure au même groupe d’administration. Créez ensuite un budget pour les coûts combinés.

1. Dans Cost Management, sélectionnez **Budgets**.
1. Sélectionnez **Ajouter**.
1. Sélectionnez **Modifier l’étendue**, puis sélectionnez le groupe d’administration.
1. Terminez la création du budget.

## <a name="costs-in-budget-evaluations"></a>Coûts des évaluations de budget

Les évaluations des coûts budgétaires incluent désormais les données d’instance réservée et d’achat. Si les frais vous concernent, vous pouvez recevoir des alertes à mesure que des frais sont incorporés dans vos évaluations. Connectez-vous au [portail Azure](https://portal.azure.com) pour vérifier que les seuils budgétaires sont configurés correctement afin de tenir compte des nouveaux coûts. Vos frais facturés Azure ne changent pas. Les budgets sont désormais évalués pour un ensemble plus complet de vos coûts. Si les frais ne vous concernent pas, le comportement de votre budget reste inchangé.

Si vous souhaitez filtrer les nouveaux coûts afin que les budgets soient évalués par rapport aux seuls frais de consommation Azure de premier tiers, ajoutez les filtres suivants à votre budget :

- Type de serveur de publication : Azure
- Type de frais : Usage

Les évaluations des coûts budgétaires sont basées sur le coût réel. Elles n’incluent pas l’amortissement. Pour plus d’informations sur les options de filtrage mises à votre disposition dans les budgets, consultez [Comprendre les options de regroupement et de filtrage](group-filter.md).

## <a name="trigger-an-action-group"></a>Déclencher un groupe d’actions

Lorsque vous créez ou modifiez un budget pour l’étendue d’un abonnement ou d’un groupe de ressources, vous pouvez le configurer pour qu’il appelle un groupe d’actions. Le groupe d’actions peut effectuer différentes actions quand votre seuil budgétaire est atteint. 

Les groupes d’actions sont actuellement pris en charge uniquement pour les étendues d’abonnement et de groupe de ressources. Pour plus d’informations sur la création de groupes d’actions, consultez [Configurer les paramètres du groupe d’actions de base](../../azure-monitor/alerts/action-groups.md#configure-basic-action-group-settings). 

Pour plus d’informations sur l’utilisation d’une automatisation basée sur le budget avec des groupes d’actions, consultez [Gérer les coûts avec Azure Budgets](../manage/cost-management-budget-scenario.md).

Pour créer ou mettre à jour des groupes d’actions, sélectionnez **Gérer le groupe d’action** pendant la création ou la modification d’un budget.

:::image type="content" source="./media/tutorial-acm-create-budgets/trigger-action-group.png" alt-text="Capture d’écran illustrant la création d’un budget avec l’option Gérer le groupe d’action mise en évidence." lightbox="./media/tutorial-acm-create-budgets/trigger-action-group.png" :::

Sélectionnez ensuite **Ajouter un groupe d’actions** et créez le groupe d’actions.

L’intégration au budget avec les groupes d’actions ne fonctionne que pour les groupes d’actions pour lesquels le schéma d’alerte commun est désactivé. Pour plus d’informations sur la désactivation du schéma, consultez [Comment activer le schéma d’alerte commun ?](../../azure-monitor/alerts/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="create-and-edit-budgets-with-powershell"></a>Créer et modifier des budgets avec PowerShell

Si vous êtes client Contrat Entreprise, vous pouvez créer et modifier des budgets par programmation avec le module Azure PowerShell. 

>[!Note]
>Les clients disposant d’un contrat client Microsoft doivent utiliser l' [API REST budgets](/rest/api/consumption/budgets/create-or-update) pour créer des budgets par programme, car PowerShell et CLI ne sont pas encore pris en charge.

Pour télécharger la dernière version d’Azure PowerShell, exécutez la commande suivante :

```azurepowershell-interactive
install-module -name Az
```

Les exemples de commandes suivantes créent un budget.

```azurepowershell-interactive
#Sign into Azure Powershell with your account

Connect-AzAccount

#Select a subscription to to monitor with a budget

select-AzSubscription -Subscription "Your Subscription"

#Create an action group email receiver and corresponding action group

$email1 = New-AzActionGroupReceiver -EmailAddress test@test.com -Name EmailReceiver1
$ActionGroupId = (Set-AzActionGroup -ResourceGroupName YourResourceGroup -Name TestAG -ShortName TestAG -Receiver $email1).Id

#Create a monthly budget that sends an email and triggers an Action Group to send a second email. Make sure the StartDate for your monthly budget is set to the first day of the current month. Note that Action Groups can also be used to trigger automation such as Azure Functions or Webhooks.

Get-AzContext
New-AzConsumptionBudget -Amount 100 -Name TestPSBudget -Category Cost -StartDate 2020-02-01 -TimeGrain Monthly -EndDate 2022-12-31 -ContactEmail test@test.com -NotificationKey Key1 -NotificationThreshold 0.8 -NotificationEnabled -ContactGroup $ActionGroupId
```

## <a name="create-a-budget-with-an-azure-resource-manager-template"></a>Créer un budget avec un modèle Azure Resource Manager

Vous pouvez créer un budget en utilisant un modèle Azure Resource Manager. Pour utiliser le modèle, consultez [Créer un budget avec un modèle Azure Resource Manager](quick-create-budget-template.md).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous avez créé un budget et que vous n’en avez plus besoin, affichez ses détails et supprimez-le.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créez un budget dans le portail Azure
> * Créer et modifier des budgets avec PowerShell
> * Créer un budget avec un modèle Azure Resource Manager

Passez au tutoriel suivant pour créer une exportation récurrente de vos données de gestion des coûts.

> [!div class="nextstepaction"]
> [Créer et gérer des données exportées](tutorial-export-acm-data.md)
