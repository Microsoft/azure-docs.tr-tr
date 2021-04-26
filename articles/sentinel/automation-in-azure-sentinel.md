---
title: Azure Sentinel 'de Otomasyon 'a giriş | Microsoft Docs
description: Bu makalede, Azure Sentinel 'in güvenlik düzenlemesi, otomasyon ve yanıt (SOAR) özellikleri tanıtılmakta ve SOAR bileşenleri-Otomasyon kuralları ve PlayBook 'lar açıklanmaktadır.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 54d7c997ce17c927a692e84f6094e3a08f707af7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610097"
---
# <a name="security-orchestration-automation-and-response-soar-in-azure-sentinel"></a>Azure Sentinel 'de güvenlik düzenlemesi, otomasyon ve yanıt (SOAR)

Bu makalede, Azure Sentinel 'in güvenlik düzenlemesi, otomasyon ve yanıt (SOAR) özellikleri açıklanmakta ve güvenlik tehditlerine yanıt olarak otomasyon kurallarının ve PlayBook kullanmanın, SOC 'nin verimliliğini nasıl artıracağını ve zaman ve kaynaklarınızın nasıl tasarruf edileceğini gösterir.

## <a name="azure-sentinel-as-a-soar-solution"></a>SOAR çözümü olarak Azure Sentinel

### <a name="the-problem"></a>Sorun

SıEM/SOC ekipleri genellikle, kullanılabilir personelin çok büyük olması için birimlerde düzenli olarak güvenlik uyarıları ve olayları açığa kaldırmalardır. Bu, çok sayıda uyarının yoksayıldığı ve çok sayıda olayın incelenemediği durumlarda, kuruluşun fark edilmemiş saldırılara karşı savunmasız kalmasına neden olur.

### <a name="the-solution"></a>Çözüm

Azure Sentinel, güvenlik bilgileri ve olay yönetimi (SıEM) sistemi olmanın yanı sıra güvenlik düzenlemesi, otomasyon ve yanıt (SOAR) için de bir platformdur. Birincil amaçlarından biri, güvenlik Işlemleri merkezinizin ve personelinizin (SOC/SecOps) sorumluluğu olan yinelenen ve öngörülebilir bir zenginleştirme, yanıt ve düzeltme görevlerinin otomatikleştirilmesi, gelişmiş tehditler hakkında daha ayrıntılı araştırma ve savunma konusunda daha ayrıntılı bilgi için zaman ve kaynak boşaltmaya yöneliktir. Otomasyon, Azure Sentinel 'de olay işleme ve yanıt otomasyonunu merkezi olarak yöneten Otomasyon kurallarından, tehdit yanıt görevlerinize güçlü ve esnek gelişmiş otomasyon sağlamak üzere önceden belirlenmiş eylem dizilerini çalıştıran PlayBook 'lar için birkaç farklı form alır.

## <a name="automation-rules"></a>Otomasyon kuralları

Otomasyon kuralları Azure Sentinel 'de yeni bir kavramdır. Bu özellik, kullanıcıların olay işleme otomasyonunu merkezi olarak yönetmesine olanak tanır. Otomasyon kuralları, bir seferde birden fazla analiz kuralına yönelik yanıtları otomatik hale getirmenize, el ile etiketleme, atayabilir veya kapatabilir ve bu da yürütülen eylemlerin sırasını kontrol etmenizi sağlar ve bunları çalıştırabilirsiniz. Otomasyon kuralları, Azure Sentinel 'de Otomasyon kullanımını kolaylaştırır ve olay düzenleme işlemleriniz için karmaşık iş akışlarını basitleştirmenize imkan sağlar.

[Otomasyon kurallarının bu tüm açıklamasından](automate-incident-handling-with-automation-rules.md)daha fazla bilgi edinin.

> [!IMPORTANT]
>
> - **Otomasyon kuralları** özelliği şu anda **önizlemededir**. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

## <a name="playbooks"></a>Playbook'lar

PlayBook, Azure Sentinel 'ten bir yordam olarak çalıştırılabilen bir yanıt ve düzeltme eylemleri ve mantığı koleksiyonudur. Bir PlayBook, tehdit yanıtınızı otomatikleştirmenize ve düzenlemeye yardımcı olabilir, hem iç hem de dış diğer sistemlerle tümleşebilir ve sırasıyla bir analiz kuralı veya Otomasyon kuralı tarafından tetiklendiğinde belirli uyarılara veya olaylara yanıt olarak otomatik çalışacak şekilde ayarlanabilir. Olaylar sayfasından, uyarılara yanıt olarak isteğe bağlı olarak el ile de çalıştırılabilir.

Azure Sentinel 'de playbooks, kurumsal bir sistem genelinde sistemler arasında görev ve iş akışlarını zamanlamanıza, otomatikleştirmenize ve ayarlamanıza yardımcı olan [Azure Logic Apps](../logic-apps/logic-apps-overview.md)yerleşik iş akışlarını temel alır. Bu, PlayBook 'lar Logic Apps ' tümleştirme ve düzenleme özellikleri ve kullanımı kolay tasarım araçları ve Katman 1 Azure hizmeti 'nin ölçeklenebilirlik, güvenilirlik ve hizmet düzeyi 'nin tüm gücünden ve özelleştirme avantajlarından faydalanabilir.

[PlayBook](automate-responses-with-playbooks.md)'lar hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Azure Sentinel 'in, SOC 'in daha etkili ve verimli bir şekilde çalışmasını sağlamak için Otomasyonu nasıl kullandığını öğrendiniz.

- Olay işleme otomasyonu hakkında bilgi edinmek için bkz. [Azure Sentinel 'de olay Işlemeyi otomatikleştirme](automate-incident-handling-with-automation-rules.md).
- Gelişmiş otomasyon seçenekleri hakkında daha fazla bilgi edinmek için bkz. [Azure Sentinel 'de PlayBook 'ları ile tehdit yanıtını otomatikleştirme](automate-responses-with-playbooks.md).
- Otomasyon kuralları ve PlayBook 'ları uygulama konusunda yardım için bkz. [öğretici: Azure Sentinel 'de tehdit yanıtlarını otomatikleştirmek için PlayBook 'Ları kullanma](tutorial-respond-threats-playbook.md).
