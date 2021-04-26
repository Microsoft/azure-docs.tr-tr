---
title: Azure Web uygulaması güvenlik duvarı ve Azure Ilkesi
description: Azure Ilkesiyle birleştirilmiş Azure Web uygulaması güvenlik duvarı (WAF), WAF kaynakları için kurumsal standartları zorunlu kılabilir ve uyumluluğu değerlendirmenize yardımcı olabilir
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: 7798d7e960286d4f8aa971eb2eb0b03d24bd6360
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97589466"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Azure Web uygulaması güvenlik duvarı ve Azure Ilkesi

Azure Ilkesiyle birleştirilmiş Azure Web uygulaması güvenlik duvarı (WAF), kurumsal standartları zorunlu kılabilir ve WAF kaynakları için uygun ölçekte uyumluluğu değerlendirmenize yardımcı olabilir. Azure ilkesi, ortamın genel durumunu değerlendirmek için toplanan bir görünüm sağlayan, kaynak başına, ilke başına ayrıntı düzeyi ile ayrıntıya gitme olanağı sunan bir idare aracıdır. Azure ilkesi Ayrıca, mevcut kaynaklar için toplu düzeltme ve yeni kaynaklar için otomatik düzeltme aracılığıyla kaynaklarınızı uyumluluğa getirmeye yardımcı olur.

## <a name="azure-policy-for-web-application-firewall"></a>Web uygulaması güvenlik duvarı için Azure Ilkesi

WAF kaynaklarını yönetmek için birkaç yerleşik Azure Ilke tanımı vardır. İlke tanımlarının ve bunların işlevlerini bir dökümü aşağıdaki gibidir:

1. **Azure ön kapı hizmeti Için Web uygulaması güvenlik duvarı (WAF) etkinleştirilmelidir**: kaynak oluşturma üzerinde bir WAF varsa, Azure ön kapı Hizmetleri üzerinde değerlendirilir. İlkenin üç etkisi vardır: denetim, reddetme ve devre dışı bırakma. Azure ön kapısının bir WAF 'si olmadığında ve kullanıcıların Azure ön kapısının uyumlu olmadığını görmesini sağlayan denetim izler. Reddetme, bir WAF iliştiriliyorsa Azure ön kapı hizmeti 'nin oluşturulmasını engeller. Devre dışı bırakılan bu ilkeyi kapatır.

2. **Application Gateway Için Web uygulaması güvenlik duvarı (WAF) etkinleştirilmelidir**: kaynak oluşturma üzerinde bir WAF varsa, uygulama ağ geçitleri üzerinde değerlendirilir. İlkenin üç etkisi vardır: denetim, reddetme ve devre dışı bırakma. Denetim, bir Application Gateway WAF olmadığında ve kullanıcıların ne Application Gateway uyumlu olmadığını görmesini sağlayan izler. Reddetme, bir WAF iliştiriliyorsa Application Gateway oluşturulmasını engeller. Devre dışı bırakılan bu ilkeyi kapatır.

3. **Web uygulaması güvenlik duvarı (WAF), Azure ön kapı hizmeti için belirtilen modu kullanmalıdır**: ' algılama ' veya ' önleme ' modunun Azure ön kapı hizmeti Için tüm Web uygulaması güvenlik duvarı ilkelerine etkin olmasını sağlamak için kullanın. İlkenin üç etkisi vardır: denetim, reddetme ve devre dışı bırakma. Bir WAF belirtilen moda uygun olmadığında denetim izler. Deny, doğru modda değilse WAF 'nin oluşturulmasını engeller. Devre dışı bırakılan bu ilkeyi kapatır.

4. **Web uygulaması güvenlik duvarı (WAF) Application Gateway için belirtilen modu kullanmalıdır**: mantarihlere, Application Gateway Için tüm Web uygulaması güvenlik duvarı ilkelerinde etkin olması Için ' algılama ' veya ' önleme ' modunun kullanılması gerekir. İlkenin üç etkisi vardır: denetim, reddetme ve devre dışı bırakma. Bir WAF belirtilen moda uygun olmadığında denetim izler. Deny, doğru modda değilse WAF 'nin oluşturulmasını engeller. Devre dışı bırakılan bu ilkeyi kapatır.

## <a name="launch-an-azure-policy"></a>Azure Ilkesi başlatma

1.  Azure giriş sayfasında, arama çubuğuna Ilke yazın ve Azure Ilkesi simgesine tıklayın.

2.  Azure Ilke hizmetinde, **yazma** altında, **atamalar**' ı seçin.

:::image type="content" source="../media/waf-azure-policy/policy-home.png" alt-text="Azure Policy içindeki atamalar sekmesi":::

3.  Atamalar sayfasında, üst kısımdaki **Ilke ata** simgesini seçin.

:::image type="content" source="../media/waf-azure-policy/assign-policy.png" alt-text="Ilke ata sayfasında temel bilgiler sekmesi":::

4.  Ilke atama sayfası temelleri sekmesinde aşağıdaki alanları güncelleştirin:
    1.  **Kapsam**: hangi Azure aboneliklerinin ve kaynak gruplarının ilke tanımından etkilenmesi gerektiğini seçin.
    2.  **Dışlamalar**: ilke atamasından dışlamak üzere kapsamdan kaynak seçin.
    3.  **Ilke tanımı**: dışlamaları olan kapsama uygulanacak ilke tanımını seçin. İlgili Web uygulaması güvenlik duvarı Azure Ilkesini seçmek için arama çubuğuna "Web uygulaması güvenlik duvarı" yazın.

:::image type="content" source="../media/waf-azure-policy/policy-listing.png" alt-text="' Kullanılabilir tanımlar ' sayfasında ' Ilke tanımları ' sekmesini gösteren ekran görüntüsü.":::

5.  **Parametreler** sekmesini seçin ve ilke atama parametrelerini güncelleştirin. Parametrenin ne yaptığını netleştirmek için, daha fazla açıklama için parametre adının yanındaki bilgi simgesinin üzerine gelin.

6.  İlke atamasını sonlandırmak için **gözden geçir + oluştur** ' u seçin. İlke ataması, yeni kaynaklar için etkin olana kadar yaklaşık 15 dakika sürer.
