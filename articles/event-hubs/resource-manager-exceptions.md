---
title: Azure Event Hubs Kaynak Yöneticisi özel durumlar | Microsoft Docs
description: Azure Resource Manager ve önerilen eylemler tarafından ortaya çıkacak Azure Event Hubs özel durumlarının listesi.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: cec24696d0d49ba408860f6562c34dd14876c311
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91334217"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Azure Event Hubs Kaynak Yöneticisi özel durumlar
Bu makalede, Azure Resource Manager aracılığıyla şablonlar veya doğrudan çağrılar kullanılarak Azure Event Hubs ile etkileşim kurarken oluşturulan özel durumlar listelenmektedir.

> [!IMPORTANT]
> Bu belge sıkça güncelleştirilir. Lütfen güncelleştirmeler için yeniden denetleyin.

Aşağıdaki bölümlerde Azure Resource Manager aracılığıyla ortaya çıkacak çeşitli özel durumlar/hatalar sağlanmaktadır.

## <a name="error-code-conflict"></a>Hata kodu: çakışma

| Hata kodu | Hata alt kodu | Hata iletisi | Description | Öneri |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Çakışma | 40300 | EventHub türünde en fazla kaynak sayısına ulaşıldı veya bu sınıra ulaşıldı. Gerçek: #, izin verilen en fazla: # | Ad alanı, içerebileceği Event Hubs sayısı için [kotasına](event-hubs-quotas.md) ulaştı. | Kullanılmayan veya gereksiz Olay Hub 'larını ad alanından silin veya [adanmış bir kümeye](event-hubs-dedicated-overview.md)yükseltmeyi göz önünde bulundurun. |
| Çakışma | yok | Çoğaltma devam ettiğinden olağanüstü durum kurtarma (DR) yapılandırması silinemiyor. DR yapılandırmasını silmeye çalışmadan önce yük devretmek veya eşleşmeyi kesin. | [Geodr çoğaltması](event-hubs-geo-dr.md) devam ediyor, bu nedenle yapılandırma Şu anda silinemiyor. | Yapılandırma silme engelini kaldırmak için, çoğaltma tamamlanana kadar bekleyin, yük devretme tetikleyin veya GeoDR eşleştirmesini kesin. |
| Çakışma | yok | Ad alanı güncelleştirmesi arka uçta çakışmayla başarısız oldu. | Bu ad alanı üzerinde şu anda başka bir işlem gerçekleştirilemiyor. | Geçerli işlem tamamlanana kadar bekleyip yeniden deneyin. |

## <a name="error-code-429"></a>Hata kodu: 429

| Hata kodu | Hata alt kodu | Hata iletisi | Description | Öneri |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | yok | Geçişte ad alanı sağlama | Bu ad alanı üzerinde şu anda başka bir işlem gerçekleştirilemiyor. | Geçerli işlem tamamlanana kadar bekleyip yeniden deneyin. |
| 429 | yok | Olağanüstü durum kurtarma işlemi devam ediyor. | Bu ad alanı veya eşleştirme üzerinde bir [Geodr](event-hubs-geo-dr.md) işlemi şu anda gerçekleştirilemiyor. | Geçerli GeoDR işlemi tamamlanana kadar bekleyin ve sonra yeniden deneyin. |

## <a name="error-code-badrequest"></a>Hata kodu: Rozrequest

| Hata kodu | Hata alt kodu | Hata iletisi | Description | Öneri |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Işlemindeki hatalı istek | 40000 | Bir olay hub 'ı için PartitionCount değiştirilemez. | Azure Event Hubs temel veya standart katmanı bölümlerinin değiştirilmesini desteklemez. | Temel veya Standart katman ad alanında istenen sayıda bölüm içeren yeni bir olay hub 'ı oluşturun. Bölüm ölçeği genişletme [adanmış kümeler](event-hubs-dedicated-overview.md)için desteklenir. |
| Işlemindeki hatalı istek | 40000 | Messageretentionındays için ' # ' değeri temel katman için geçerli değil. değer ' 1 ' günü aşamaz. | Temel katman Event Hubs ad alanları yalnızca 1 güne kadar olan ileti bekletmesini destekler. | İleti bekletmenin birden fazla günü istenirse, [Standart bir Event Hubs ad alanı oluşturun](event-hubs-create.md). | 
| Işlemindeki hatalı istek | yok | Belirtilen ad kullanılamıyor. | Ad alanı adları benzersiz olmalı ve belirtilen ad zaten alınmış olmalıdır. | Mevcut ad alanının sahibiyseniz, belirtilen ada sahip değilseniz, veri kaybına neden olacak şekilde onu silebilirsiniz. Ardından, aynı adla yeniden deneyin. Ad alanı silmek için güvenli değilse (veya sahip değilseniz), başka bir ad alanı adı seçin. |
| Işlemindeki hatalı istek | yok | Belirtilen abonelik ad alanı kotasına ulaştı. | Aboneliğiniz, tutabildiği ad alanı sayısı [kotasına](event-hubs-quotas.md) ulaştı. | Bu abonelikte kullanılmayan ad alanlarını silmeyi, başka bir abonelik oluşturmayı veya [adanmış bir kümeye](event-hubs-dedicated-overview.md)yükseltmeyi düşünün. |
| Işlemindeki hatalı istek | yok | İkincil olan bir ad alanı güncelleştirilemez | [Geodr eşleştirmesinde](event-hubs-geo-dr.md)ikincil ad alanı olduğundan ad alanı güncelleştirilemiyor. | Uygunsa, bunun yerine bu eşleştirmeden birincil ad alanı üzerinde değişiklik yapın. Aksi takdirde, değişikliği yapmak için GeoDR eşleştirmesini kesin. |
| Işlemindeki hatalı istek | yok | Temel SKU 'da otomatik Şişir ayarlanamaz | Otomatik Şişir temel katmanda Event Hubs ad alanlarında etkinleştirilemez. | Bir ad alanında [otomatik olarak Şişir etkinleştirmek](event-hubs-auto-inflate.md) için, bunun Standart katman olduğundan emin olun. |
| Işlemindeki hatalı istek | yok | Ad alanını oluşturmak için yeterli kapasite yok. Event Hubs yöneticinize başvurun. | Seçilen bölge kapasitesinde ve daha fazla ad alanı oluşturulamıyor. | Ad alanınızı barındırmak için başka bir bölge seçin. |
| Işlemindeki hatalı istek | yok | ' Namespace Name ' ad alanı ' temel ' katmanını kullandığından, işlem ' ConsumerGroup ' varlık türünde gerçekleştirilemiyor.  | Temel katman Event Hubs ad alanları bir tüketici grubunun kotasına (Event-hub-quotas.md) sahiptir (varsayılan). Daha fazla tüketici grubu oluşturulması desteklenmez. | Varsayılan tüketici grubunu ($Default) kullanmaya devam edin veya daha fazla gerekliyse Standart katman Event Hubs ad alanı kullanmayı deneyin. | 
| Işlemindeki hatalı istek | yok | ' Namespace Name ' ad alanı yok. | Belirtilen ad alanı bulunamadı. | İki kez, ad alanı adının doğru olup olmadığını ve aboneliğinizde bulunduğunu denetleyin. Değilse, [bir Event Hubs ad alanı oluşturun](event-hubs-create.md). | 
| Işlemindeki hatalı istek | yok | Kaynağın Location özelliği, kendisini kapsayan ad alanıyla eşleşmiyor. | Belirli bir bölgede bir olay hub 'ı oluşturmak, ad alanının bölgesiyle eşleşmediğinden başarısız oldu. | Olay Hub 'ını ad alanıyla aynı bölgede oluşturmayı deneyin. | 

## <a name="error-code-internal-server-error"></a>Hata kodu: Iç sunucu hatası

| Hata kodu | Hata alt kodu | Hata iletisi | Description | Öneri |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| İç sunucu hatası | yok | İç sunucu hatası. | Event Hubs hizmetinde bir iç hata oluştu. | Başarısız olan işlemi yeniden deneyin. İşlem başarısız olmaya devam ederse desteğe başvurun. |