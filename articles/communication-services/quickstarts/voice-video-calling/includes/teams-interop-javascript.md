---
title: Hızlı başlangıç-bir Web uygulamasından takımlar toplantısına ekleme
description: Bu öğreticide, JavaScript için SDK 'Yı çağıran Azure Iletişim Hizmetleri 'ni kullanarak bir takımlar toplantısına nasıl katılacağınızı öğreneceksiniz.
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 6747d1d3cfba1c9e2bee7a8a7a48d67d6bed9f8e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564698"
---
Bu hızlı başlangıçta, JavaScript için SDK 'Yı çağıran Azure Iletişim Hizmetleri 'ni kullanarak bir takımlar toplantısına nasıl katılacağınızı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

- [Web uygulamasını çağıran](../getting-started-with-calling.md), çalışan bir iletişim hizmetleri.
- Bir [takımlar dağıtımı](/deployoffice/teams-install).


## <a name="add-the-teams-ui-controls"></a>Takımlar Kullanıcı arabirimi denetimlerini ekleme

index.html içindeki kodu aşağıdaki kod parçacığıyla değiştirin.
Metin kutusu, takımlar toplantı bağlamını girmek için kullanılacaktır ve bu düğme belirtilen toplantıya katılması için kullanılacak:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - Calling Sample</title>
</head>
<body>
    <h4>Azure Communication Services</h4>
    <h1>Teams meeting join quickstart</h1>
    <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
        <p><span style="font-weight: bold" id="recording-state"></span></p>
    <div>
        <button id="join-meeting-button" type="button" disabled="false">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
    </div>
    <script src="./bundle.js"></script>
</body>

</html>
```

## <a name="enable-the-teams-ui-controls"></a>Takımlar Kullanıcı arabirimi denetimlerini etkinleştir

client.js dosyanın içeriğini aşağıdaki kod parçacığıyla değiştirin.

```javascript
import { CallClient } from "@azure/communication-calling";
import { Features } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const meetingLinkInput = document.getElementById('teams-link-input');
const hangUpButton = document.getElementById('hang-up-button');
const teamsMeetingJoinButton = document.getElementById('join-meeting-button');
const callStateElement = document.getElementById('call-state');
const recordingStateElement = document.getElementById('recording-state');

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'ACS user'});
    teamsMeetingJoinButton.disabled = false;
}
init();

hangUpButton.addEventListener("click", async () => {
    // end the current call
    await call.hangUp();
  
    // toggle button states
    hangUpButton.disabled = true;
    teamsMeetingJoinButton.disabled = false;
    callStateElement.innerText = '-';
  });

teamsMeetingJoinButton.addEventListener("click", () => {    
    // join with meeting link
    call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
    call.on('stateChanged', () => {
        callStateElement.innerText = call.state;
    })

    call.api(Features.Recording).on('isRecordingActiveChanged', () => {
        if (call.api(Features.Recording).isRecordingActive) {
            recordingStateElement.innerText = "This call is being recorded";
        }
        else {
            recordingStateElement.innerText = "";
        }
    });
    // toggle button states
    hangUpButton.disabled = false;
    teamsMeetingJoinButton.disabled = true;
});
```

## <a name="get-the-teams-meeting-link"></a>Takımlar toplantı bağlantısını alın

Takımlar toplantı bağlantısı, Graph API 'Leri kullanılarak alınabilir. Bu, [Graph belgelerinde](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)ayrıntılıdır.
SDK 'Yı çağıran Iletişim Hizmetleri, bir tam takımlar toplantısı bağlantısını kabul eder. Bu bağlantı, kaynağın bir parçası olarak döndürülür `onlineMeeting` , [ `joinWebUrl` özelliğin](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) altında erişilebilir, toplantı davetini davet eden ekipteki **Toplantı** URL 'sinden gerekli toplantı bilgilerini de alabilirsiniz.

## <a name="run-the-code"></a>Kodu çalıştırma

Web paketi kullanıcıları `webpack-dev-server` uygulamanızı derlemek ve çalıştırmak için kullanabilir. Uygulama ana bilgisayarınızı yerel bir Web sunucusuna paketetmek için aşağıdaki komutu çalıştırın:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Tarayıcınızı açın ve adresine gidin http://localhost:8080/ . Şunları görmeniz gerekir:

:::image type="content" source="../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="Tamamlanan JavaScript uygulamasının ekran görüntüsü.":::

Iletişim Hizmetleri uygulamanızın içinden takım toplantısına katmak için, metin kutusuna takımlar bağlamını ekleyin ve *takımlar toplantısına* Ekle ' ye basın.
