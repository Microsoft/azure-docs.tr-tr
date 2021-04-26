---
title: Azure ön kapısı-uygulama katmanı güvenliği | Microsoft Docs
description: Bu makale, Azure ön kapısının uygulamanızın arka uçlarınızın korunmasına ve güvenliğini sağlamanıza nasıl olanak sağladığını anlamanıza yardımcı olur
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 4ee50b4c7da27df3630c1b4d263f076da44189bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89399948"
---
# <a name="application-layer-security-with-front-door"></a>Ön kapılı uygulama katmanı güvenliği
Azure ön kapısı, Web uygulamalarınızı ağ saldırılarına karşı korumak için Web uygulaması koruma yeteneği ve SQL ekleme veya siteler arası betik oluşturma (XSS) gibi yaygın web güvenlik açıklarını sağlar. Http (s) ön uçları için etkin, ön kapıların uygulama katmanı güvenliği küresel olarak dağıtılır ve her zaman açıktır, bu da arka uçınızdan uzakta olan Azure 'un ağ ucunda kötü amaçlı saldırıları durduruyor. Ön kapı, ek güvenlik ve performans iyileştirmesi sayesinde son kullanıcılarınıza hızlı ve güvenli Web deneyimleri sunar.

## <a name="application-protection"></a>Uygulama koruması
Ön kapı uygulama koruması, dünyanın her tarafında, uygulamalar ile birlikte her bir kenar ortamında yapılandırılır ve http olmayan trafiğin Web uygulamalarınıza ulaşmasını otomatik olarak engeller. Çok kiracılı dağıtılmış mimarimiz, performansı etkilemeden genel koruma kullanılmasına izin vermez. Http (s) iş yükleri için, ön kapıdaki Web uygulaması koruma hizmeti özel kurallar için zengin bir kural altyapısı, yaygın saldırılara karşı önceden yapılandırılmış RuleSet ve bir kuralla eşleşen tüm istekler için ayrıntılı günlük kaydı sağlar. Yalnızca izin verme, engelleme veya günlüğe kaydetme dahil esnek eylemler desteklenir.

## <a name="custom-access-control-rules"></a>Özel erişim denetimi kuralları
- **IP izin verilenler listesi ve Engellenenler listesi:** İstemci IP adresleri listesine göre Web uygulamalarınıza erişimi denetlemek için özel kurallar yapılandırabilirsiniz. IP v4 ve IP V6 desteklenir
- **Coğrafi tabanlı erişim denetimi:** İstemci IP 'sinden alınan ülke koduna göre Web uygulamalarınıza erişimi denetlemek için özel kurallar yapılandırabilirsiniz
- **Http parametreleri filtreleme:** Üst bilgiler, URL ve sorgu dizeleri dahil olmak üzere eşleşen http (s) istek parametrelerine göre özel erişim kuralları yapılandırabilirsiniz

## <a name="azure-managed-rules"></a>Azure tarafından yönetilen kurallar
- Yaygın en üst OWASP güvenlik açıklarına karşı önceden yapılandırılmış bir kurallar kümesi varsayılan olarak etkinleştirilmiştir. Önizleme aşamasında, kurallar kümesi SQLi ve XSS istekleri denetimini içerir. Ek kurallar eklenecek. Önceden yapılandırılmış kuralların uygulamalarınız için beklendiği gibi çalıştığını doğrulamak için yalnızca günlük eylemi ile başlatmayı seçebilirsiniz 

## <a name="rate-limiting"></a>Hız sınırlaması
- Bir hız denetim kuralı, anormal yüksek trafiği herhangi bir istemci IP 'sinden sınırlayabilmektir.  Bir dakikalık süre boyunca istemci IP 'si tarafından izin verilen Web istekleri sayısında bir eşik ayarlayabilirsiniz.

## <a name="centralized-protection-policy"></a>Merkezi koruma ilkesi
- Birkaç koruma kuralı tanımlayabilir ve bunları öncelik sırasıyla bir Ilkeye ekleyebilirsiniz. Özel kurallar, özel durumlara izin vermek için yönetilen RuleSet 'ten daha yüksek önceliğe sahiptir. Web uygulamanızla tek bir ilke ilişkilendirilir.  Aynı Web uygulaması koruma ilkesi tüm konumlarda tüm kenar sunucularına çoğaltılırsa, tüm bölgelerde tutarlı güvenlik ilkesi olduğundan emin olun

## <a name="configuration"></a>Yapılandırma
- Önizleme süresince, ön kapıda uygulama koruma kuralları ve ilkeleri oluşturup dağıtmak için REST API 'Leri, PowerShell veya CLı kullanabilirsiniz. Hizmet genel kullanıma açılmadan önce Portal erişimi desteklenecektir. 


## <a name="monitoring"></a>İzleme
Ön kapı, uyarıları izlemek ve eğilimleri kolayca izlemek için Azure Izleyici ile tümleştirilmiş gerçek zamanlı ölçümleri kullanarak Web uygulamalarını izleme olanağı sağlar.

## <a name="pricing"></a>Fiyatlandırma
Ön kapısının uygulama katmanı güvenliği önizleme sırasında ücretsizdir.


## <a name="next-steps"></a>Sonraki adımlar

- [Front Door oluşturmayı](quickstart-create-front-door.md) öğrenin.
- [Front Door’un nasıl çalıştığını](front-door-routing-architecture.md) öğrenin.
