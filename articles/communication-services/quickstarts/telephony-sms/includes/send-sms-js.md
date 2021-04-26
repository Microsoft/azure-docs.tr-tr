---
title: include dosyası
description: include dosyası
services: azure-communication-services
author: bertong
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: bertong
ms.openlocfilehash: 8fe8b853fe07af40603950a61c0dd2a1df74d14e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644303"
---
SMS mesajları göndermek için Iletişim Hizmetleri JavaScript SMS SDK 'sını kullanarak Azure Iletişim Hizmetleri ile çalışmaya başlayın.

Bu hızlı başlangıcı tamamlamak, Azure hesabınızda birkaç ABD Doları veya daha kısa bir ücret doğurur.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-js-pr/tree/feature/communication/sdk/communication/communication-sms) | [Package (NPM)](https://www.npmjs.com/package/@azure/communication-sms) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Etkin LTS ve bakım LTS sürümleri (8.11.1 ve 10.14.1 önerilir).
- Etkin bir Iletişim Hizmetleri kaynağı ve bağlantı dizesi. [Iletişim Hizmetleri kaynağı oluşturun](../../create-communication-resource.md).
- SMS etkin telefon numarası. [Telefon numarası alın](../get-phone-number.md).

### <a name="prerequisite-check"></a>Önkoşul denetimi

- Node.js yüklendiğini denetlemek için bir Terminal veya komut penceresinde komutunu çalıştırın `node --version` .
- Iletişim Hizmetleri kaynağınız ile ilişkili telefon numaralarını görüntülemek için [Azure Portal](https://portal.azure.com/)oturum açın, iletişim hizmetleri kaynağınızı bulun ve sol gezinti bölmesinden **telefon numaraları** sekmesini açın.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-nodejs-application"></a>Yeni bir Node.js uygulaması oluşturma

İlk olarak, Terminal veya komut pencerenizi açın, uygulamanız için yeni bir dizin oluşturun ve bu uygulamaya gidin.

```console
mkdir sms-quickstart && cd sms-quickstart
```

`npm init -y`Dosya **üzerinde** varsayılan ayarlarla birpackage.jsoluşturmak için öğesini çalıştırın.

```console
npm init -y
```

Proje kök dizininde **send-sms.js** adlı bir dosya oluşturmak için bir metin düzenleyici kullanın. Bu hızlı başlangıç için tüm kaynak kodu aşağıdaki bölümlerde bu dosyaya ekleyeceksiniz.

### <a name="install-the-package"></a>Paketi yükler

`npm install`JavaScript Için Azure Communication SERVICES SMS SDK 'sını yüklemek için komutunu kullanın.

```console
npm install @azure/communication-sms --save
```

`--save`Seçeneği, kitaplığı dosyadaki **package.js** bir bağımlılık olarak listeler.

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, Node.js için Azure Communication Services SMS SDK 'sının önemli özelliklerinden bazılarını işler.

| Ad                                  | Açıklama                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Bu sınıf tüm SMS işlevleri için gereklidir. Bunu Abonelik bilgileriniz ile birlikte başlatır ve SMS mesajları göndermek için kullanabilirsiniz. |
| SmsSendRequest | Bu arabirim, SMS isteği oluşturmaya yönelik modeldir (örn. telefon numaralarını ve SMS içeriğini yapılandırın. |
| Smssendoseçenekleri | Bu arabirim, teslim raporlamayı yapılandırmak için seçenekler sağlar. `enableDeliveryReport`Olarak ayarlanırsa `true` , teslim başarılı olduğunda bir olay yayınlanır. |
| SmsSendResult               | Bu sınıf, SMS hizmetinden elde edilen sonucu içerir.                                          |

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

SDK 'dan **SmsClient** içeri aktarın ve bağlantı dizeniz ile örneğini oluşturun. Aşağıdaki kod, adlı bir ortam değişkeninden kaynak için bağlantı dizesini alır `COMMUNICATION_SERVICES_CONNECTION_STRING` . [Kaynağınızın bağlantı dizesini yönetme](../../create-communication-resource.md#store-your-connection-string)hakkında bilgi edinin.

**send-sms.js** adlı bir dosya oluşturun ve açın ve aşağıdaki kodu ekleyin:

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-a-1n-sms-message"></a>1: N SMS iletisi gönder

Bir alıcı listesine SMS iletisi göndermek için, `send` alıcı telefon numaralarının bir listesi Ile SmsClient 'deki işlevi çağırın (tek bir alıcıya bir ileti göndermek istiyorsanız, yalnızca bir sayıyı listeye ekleyin). Bu kodu **send-sms.js** sonuna ekleyin:

```javascript
async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Hello World 👋🏻 via SMS"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```
`<from-phone-number>`Iletişim Hizmetleri kaynağınız ile ILIŞKILI SMS özellikli bir telefon numarasıyla ve `<to-phone-number-1>` `<to-phone-number-2>` ileti göndermek istediğiniz telefon sayısıyla değiştirmelisiniz.

> [!WARNING]
> Telefon numaralarının E. 164 uluslararası standart biçiminde sağlanması gerektiğini unutmayın. (ör: + 14255550123).

## <a name="send-a-1n-sms-message-with-options"></a>Seçeneklerle 1: N SMS ileti gönderin

Ayrıca, teslim raporunun etkinleştirilip etkinleştirilmeyeceğini ve özel Etiketler ayarlayamayacağını belirtmek için bir seçenekler nesnesini geçirebilirsiniz.

```javascript

async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Weekly Promotion!"
  }, {
    //Optional parameters
    enableDeliveryReport: true,
    tag: "marketing"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```

`<from-phone-number>`Iletişim Hizmetleri kaynağınız ile ILIŞKILI SMS özellikli bir telefon numarasıyla ve `<to-phone-number-1>` `<to-phone-number-2>` ileti göndermek istediğiniz telefon numarası ile değiştirmelisiniz.

> [!WARNING]
> Telefon numaralarının E. 164 uluslararası standart biçiminde sağlanması gerektiğini unutmayın. (ör: + 14255550123).

`enableDeliveryReport`Parametresi, teslim raporlamayı yapılandırmak için kullanabileceğiniz isteğe bağlı bir parametredir. Bu, SMS iletileri teslim edildiğinde olayları yayma isteyebileceğiniz senaryolar için yararlıdır. SMS iletilerinize yönelik teslim raporlamayı yapılandırmak için [SMS olayları](../handle-sms-events.md) Hızlı Başlangıç Kılavuzu ' na bakın.
`tag` , teslim raporuna bir etiket uygulamak için kullanabileceğiniz isteğe bağlı bir parametredir.

## <a name="run-the-code"></a>Kodu çalıştırma

`node` **send-sms.js** dosyasına eklediğiniz kodu çalıştırmak için komutunu kullanın.

```console

node ./send-sms.js

```
