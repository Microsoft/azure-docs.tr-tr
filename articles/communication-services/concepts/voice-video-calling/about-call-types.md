---
title: Azure Iletişim hizmetlerinde ses ve video kavramları
titleSuffix: An Azure Communication Services concept document
description: Iletişim Hizmetleri çağrı türleri hakkında bilgi edinin.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/25/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 8a25f69019e194650bb6aa2f5b8ae19dd37fbc48
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729176"
---
# <a name="voice-and-video-concepts"></a>Ses ve görüntü kavramları

Azure Iletişim Hizmetleri 'ni kullanarak bir veya daha fazla ses ve video çağrısı yapabilirsiniz. Çağrılarınız, Internet 'e bağlı diğer cihazlara ve eski telefonlar için yapılabilir. Kullanıcılarınızın özel konuşmalardaki veya grup tartışmalarında bir diğerine konuşmasını sağlayan uygulamalar oluşturmak için Iletişim Hizmetleri JavaScript, Android veya iOS SDK 'larını kullanabilirsiniz. Azure Iletişim Hizmetleri, hizmetlere veya botlara yapılan çağrıları destekler.

## <a name="call-types-in-azure-communication-services"></a>Azure Iletişim hizmetlerinde çağrı türleri

Azure Iletişim hizmetlerinde yapabileceğiniz birden çok tür çağrı vardır. Yaptığınız çağrıların türü, sinyal şemanızı, medya trafiği akışlarınızı ve fiyatlandırma modelinizi belirlemektir.

### <a name="voice-over-ip-voip"></a>IP üzerinden ses (VoIP)

Uygulamanızın bir kullanıcısı, bir internet veya veri bağlantısı üzerinden uygulamanızın başka bir kullanıcısını çağırdığında, çağrı IP üzerinden (VoIP) ses üzerinden yapılır. Bu durumda, hem sinyal hem de medya internet üzerinden akar.

### <a name="public-switched-telephone-network-pstn"></a>Ortak anahtarlı telefon ağı (PSTN)

Kullanıcılarınızın geleneksel telefon numarası ile her etkileşim kurması durumunda, aramalar PSTN (ortak anahtarlı telefon ağı) sesli arama tarafından kolaylaştırılmış. PSTN çağrıları yapmak ve almak için, Azure Communication Services kaynağınız için telefon özellikleri eklemeniz gerekir. Bu durumda, sinyal ve medya, kullanıcılarınızı bağlamak için IP tabanlı ve PSTN tabanlı teknolojilerin birleşimini kullanır.

### <a name="one-to-one-call"></a>Bire bir çağrı

Azure Iletişim hizmetlerinde bir bire bir çağrı, kullanıcılarınızın SDK 'lerimizden birini kullanarak başka bir kullanıcıya bağlanmasından oluşur. Çağrı, VoIP veya PSTN olabilir.

### <a name="group-call"></a>Grup çağrısı

Azure Iletişim hizmetlerinde bir grup çağrısı, üç veya daha fazla katılımcı birbirine bağlandığında oluşur. VoIP ve PSTN bağlantılı kullanıcıların herhangi bir birleşimi bir grup çağrısında bulunabilir. Bire bir çağrı, çağrıya daha fazla katılımcı eklenerek bir grup çağrısına dönüştürülebilir. Bu katılımcılardan biri bir bot olabilir.

### <a name="supported-video-standards"></a>Desteklenen video standartları
H. (MPEG-4) destekliyoruz.

### <a name="video-quality"></a>Video kalitesi 
Yerel (iOS, Android) SDK 'larda tam HD 1080p ' i destekliyoruz. Web (JS) SDK 'Sı için standart HD 720p 'yi destekliyoruz. Kalite, kullanılabilir bant genişliğine bağlıdır.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Çağırma ile çalışmaya başlayın](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:
- Genel [çağrı akışları](../call-flows.md) hakkında bilgi edinin
- [Telefon numarası türleri](../telephony-sms/plan-solution.md)
- [ÇAĞıRAN SDK özellikleri](../voice-video-calling/calling-sdk-features.md) hakkında bilgi edinin
