---
title: Azure altyapınızın uyumluluk otomasyonu için InSpec kullanma
description: InSpec kullanarak Azure dağıtımlarınızdaki sorunları tespit etme hakkında bilgi edinin
keywords: Azure, Chef, DevOps, sanal makineler, genel bakış, otomatikleştir, inspec
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: 2531277eb1aa6048c93240031652e09582409e56
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158232"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Azure altyapınızın uyumluluk otomasyonu için InSpec kullanma

[InSpec](https://www.chef.io/inspec/) , yazılım mühendisleri, işlemler ve güvenlik mühendisleri arasında paylaşılabilen güvenlik & Uyumluluk kurallarını açıklamak için açık kaynaklı bir dildir. InSpec, altyapınızın gerçek durumunu, kolay okunabilir ve kolay yazılabilir InSpec kodunda ifade ettiğiniz istenen durumla karşılaştırarak işe yarar. InSpec ihlalleri algılar ve bulguları rapor biçiminde görüntüler, ancak sizi düzeltme denetimine geçirir.

Sanal makineler, ağ yapılandırması, Azure Active Directory ayarları ve daha fazlası dahil olmak üzere bir abonelikte kaynakların ve kaynak gruplarının durumunu doğrulamak için InSpec kullanabilirsiniz.

Bu makalede, Azure 'da güvenlik ve uyumluluk kolaylaştırmak için InSpec kullanmanın avantajları açıklanmaktadır.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Uyumluluğun anlaşılması ve değerlendirilmesi kolay olsun

Elektronik tablolarda veya Word belgelerinde yazılan uyumluluk belgeleri, bu gereksinimlerin yorumu için açık kalmasını sağlar. InSpec ile, gereksinimlerinizi sürümlenmiş, çalıştırılabilir ve okunabilir bir koda dönüştürürler. Kod, net bir amaç ile somut testlerin ne kadar değerlendirileceğini öğrenmek için konuşmaları değiştirir.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Fleet genelinde sorunları algılayın ve düzeltme önceliğini belirleyin

InSpec aracısız algılama modu, sıkışık düzeyini hızla değerlendirmenizi sağlar. Etki/önem puanlaması için yerleşik meta veriler düzeltilmeye odaklanabilecek alanlara yardımcı olur. Ayrıca, kuralları yeni güvenlik açıklarına veya düzenlemelerine yanıt olarak hızlıca yazabilir ve hemen alabilirsiniz.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Ilke Konuk yapılandırması ile Azure sanal makinelerini denetleme

Azure, Azure [Ilke Konuk yapılandırması](/azure/governance/policy/concepts/guest-configuration)aracılığıyla Azure sanal makinelerini denetlemek Için Chef InSpec tanımlarının kullanımını doğrudan destekler. Konuk yapılandırması, bir Linux sanal makinesini bir belirtilen Chef InSpec tanımına ve rapor uyumluluğunu Azure Ilkesi aracılığıyla geri değerlendirir. Bu denetimlerin sonuçları Azure Izleyici günlükleri aracılığıyla da bildirilir; uyarıları ve diğer otomasyon senaryolarını etkinleştirme.

## <a name="satisfy-audits"></a>Denetimleri karşılaın

InSpec ile, her zaman üç ayda bir veya yıllık gibi önceden belirlenmiş aralıklarda değil, denetim sorularına yanıt verebilirsiniz. InSpec testlerini sürekli olarak çalıştırarak, bir denetleyici bulguları tarafından şaşırmaktansa, tam uyumluluk duruşunuzu ve geçmişinizi bilmenin bir denetim döngüsünü girersiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Azure Cloud Shell InSpec deneyin](https://shell.azure.com)