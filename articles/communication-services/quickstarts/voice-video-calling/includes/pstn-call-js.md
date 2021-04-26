---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: nikuklic
ms.openlocfilehash: df8d3bb5986fd31f4a9079b6e6e077126867dbfe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104729507"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Dağıtılan bir Iletişim Hizmetleri kaynağı. [Iletişim Hizmetleri kaynağı oluşturun](../../create-communication-resource.md).
- Iletişim Hizmetleri kaynağınız için alınan telefon numarası. [telefon numarası alma](../../telephony-sms/get-phone-number.md).
- Bir `User Access Token` çağrı istemcisini etkinleştirmek için. [Nasıl yapılır `User Access Token` ](../../access-tokens.md) hakkında daha fazla bilgi edinmek için


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
      id="callee-phone-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 230px;"
    />
    <div>
      <button id="call-phone-button" type="button">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-phone-button" type="button" disabled="true">
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

const calleePhoneInput = document.getElementById("callee-phone-input");
const callPhoneButton = document.getElementById("call-phone-button");
const hangUpPhoneButton = document.getElementById("hang-up-phone-button");

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential('<USER ACCESS TOKEN with PSTN scope>');
    callAgent = await callClient.createCallAgent(tokenCredential);
  //  callPhoneButton.disabled = false;
}

init();

```

## <a name="start-a-call-to-phone"></a>Telefon çağrısı Başlat

Iletişim Hizmetleri kaynağında edindiğiniz telefon numarasını belirtin; Bu, çağrıyı başlatmak için kullanılır:
> [!WARNING]
> Telefon numaralarının E. 164 uluslararası standart biçiminde sağlanması gerektiğini unutmayın. (ör.: + 12223334444)

Tıklandığında verdiğiniz telefon numarası için bir çağrı başlatmak üzere bir olay işleyicisi ekleyin `callPhoneButton` :


```javascript
callPhoneButton.addEventListener("click", () => {
  // start a call to phone
  const phoneToCall = calleePhoneInput.value;
  call = callAgent.startCall(
    [{phoneNumber: phoneToCall}], { alternateCallerId: {phoneNumber: 'YOUR AZURE REGISTERED PHONE NUMBER HERE: +12223334444'}
  });
  // toggle button states
  hangUpPhoneButton.disabled = false;
  callPhoneButton.disabled = true;
});
```

## <a name="end-a-call-to-phone"></a>Telefon çağrısını Sonlandır

Tıklandığında geçerli çağrıyı sonlandırmak için bir olay dinleyicisi ekleyin `hangUpPhoneButton` :

```javascript
hangUpPhoneButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({
    forEveryone: true
  });

  // toggle button states
  hangUpPhoneButton.disabled = true;
  callPhoneButton.disabled = false;
});
```

`forEveryone`Özelliği tüm çağrı katılımcıları için çağrıyı sonlandırır.

## <a name="run-the-code"></a>Kodu çalıştırma

`webpack-dev-server`Uygulamanızı derlemek ve çalıştırmak için kullanın. Uygulama konağını yerel bir Web sunucusuna eklemek için aşağıdaki komutu çalıştırın:


```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Tarayıcınızı açın ve adresine gidin `http://localhost:8080/` . Şunları görmeniz gerekir:

:::image type="content" source="../media/javascript/pstn-calling-javascript-app.png" alt-text="Tamamlanan JavaScript uygulamasının ekran görüntüsü.":::

Eklenen metin alanına bir telefon numarası girerek ve **telefon görüşmesini Başlat** düğmesine tıklayarak gerçek telefon numarasına bir çağrı yerleştirebilirsiniz.

> [!WARNING]
> Telefon numaralarının E. 164 uluslararası standart biçiminde sağlanması gerektiğini unutmayın. (ör.: + 12223334444)
