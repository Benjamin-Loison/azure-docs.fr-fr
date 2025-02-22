---
title: Accéder aux journaux des requêtes lentes - Portail Azure - Azure Database pour MySQL
description: Cet article décrit comment configurer et consulter les journaux des requêtes lentes dans Azure Database pour MySQL à partir du portail Azure.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 3/15/2021
ms.openlocfilehash: 0ac29b34600f03b7270bd717ad9f5147cc41c7cb
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122641150"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Configurer et consulter les journaux des requêtes lentes à partir du portail Azure

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Vous pouvez configurer, lister et télécharger les [journaux des requêtes lentes Azure Database pour MySQL](concepts-server-logs.md) à partir du portail Azure.

## <a name="prerequisites"></a>Prérequis
Les étapes décrites dans cet article supposent que vous disposez d’un [serveur Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Configuration de la journalisation
Configurer l’accès au journal des requêtes lentes de MySQL. 

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Sélectionnez votre serveur Azure Database pour MySQL.

3. Dans la section **Supervision** de la barre latérale, sélectionnez **Journaux du serveur**. 
   :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="Capture d’écran des options Journaux du serveur":::

4. Pour afficher les paramètres du serveur, sélectionnez **Cliquez ici pour activer les journaux et configurer les paramètres associés**.

5. Définissez **slow_query_log** sur **ON** (Activé).

6. Sélectionnez l’emplacement de sortie des journaux avec **log_output**. Pour envoyer des journaux au stockage local et aux journaux de diagnostic Azure Monitor, sélectionnez **Fichier**.

7. Pensez à définir « long_query_time » qui représente le seuil de temps de requête pour les requêtes qui seront collectées dans le fichier journal des requêtes lentes. Les valeurs minimales et par défaut de long_query_time sont 0 et 10, respectivement.

8. Ajustez d’autres paramètres, tels que log_slow_admin_statements pour enregistrer les instructions administratives. Par défaut, les instructions administratives ne sont pas enregistrées, tout comme les requêtes qui n’utilisent pas d’index pour les recherches. 

9. Sélectionnez **Enregistrer**. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Capture d’écran des paramètres du journal des requêtes lentes et de l’enregistrement.":::

À partir de la page **Paramètres du serveur**, vous pouvez revenir à la liste des journaux en fermant la page.

## <a name="view-list-and-download-logs"></a>Afficher la liste et télécharger les journaux d’activité
Une fois que la journalisation a commencé, vous pouvez afficher la liste des journaux des requêtes lentes disponibles et télécharger des fichiers journaux individuels.

1. Ouvrez le portail Azure.

2. Sélectionnez votre serveur Azure Database pour MySQL.

3. Dans la section **Supervision** de la barre latérale, sélectionnez **Journaux du serveur**. La page présente la liste de vos fichiers journaux.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/4-server-logs-list.png" alt-text="Capture d’écran de la page Journaux du serveur, avec la liste des journaux mise en évidence":::

   > [!TIP]
   > La convention d’affectation des noms de journaux est **mysql-slow-<nom de votre serveur>-aaaammjjhh.log**. La date et l’heure utilisées dans le nom de fichier correspondent au moment où le journal a été émis. Les fichiers journaux font l’objet d’une rotation toutes les 24 heures ou une fois qu’ils ont atteint la taille de 7,5 Go, selon ce qui se produit en premier. 

4. Si nécessaire, utilisez la zone de recherche pour trouver rapidement un journal spécifique en fonction d’une date et d’une heure. La recherche est effectuée sur le nom du journal.

5. Pour téléchargez un fichier journal en particulier, sélectionnez l’icône de flèche bas à côté du fichier journal dans la ligne du tableau.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/5-download.png" alt-text="Capture d’écran de la page Journaux du serveur, avec l’icône de flèche bas mise en évidence":::

## <a name="set-up-diagnostic-logs"></a>Configurer les journaux de diagnostic

1. En dessous de la section **Supervision** dans la barre latérale, sélectionnez **Paramètres de diagnostic** > **Ajouter des paramètres de diagnostic**.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png" alt-text="Capture d’écran des options Paramètres de diagnostic":::

2. Entrez un nom de paramètre de diagnostic.

3. Spécifiez les récepteurs de données auxquels envoyer les journaux des requêtes lentes (compte de stockage, hub d’événements ou espace de travail Log Analytics).

4. Sélectionnez **MySqlSlowLogs** comme type de journal.
:::image type="content" source="./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png" alt-text="Capture d’écran des options de configuration des paramètres de diagnostic":::

5. Après avoir configuré les récepteurs de données auxquels envoyer les journaux des requêtes lentes, sélectionnez **Enregistrer**.
:::image type="content" source="./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png" alt-text="Capture d’écran des options de configuration des paramètres de diagnostic, avec l’option Enregistrer mise en évidence":::

6. Accédez aux journaux des requêtes lentes en les explorant dans les récepteurs de données que vous avez configurés. L’affichage des journaux peut prendre jusqu’à 10 minutes.

## <a name="next-steps"></a>Étapes suivantes
- Pour savoir comment télécharger des journaux des requêtes lentes par programmation, consultez [Accéder aux journaux des requêtes lentes dans l’interface CLI](howto-configure-server-logs-in-cli.md).
- Découvrez les [journaux des requêtes lentes](concepts-server-logs.md) dans Azure Database pour MySQL.
- Pour plus d’informations sur les définitions de paramètres et la journalisation MySQL, consultez la documentation MySQL sur les [journaux](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).