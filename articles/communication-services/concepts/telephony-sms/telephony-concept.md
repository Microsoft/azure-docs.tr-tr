---
title: Azure Iletişim Hizmetleri için PSTN telefon tümleştirme kavramları
description: Azure Iletişim Hizmetleri uygulamanızda PSTN çağırma yeteneklerini tümleştirmeyi öğrenin.
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 7aebb10fb3855936d930685f1a8eef2588140ad1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105930464"
---
# <a name="telephony-concepts"></a>Telefon kavramları

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-phone-numbers.md)]

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

SDK 'Ları çağıran Azure Iletişim Hizmetleri, uygulamalarınıza telefon ve PSTN eklemek için kullanılabilir. Bu sayfa, temel telefon kavramlarını ve yeteneklerini özetler. Belirli SDK dilleri ve özellikleri hakkında daha fazla bilgi edinmek için bkz. [arama kitaplığı](../../quickstarts/voice-video-calling/calling-client-samples.md) .

## <a name="overview-of-telephony"></a>Telefona genel bakış
Kullanıcılarınız geleneksel bir telefon numarasıyla etkileşdiğinde, aramalar PSTN (ortak anahtarlı telefon ağı) sesli arama tarafından kolaylaştırılmış. PSTN çağrıları yapmak ve almak için, Azure Communication Services kaynağınız için telefon özellikleri eklemeniz gerekir. Bu durumda, sinyal ve medya, kullanıcılarınızı bağlamak için IP tabanlı ve PSTN tabanlı teknolojilerin birleşimini kullanır. İletişim Hizmetleri, PSTN ağına ulaşmak için iki farklı yol sağlar: Azure Cloud çağıran ve SIP arabirimi.

### <a name="azure-cloud-calling"></a>Azure bulut çağrılıyor

Uygulamanıza veya hizmetinize PSTN bağlantısı eklemenin kolay bir yolu, bu durumda Microsoft, telekomünikasyon sağlayıcıdır. Sayıları doğrudan Microsoft 'tan satın alabilirsiniz. Azure Cloud çağıran, Iletişim hizmetlerine yönelik hepsi bir arada bulut telefon çözümüdür. Bu, ACS 'yi ortak anahtarlı telefon ağına (PSTN) bağlayan en basit seçenektir. böylece, dünyanın her yerindeki hatlara ve cep telefonlarına yönelik çağrılara olanak sağlanır. Bu seçenekle, Microsoft, aşağıdaki diyagramda gösterildiği gibi, PSTN dakel bu şekilde davranır:

![Azure bulut çağırma diyagramı.](../media/telephony-concept/azure-calling-diagram.png)

Aşağıdaki ' Evet ' yanıtını alırsanız Azure Cloud çağıran, sizin için doğru çözümdür:
- Azure Cloud çağırma, bölgenizde kullanılabilir.
- Geçerli PSTN taşıyıcınızın tutulması gerekmez.
- PSTN 'ye Microsoft tarafından yönetilen erişimi kullanmak istiyorsunuz.

Bu seçenekle:
- Doğrudan Microsoft 'tan numaralar alabilir ve dünyanın dört bir yanındaki telefonları çağırabilirsiniz.
- Azure Cloud çağıran Azure Iletişim hizmetlerinden çalıştığından, şirket içi dağıtımın dağıtılması veya bakımını yapmanız gerekmez.
- Note: gerekirse, üçüncü taraf PBXs, analog cihazlar ve SBC tarafından desteklenen diğer üçüncü taraf telefon donanımlarıyla birlikte çalışabilirlik için SIP arabirimi aracılığıyla desteklenen bir oturum kenarlığı denetleyicisini (SBC) bağlamayı tercih edebilirsiniz.

Bu seçenek, Azure Iletişim hizmetlerine kesintisiz bağlantı gerektirir.

### <a name="sip-interface"></a>SIP arabirimi

Bu seçenekle, eski şirket içi telefonu ve tercih ettiğiniz taşıyıcınızın Azure Iletişim hizmetlerine bağlanmasını sağlayabilirsiniz. Azure Cloud çağıran, ülkenizde/bölgenizde mevcut olmasa bile, ACS uygulamalarınıza PSTN çağırma özellikleri sağlar. 

![SIP arabirimi diyagramı.](../media/telephony-concept/sip-interface-diagram.png)

Aşağıdaki sorulardan birine ' Yes ' yanıtı verirseniz, SIP arabirimi sizin için doğru çözümdür:

- ACS 'yi PSTN çağırma özellikleri ile kullanmak istiyorsunuz.
- Geçerli PSTN taşıyıcınızın korunması gerekir.
- Azure bulut çağrısı, bazı çağrılarınız aracılığıyla bazı çağrılarla yönlendirme karıştırmak istiyorsunuz.
- Ek yük defterleri, analog cihazlar vb. gibi üçüncü taraf PBXs ve/veya ekipmanlarla birlikte çalışmanız gerekir.

Bu seçenekle:

- Desteklenen SBC 'nizi, ek şirket içi yazılımlara gerek duymadan Azure Iletişim Hizmetleri 'ne bağlayın.
- ACS ile herhangi bir telefon taşıyıcısı kullanabilirsiniz.
- Bu seçeneği yapılandırmayı ve yönetmeyi seçebilirsiniz ya da taşıyıcı veya iş ortağınız tarafından yapılandırılabilir ve yönetilebilir (Bu seçenek, taşıyıcınızın veya ortağınızın bu seçeneği sağladığını sor).
- Bir üçüncü taraf PBX ve analog cihazlar gibi telefon donatımı ve ACS gibi birlikte çalışabilirliği yapılandırabilirsiniz.

Bu seçenek şunları gerektirir:

- Azure 'a kesintisiz bağlantı.
- Desteklenen bir SBC 'yi dağıtma ve sürdürme.
- Üçüncü taraf taşıyıcısı olan bir anlaşma. (Iletişim hizmetlerinde olan kullanıcılar için üçüncü taraf PBX, analog cihazlar veya diğer telefon donanımlarıyla bağlantı sağlamak için bir seçenek olarak dağıtılmadığı sürece.)

## <a name="next-steps"></a>Sonraki adımlar

### <a name="conceptual-documentation"></a>Kavramsal belgeler

- [Azure Iletişim hizmetlerindeki telefon numarası türleri](./plan-solution.md)
- [SIP arabirimi planı](./sip-interface-infrastructure.md)
- [Fiyatlandırma](../pricing.md)

### <a name="quickstarts"></a>Hızlı Başlangıçlar

- [Telefon numarası alın](../../quickstarts/telephony-sms/get-phone-number.md)
- [Telefon çağrısı](../../quickstarts/voice-video-calling/pstn-call.md)
