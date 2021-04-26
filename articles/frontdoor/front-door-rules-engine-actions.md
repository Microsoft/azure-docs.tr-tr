---
title: Azure ön kapı kuralları altyapı eylemleri
description: Bu makalede, Azure ön kapısı kuralları altyapısında yapabileceğiniz çeşitli eylemlerin bir listesi sunulmaktadır.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: 7b5358ec6bf0f41c860501c70ad175d7cf4bfe97
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062854"
---
# <a name="azure-front-door-rules-engine-actions"></a>Azure Front Door Kural Altyapısı Eylemleri

[AFD kural altyapısında](front-door-rules-engine.md), bir kural sıfır veya daha fazla eşleşme koşulu ve eylemden oluşur. Bu makale, AFD kural altyapısında kullanabileceğiniz eylemlerin ayrıntılı açıklamalarını sağlar.

Bir eylem, eşleşme koşulunun veya eşleştirme koşulları kümesinin tanımladığı istek türüne uygulanan davranışı tanımlar. AFD kuralları altyapısında, bir kural en fazla beş eylem içerebilir. Yalnızca biri yol yapılandırması geçersiz kılma eylemi olabilir (ileri veya yeniden yönlendirme).

Aşağıdaki eylemler Azure ön kapı kuralları altyapısında kullanılabilir.  

## <a name="modify-request-header"></a>İstek üst bilgisini Değiştir

Bu eylemi, kaynağına gönderilen isteklerde bulunan üst bilgileri değiştirmek için kullanın.

### <a name="required-fields"></a>Gerekli alanlar

Eylem | HTTP üst bilgi adı | Değer
-------|------------------|------
Ekle | Bu seçenek belirlendiğinde ve kural eşleştiğinde, **üst bilgi adı** 'nda belirtilen üstbilgi belirtilen değere sahip isteğe eklenir. Üst bilgi zaten mevcutsa, değer mevcut değere eklenir. | Dize
Üzerine yaz | Bu seçenek belirlendiğinde ve kural eşleştiğinde, **üstbilgi adı** bölümünde belirtilen üstbilgi belirtilen değere sahip isteğe eklenir. Üst bilgi zaten mevcutsa, belirtilen değer varolan değerin üzerine yazar. | Dize
Sil | Bu seçenek eşleşen kurallarla seçildiğinde ve kuralda belirtilen üst bilgi varsa, üst bilgi istekten silinir. | Dize

## <a name="modify-response-header"></a>Yanıt üst bilgisini Değiştir

İstemcilerinize döndürülen yanıtlarda bulunan üstbilgileri değiştirmek için bu eylemi kullanın.

### <a name="required-fields"></a>Gerekli alanlar

Eylem | HTTP üst bilgi adı | Değer
-------|------------------|------
Ekle | Bu seçenek belirlendiğinde ve kural eşleştiğinde, **üst bilgi adı** 'nda belirtilen üst bilgi yanıta belirtilen **değer** kullanılarak eklenir. Üst bilgi zaten mevcutsa, **değer** var olan değere eklenir. | Dize
Üzerine yaz | Bu seçenek belirlendiğinde ve kural eşleştiğinde, **üst bilgi adı** 'nda belirtilen üst bilgi yanıta belirtilen **değer** kullanılarak eklenir. Üst bilgi zaten mevcutsa, **değer** varolan değerin üzerine yazar. | Dize
Sil | Bu seçenek belirlendiğinde ve kural kuralda belirtilen üstbilgiyle eşleşiyorsa, üst bilgi yanıttan silinir. | Dize

## <a name="route-configuration-overrides"></a>Yol yapılandırması geçersiz kılmaları 

### <a name="route-type-redirect"></a>Yol türü: yeniden yönlendirme

İstemcileri yeni bir URL 'ye yönlendirmek için bu eylemi kullanın. 

#### <a name="required-fields"></a>Gerekli alanlar

Alan | Açıklama 
------|------------
Yeniden yönlendirme türü | İstek sahibine döndürülecek yanıt türünü seçin: bulunan (302), taşınan (301), geçici yeniden yönlendirme (307) ve kalıcı yeniden yönlendirme (308).
Yeniden yönlendirme protokolü | Match Isteği, HTTP, HTTPS.
Hedef konak | İsteğin yeniden yönlendirilmesini istediğiniz ana bilgisayar adını seçin. Gelen ana bilgisayarı korumak için boş bırakın.
Hedef yol | Yeniden Yönlendirmede kullanılacak yolu tanımlayın. Gelen yolu korumak için boş bırakın.  
Sorgu dizesi | Yeniden Yönlendirmede kullanılan sorgu dizesini tanımlayın. Gelen sorgu dizesini korumak için boş bırakın. 
Hedef parça | Yeniden Yönlendirmede kullanılacak parçayı tanımlayın. Gelen parçayı korumak için boş bırakın. 


### <a name="route-type-forward"></a>Yol türü: Ilet

İstemcileri yeni bir URL 'ye iletmek için bu eylemi kullanın. Bu eylem, URL yeniden yazılmasına ve önbelleğe almaya yönelik alt eylemleri de içerir. 

Alan | Açıklama 
------|------------
Arka uç havuzu | İstekleri geçersiz kılmak ve hizmeti sağlamak için arka uç havuzunu seçin. Bu, önceden yapılandırılmış olan tüm arka uç havuzlarınızı ön kapı profilinizde de gösterir. 
İletme Protokolü | Match Isteği, HTTP, HTTPS.
URL yeniden yazma | Bu eylemi, kaynağına yönlendiren bir isteğin yolunu yeniden yazmak için kullanın. Etkinleştirilirse, aşağıdaki ek alanlara bakın
Önbelleğe Alma | Etkin, devre dışı. Etkinleştirilirse, aşağıdaki ek alanlara bakın. 

#### <a name="url-rewrite"></a>URL yeniden yazma

Arka uca iletme isteği oluştururken kullanılacak isteğe bağlı bir **özel Iletme yolunu** yapılandırmak için bu ayarı kullanın.

Alan | Açıklama 
------|------------
Özel iletme yolu | İsteklerin iletileceği yolu tanımlayın. 

#### <a name="caching"></a>Önbelleğe Alma

Sorgu dizeleri içeren istekler için dosyaların nasıl önbelleğe alınacağını denetlemek için bu ayarları kullanın. İçeriğinizi tüm parametrelere veya seçili parametrelere göre önbelleğe almak isteyip istemediğinizi belirtir. İçeriğin önbellekte ne kadar süreyle kalacağını denetlemek için yaşam süresi (TTL) değerinin üzerine yazmak üzere ek ayarları kullanabilirsiniz. Bir eylem olarak önbelleğe almayı zorlamak için, önbelleğe alma alanını "etkin" olarak ayarlayın. Önbelleğe almayı zorlarsanız, aşağıdaki seçenekler görünür: 

Önbellek davranışı |  Description              
---------------|----------------
Sorgu dizelerini yoksay | Varlık önbelleğe alındıktan sonra, önbelleğe alınan varlık sona erene kadar tüm istekleri Sorgu dizelerini yoksayar.
Her benzersiz URL'yi önbelleğe al | Sorgu dizesi dahil olmak üzere benzersiz bir URL 'SI olan her istek kendi önbelleğine sahip benzersiz bir varlık olarak değerlendirilir.
Belirtilen Sorgu dizelerini yoksay | "Sorgu parametreleri" ayarında listelenen istek URL sorgusu dizeleri önbelleğe alma için yok sayılır.
Belirtilen Sorgu dizelerini dahil et | "Sorgu parametreleri" ayarında listelenen istek URL sorgusu dizeleri önbelleğe alma için kullanılır.

Ek alanlar |  Description 
------------------|---------------
Dinamik sıkıştırma | Ön kapı, kenardaki içeriği dinamik olarak sıkıştırarak daha küçük ve daha hızlı bir yanıt elde edebilir.
Sorgu parametreleri | Önbelleğe alma için temel olarak kullanılacak izin verilen (veya izin verilmeyen) parametrelerin virgülle ayrılmış listesi.
Önbellek süresi | Gün, saat, dakika, saniye cinsinden önbellek süre sonu süresi. Tüm değerler tamsayı olmalıdır. 

## <a name="next-steps"></a>Sonraki adımlar

- İlk [kural altyapılarınızı](front-door-tutorial-rules-engine.md)yapılandırmayı öğrenin. 
- [Kural altyapısı eşleştirme koşulları](front-door-rules-engine-match-conditions.md) hakkında daha fazla bilgi edinin
- [Azure ön kapı kuralları altyapısı](front-door-rules-engine.md) hakkında daha fazla bilgi edinin
