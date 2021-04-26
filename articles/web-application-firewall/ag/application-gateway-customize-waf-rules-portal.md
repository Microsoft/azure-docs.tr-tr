---
title: Portalı kullanarak kuralları özelleştirme-Azure Web uygulaması güvenlik duvarı
description: Bu makalede, Azure portal Application Gateway Web uygulaması Güvenlik Duvarı kurallarının nasıl özelleştirileceği hakkında bilgi verilmektedir.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 04/21/2021
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 0ab122d178e5390a53e5a3a39f1b7763b298dc6d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878335"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Azure portal kullanarak Web uygulaması güvenlik duvarı kurallarını özelleştirme

Azure Application Gateway Web uygulaması güvenlik duvarı (WAF), Web uygulamaları için koruma sağlar. Bu korumalar, Open Web Application Security Project (OWASP) çekirdek kural kümesi (CCR) tarafından sağlanır. Bazı kurallar yanlış pozitif sonuçlar oluşmasına neden olabilir ve gerçek trafiği engelleyebilir. Bu nedenle, Application Gateway kural gruplarını ve kuralları özelleştirme yeteneği sağlar. Belirli kural grupları ve kuralları hakkında daha fazla bilgi için bkz. [Web uygulaması güvenlik duvarı liste grupları ve kuralları listesi](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Uygulama ağ geçidiniz WAF katmanını kullanmıyor ise, uygulama ağ geçidini WAF katmanına yükseltme seçeneği sağ bölmede görüntülenir. 

:::image type="content" source="../media/application-gateway-customize-waf-rules-portal/1.png" alt-text="WAF 'yi etkinleştir"::: 

## <a name="view-rule-groups-and-rules"></a>Kural gruplarını ve kuralları görüntüle

**Kural gruplarını ve kuralları görüntülemek için**
1. Uygulama ağ geçidine gidin ve **Web uygulaması güvenlik duvarı**' nı seçin.  
2. **WAF ilkenizi** seçin.
2. **Yönetilen kurallar**' ı seçin.

   Bu görünümde, seçilen kural kümesiyle birlikte sunulan tüm kural gruplarının sayfasındaki bir tablo gösterilir. Kuralın tüm onay kutuları seçilidir.

## <a name="disable-rule-groups-and-rules"></a>Kural gruplarını ve kuralları devre dışı bırak

> [!IMPORTANT]
> Kural gruplarını veya kuralları devre dışı bırakırken dikkatli olun. Bu, sizi daha fazla güvenlik riskine maruz bırakabiliyor.

**Kural gruplarını veya belirli kuralları devre dışı bırakmak için**

   1. Devre dışı bırakmak istediğiniz kuralları veya kural gruplarını arayın.
   2. Devre dışı bırakmak istediğiniz kuralların onay kutularını seçin. 
   3. Seçili kurallar için sayfanın üst kısmındaki (etkinleştir/devre dışı bırak) eylemi seçin.
   2. **Kaydet**’i seçin.
    :::image type="content" source="../media/application-gateway-customize-waf-rules-portal/figure3.png" alt-text="Devre dışı bırakılmış kuralları Kaydet"::: 

## <a name="mandatory-rules"></a>Zorunlu kurallar

Aşağıdaki listede, WAF 'nin önleme modundayken isteği engellemesini sağlayan koşullar yer almaktadır. Algılama modunda, özel durumlar olarak günlüğe kaydedilir.

Bunlar yapılandırılamaz veya devre dışı bırakılamaz:

* Gövde incelemesi devre dışı bırakılmadığı takdirde istek gövdesi ayrıştırılamadı, istek engellenmiyor (XML, JSON, form verileri)
* İstek gövdesi (dosya olmadan) veri uzunluğu yapılandırılan sınırdan daha büyük
* İstek gövdesi (dosyalar dahil) sınırdan daha büyük
* WAF altyapısında bir iç hata oluştu

X + x 'e özgü:

* Gelen anomali puanı eşiği aştı

## <a name="next-steps"></a>Sonraki adımlar

Devre dışı kurallarınızı yapılandırdıktan sonra, WAF günlüklerinizi görüntülemeyi öğrenebilirsiniz. Daha fazla bilgi için bkz. [tanılama Application Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).