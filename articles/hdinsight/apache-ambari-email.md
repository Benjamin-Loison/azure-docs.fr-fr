---
title: 'Tutoriel : Configurer des notifications par e-mail Apache Ambari dans Azure HDInsight'
description: Cet article explique comment utiliser SendGrid avec Apache Ambari pour les notifications par e-mail.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/10/2020
ms.openlocfilehash: e9c7e9e44cd09460ede9046cadb1800871b5052d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726722"
---
# <a name="tutorial-configure-apache-ambari-email-notifications-in-azure-hdinsight"></a>Tutoriel : Configurer des notifications par e-mail Apache Ambari dans Azure HDInsight

Dans ce tutoriel, vous allez configurer des notifications par e-mail Apache Ambari à l’aide de SendGrid. [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) simplifie la gestion et la supervision d’un cluster HDInsight en fournissant une interface utilisateur web et une API REST faciles à utiliser. Ambari est inclus dans les clusters HDInsight, et sert à surveiller le cluster et à apporter des modifications de configuration. [SendGrid](https://sendgrid.com/solutions/) est un service de messagerie dans le cloud gratuit qui fournit des fonctionnalités fiables en matière de remise d’e-mail transactionnel, de scalabilité et d’analyse en temps réel ainsi que des API flexibles qui facilitent l’intégration personnalisée. Les clients Azure peuvent débloquer 25 000 courriers électroniques gratuits chaque mois.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Obtenir le nom d’utilisateur SendGrid
> * Configurer des notifications par e-mail Apache Ambari

## <a name="prerequisites"></a>Prérequis

* Un compte de messagerie SendGrid. Pour obtenir des instructions, consultez [Envoi de courriers électroniques à l’aide de SendGrid avec Azure](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#create-a-twilio-sendgrid-accountcreate-a-twilio-sendgrid-account).

* Un cluster HDInsight. Consultez [Créer des clusters Apache Hadoop à l’aide du Portail Azure](./hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="obtain-sendgrid-username"></a>Obtenir le nom d’utilisateur SendGrid

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre ressource SendGrid.

1. Dans la page de présentation, sélectionnez **Gérer** pour accéder à la page web SendGrid de votre compte.

    :::image type="content" source="./media/apache-ambari-email/azure-portal-sendgrid-manage.png" alt-text="Présentation de SendGrid dans le portail Azure":::

1. Dans le menu de gauche, accédez au nom de votre compte, puis à **Détails du compte**.

    :::image type="content" source="./media/apache-ambari-email/sendgrid-dashboard-navigation.png" alt-text="Navigation dans le tableau de bord SendGrid":::

1. Dans la page **Détails du compte**, notez le **Nom d’utilisateur**.

    :::image type="content" source="./media/apache-ambari-email/sendgrid-account-details.png" alt-text="Détails du compte SendGrid":::

## <a name="configure-ambari-e-mail-notification"></a>Configurer la notification par e-mail Ambari

1. Dans un navigateur web, accédez à `https://CLUSTERNAME.azurehdinsight.net/#/main/alerts`, où `CLUSTERNAME` est le nom de votre cluster.

1. Dans la liste déroulante **Actions**, sélectionnez **Manage Notifications** (Gérer les notifications).

1. Dans la fenêtre **Manage Alert Notifications** (Gérer les notifications d’alerte), sélectionnez l’icône **+** .

    :::image type="content" source="./media/apache-ambari-email/azure-portal-create-notification.png" alt-text="La capture d’écran présente la boîte de dialogue Manage Alert Notifications (Gérer les notifications d’alerte).":::

1. Dans la boîte de dialogue **Create Alert Notification** (Créer une notification d’alerte), fournissez les informations suivantes :

    |Propriété |Description |
    |---|---|
    |Nom|Fournissez un nom pour la notification.|
    |Groupes|Configurez selon vos besoins.|
    |severity|Configurez selon vos besoins.|
    |Description|facultatif.|
    |Méthode|Laissez **EMAIL**.|
    |Email To (Adresse e-mail de destination)|Indiquez la ou les adresses e-mails auxquelles envoyer les notifications, séparées par une virgule.|
    |SMTP Server (Serveur SMTP)|`smtp.sendgrid.net`|
    |SMTP Port (Port SMTP)|25 ou 587 (pour les connexions non chiffrées/TLS).|
    |Email From (Adresse e-mail source)|Indiquez une adresse e-mail. Il n’est pas nécessaire que l’adresse soit authentique.|
    |Use authentication (Utiliser l’authentification)|Cochez cette case.|
    |Nom d’utilisateur|Indiquez le nom d’utilisateur SendGrid.|
    |Mot de passe|Indiquez le mot de passe que vous avez utilisé lors de la création de la ressource SendGrid dans Azure.|
    |Password Confirmation (Confirmation de mot de passe)|Retapez le mot de passe.|
    |Start TLS (Démarrer TLS)|Cochez cette case.|

    :::image type="content" source="./media/apache-ambari-email/ambari-create-alert-notification.png" alt-text="La capture d’écran présente la boîte de dialogue Create Alert Notification (Créer une notification d’alerte).":::

    Sélectionnez **Enregistrer**. Vous revenez à la fenêtre **Manage Alert Notifications** (Gérer les notifications d’alerte).

1. Dans la fenêtre **Manage Alert Notifications**, sélectionnez l’icône **Close** (Fermer).

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à configurer des notifications par e-mail Apache Ambari à l’aide de SendGrid. Pour en savoir plus sur Apache Ambari, consultez les pages suivantes :

* [Gérer des clusters HDInsight à l’aide de l’interface utilisateur web d’Apache Ambari](./hdinsight-hadoop-manage-ambari.md)

* [Créer une notification d’alerte](https://docs.cloudera.com/HDPDocuments/Ambari-latest/managing-and-monitoring-ambari/content/amb_create_an_alert_notification.html)