---
title: Azure ön kapı mimarisi ve terminolojisi için kural altyapısı
description: Bu makalede, Azure ön kapısı kuralları altyapı özelliğine bir genel bakış sunulmaktadır.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/29/2020
ms.author: duau
ms.openlocfilehash: 110d0a347020e2bf2c1e957ed6dab65ffe13e2f0
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056520"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>Azure ön kapısının kural Altyapısı nedir? 

Rules Engine, HTTP isteklerinin kenarda nasıl işlendiğini özelleştirmenizi ve Web uygulamanıza daha denetimli bir davranış sağlar. Azure ön kapısının kural altyapısı, aşağıdakiler dahil olmak üzere birkaç temel özelliğe sahiptir:

* Tüm son kullanıcılarınızın güvenli bir bağlantı üzerinden içeriklerle etkileşime geçmesini sağlamak için HTTPS 'yi zorlar.
* , Çıkış noktaları arası kaynak paylaşımı (CORS) senaryolarında HTTP Strict-Transport-Security (HSTS), X-XSS-koruma, Content-Security-Policy, X-Frame-Options ve Access-Control-Allow-Origin üstbilgileri gibi tarayıcı tabanlı güvenlik açıklarını önlemeye yönelik güvenlik üst bilgilerini uygular. Güvenlik tabanlı öznitelikler, tanımlama bilgileriyle de tanımlanabilir.
* İstek üst bilgileri içerikleri, tanımlama bilgileri veya sorgu dizeleri desenlerine bağlı olarak, istekleri uygulamanızın mobil veya masaüstü sürümlerine yönlendirin.
* Yeni konak adları, yolları veya protokolleri yönlendirmek üzere istemciye 301, 302, 307 ve 308 yeniden yönlendirme özelliklerini döndürmek için yeniden yönlendirme yeteneklerini kullanın.
- Yönlendirmenize ait önbelleğe alma yapılandırmasını gelen isteklere göre dinamik olarak değiştirin.
- İstek URL 'SI yolunu yeniden yazın ve isteği yapılandırılmış arka uç havuzunuzdaki uygun arka uca iletin.

## <a name="architecture"></a>Mimari 

Kural altyapısı, istekleri kenarda işler. Bir istek ön kapı uç noktanıza rastlayarak WAF önce yürütülür ve ardından ön uç/etki alanınız ile ilişkili kurallar altyapısı yapılandırması gelir. Bir kural Altyapısı yapılandırması yürütülürse, üst yönlendirme kuralının zaten bir eşleşme olduğu anlamına gelir. Her kuraldaki tüm eylemlerin yürütülmesi için, bir kural içindeki tüm eşleşme koşullarının karşılanması gerekir. Bir istek, kural altyapısı yapılandırmanızda herhangi bir koşula uymazsa, varsayılan yönlendirme kuralı yürütülür. 

Örneğin, aşağıdaki diyagramda bir kural altyapısı bir yanıt üst bilgisini ekleyecek şekilde yapılandırılır. Üst bilgi, eşleşme koşulu karşılanıyorsa önbellek denetiminin maksimum yaşını değiştirir. 

![Yanıt üst bilgisi eylemi](./media/front-door-rules-engine/rules-engine-architecture-3.png)

Başka bir örnekte, kural altyapısının, eşleşme koşulu, cihaz türü true olduğunda bir kullanıcıyı sitenin mobil sürümüne gönderecek şekilde yapılandırıldığını görüyoruz. 

![Yönlendirme yapılandırması geçersiz kılma](./media/front-door-rules-engine/rules-engine-architecture-1.png)

Bu örneklerin her ikisinde de eşleşme koşullarından hiçbiri karşılanmazsa, belirtilen yol kuralı yürütülür. 

## <a name="terminology"></a>Terminoloji 

AFD kural altyapısı ile, her biri bir kural kümesinden oluşan kural motoru yapılandırmalarının bir bileşimini oluşturabilirsiniz. Aşağıda, kural motorunuz yapılandırılırken daha fazla yararlı terminoloji özetlenmektedir. 

- *Kural Altyapısı yapılandırması*: tek yol kuralına uygulanan bir kurallar kümesi. Her yapılandırma 25 kuralla sınırlıdır. En fazla 10 yapılandırma oluşturabilirsiniz. 
- *Kural altyapısı kuralı*: en fazla 10 eşleşme koşulu ve 5 eylemden oluşan bir kural.
- *Koşulu Eşleştir*: gelen isteklerinizi ayrıştırmak için kullanılabilecek çok sayıda eşleşme koşulu vardır. Bir kural, en fazla 10 eşleşme koşulu içerebilir. Eşleşme koşulları bir **ve** işleciyle değerlendirilir. Eşleşen koşulların tam bir listesi [burada](front-door-rules-engine-match-conditions.md)bulunabilir. 
- *Eylem*: Eylemler, gelen isteklerinize ne olduğunu belirler-istek/yanıt üst bilgisi eylemleri, iletme, yeniden yönlendirmeler ve yeniden gönderme işlemleri bugün kullanılabilir. Bir kural, en fazla beş eylem içerebilir; Ancak, bir kuralda yalnızca bir yol yapılandırması geçersiz kılma bulunabilir.  Eylemlerin tam listesini [burada](front-door-rules-engine-actions.md)bulabilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

- İlk [kural altyapısı yapılandırmanızı](front-door-tutorial-rules-engine.md)nasıl yapılandıracağınızı öğrenin. 
- [Front Door oluşturmayı](quickstart-create-front-door.md) öğrenin.
- [Front Door’un nasıl çalıştığını](front-door-routing-architecture.md) öğrenin.
