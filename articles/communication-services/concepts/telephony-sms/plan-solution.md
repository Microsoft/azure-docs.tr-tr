---
title: Azure Iletişim hizmetlerindeki telefon numarası türleri
titleSuffix: An Azure Communication Services concept document
description: SMS ve telefon için farklı türlerde telefon numaralarını etkin bir şekilde kullanmayı öğrenin.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/10/2021
ms.topic: overview
ms.custom: references_regions
ms.service: azure-communication-services
ms.openlocfilehash: 66edf35f292f5b681578d49b8be784de66e4d904
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936538"
---
# <a name="phone-number-types-in-azure-communication-services"></a>Azure Iletişim hizmetlerindeki telefon numarası türleri

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-phone-numbers.md)]

> [!IMPORTANT]
> Telefon numarası kullanılabilirliği Şu anda, ABD veri konumuna sahip Birleşik Devletler ve Iletişim Hizmetleri kaynaklarında fatura adresi olan ücretli Azure abonelikleri ile kısıtlıdır. Telefon numaraları, deneme hesaplarında alınamıyor veya Azure Ücretsiz kredileri kullanılıyor. Daha fazla bilgi için bu belgenin [abonelik uygunluğu](#azure-subscriptions-eligibility) bölümünü ziyaret edin.


Azure Iletişim Hizmetleri, sesli aramalar yapmak ve ortak anahtarlı telefon ağı (PSTN) ile SMS mesajları göndermek için telefon numaralarını kullanmanıza olanak sağlar. Bu belgede, Iletişim hizmetlerini kullanarak telefon ve SMS çözümünüzü planlamak için telefon numarası türlerini, yapılandırma seçeneklerini ve bölge kullanılabilirliğini gözden geçireceğiz.

## <a name="azure-subscriptions-eligibility"></a>Azure aboneliklerine uygunluk

Telefon numarası almak için ücretli bir Azure aboneliğinde olmanız gerekir. Telefon numaraları, deneme hesaplarında veya Azure Ücretsiz kredilerine göre alınamıyor.

Telefon numarası kullanılabilirliği Şu anda, ABD veri konumuna sahip Birleşik Devletler ve Iletişim Hizmetleri kaynaklarında faturalama adresine sahip Azure abonelikleri ile kısıtlıdır.


## <a name="number-types-and-features"></a>Sayı türleri ve özellikleri
İletişim Hizmetleri iki tür telefon numarası sunar: **Yerel** **ve ücretsiz**.

### <a name="local-numbers"></a>Yerel numaralar
Yerel (coğrafi) numaralar Birleşik Devletler yerel alan kodlarından oluşan 10 basamaklı telefon numaralarıdır. Örneğin, `+1 (206) XXX-XXXX` alan koduna sahip yerel bir sayıdır `206` . Bu alan kodu Seattle şehrine atanır. Bu telefon numaraları genellikle bireyler ve yerel işletmeler tarafından kullanılır. Azure Iletişim Hizmetleri Birleşik Devletler yerel sayılar sunar. Bu sayılar, telefon çağrılarını yerleştirmek için kullanılabilir ancak SMS iletileri gönderemezler.

### <a name="toll-free-numbers"></a>Ücretsiz numaralar
Ücretsiz numaralar, ücretsiz alan kodlarına sahip 10 basamaklı telefon numaralarıdır ve ücretsiz olarak herhangi bir telefon numarasından çağrılabilecek. Örneğin, `+1 (800) XXX-XXXX` Kuzey Amerika bölgesindeki ücretsiz bir sayıdır. Bu telefon numaraları genellikle müşteri hizmeti amaçları için kullanılır. Azure Iletişim Hizmetleri Amerika Birleşik Devletleri 'nde ücretsiz numaralar sunar. Bu sayılar, telefon çağrılarını yerleştirmek ve SMS iletileri göndermek için kullanılabilir. Ücretsiz numaralar kişiler tarafından kullanılamaz ve yalnızca uygulamalara atanabilir.

#### <a name="choosing-a-phone-number-type"></a>Telefon numarası türü seçme

Telefon numaranız bir uygulama tarafından kullanılacaksa (örneğin, hizmetiniz adına çağrı yapmak veya ileti göndermek için), ücretsiz veya yerel (coğrafi) bir numara seçebilirsiniz. Uygulamanız SMS mesajları gönderiyor ve/veya çağrılar yaparken ücretsiz bir numara seçebilirsiniz.

Telefon numaranız bir kişi tarafından kullanılıyorsa (örneğin, çağıran uygulamanızın bir kullanıcısı), yerel (coğrafi) telefon numarası kullanılmalıdır.

Aşağıdaki tabloda bu telefon numarası türleri özetlenmektedir:

| Telefon numarası türü | Örnek                              | Ülke kullanılabilirliği    | Telefon numarası özelliği |Yaygın kullanım durumu                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------|------------------------------------------------------------------------------------------------------------------- |
| Yerel (coğrafi)        | + 1 (yerel alan kodu) XXX XX XX  | ABD                      | Çağırma (giden) | Uygulamalarınızda kullanıcılara telefon numarası atama  |
| Toll-Free         | + 1 (ücretsiz alan *kodu*) xxx xx xx | ABD                      | Çağırma (giden), SMS (gelen/giden)| Telefon numaralarını etkileşimli sesli yanıt (ıVR) sistemleri/botlar, SMS uygulamalarına atama                                        |


### <a name="phone-number-capabilities-in-azure-communication-services"></a>Azure Iletişim hizmetlerinde telefon numarası özellikleri

[!INCLUDE [Emergency Calling Notice](../../includes/emergency-calling-notice-include.md)]

Çoğu telefon numarası için "a la carte" özellik kümesini yapılandırmanıza izin veririz. Bu yetenekler, Azure Iletişim Hizmetleri içindeki telefon numaralarınızı kiralayan şekilde seçilebilir.

Sizin için kullanılabilir olan yetenekler, kullandığınız ülkeye, kullanım servis talebine ve seçtiğiniz telefon numarası türüne bağlıdır. Bu yetenekler, yasal gereksinimler nedeniyle ülkeye göre farklılık gösterir. Azure Iletişim Hizmetleri aşağıdaki telefon numarası yeteneklerini sunar:

- **Tek yönlü gıden SMS** Bu seçenek kullanıcılarınıza SMS iletileri göndermenizi sağlar. Bu, bildirim ve iki öğeli kimlik doğrulama senaryolarında yararlı olabilir.
- **İki yönlü gelen ve gıden SMS** Bu seçenek, telefon numaralarını kullanarak kullanıcılarınızın iletilerinizi göndermenizi ve almanızı sağlar. Bu, müşteri hizmeti senaryolarında yararlı olabilir.
- **Tek yönlü giden telefon çağırma** Bu seçenek, kullanıcılarınız için çağrılar yapmanıza ve hizmetiniz tarafından verilen giden çağrılar için arayan KIMLIĞINI yapılandırmanıza olanak tanır. Bu, müşteri hizmeti ve sesli bildirim senaryolarında yararlı olabilir.

## <a name="countryregion-availability"></a>Ülke/bölge kullanılabilirliği

Aşağıdaki tabloda, bu telefon numarası türleriyle ilişkili gelen ve giden çağrı ve SMS özellikleriyle birlikte farklı türlerde telefon numaralarını nereden edinebileceğiniz gösterilmektedir.

|Sayı türü| Sayıları alma | Çağrısı yap                                        | Şuradan gelen çağrıları al                                    |Iletileri buraya gönder       | Şuradan Ileti al |
|-----------| ------------------ | ---------------------------------------------------  |-------------------------------------------------------|-----------------------|--------|
| Yerel (coğrafi)  | ABD                 | ABD, Kanada, Birleşik Krallık, Almanya, Fransa,. + daha fazla *| ABD, Kanada, Birleşik Krallık, Almanya, Fransa,. + daha fazla * |Kullanılamaz| Kullanılamaz |
| Toll-Free | ABD                 | ABD                                                   | ABD                                                    |ABD                | ABD |

* Çağrı hedefleri ve fiyatlandırma hakkında daha fazla bilgi için [fiyatlandırma sayfasına](../pricing.md)bakın.


## <a name="next-steps"></a>Sonraki adımlar

### <a name="quickstarts"></a>Hızlı Başlangıçlar

- [Telefon numarası alın](../../quickstarts/telephony-sms/get-phone-number.md)
- [Bir çağrı yerleştir](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [SMS gönderme](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>Kavramsal belgeler

- [Ses ve görüntü kavramları](../voice-video-calling/about-call-types.md)
- [Telefon kavramları](./telephony-concept.md)
- [Çağrı akışları](../call-flows.md)
- [Fiyatlandırma](../pricing.md)
