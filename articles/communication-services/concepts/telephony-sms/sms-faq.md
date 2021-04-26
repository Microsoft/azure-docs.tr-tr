---
title: SMS HAKKıNDA SSS
titleSuffix: An Azure Communication Services concept document
description: SMS HAKKıNDA SSS
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/26/2021
ms.topic: reference
ms.service: azure-communication-services
ms.openlocfilehash: 1ba7c730542adb74356d71f2482cce57e633cb65
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646461"
---
# <a name="sms-faq"></a>SMS HAKKıNDA SSS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
## <a name="can-a-customer-use-azure-communication-services-for-emergency-purposes"></a>Bir müşteri acil amaçlı Azure Iletişim hizmetlerini kullanabilir mi?

Azure Iletişim Hizmetleri Birleşik Devletler metin ile 911 işlevselliğini desteklemez, ancak Federal Iletişim Komisyonu (FCC) kuralları altında bunu yapmak için bir yükümlülüğünüz olabilir.  FCC, metin-911 kurallarının hizmetinize veya uygulamanıza uygulanıp uygulanmadığını değerlendirmelisiniz. Bu kurallara göre ele aldığınız ölçüde, 911 metin iletisini, bunları isteyen acil durum çağrı merkezlerine yönlendirmekten sorumlu olacaksınız. Kendi metin-911 teslim modelinizi saptayabilirsiniz, ancak her bir yaklaşım, temel alınan mobil taşıyıcı aracılığıyla 911 metin sunmak için kullanıcının mobil cihazında yerel çeviriciyi otomatik olarak başlatmayı içerir.

## <a name="are-there-any-limits-on-sending-messages"></a>İleti gönderilirken herhangi bir sınırlama var mı?

SLA 'larımızla tutarlı yüksek hizmet kalitesini sunmaya devam edebilmemiz için, Azure Iletişim Hizmetleri hız sınırlarını uygular (her temel öğe için farklı). Sınırın ötesinde API 'lerimizi çağıran geliştiriciler, 429 HTTP durum kodu yanıtı alır. Şirketinizde hız limitlerini aşan gereksinimler varsa lütfen bize e-posta gönderin phone@microsoft.com .

SMS için hız sınırları:

|İşlem|Kapsam|Zaman aralığı| Limit (istek #) | Dakika başına ileti birimi|
|---------|-----|-------------|-------------------|-------------------------|
|Ileti gönder|Sayı başına|60|200|200|

## <a name="how-does-azure-communication-services-handle-opt-outs-for-toll-free-numbers"></a>Azure Iletişim Hizmetleri, ücretsiz numaralar için kabul etme işlemini nasıl işler?

Ücretsiz telefon numaraları uygulanan ve ABD taşıyıcılar tarafından zorlanır.
- Durdur-bir kısa mesaj alıcısı kabul etmek isterse, ücretsiz numaraya ' STOP ' gönderebilirler. Taşıyıcı durdurma için aşağıdaki varsayılan yanıtı gönderir: "ağ MSG: Bu sayıdan gönderilen tüm metinleri engelleyen" Durdur "sözcüğüyle yanıtladınız. İletileri yeniden almak için "Durdur" metnini geri alın. "
- Başlat/kaldır-alıcı ücretsiz bir sayıdan kısa mesajlardan metin iletilerine yeniden gönderim isterse, ücretsiz numarayı ' Başlat ' veya ' Durdur ' a GERI gönderebilirler. Taşıyıcı, Başlat/Durdur için şu varsayılan yanıtı gönderir: "ağ ILETISI:" durdurulmuş olursunuz "ve bu sayıdan yeniden ileti almaya başlayacaktır."
- Azure Iletişim Hizmetleri Durdur iletisini algılayacak ve alıcıya yapılan tüm iletileri engelleyecek. Teslim raporu, "verilen alıcı için gönderici engellendi" olarak durum iletisiyle başarısız bir teslim olduğunu gösterir.
- Durdur, kaldır ve Başlat iletileri size geri alınacaktır. Azure Iletişim Hizmetleri, iletişimlerinizi kapatan alıcılara daha fazla ileti gönderme denemesi yapılmadığını sağlamak için bu kabul etme işlemini izlemenizi ve uygulamanızı önerir.

## <a name="how-can-i-receive-messages-using-azure-communication-services"></a>Azure Iletişim hizmetlerini kullanarak nasıl ileti alabilirim?

Azure Iletişim Hizmetleri müşterileri, gelen iletileri almak için Azure Event Grid kullanabilir. Olay kılavuzunuzun iletilerini almak için kurulumunu yapmak üzere bu [hızlı](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/handle-sms-events) başlangıcı izleyin.

## <a name="can-i-sendreceive-long-messages-2048-chars"></a>Uzun iletiler gönderebilir/alabilir (>2048 karakter) miyim?

Azure Iletişim Hizmetleri, SMS üzerinden uzun mesajların gönderilmesini ve alınmasını destekler. Bununla birlikte, bazı kablosuz taşıyıcılar veya cihazlar uzun iletiler alırken farklı şekilde hareket edebilir.

## <a name="how-are-messages-sent-to-landline-numbers-treated"></a>Sabit numaralarına gönderilen iletiler nasıl işlenir?

Birleşik Devletler Azure Iletişim Hizmetleri, yer işareti numaralarını denetlemez ve teslim için bu numarayı teslim etmek üzere taşıyıcılar ile göndermeye çalışır. Müşteriler, yer çizgisi numaralarına gönderilen iletiler için ücretlendirilir. 

## <a name="can-i-send-messages-to-multiple-recipients"></a>Birden çok alıcıya ileti gönderebilir miyim?


Evet, birden çok alıcıya sahip bir istek yapabilirsiniz. Birden çok alıcıya ileti göndermek için bu [hızlı](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/send?pivots=programming-language-csharp) başlangıcı izleyin.
