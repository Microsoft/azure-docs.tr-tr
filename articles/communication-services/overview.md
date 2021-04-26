---
title: Azure İletişim Hizmetleri nedir?
description: Azure Iletişim hizmetlerinin gerçek zamanlı iletişimlerle zengin kullanıcı deneyimleri geliştirmenize nasıl yardımcı olduğunu öğrenin.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 8c2559315e3bfffc41c138be6826adae95dd7b07
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588114"
---
# <a name="what-is-azure-communication-services"></a>Azure İletişim Hizmetleri nedir?

Azure Iletişim Hizmetleri, uygulamalarınıza kolayca gerçek zamanlı ses, video ve telefon iletişimi eklemenizi sağlar. İletişim Hizmetleri SDK 'Ları, iletişim çözümlerinizi SMS işlevselliği eklemenize de olanak tanır. Azure Iletişim Hizmetleri, kimlik belirsiz ve son kullanıcıların tanımlanması ve kimliklerinin doğrulanabilmesi üzerinde tamamen denetim sahibi olursunuz. İnsanlara iletişim veri düzlemine veya hizmetlerine (botlar) bağlanabilirsiniz.

Uygulamalar şunlardır:

- **İşletmeden müşteriye (B2C).** Bir iş çalışanları ve Hizmetleri, özel bir tarayıcıda veya mobil uygulamada ses, video ve zengin metin sohbeti kullanarak tüketicilerle etkileşime girebilirler. Bir kuruluş SMS mesajları gönderebilir ve alabilir ya da Azure aracılığıyla aldığınız telefon numarasını kullanarak etkileşimli bir ses yanıt sistemi (ıVR) işleyebilir. [Microsoft ekipleri Ile tümleştirme,](./quickstarts/voice-video-calling/get-started-teams-interop.md) tüketicilerin çalışanlar tarafından barındırılan takımlar toplantılarına katılmasına izin verir; çalışanların ekiplere zaten alışkın olabileceği uzak sağlık, bankacılık ve ürün destek senaryolarında idealdir.
- **Tüketiciden tüketici.** Ses, video ve zengin metin sohbeti ile tüketiciden müşteriye etkileşimi için ilgi çekici sosyal boşluklar oluşturun. Herhangi bir kullanıcı arabirimi türü, Azure Iletişim Hizmetleri SDK 'Ları üzerinde oluşturulabilir, ancak tüm uygulama örneklerini ve Kullanıcı arabirimi varlıklarını hızlıca kullanmaya başlamanıza yardımcı olmak için kullanılabilir.

Daha fazla bilgi edinmek için [Microsoft](https://www.youtube.com/watch?v=apBX7ASurgM) ortaklığımızı veya aşağıda bağlanılan kaynakları inceleyin.

## <a name="common-scenarios"></a>Genel senaryolar

<br>

| Kaynak                               |Açıklama                           |
|---                                    |---                                   |
|**[İletişim Hizmetleri kaynağı oluşturma](./quickstarts/create-communication-resource.md)**|İlk Iletişim Hizmetleri kaynağınızı sağlamak için Azure portal veya Iletişim Hizmetleri SDK 'sını kullanarak Azure Iletişim hizmetlerini kullanmaya başlayın. Iletişim Hizmetleri kaynak bağlantı dizeniz olduktan sonra, ilk Kullanıcı erişim belirteçlerinizi sağlayabilirsiniz.|
|**[Telefon numarası alın](./quickstarts/telephony-sms/get-phone-number.md)**|Azure Iletişim hizmetlerini kullanarak telefon numaralarını temin edebilir ve serbest bırakabilirsiniz. Bu telefon numaraları, telefon aramaları başlatmak veya almak ve SMS çözümleri oluşturmak için kullanılabilir.|
|**[Uygulamanızdan SMS gönderin](./quickstarts/telephony-sms/send.md)**|Azure Iletişim Hizmetleri SMS SDK 'Sı, hizmet uygulamalarından SMS iletileri gönderme ve alma kullanılır.|

Iletişim Hizmetleri kaynağı oluşturduktan sonra, ses ve video çağırma veya kısa sohbet gibi istemci senaryoları oluşturmaya başlayabilirsiniz:

| Kaynak                               |Açıklama                           |
|---                                    |---                                   |
|**[İlk Kullanıcı erişim belirtecinizi oluşturma](./quickstarts/access-tokens.md)**|Kullanıcı erişim belirteçleri, istemcilerin kimliğini Azure Iletişim Hizmetleri kaynağına karşı doğrulamak için kullanılır. Bu belirteçler, Iletişim Hizmetleri SDK 'Sı kullanılarak sağlanır ve yeniden yayımlandı.|
|**[Ses ve video çağırma ile çalışmaya başlama](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Azure Iletişim Hizmetleri, arama SDK 'sını kullanarak tarayıcınıza veya yerel uygulamalarınıza ses ve video çağırma olanağı eklemenize olanak tanır. |
|**[Arama uygulamanızla bir Teams toplantısına katılma](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Azure Iletişim Hizmetleri, Microsoft ekipleriyle etkileşime geçen özel toplantı deneyimleri oluşturmak için kullanılabilir. Iletişim Hizmetleri çözümünüzün kullanıcıları ses, video, sohbet ve ekran paylaşımı üzerinde takımlar katılımcıları ile etkileşime geçebilir.|
|**[Sohbetle çalışmaya başlama](./quickstarts/chat/get-started.md)**|Azure Iletişim Hizmetleri sohbet SDK 'Sı, uygulamalarınıza gerçek zamanlı kısa mesaj sohbeti eklemek için kullanılır.|

## <a name="samples"></a>Örnekler

Aşağıdaki örnekler, Azure Iletişim hizmetlerinin uçtan uca kullanımını gösterir. Bu örnekleri kullanarak kendi Iletişim Hizmetleri çözümlerinizi önyükleyebilirsiniz.
<br>

| Örnek adı                               | Description                           |
|---                                    |---                                   |
|**[Hero örneğini çağıran Grup](./samples/calling-hero-sample.md)**| Tarayıcılar, iOS ve Android cihazlar için Grup çağırma için tasarlanan bir uygulama örneği indirin. |
|**[Grup sohbeti Hero örneği](./samples/chat-hero-sample.md)**| Tarayıcılar için Grup metni sohbeti için tasarlanan bir uygulama örneği indirin. |


## <a name="platforms-and-sdk-libraries"></a>Platformlar ve SDK kitaplıkları

Aşağıdaki kaynaklarla Azure Communication Services SDK 'Ları hakkında daha fazla bilgi edinin. Kendi istemcilerinizi derlemek veya hizmete Internet üzerinden erişmek istiyorsanız, REST API 'Leri çoğu işlevsellik için kullanılabilir.

| Kaynak                               | Açıklama                           |
|---                                    |---                                   |
|**[SDK kitaplıkları ve REST API 'Leri](./concepts/sdk-options.md)**|Azure Iletişim Hizmetleri Özellikleri, kavramsal olarak, her biri bir SDK tarafından temsil edilen altı alan halinde düzenlenir. Gerçek zamanlı iletişim gereksinimlerinize göre hangi SDK kitaplıklarının kullanılacağına karar verebilirsiniz.|
|**[SDK 'ya genel bakış](./concepts/voice-video-calling/calling-sdk-features.md)**|SDK 'ya genel bakış çağıran Iletişim hizmetlerini gözden geçirin.|
|**[Sohbet SDK genel bakış](./concepts/chat/sdk-features.md)**|Iletişim Hizmetleri sohbet SDK 'sına genel bakış konusunu inceleyin.|
|**[SMS SDK genel bakış](./concepts/telephony-sms/sdk-features.md)**|Iletişim Hizmetleri SMS SDK 'sına genel bakış ' a bakın.|

## <a name="other-microsoft-communication-services"></a>Diğer Microsoft Iletişim Hizmetleri

Şu anda Iletişim hizmetleriyle doğrudan birlikte çalışabilen, kullanmayı düşünebileceğiniz iki farklı Microsoft iletişim ürünü vardır:

 - [Microsoft Graph bulut Iletişimi API 'leri](/graph/cloud-communications-concept-overview) , kuruluşların Microsoft 365 lisanslarla Azure Active Directory kullanıcılara bağlı iletişim deneyimleri oluşturmasına olanak tanır. Bu, Azure Active Directory bağlı olan veya Microsoft ekiplerinde üretkenlik deneyimlerini uzatmak istediğiniz durumlarda idealdir. Ayrıca [takımlar deneyimi](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest) içinde uygulama ve özelleştirme oluşturmak Için API 'ler vardır.

 - [Azure PlayFab partisi](/gaming/playfab/features/multiplayer/networking/) , oyunlara düşük gecikmeli sohbet ve veri iletişimi eklemeyi basitleştirir. Sohbet ve ağ sistemlerini Iletişim hizmetleriyle oyun oynamak için PlayFab, Xbox 'ta hazırlanmış ve ücretsiz bir seçenektir.


## <a name="next-steps"></a>Sonraki Adımlar

 - [İletişim Hizmetleri kaynağı oluşturma](./quickstarts/create-communication-resource.md)
