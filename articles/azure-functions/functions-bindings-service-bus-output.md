---
title: Liaisons de sortie Azure Service Bus pour Azure Functions
description: Apprenez à envoyer des messages Azure Service Bus à partir d’Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 0df83e6e335599b4942d53962add9083a2ecce12
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130138272"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Liaison de sortie Azure Service Bus pour Azure Functions

Utilisez la liaison de sortie Azure Service Bus pour envoyer des messages de file d’attente ou de rubrique.

Pour plus d’informations sur les détails d’installation et de configuration, consultez la [vue d’ensemble](functions-bindings-service-bus.md).

## <a name="example"></a>Exemple

# <a name="c"></a>[C#](#tab/csharp)

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui envoie un message de la file d’attente Service Bus :

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

L’exemple suivant montre une liaison de sortie Service Bus dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction utilise un déclencheur de minuteur pour envoyer un message de file d’attente toutes les 15 secondes.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Voici le code de script C# qui crée un message unique :

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Voici le code de script C# qui crée plusieurs messages :

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="java"></a>[Java](#tab/java)

L’exemple suivant montre une fonction Java qui envoie un message à une file d’attente Service Bus `myqueue` lorsqu’elle est déclenchée par une requête HTTP.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 Dans la [bibliothèque du runtime des fonctions Java](/java/api/overview/azure/functions/runtime), utilisez l’annotation `@QueueOutput` sur les paramètres de fonction dont la valeur serait écrite dans une file d’attente Service Bus.  Le type de paramètre doit être `OutputBinding<T>`, où T désigne n’importe quel type Java natif d’un POJO.

Les fonctions Java peuvent également écrire dans une rubrique Service Bus. L’exemple suivant utilise l’annotation `@ServiceBusTopicOutput` afin de décrire la configuration pour la liaison de sortie. 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

L’exemple suivant montre une liaison de sortie Service Bus dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction utilise un déclencheur de minuteur pour envoyer un message de file d’attente toutes les 15 secondes.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Voici le code de script JavaScript qui crée un message unique :

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Voici le code de script JavaScript qui crée plusieurs messages :

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

L’exemple suivant montre une liaison de sortie Service Bus dans un fichier *function.json* et une [fonction PowerShell](functions-reference-powershell.md) qui utilise la liaison. 

Voici les données de liaison dans le fichier *function.json* :

```json
{
  "bindings": [
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "outputSbMsg",
      "queueName": "outqueue",
      "topicName": "outtopic"
    }
  ]
}
```

Voici le code PowerShell qui crée un message en tant que sortie de la fonction.

```powershell
param($QueueItem, $TriggerMetadata) 

Push-OutputBinding -Name outputSbMsg -Value @{ 
    name = $QueueItem.name 
    employeeId = $QueueItem.employeeId 
    address = $QueueItem.address 
} 
```

# <a name="python"></a>[Python](#tab/python)

L’exemple suivant montre comment écrire dans une file d’attente Service Bus en Python.

Une définition de liaison Service Bus figure dans *function.json*, où *type* a la valeur `serviceBus`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

Dans *_\_init_\_.py*, vous pouvez écrire un message dans la file d’attente en passant une valeur à la méthode `set`.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Attributs et annotations

# <a name="c"></a>[C#](#tab/csharp)

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Le constructeur de l’attribut prend le nom de la file d’attente ou la rubrique et l’abonnement. Vous pouvez également spécifier les droits d’accès de la connexion. La détermination du paramètre des droits d’accès est expliquée dans la section [Sortie - configuration](#configuration). Voici un exemple qui illustre l’attribut appliqué à la valeur de retour de la fonction :

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Vous pouvez définir la propriété `Connection` pour spécifier le nom d’un paramètre d’application contenant la chaîne de connexion Service Bus à utiliser, comme illustré dans l’exemple suivant :

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Pour obtenir un exemple complet, voir [Exemple de sortie](#example).

Vous pouvez utiliser l’attribut `ServiceBusAccount` pour spécifier le compte Service Bus à utiliser au niveau de la classe, de la méthode ou du paramètre.  Pour plus d’informations, consultez [Déclencheur - attributs](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Les attributs ne sont pas pris en charge par le script C#.

# <a name="java"></a>[Java](#tab/java)

Les annotations `ServiceBusQueueOutput` et `ServiceBusTopicOutput` sont disponibles pour écrire un message en tant que sortie de fonction. Le paramètre assorti de ces annotations doit être déclaré en tant que `OutputBinding<T>` où `T` est le type correspondant au type du message.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Les attributs ne sont pas pris en charge par JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Les attributs ne sont pas pris en charge par PowerShell.

# <a name="python"></a>[Python](#tab/python)

Les attributs ne sont pas pris en charge par Python.

---

## <a name="configuration"></a>Configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `ServiceBus`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Doit être défini sur « serviceBus ». Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure.|
|**direction** | n/a | Doit être défini sur « out ». Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure. |
|**name** | n/a | Nom de la variable qui représente le message de la file d’attente ou de la rubrique dans le code de la fonction. Défini sur « $return » pour faire référence à la valeur de retour de la fonction. |
|**queueName**|**QueueName**|Nom de la file d’attente.  Défini uniquement en cas d’envoi de messages de file d’attente, ne s’applique pas à une rubrique.
|**topicName**|**TopicName**|Nom de la rubrique. Défini uniquement en cas d’envoi de messages de rubrique, ne s’applique pas à une file d’attente.|
|**connection**|**Connection**|Nom d’un paramètre d’application ou d’une collection de paramètres d’application qui spécifie la façon de se connecter à Service Bus. Consultez [Connexions](#connections).|
|**accessRights** (v1 uniquement)|**y accéder**|Droits d’accès de la chaîne de connexion. Les valeurs disponibles sont `manage` et `listen`. La valeur par défaut est `manage`, ce qui indique que `connection` a l'autorisation **Gérer**. Si vous utilisez une chaîne de connexion qui n’a pas l'autorisation **Gérer**, définissez `accessRights` sur « écouter ». Sinon, le runtime Functions pourrait échouer à effectuer des opérations qui nécessitent des droits de gestion. Dans Azure Functions versions 2.x et ultérieures, cette propriété n’est pas disponible parce que la version la plus récente du Kit de développement logiciel (SDK) Service Bus ne prend pas en charge les opérations de gestion.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

[!INCLUDE [functions-service-bus-connections](../../includes/functions-service-bus-connections.md)]

## <a name="usage"></a>Usage

Dans Azure Functions version 1.x, le runtime crée la file d’attente si elle n’existe pas et si vous avez défini `accessRights` sur `manage`. Dans Functions version 2.x et ultérieur, la file d’attente ou la rubrique doit déjà exister. Si vous spécifiez une file d’attente ou une rubrique qui n’existe pas, la fonction échoue. 

# <a name="c"></a>[C#](#tab/csharp)

Utilisez les types de paramètres suivants pour la liaison de sortie :

* `out T paramName` - `T` peut être n’importe quel type sérialisable au format JSON. Si la valeur du paramètre est null lorsque la fonction se termine, Functions crée le message avec un objet null.
* `out string` - Si la valeur du paramètre est null lorsque la fonction se termine, Functions ne crée pas de message.
* `out byte[]` - Si la valeur du paramètre est null lorsque la fonction se termine, Functions ne crée pas de message.
* `out BrokeredMessage` - Si la valeur du paramètre est null lorsque la fonction se termine, Functions ne crée pas de message (pour Functions 1.x).
* `out Message` - Si la valeur du paramètre est null quand la fonction se termine, Functions ne crée pas de message (pour Functions 2.x et ultérieur)
* `ICollector<T>` ou `IAsyncCollector<T>` (pour les méthodes asynchrones) - Pour la création de plusieurs messages. Un message est créé quand vous appelez la méthode `Add` .

Quand vous utilisez des fonctions C# :

* Les fonctions asynchrones ont besoin d’une valeur renvoyée ou de `IAsyncCollector` au lieu d’un paramètre `out`.

* Pour accéder à l’ID de session, établissez une liaison à un type [`Message`](/dotnet/api/microsoft.azure.servicebus.message) et utilisez la propriété `sessionId`.

### <a name="additional-types"></a>Autres types 

Les applications utilisant la version 5.0.0 ou ultérieure de l’extension Service Bus utilisent le type `ServiceBusMessage` dans [Azure.Messaging.ServiceBus](/dotnet/api/azure.messaging.servicebus.servicebusmessage) au lieu de celui de l’espace de noms [Microsoft.Azure.ServiceBus](/dotnet/api/microsoft.azure.servicebus.message). Cette version ne prend plus en charge le type hérité `Message`, mais elle prend désormais en charge les types suivants :

- [ServiceBusMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessage)

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Utilisez les types de paramètres suivants pour la liaison de sortie :

* `out T paramName` - `T` peut être n’importe quel type sérialisable au format JSON. Si la valeur du paramètre est null lorsque la fonction se termine, Functions crée le message avec un objet null.
* `out string` - Si la valeur du paramètre est null lorsque la fonction se termine, Functions ne crée pas de message.
* `out byte[]` - Si la valeur du paramètre est null lorsque la fonction se termine, Functions ne crée pas de message.
* `out BrokeredMessage` - Si la valeur du paramètre est null lorsque la fonction se termine, Functions ne crée pas de message (pour Functions 1.x).
* `out Message` - Si la valeur du paramètre est null quand la fonction se termine, Functions ne crée pas de message (pour Functions 2.x et ultérieur)
* `ICollector<T>` ou `IAsyncCollector<T>` - Pour la création de plusieurs messages. Un message est créé quand vous appelez la méthode `Add` .

Quand vous utilisez des fonctions C# :

* Les fonctions asynchrones ont besoin d’une valeur renvoyée ou de `IAsyncCollector` au lieu d’un paramètre `out`.

* Pour accéder à l’ID de session, établissez une liaison à un type [`Message`](/dotnet/api/microsoft.azure.servicebus.message) et utilisez la propriété `sessionId`.

### <a name="additional-types"></a>Autres types 
Les applications utilisant la version 5.0.0 ou ultérieure de l’extension Service Bus utilisent le type `ServiceBusMessage` dans [Azure.Messaging.ServiceBus](/dotnet/api/azure.messaging.servicebus.servicebusmessage) au lieu de celui de l’espace de noms [Microsoft.Azure.ServiceBus](/dotnet/api/microsoft.azure.servicebus.message). Cette version ne prend plus en charge le type hérité `Message`, mais elle prend désormais en charge les types suivants :

- [ServiceBusMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessage)

# <a name="java"></a>[Java](#tab/java)

Utilisez le [Kit de développement logiciel (SDK) Azure Service Bus](../service-bus-messaging/index.yml) plutôt que la liaison de sortie intégrée.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Accédez à la file d’attente ou la rubrique à l’aide de `context.bindings.<name from function.json>`. Vous pouvez affecter une chaîne, un tableau d’octets ou un objet Javascript (désérialisé au format JSON) à `context.binding.<name>`.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

La sortie dans Service Bus est disponible par le biais de l’applet de commande `Push-OutputBinding`, où vous passer des arguments qui correspondent au nom désigné par le paramètre du nom de la liaison dans le fichier *function.json*.

# <a name="python"></a>[Python](#tab/python)

Utilisez le [Kit de développement logiciel (SDK) Azure Service Bus](../service-bus-messaging/index.yml) plutôt que la liaison de sortie intégrée.

---

## <a name="exceptions-and-return-codes"></a>Exceptions et codes de retour

| Liaison | Informations de référence |
|---|---|
| Service Bus | [Codes d’erreur de Service Bus](../service-bus-messaging/service-bus-messaging-exceptions.md) |
| Service Bus | [Limites de Service Bus](../service-bus-messaging/service-bus-quotas.md) |

## <a name="next-steps"></a>Étapes suivantes

- [Exécuter une fonction durant la création d’un message de file d’attente ou de rubrique Service Bus (déclencheur)](./functions-bindings-service-bus-trigger.md)
