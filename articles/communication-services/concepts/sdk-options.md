---
title: Azure Iletişim Hizmetleri için SDK 'Lar ve REST API 'Leri
titleSuffix: An Azure Communication Services concept document
description: Azure Iletişim Hizmetleri SDK 'Ları ve REST API 'Leri hakkında daha fazla bilgi edinin.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: d71720b446baa8e2e5e15c407a69d9ff884004b2
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307699"
---
# <a name="sdks-and-rest-apis"></a>SDK 'Lar ve REST API 'Leri

Azure Iletişim Hizmetleri Özellikleri, kavramsal olarak altı alan halinde düzenlenir. Çoğu alan, doğrudan Internet üzerinden kullanabileceğiniz, yayımlanan REST API 'Lerine karşı programlanan tam açık kaynaklı SDK 'Lara sahiptir. Çağıran SDK özel ağ arabirimlerini kullanır ve şu anda kapalı kaynağıdır. SDK 'lar için örnekler ve daha fazla teknik ayrıntı [Azure Iletişim Hizmetleri GitHub](https://github.com/Azure/communication)deposunda yayımlanır.

## <a name="rest-apis"></a>REST API'leri
İletişim Hizmetleri API 'Leri, [docs.Microsoft.com](/rest/api/azure/)' deki DIĞER Azure REST API 'leri ile birlikte belgelenmiştir. Bu belgede, HTTP iletilerinizi nasıl yapılandıracağınızı ve Postman kullanmaya yönelik yönergeler sunulmaktadır. Bu belge [GitHub](https://github.com/Azure/azure-rest-api-specs)'da Swagger biçiminde de sunulur.


## <a name="sdks"></a>SDK

| Bütünleştirilmiş Kod | Ad alanları| Protokoller | Özellikler |
|------------------------|-------------------------------------|---------------------------------|--------------------------------------------------------------------------------------------|
| Azure Resource Manager | Azure. ResourceManager. Communication | [REST](https://docs.microsoft.com/rest/api/communication/communicationservice)| Iletişim Hizmetleri kaynaklarını sağlama ve yönetme|
| Common | Azure. Communication. Common| REST | Diğer SDK 'lar için temel türleri sağlar |
| Kimlik | Azure. Communication. Identity| [REST](https://docs.microsoft.com/rest/api/communication/communicationidentity)| Kullanıcıları yönetme, belirteçleri erişim|
| Telefon numaraları _(Beta)_| Azure. Communication. PhoneNumbers| [REST](https://docs.microsoft.com/rest/api/communication/phonenumberadministration)| Telefon numaralarını edinin ve yönetin |
| Sohbet | Azure. Communication. sohbet| Özel sinyalle [rest](https://docs.microsoft.com/rest/api/communication/) | Uygulamalarınıza gerçek zamanlı metin tabanlı sohbet ekleyin |
| SMS| Azure. Communication. SMS | [REST](https://docs.microsoft.com/rest/api/communication/sms)| SMS iletileri gönderme ve alma|
| Events| Azure. Communication. çağrılıyor | Özel aktarım | Ses, video, ekran paylaşımı ve diğer gerçek zamanlı veri iletişim yeteneklerini kullanın |

Azure Resource Manager, kimlik ve SMS SDK 'Ları, hizmet tümleştirmesine odaklanır ve çoğu durumda, bu işlevleri Son Kullanıcı uygulamalarıyla tümleştirirseniz güvenlik sorunları ortaya çıkar. Ortak ve sohbet SDK 'Ları hizmet ve istemci uygulamalarına uygundur. Çağıran SDK, istemci uygulamaları için tasarlanmıştır. Hizmet senaryolarına odaklanan bir SDK geliştirme aşamasındadır.


### <a name="languages-and-publishing-locations"></a>Diller ve yayımlama konumları

Bireysel SDK paketleri için yayımlama konumları aşağıda ayrıntılı olarak verilmiştir.

| Alan           | JavaScript | .NET | Python | Java SE | iOS | Android | Diğer                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Communication)    |   [Pypı](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [GitHub aracılığıyla git](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| Common         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Common/)    | Yok      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| Kimlik | [npm](https://www.npmjs.com/package/@azure/communication-identity)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Identity)    | [Pypı](https://pypi.org/project/azure-communication-identity/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-identity)   | -              | -              | -                            |
| Telefon numaraları | [npm](https://www.npmjs.com/package/@azure/communication-phone-numbers)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.PhoneNumbers)    | [Pypı](https://pypi.org/project/azure-communication-phonenumbers/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-phonenumbers)   | -              | -              | -                            |
| Sohbet           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.nuget.org/packages/Azure.Communication.Chat)     | [Pypı](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)  | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -                              |
| SMS            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Sms)    | [Pypı](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| Events        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [GitHub](https://github.com/Azure/Communication/releases)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| Başvuru Belgeleri     | [belgeler](https://azure.github.io/azure-sdk-for-js/communication.html)         | [belgeler](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [belgeler](http://azure.github.io/azure-sdk-for-java/communication.html)     | [belgeler](/objectivec/communication-services/calling/)      | [belgeler](/java/api/com.azure.android.communication.calling)            | -                              |


## <a name="rest-api-throttles"></a>REST API kısıtlar
Bazı REST API 'Leri ve karşılık gelen SDK yöntemlerinin kısıtlama limitleri vardır. Bu kısıtlama sınırlarının aşılması bir  `429 - Too Many Requests` hata yanıtı tetikleyecektir. Bu sınırlar, [Azure desteği isteğiyle](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)artırılabilir.

| API                                                                                                                          | Kısıtlama            |
|------------------------------------------------------------------------------------------------------------------------------|---------------------|
| [Tüm arama telefon numarası plan API 'Leri](https://docs.microsoft.com/rest/api/communication/phonenumberadministration)         | 4 istek/gün      |
| [Satın alma telefon numarası planı](https://docs.microsoft.com/rest/api/communication/phonenumberadministration/purchasesearch) | 1 aylık satın alma  |
| [SMS gönder](https://docs.microsoft.com/rest/api/communication/sms/send)                                                       | 200 istek/dakika |


## <a name="sdk-platform-support-details"></a>SDK Platformu destek ayrıntıları

### <a name="ios-and-android"></a>iOS ve Android 

- İletişim Hizmetleri iOS SDK 'Ları hedef iOS sürüm 13 + ve Xcode 11 +.
- Android Java SDK 'Ları hedef Android API düzeyi 21 + ve Android Studio 4.0 +

### <a name="net"></a>.NET 

Iletişim Hizmetleri paketleri, çağırma haricinde, aşağıda listelenen platformları destekleyen .NET Standard 2,0 ' i hedeflemelidir.

.NET Framework 4.6.1 aracılığıyla destek
- Windows 10, 8,1, 8 ve 7
- Windows Server 2012 R2, 2012 ve 2008 R2 SP1

.NET Core 2,0 aracılığıyla destek:
- Windows 10 (1607 +), 7 SP1 +, 8,1
- Windows Server 2008 R2 SP1 +
- Max OS X 10.12 +
- Linux çoklu sürüm/dağıtım
- UWP 10.0.16299 (RS3) Eylül 2017
- Unity 2018,1
- Mono 5,4
- Xamarin iOS 10,14
- Xamarin Mac 3,8

## <a name="api-stability-expectations"></a>API kararlılık beklentileri

> [!IMPORTANT]
> Bu bölüm, REST API 'Ler ve **tutarlı** olarak Işaretlenmiş SDK 'lar hakkında rehberlik sağlar. Yayın öncesi, önizleme veya beta olarak işaretlenen API 'Ler, **bildirimde bulunulmadan** değiştirilebilir veya kullanım dışı bırakılmış olabilir.

Gelecekte Iletişim Hizmetleri SDK 'Larının sürümlerini devre dışı bırakabiliriz ve REST API 'lerimiz ve yayınlanmış SDK 'larda önemli değişiklikler sağlayabiliriz. Azure iletişim hizmetleri, hizmet sürümlerinin kullanımdan kaldırılması için *genellikle* iki desteklenebilirlik ilkesi izler:

- Bir Iletişim Hizmetleri arabirimi değişikliği nedeniyle kodu değiştirmeniz gerekmeden önce en az üç yıl önce bilgilendirilirsiniz. Tüm belgelenen REST API 'leri ve SDK API 'Leri, arabirimlerin kullanımdan çıkarmadan önce en az üç yıllık uyarı ister.
- SDK derlemelerini en son ikincil sürüme güncelleştirmek zorunda kalmadan önce en az bir yıl bilgilendirilirsiniz. Bu gerekli güncelleştirmeler aynı ana sürümde olduklarından herhangi bir kod değişikliği gerektirmemelidir. Bu özellikle, genellikle güvenlik ve performans güncelleştirmeleri gerektiren gerçek zamanlı bileşenlere sahip olan çağrı ve sohbet kitaplıkları için geçerlidir. Iletişim Hizmetleri SDK 'larınızı güncel tutmanız önerilir.

### <a name="api-and-sdk-decommissioning-examples"></a>API ve SDK yetkisini alma örnekleri

**SMS REST API v24 sürümünü uygulamanıza tümleştirdiniz. Azure Iletişim yayınları v25.**

Bu API 'Ler çalışmayı durdurmadan ve v25 ' ye güncelleştirilmeye zorlanacak üç yıllık uyarı alırsınız. Bu güncelleştirme için bir kod değişikliği gerekebilir.

**Çağıran SDK 'nın v 2.02 sürümünü uygulamanıza tümleştirdiniz. Azure Iletişim yayımları v 2.05.**

V 2.05 sürümünün 12 ay içinde çağıran SDK 'nın v 2.05 sürümüne güncelleştirmeniz gerekebilir. Bu, sanal 2.05 v2 ana sürümünde olduğundan ve hiç bir değişiklik olmadığından, bir kod değişikliğine gerek olmadan yapıtın basit bir değişikliği olmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki SDK genel bakışlarına bakın:

- [SDK 'Ya genel bakış](../concepts/voice-video-calling/calling-sdk-features.md)
- [Sohbet SDK genel bakış](../concepts/chat/sdk-features.md)
- [SMS SDK genel bakış](../concepts/telephony-sms/sdk-features.md)

Azure Iletişim Hizmetleri 'ni kullanmaya başlamak için:

- [Azure Iletişim kaynakları oluşturma](../quickstarts/create-communication-resource.md)
- [Kullanıcı erişim belirteçleri](../quickstarts/access-tokens.md) oluştur
