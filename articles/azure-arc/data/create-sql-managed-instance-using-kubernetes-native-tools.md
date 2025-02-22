---
title: Créer une instance gérée SQL à l’aide des outils Kubernetes
description: Créer une instance gérée SQL à l’aide des outils Kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 502b3ffc28e1eb1880e2611b0bf33dbe4cf1aeb4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128675304"
---
# <a name="create-azure-sql-managed-instance-using-kubernetes-tools"></a>Créer une instance gérée Azure SQL à l’aide des outils Kubernetes


## <a name="prerequisites"></a>Prérequis

Vous devez avoir déjà créé un [contrôleur de données Azure Arc](./create-data-controller.md).

Pour créer une instance gérée SQL à l’aide des outils Kubernetes, vous devez avoir installé ceux-ci.  Les exemples de cet article utilisent `kubectl`, mais des approches similaires peuvent être utilisées avec d’autres outils Kubernetes tels que le tableau de bord Kubernetes, `oc`ou `helm` si vous êtes familiarisé avec ces outils et Kubernetes yaml/json.

[Installer l’outil kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>Vue d’ensemble

Pour créer une instance managée SQL, vous devez créer un secret Kubernetes pour stocker votre identifiant et votre mot de passe d’administrateur système en toute sécurité et une ressource personnalisée d’instance managée SQL basée sur la définition de la ressource personnalisée SqlManagedInstance.

## <a name="create-a-yaml-file"></a>Créer un fichier YAML

Vous pouvez utiliser le [modèle de fichier YAML](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/sqlmi.yaml) comme point de départ pour créer votre propre fichier YAML d’instance gérée SQL personnalisée.  Téléchargez ce fichier sur votre ordinateur local et ouvrez-le dans un éditeur de texte.  Il est utile d’utiliser un éditeur de texte tel que [VS Code](https://code.visualstudio.com/download) qui prend en charge la mise en surbrillance de la syntaxe et le linting pour les fichiers YAML.

Voici un exemple de fichier YAML :

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
  username: <your base64 encoded username>
kind: Secret
metadata:
  name: sql1-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1
kind: SqlManagedInstance
metadata:
  name: sql1
  annotations:
    exampleannotation1: exampleannotationvalue1
    exampleannotation2: exampleannotationvalue2
  labels:
    examplelabel1: examplelabelvalue1
    examplelabel2: examplelabelvalue2
spec:
  security:
    adminLoginSecret: sql1-login-secret
  scheduling:
    default:
      resources:
        limits:
          cpu: "2"
          memory: 4Gi
        requests:
          cpu: "1"
          memory: 2Gi
  services:
    primary:
      type: LoadBalancer
  storage:
    backups:
      volumes:
      - className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
        size: 5Gi
    data:
      volumes:
      - className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
        size: 5Gi
    datalogs:
      volumes:
      - className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
        size: 5Gi
    logs:
      volumes:
      - className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
        size: 5Gi
```

### <a name="customizing-the-login-and-password"></a>Personnalisation de l’identifiant et du mot de passe

Une clé secrète Kubernetes est stockée sous la forme d’une chaîne encodée en base64, une pour le nom d’utilisateur et l’autre pour le mot de passe.  Vous devez coder en base64 un identifiant et un mot de passe d’administrateur système et les placer à l’emplacement de l’espace réservé à `data.password` et `data.username`.  N’incluez pas les symboles `<` et `>` fournis dans le modèle.

> [!NOTE]
> Pour une sécurité optimale, l’utilisation de la valeur « sa » n’est pas autorisée pour l’identifiant.
> Respectez la [stratégie de complexité de mot de passe](/sql/relational-databases/security/password-policy#password-complexity).

Vous pouvez utiliser un outil en ligne pour coder en base64 le nom d’utilisateur et le mot de passe souhaités, ou vous pouvez utiliser des outils d’interface de ligne de commande intégrés en fonction de votre plateforme.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('example'))
```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

### <a name="customizing-the-name"></a>Personnalisation du nom

Le modèle a la valeur « sql1 » pour l’attribut de nom.  Vous pouvez modifier cela, mais il doit s’agir de caractères qui respectent les normes DNS en matière d’attribution de noms.  Vous devez également modifier le nom du secret pour qu’il corresponde.  Par exemple, si vous modifiez le nom de l’instance gérée SQL en « sql2 », vous devez modifier le nom du secret de « sql1-login-secret » en « sql2-login-secret ».

### <a name="customizing-the-resource-requirements"></a>Personnalisation des besoins en ressources

Vous pouvez modifier les besoins en ressources (la RAM, les limites principales et les requêtes) le cas échéant.  

> [!NOTE]
> Vous pouvez en apprendre plus sur la [gouvernance des ressources Kubernetes](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

Conditions requises pour les requêtes et les limites de ressources :
- La valeur limite des cœurs est **obligatoire** à des fins de facturation.
- Les autres requêtes et limites de ressources sont facultatives.
- La limite et la requête de cœurs doivent être une valeur entière positive, le cas échéant.
- Un cœur au minimum est obligatoire pour la requête de cœurs, le cas échéant.
- Le format de la valeur de mémoire respecte la notation Kubernetes.  
- Au minimum, 2 Go sont requis pour la requête de mémoire, le cas échéant.
- En règle générale, vous devez disposer de 4 Go de RAM pour chaque cœur pour les cas d’usage de production.

### <a name="customizing-service-type"></a>Personnalisation du type de service

Le type de service peut être modifié en NodePort si vous le souhaitez.  Un numéro de port aléatoire sera attribué.

### <a name="customizing-storage"></a>Personnalisation du stockage

Vous pouvez personnaliser les classes de stockage en fonction de votre environnement.  Si vous n’êtes pas sûr des classes de stockage disponibles, vous pouvez exécuter la commande `kubectl get storageclass` pour les afficher.  Le modèle a une valeur par défaut « default ».  Cela signifie qu’il existe une classe de stockage _nommée_ « default », et non pas qu’une classe de stockage _est_ utilisée par défaut.  Vous pouvez également modifier la taille de votre stockage.  Vous pouvez en apprendre plus sur [la configuration du stockage](./storage-configuration.md).

## <a name="creating-the-sql-managed-instance"></a>Création de l’instance gérée SQL

Maintenant que vous avez personnalisé le fichier YAML de l’instance gérée SQL, vous pouvez créer cette dernière en exécutant la commande suivante :

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\sqlmi.yaml
```

## <a name="monitoring-the-creation-status"></a>Surveillance de l’état de la création

La création de l’instance gérée SQL prendra quelques minutes. Vous pouvez surveiller la progression dans une autre fenêtre du terminal à l’aide des commandes suivantes :

> [!NOTE]
>  Les exemples de commandes ci-dessous supposent que vous avez créé une instance gérée SQL nommée « sql1 » et un espace de noms Kubernetes avec le nom « arc ».  Si vous avez utilisé un autre nom pour l’espace de noms ou l’instance gérée SQL, vous pouvez remplacer « arc » et « sqlmi » par ces noms.

```console
kubectl get sqlmi/sql1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

Vous pouvez également vérifier l’état de la création de n’importe quel pod en exécutant une commande comme celle qui figure ci-dessous.  C’est particulièrement utile pour résoudre les problèmes.

```console
kubectl describe pod/<pod name> --namespace arc

#Example:
#kubectl describe pod/sql1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Résolution des problèmes de création

Si vous rencontrez des problèmes avec la création, consultez le [Guide de résolution des problèmes](troubleshoot-guide.md).

## <a name="next-steps"></a>Étapes suivantes

[Se connecter à l’Instance managée SQL avec Azure Arc](connect-managed-instance.md)
