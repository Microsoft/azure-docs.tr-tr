---
author: mmacy
ms.author: marsma
ms.date: 12/16/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 53198c663d318a2eb47bcb3207939bbcb1fdd59c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98763574"
---
Microsoft kimlik doğrulama kitaplığı (MSAL) uygulamaları, sorunları tanılamanıza yardımcı olabilecek günlük iletileri oluşturur. Bir uygulama, birkaç satırlık kodla günlüğe kaydetmeyi yapılandırabilir ve ayrıntı düzeyi üzerinde özel denetime sahip olabilir ve kişisel ve kurumsal verilerin günlüğe kaydedilip kaydedilmeyeceğini belirtir. Bir MSAL günlüğü geri araması oluşturmanızı ve kullanıcıların, kimlik doğrulama sorunları olduğunda günlükleri göndermesi için bir yol sağlamanızı öneririz.

## <a name="logging-levels"></a>Günlüğe kaydetme düzeyleri

MSAL çeşitli düzeylerde günlük ayrıntısı sağlar:

- Hata: bir şeyin yanlış geçmiş olduğunu ve bir hatanın oluşturulduğunu gösterir. Hata ayıklama ve tanımlama sorunlarını belirlemek için kullanılır.
- Uyarı: bir hata veya hata olması gerekmez, ancak tanılama ve işaret noktası sorunlarına yöneliktir.
- Bilgi: MSAL, hata ayıklama amaçlı olarak değil bilgilendirici amaçlar için tasarlanan olayları günlüğe kaydeder.
- Verbose: varsayılan. MSAL, kitaplık davranışının tüm ayrıntılarını günlüğe kaydeder.

## <a name="personal-and-organizational-data"></a>Kişisel ve Kurumsal veriler

Varsayılan olarak, MSAL günlükçüsü son derece hassas kişisel veya kurumsal verileri yakalamaz. Kitaplığı, bunu yapmayı seçerseniz kişisel ve kurumsal verileri günlüğe kaydetmeyi etkinleştirme seçeneğini sağlar.

Aşağıdaki bölümlerde, uygulamanız için MSAL hata günlüğü hakkında daha fazla bilgi sağlanmaktadır.