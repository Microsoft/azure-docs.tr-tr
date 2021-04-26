---
title: Hızlı başlangıç-Azure Iletişim hizmetlerini kullanarak bir Web uygulamasına çağıran VOıP ekleme
description: Bu öğreticide, JavaScript için SDK 'Yı çağıran Azure Iletişim hizmetlerini kullanmayı öğreneceksiniz
author: ddematheu
ms.author: nimag
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: bd9a607b46c87ca590bcefef310f822d6f23483c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386798"
---
Bu hızlı başlangıçta, JavaScript için SDK 'Yı çağıran Azure Iletişim hizmetlerini kullanarak nasıl çağrı başlayacağınızı öğreneceksiniz.

> [!NOTE]
> Bu belge, çağıran SDK 'nın 1.0.0-Beta. 10 sürümünü kullanır.


## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Etkin LTS ve bakım LTS sürümleri (8.11.1 ve 10.14.1 önerilir).
- Etkin bir Iletişim Hizmetleri kaynağı. [Iletişim Hizmetleri kaynağı oluşturun](../../create-communication-resource.md).
- Arama istemcisini başlatmak için bir Kullanıcı erişim belirteci. [Kullanıcı erişim belirteçleri oluşturmayı ve yönetmeyi](../../access-tokens.md)öğrenin.


[!INCLUDE [Calling with JavaScript](./get-started-javascript-setup.md)]

Kod şu şekildedir:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="token-input"
      type="text"
      placeholder="User access token"
      style="margin-bottom:1em; width: 200px;"
    />
    </div>
    <button id="token-submit" type="button">
        Submit
    </button>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px; display: block;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Bu hızlı başlangıç için uygulama mantığını içeren **client.js** adlı projenizin kök dizininde bir dosya oluşturun. Çağıran istemciyi içeri aktarmak ve iş mantığımızı ekleyebilmemiz için DOM öğelerine başvurular almak için aşağıdaki kodu ekleyin. 

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
let userTokenCredential = "";
const userToken = document.getElementById("token-input");
const calleeInput = document.getElementById("callee-id-input");
const submitToken = document.getElementById("token-submit");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
```

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, Azure Communication Service 'ın SDK 'yi çağıran bazı önemli özelliklerinden bazılarını işler:

| Ad                             | Açıklama                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient, çağıran SDK 'ya ana giriş noktasıdır.                                                                       |
| CallAgent                        | CallAgent, çağrıları başlatmak ve yönetmek için kullanılır.                                                                                            |
| AzureCommunicationTokenCredential | AzureCommunicationTokenCredential sınıfı, CallAgent örneğini oluşturmak için kullanılan CommunicationTokenCredential arabirimini uygular. |


## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Metin alanına kaynağınız için geçerli bir Kullanıcı erişim belirteci giriniz ve ' Gönder ' seçeneğine tıklamanız gerekir. Kullanılabilir bir belirteciniz yoksa, [Kullanıcı erişim belirteci](../../access-tokens.md) belgelerine başvurun. Kullanarak `CallClient` , `CallAgent` bir örneği başlatın, bu, `CommunicationTokenCredential` çağrısı yapıp almamızı sağlar. Aşağıdaki kodu **client.js** ekleyin:

```javascript
submitToken.addEventListener("click", async () => {
  const callClient = new CallClient(); 
  const userTokenCredential = userToken.value;
    try {
      tokenCredential = new AzureCommunicationTokenCredential(userTokenCredential);
      callAgent = await callClient.createCallAgent(tokenCredential);
      callButton.disabled = false;
      submitToken.disabled = true;
    } catch(error) {
      window.alert("Please submit a valid token!");
    }
})
```

## <a name="start-a-call"></a>Bir çağrı başlatın

Tıklandığında bir çağrı başlatmak için bir olay işleyicisi ekleyin `callButton` :

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ id: userToCall }],
        {}
    );
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```

## <a name="end-a-call"></a>Çağrıyı Sonlandır

Tıklandığında geçerli çağrıyı sonlandırmak için bir olay dinleyicisi ekleyin `hangUpButton` :

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
  submitToken.disabled = false;
});
```

`forEveryone`Özelliği tüm çağrı katılımcıları için çağrıyı sonlandırır.

## <a name="run-the-code"></a>Kodu çalıştırma

`webpack-dev-server`Uygulamanızı derlemek ve çalıştırmak için kullanın. ' Deki uygulama konağını yerel bir Web sunucusuna paketetmek için aşağıdaki komutu çalıştırın:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Tarayıcınızı açın ve adresine gidin http://localhost:8080/ . Şunları görmeniz gerekir:

:::image type="content" source="../media/javascript/calling-javascript-app-2.png" alt-text="Tamamlanan JavaScript uygulamasının ekran görüntüsü.":::

İlgili metin alanlarında geçerli bir Kullanıcı erişim belirteci ve Kullanıcı KIMLIĞI sağlayıp **çağrıya başla** düğmesine tıklayarak gıden bir VoIP çağrısı yapabilirsiniz. Çağırmak `8:echo123` sizi bir Echo bot ile bağlar, bu, başlamak ve ses cihazlarınızın çalıştığını doğrulamak için harika bir yoldur.

## <a name="sample-code"></a>Örnek kod

Örnek uygulamayı [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-voice-calling)'dan indirebilirsiniz.
