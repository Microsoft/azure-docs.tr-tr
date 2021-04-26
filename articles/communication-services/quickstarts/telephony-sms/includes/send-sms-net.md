---
title: include dosyası
description: include dosyası
services: azure-communication-services
author: peiliu
manager: rejooyan
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: peiliu
ms.openlocfilehash: 5fd209c612f90e3912e244daf60d20edf30a08c6
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113336"
---
SMS mesajları göndermek için Iletişim Hizmetleri C# SMS SDK 'sını kullanarak Azure Iletişim Hizmetleri ile çalışmaya başlayın.

Bu hızlı başlangıcı tamamlamak, Azure hesabınızda birkaç ABD Doları veya daha kısa bir ücret doğurur.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Package (NuGet)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- İşletim sisteminiz için en son sürüm [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) .
- Etkin bir Iletişim Hizmetleri kaynağı ve bağlantı dizesi. [Iletişim Hizmetleri kaynağı oluşturun](../../create-communication-resource.md).
- SMS etkin telefon numarası. [Telefon numarası alın](../get-phone-number.md).

### <a name="prerequisite-check"></a>Önkoşul denetimi

- Bir Terminal veya komut penceresinde, `dotnet` .NET SDK 'nın yüklü olup olmadığını denetlemek için komutunu çalıştırın.
- Iletişim Hizmetleri kaynağınız ile ilişkili telefon numaralarını görüntülemek için [Azure Portal](https://portal.azure.com/)oturum açın, iletişim hizmetleri kaynağınızı bulun ve sol gezinti bölmesinden **telefon numaraları** sekmesini açın.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-c-application"></a>Yeni bir C# uygulaması oluşturma

Konsol penceresinde (cmd, PowerShell veya Bash gibi), `dotnet new` adıyla yeni bir konsol uygulaması oluşturmak için komutunu kullanın `SmsQuickstart` . Bu komut, tek bir kaynak dosyası olan basit bir "Merhaba Dünya" C# projesi oluşturur: **program. cs**.

```console
dotnet new console -o SmsQuickstart
```

Dizininizi yeni oluşturulan uygulama klasörüyle değiştirin ve `dotnet build` uygulamanızı derlemek için komutunu kullanın.

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>Paketi yükler

Hala uygulama dizininde, komutunu kullanarak .NET için Azure Communication Services SMS SDK 'sını yükleyebilirsiniz `dotnet add package` .

```console
dotnet add package Azure.Communication.Sms --version 1.0.0
```

`using`Ad alanını dahil etmek Için **program. cs** ' nin üstüne bir yönerge ekleyin `Azure.Communication` .

```csharp

using System;
using System.Collections.Generic;

using Azure;
using Azure.Communication;
using Azure.Communication.Sms;

```

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, C# için Azure Communication Services SMS SDK 'sının önemli özelliklerinden bazılarını işler.

| Ad                                       | Açıklama                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | Bu sınıf tüm SMS işlevleri için gereklidir. Bunu Abonelik bilgileriniz ile birlikte başlatır ve SMS mesajları göndermek için kullanabilirsiniz.                           |
| Smssendoseçenekleri | Bu sınıf, teslim raporlamayı yapılandırmak için seçenekler sağlar. Enable_delivery_report true olarak ayarlanırsa, teslim başarılı olduğunda bir olay yayınlanır |
| SmsSendResult               | Bu sınıf, SMS hizmetinden elde edilen sonucu içerir.                                          |

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

 **Program. cs** 'yi bir metin düzenleyicisinde açın ve `Main` `SmsClient` bağlantı dizeniz ile başlatmak için yöntemin gövdesini kodla değiştirin. Aşağıdaki kod, adlı bir ortam değişkeninden kaynak için bağlantı dizesini alır `COMMUNICATION_SERVICES_CONNECTION_STRING` . [Kaynağınızın bağlantı dizesini yönetme](../../create-communication-resource.md#store-your-connection-string)hakkında bilgi edinin.


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-a-11-sms-message"></a>1:1 SMS iletisi gönder

Tek bir alıcıya SMS iletisi göndermek için, `Send` `SendAsync` SmsClient ' dan or işlevini çağırın. Bu kodu `Main` **program. cs** içindeki yönteminin sonuna ekleyin:

```csharp
SmsSendResult sendResult = smsClient.Send(
    from: "<from-phone-number>",
    to: "<to-phone-number>",
    message: "Hello World via SMS"
);

Console.WriteLine($"Sms id: {sendResult.MessageId}");
```
`<from-phone-number>`Iletişim Hizmetleri kaynağınız ile ILIŞKILI SMS özellikli telefon numarasıyla ve `<to-phone-number>` ileti göndermek istediğiniz telefon numarasıyla değiştirmelisiniz.

> [!WARNING]
> Telefon numaralarının E. 164 uluslararası standart biçiminde sağlanması gerektiğini unutmayın. (ör: + 14255550123).

## <a name="send-a-1n-sms-message-with-options"></a>Seçeneklerle 1: N SMS ileti gönderin
Bir alıcı listesine SMS iletisi göndermek için `Send` `SendAsync` alıcının telefon numaralarının bir listesiyle birlikte SmsClient 'deki veya işlevini çağırın. Ayrıca, teslim raporunun etkinleştirilip etkinleştirilmeyeceğini ve özel Etiketler ayarlayamayacağını belirtmek için isteğe bağlı parametreleri de geçirebilirsiniz.

```csharp
Response<IReadOnlyList<SmsSendResult>> response = smsClient.Send(
    from: "<from-phone-number>",
    to: new string[] { "<to-phone-number-1>", "<to-phone-number-2>" },
    message: "Weekly Promotion!",
    options: new SmsSendOptions(enableDeliveryReport: true) // OPTIONAL
    {
        Tag = "marketing", // custom tags
    });

IEnumerable<SmsSendResult> results = response.Value;
foreach (SmsSendResult result in results)
{
    Console.WriteLine($"Sms id: {result.MessageId}");
    Console.WriteLine($"Send Result Successful: {result.Successful}");
}
```

`<from-phone-number>`Iletişim Hizmetleri kaynağınız ile ILIŞKILI SMS özellikli bir telefon numarasıyla ve `<to-phone-number-1>` `<to-phone-number-2>` ileti göndermek istediğiniz telefon numarası ile değiştirmelisiniz.

> [!WARNING]
> Telefon numaralarının E. 164 uluslararası standart biçiminde sağlanması gerektiğini unutmayın. (ör: + 14255550123).

`enableDeliveryReport`Parametresi, teslim raporlamayı yapılandırmak için kullanabileceğiniz isteğe bağlı bir parametredir. Bu, SMS iletileri teslim edildiğinde olayları yayma isteyebileceğiniz senaryolar için yararlıdır. SMS iletilerinize yönelik teslim raporlamayı yapılandırmak için [SMS olayları](../handle-sms-events.md) Hızlı Başlangıç Kılavuzu ' na bakın.

`Tag` Teslim raporuna bir etiket uygulamak için kullanılır

## <a name="run-the-code"></a>Kodu çalıştırma

Uygulamayı komut ile uygulama dizininizden çalıştırın `dotnet run` .

```console
dotnet run
```

## <a name="sample-code"></a>Örnek Kod

Örnek uygulamayı [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS) 'dan indirebilirsiniz
