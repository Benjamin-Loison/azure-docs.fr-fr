---
title: Fichier include
description: inclure fichier
services: azure-communication-services
author: probableprime
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: rifox
ms.openlocfilehash: e8afed5b318a3a6601d90fcd235476174e40e358
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967892"
---
## <a name="sample-code"></a>Exemple de code
Vous trouverez le code finalisé pour ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-chat).

## <a name="prerequisites"></a>Prérequis
Avant de commencer, assurez-vous de :

- Créer un compte Azure avec un abonnement actif. Pour plus d’informations, consultez [Créer un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installez [Node.js](https://nodejs.org/en/download/), versions Active LTS et Maintenance LTS.
- Créer une ressource Azure Communication Services. Pour plus d’informations, consultez [Créer des ressources Azure Communication Services](../../create-communication-resource.md). Vous devrez **enregistrer le point de terminaison de votre ressource** pour ce guide de démarrage rapide.
- Créez *trois* utilisateurs ACS et émettez pour eux un jeton d’accès utilisateur [Jeton d’accès utilisateur](../../access-tokens.md). Veillez à définir l’étendue sur **chat** (conversation) et **prenez note de la chaîne du jeton et de la chaîne userId**. La démonstration complète crée un fil avec deux participants initiaux, puis ajoute un troisième participant au fil.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-web-application"></a>Créer une application web

Pour commencer, ouvrez votre terminal ou votre fenêtre Commande pour créer un répertoire pour votre application, puis accédez-y.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Exécutez `npm init -y` pour créer un fichier **package.json** avec les paramètres par défaut.

```console
npm init -y
```

### <a name="install-the-packages"></a>Installer les packages

Utilisez la commande `npm install` pour installer les kits de développement logiciel (SDK) Communication Services pour JavaScript ci-dessous.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

L’option `--save` liste la bibliothèque comme dépendance dans votre fichier **package.json**.

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

Ce guide de démarrage rapide utilise webpack pour regrouper les ressources de l’application. Exécutez la commande suivante pour installer les packages npm webpack, webpack-cli et webpack-dev-server, puis listez-les comme dépendances de développement dans votre fichier **package.json** :

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Créez un fichier `webpack.config.js` dans le répertoire racine. Copiez la configuration suivante dans ce fichier :

```
module.exports = {
  entry: "./client.js",
  output: {
    filename: "bundle.js"
  },
  devtool: "inline-source-map",
  mode: "development"
}
```

Ajoutez un script `start` à votre `package.json`. Nous l’utiliserons pour exécuter l’application. Dans la section `scripts` de `package.json`, ajoutez ce qui suit :

```
"scripts": {
  "start": "webpack serve --config ./webpack.config.js"
}
```

Créez un fichier **index.html** dans le répertoire racine de votre projet. Nous nous servirons de ce fichier de modèle pour ajouter la fonctionnalité de conversation (chat) avec le kit de développement logiciel (SDK) Azure Communication Chat pour JavaScript.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Chat Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Chat Quickstart</h1>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Créez un fichier dans le répertoire racine de votre projet sous le nom **client.js** qui contiendra la logique d’application pour ce démarrage rapide.

### <a name="create-a-chat-client"></a>Créer un client de conversation

Pour créer un client de conversation dans votre application web, vous allez utiliser le **point de terminaison** Communication Services, ainsi que le **jeton d’accès** qui a été généré au cours des étapes prérequises.

Les jetons d’accès utilisateur vous permettent de créer des applications clientes qui s’authentifient directement auprès d’Azure Communication Services. Ce guide de démarrage rapide n’aborde pas la création d’un niveau de service pour gérer les jetons de votre application de conversation. Consultez [Concepts relatifs aux conversations](../../../concepts/chat/concepts.md) pour en savoir plus sur l’architecture des conversations, et consultez [Jetons d’accès utilisateur](../../access-tokens.md) pour en savoir plus sur les jetons d’accès.

Dans **client.js**, utilisez le point de terminaison et le jeton d’accès dans le code ci-dessous pour ajouter une fonctionnalité de conversation en utilisant le kit de développement logiciel (SDK) Azure Communication Chat pour JavaScript.

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
// The user access token generated as part of the pre-requisites
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationTokenCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
- Remplacez **endpointUrl** par le point de terminaison de ressource Communication Services. Si vous ne l’avez pas encore fait, consultez [Créer une ressource Azure Communication Services](../../create-communication-resource.md).
- Remplacez **userAccessToken** par le jeton que vous avez émis.


### <a name="run-the-code"></a>Exécuter le code

Exécutez la commande suivante pour regrouper l’hôte d’application sur un serveur web local :
```console
npm run start
```
Ouvrez votre navigateur et accédez à http://localhost:8080/.
Dans la console d’outils de développement de votre navigateur, vous devez voir les informations suivantes :

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>Modèle objet
Les classes et interfaces suivantes gèrent quelques-unes des principales fonctionnalités du kit de développement logiciel (SDK) Azure Communication Services Chat pour JavaScript.

| Nom                                   | Description                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Cette classe est nécessaire à la fonctionnalité de conversation (Chat). Vous l’instanciez avec vos informations d’abonnement et l’utilisez pour créer, obtenir et supprimer des threads, ainsi que pour vous abonner à des événements de conversation. |
| ChatThreadClient | Cette classe est nécessaire à la fonctionnalité de fil de conversation (Chat Thread). Vous obtenez une instance via ChatClient et l’utilisez pour envoyer/recevoir/mettre à jour/supprimer des messages, ajouter/supprimer/obtenir des utilisateurs, envoyer des notifications de saisie et des accusés de lecture. |


## <a name="start-a-chat-thread"></a>Démarrer un fil de conversation

Utilisez la méthode `createThread` pour créer un fil de conversation.

`createThreadRequest` est utilisé pour décrire la demande de fil :

- Utilisez `topic` pour attribuer un sujet à cette conversation. Les sujets peuvent être mis à jour après la création du thread de conversation à l’aide de la fonction `UpdateThread`.
- Utilisez `participants` pour lister les participants à ajouter au fil de conversation.

Une fois résolue, la méthode `createChatThread` retourne une `CreateChatThreadResult`. Ce modèle contient une propriété `chatThread` où vous pouvez accéder à l’`id` du fil nouvellement créé. Vous pouvez ensuite utiliser l’`id` pour obtenir une instance de `ChatThreadClient`. Le `ChatThreadClient` peut ensuite être utilisé pour effectuer une opération dans le fil, comme envoyer des messages ou lister les participants.

```JavaScript
async function createChatThread() {
  const createChatThreadRequest = {
    topic: "Hello, World!"
  };
  const createChatThreadOptions = {
    participants: [
      {
        id: { communicationUserId: '<USER_ID>' },
        displayName: '<USER_DISPLAY_NAME>'
      }
    ]
  };
  const createChatThreadResult = await chatClient.createChatThread(
    createChatThreadRequest,
    createChatThreadOptions
  );
  const threadId = createChatThreadResult.chatThread.id;
  return threadId;
}

createChatThread().then(async threadId => {
  console.log(`Thread created:${threadId}`);
  // PLACEHOLDERS
  // <CREATE CHAT THREAD CLIENT>
  // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
  // <SEND MESSAGE TO A CHAT THREAD>
  // <LIST MESSAGES IN A CHAT THREAD>
  // <ADD NEW PARTICIPANT TO THREAD>
  // <LIST PARTICIPANTS IN A THREAD>
  // <REMOVE PARTICIPANT FROM THREAD>
  });
```

Quand vous actualisez l’onglet du navigateur, la console doit montrer les éléments suivants :
```console
Thread created: <thread_id>
```

## <a name="get-a-chat-thread-client"></a>Obtenir un client de fil de conversation

La méthode `getChatThreadClient` retourne un `chatThreadClient` pour un fil qui existe déjà. Elle peut être utilisée pour effectuer des opérations sur le fil créé : ajout de participants, envoi d’un message, etc. threadId est l’ID unique du fil de conversation existant.

```JavaScript
let chatThreadClient = chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${threadId}`);

```
Ajoutez ce code à la place du commentaire `<CREATE CHAT THREAD CLIENT>` dans **client.js**, actualisez l’onglet du navigateur et examinez la console, qui doit présenter les éléments suivants :
```console
Chat Thread client for threadId: <threadId>
```

## <a name="list-all-chat-threads"></a>Répertorier tous les fils de conversation

La méthode `listChatThreads` retourne un objet `PagedAsyncIterableIterator` de `ChatThreadItem`. Celui-ci peut être utilisé pour répertorier tous les fils de conversation.
Un itérateur de `[ChatThreadItem]` est la réponse retournée par l’énumération des threads

```JavaScript
const threads = chatClient.listChatThreads();
for await (const thread of threads) {
   // your code here
}
```

## <a name="send-a-message-to-a-chat-thread"></a>Envoyer un message à un fil de conversation

Utilisez la méthode `sendMessage` pour envoyer un message à un fil identifié par threadId.

`sendMessageRequest` est utilisé pour décrire la demande de message :

- Utilisez `content` pour fournir le contenu du message de conversation ;

`sendMessageOptions` sert à décrire les paramètres facultatifs de l’opération :

- Utilisez `senderDisplayName` pour spécifier le nom d’affichage de l’expéditeur ;
- Utilisez `type` pour spécifier le type de message, par exemple « text » ou « html » ;
- Utilisez `metadata` (facultatif) pour inclure toutes les données supplémentaires que vous souhaitez envoyer avec le message. Ce champ constitue un mécanisme permettant aux développeurs d’étendre les fonctionnalités des messages de conversation et d’ajouter des informations personnalisées au cas d’usage. Par exemple, lors du partage d’un lien de fichier dans le message, vous souhaiterez peut-être ajouter « hasAttachment:true » dans les métadonnées pour que l’application du destinataire puisse l’analyser et l’afficher en conséquence.

`SendChatMessageResult` est la réponse retournée à la suite de l’envoi du message; elle contient un ID, qui est l’ID unique du message.

```JavaScript
const sendMessageRequest =
{
  content: 'Please take a look at the attachment'
};
let sendMessageOptions =
{
  senderDisplayName : 'Jack',
  type: 'text',
  metadata: {
    'hasAttachment': 'true',
    'attachmentUrl': 'https://contoso.com/files/attachment.docx'
  }
};
const sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
const messageId = sendChatMessageResult.id;
console.log(`Message sent!, message id:${messageId}`);
```

Ajoutez ce code à la place du commentaire `<SEND MESSAGE TO A CHAT THREAD>` dans **client.js**, actualisez l’onglet du navigateur et examinez la console.
```console
Message sent!, message id:<number>
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Recevoir les messages de conversation d’un fil de conversation

Avec la signalisation en temps réel, vous pouvez vous abonner pour écouter les nouveaux messages entrants et mettre à jour en conséquence les messages actuellement en mémoire. Azure Communication Services prend en charge une [liste d’événements auxquels vous pouvez vous abonner](../../../concepts/chat/concepts.md#real-time-notifications).

```JavaScript
// open notifications channel
await chatClient.startRealtimeNotifications();
// subscribe to new notification
chatClient.on("chatMessageReceived", (e) => {
  console.log("Notification chatMessageReceived!");
  // your code here
});

```
Ajoutez ce code à la place du commentaire `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` dans **client.js**.
Si vous actualisez l’onglet du navigateur, la console doit présenter un message `Notification chatMessageReceived` ;

Vous pouvez aussi récupérer les messages de conversation en interrogeant la méthode `listMessages` selon des intervalles définis.

```JavaScript

const messages = chatThreadClient.listMessages();
for await (const message of messages) {
   // your code here
}

```
Ajoutez ce code à la place du commentaire `<LIST MESSAGES IN A CHAT THREAD>` dans **client.js**.
Si vous actualisez l’onglet, la liste des messages envoyés dans ce fil de conversation doit apparaître dans la console.

`listMessages` retourne différents types de messages qui peuvent être identifiés par `chatMessage.type`. 

Pour plus d’informations, consultez [Types de messages](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Ajouter un utilisateur comme participant au fil de conversation

Une fois qu’un fil de conversation est créé, vous pouvez y ajouter des utilisateurs et en supprimer. En ajoutant des utilisateurs, vous leur permettez d’envoyer des messages au fil de conversation et d’ajouter/supprimer d’autres participants.

Avant d’appeler la méthode `addParticipants`, vérifiez que vous avez acquis un nouveau jeton d’accès et une identité pour cet utilisateur. L’utilisateur aura besoin de ce jeton d’accès pour initialiser son client de conversation.

`addParticipantsRequest` décrit l’objet de demande où `participants` liste les participants à ajouter au fil de conversation ;
- `id`, obligatoire, est l’identificateur de communication à ajouter au fil de conversation.
- `displayName`, facultatif, est le nom d’affichage pour le participant au fil.
- `shareHistoryTime`, facultatif, est le moment à partir duquel l’historique de conversation est partagé avec le participant. Pour partager l’historique depuis le début du fil de conversation, attribuez à cette propriété une date égale ou antérieure à la date de création du fil. Pour ne pas partager l’historique antérieur au moment où le participant a été ajouté, définissez-la sur l’heure actuelle. Pour partager l’historique partiel, attribuez-lui la date de votre choix.

```JavaScript

const addParticipantsRequest =
{
  participants: [
    {
      id: { communicationUserId: '<NEW_PARTICIPANT_USER_ID>' },
      displayName: 'Jane'
    }
  ]
};

await chatThreadClient.addParticipants(addParticipantsRequest);

```
Remplacez **NEW_PARTICIPANT_USER_ID** par un [nouvel ID d’utilisateur](../../access-tokens.md). Ajoutez ce code à la place du commentaire `<ADD NEW PARTICIPANT TO THREAD>` dans **client.js**.

## <a name="list-users-in-a-chat-thread"></a>Lister les utilisateurs dans un fil de conversation
```JavaScript
const participants = chatThreadClient.listParticipants();
for await (const participant of participants) {
   // your code here
}
```
Ajoutez ce code à la place du commentaire `<LIST PARTICIPANTS IN A THREAD>` dans **client.js**, actualisez l’onglet du navigateur et examinez la console, qui doit afficher des informations sur les utilisateurs présents dans un fil.

## <a name="remove-user-from-a-chat-thread"></a>Supprimer un utilisateur d’un fil de conversation

De la même façon que vous ajoutez un participant, vous pouvez supprimer des participants d’un fil de conversation. Pour pouvoir effectuer une suppression, vous devez suivre les ID des participants que vous avez ajoutés.

Utilisez la méthode `removeParticipant` où `participant` est l’utilisateur de communication à supprimer du fil.

```JavaScript

await chatThreadClient.removeParticipant({ communicationUserId: <PARTICIPANT_ID> });
await listParticipants();
```
Remplacez **PARTICIPANT_ID** par un ID d’utilisateur utilisé à l’étape précédente (<NEW_PARTICIPANT_USER_ID>).
Ajoutez ce code à la place du commentaire `<REMOVE PARTICIPANT FROM THREAD>` dans **client.js**,
