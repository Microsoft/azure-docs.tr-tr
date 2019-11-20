---
title: Azure CDN için standart kurallar altyapısındaki eylemler | Microsoft Docs
description: Azure Content Delivery Network için standart kurallar altyapısındaki (Azure CDN) eylemler için başvuru belgeleri.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 53280bc90f629d93ff8a045c80f34a73970b43f6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171625"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Azure CDN için standart kurallar altyapısındaki eylemler

Azure Content Delivery Network için [standart kurallar altyapısında](cdn-standard-rules-engine.md) (Azure CDN), bir kural bir veya daha fazla eşleşme koşulu ve bir eylemden oluşur. Bu makalede, Azure CDN için standart kurallar altyapısında kullanabileceğiniz eylemlerin ayrıntılı açıklamaları sağlanmaktadır.

Bir kuralın ikinci bölümü bir eylemdir. Bir eylem, eşleşme koşulunun veya eşleştirme koşulları kümesinin tanımladığı istek türüne uygulanan davranışı tanımlar.

## <a name="actions"></a>Eylemler

Aşağıdaki eylemler, Azure CDN için standart kurallar altyapısında kullanılmak üzere kullanılabilir. 

### <a name="cache-expiration"></a>Önbellek süre sonu

Kuralların eşleştiği talepler için uç noktanın yaşam süresi (TTL) değerinin üzerine yazmak için bu eylemi kullanın.

#### <a name="required-fields"></a>Gerekli alanlar

Önbellek davranışı |  Açıklama              
---------------|----------------
Atlama önbelleği | Bu seçenek belirlendiğinde ve kural eşleştiğinde, içerik önbelleğe alınmaz.
Manızı | Bu seçenek belirlendiğinde ve kural eşleştiğinde, kaynaktan döndürülen TTL değeri, eylemde belirtilen değerle üzerine yazılır.
Eksikse ayarla | Bu seçenek belirlendiğinde ve kural eşleştiğinde, kaynaktan bir TTL değeri döndürülmezse, kural TTL 'yi eylemde belirtilen değere ayarlar.

#### <a name="additional-fields"></a>Ek alanlar

Gün | Saat | Dakika | Saniye
-----|-------|---------|--------
Int | Int | Int | Int 

### <a name="cache-key-query-string"></a>Önbellek anahtarı sorgu dizesi

Sorgu dizelerine göre önbellek anahtarını değiştirmek için bu eylemi kullanın.

#### <a name="required-fields"></a>Gerekli alanlar

Davranış | Açıklama
---------|------------
içeriyor | Bu seçenek belirlendiğinde ve kural eşleştiğinde, parametrelerde belirtilen sorgu dizeleri önbellek anahtarı oluşturulduğunda dahil edilir. 
Her benzersiz URL'yi önbelleğe al | Bu seçenek belirlendiğinde ve kural eşleştiğinde, her benzersiz URL 'nin kendi önbellek anahtarı vardır. 
Exclude | Bu seçenek belirlendiğinde ve kural eşleştiğinde, parametrelerde belirtilen sorgu dizeleri önbellek anahtarı oluşturulduğunda dışlanır.
Sorgu dizelerini yoksay | Bu seçenek belirlendiğinde ve kural eşleştiğinde, önbellek anahtarı oluşturulduğunda sorgu dizeleri değerlendirilmez. 

### <a name="modify-request-header"></a>İstek üst bilgisini Değiştir

Bu eylemi, kaynağına gönderilen isteklerde bulunan üst bilgileri değiştirmek için kullanın.

#### <a name="required-fields"></a>Gerekli alanlar

Eylem | HTTP üst bilgi adı | Değer
-------|------------------|------
Ekle | Bu seçenek belirlendiğinde ve kural eşleştiğinde, **üstbilgi adı** bölümünde belirtilen üstbilgi belirtilen değere sahip isteğe eklenir. Üst bilgi zaten mevcutsa, değer mevcut değere eklenir. | Dize
Üzerine yaz | Bu seçenek belirlendiğinde ve kural eşleştiğinde, **üstbilgi adı** bölümünde belirtilen üstbilgi belirtilen değere sahip isteğe eklenir. Üst bilgi zaten mevcutsa, belirtilen değer varolan değerin üzerine yazar. | Dize
Sil | Bu seçenek belirlendiğinde, kural eşleşir ve kuralda belirtilen üst bilgi bulunur, üst bilgi istekten silinir. | Dize

### <a name="modify-response-header"></a>Yanıt üst bilgisini Değiştir

İstemcilerinize döndürülen yanıtlarda bulunan üstbilgileri değiştirmek için bu eylemi kullanın.

#### <a name="required-fields"></a>Gerekli alanlar

Eylem | HTTP üst bilgi adı | Değer
-------|------------------|------
Ekle | Bu seçenek belirlendiğinde ve kural eşleştiğinde, **üst bilgi adı** 'nda belirtilen üst bilgi yanıta belirtilen **değer**kullanılarak eklenir. Üst bilgi zaten mevcutsa, **değer** var olan değere eklenir. | Dize
Üzerine yaz | Bu seçenek belirlendiğinde ve kural eşleştiğinde, **üst bilgi adı** 'nda belirtilen üst bilgi yanıta belirtilen **değer**kullanılarak eklenir. Üst bilgi zaten mevcutsa, **değer** varolan değerin üzerine yazar. | Dize
Sil | Bu seçenek belirlendiğinde, kural eşleşir ve kuralda belirtilen üst bilgi bulunur, üst bilgi yanıttan silinir. | Dize

### <a name="url-redirect"></a>URL yeniden yönlendirme

İstemcileri yeni bir URL 'ye yönlendirmek için bu eylemi kullanın. 

#### <a name="required-fields"></a>Gerekli alanlar

Alan | Açıklama 
------|------------
Tür | İstek sahibine döndürülecek yanıt türünü seçin: bulunan (302), taşınan (301), geçici yeniden yönlendirme (307) ve kalıcı yeniden yönlendirme (308).
Protokol | Match Isteği, HTTP, HTTPS.
Ana Bilgisayar Adı | İsteğin yeniden yönlendirilmesini istediğiniz ana bilgisayar adını seçin. Gelen ana bilgisayarı korumak için boş bırakın.
Yol | Yeniden Yönlendirmede kullanılacak yolu tanımlayın. Gelen yolu korumak için boş bırakın.  
Sorgu dizesi | Yeniden Yönlendirmede kullanılan sorgu dizesini tanımlayın. Gelen sorgu dizesini korumak için boş bırakın. 
Parça | Yeniden Yönlendirmede kullanılacak parçayı tanımlayın. Gelen parçayı korumak için boş bırakın. 

Mutlak bir URL kullanmanızı kesinlikle öneririz. Göreli bir URL kullanmak Azure CDN URL 'Leri geçersiz bir yola yönlendirebilir. 

### <a name="url-rewrite"></a>URL yeniden yazma

Bu eylemi, kaynağına yönlendiren bir isteğin yolunu yeniden yazmak için kullanın.

#### <a name="required-fields"></a>Gerekli alanlar

Alan | Açıklama 
------|------------
Kaynak stili | Değiştirilecek URL yolundaki kaynak modelini tanımlayın. Şu anda, kaynak stili önek tabanlı eşleşme kullanıyor. Tüm URL yollarını eşleştirmek için, kaynak model değeri olarak bir eğik çizgi ( **/** ) kullanın.
Hedef | Yeniden yazma sırasında kullanılacak hedef yolu tanımlayın. Hedef yol, kaynak deseninin üzerine yazar.
Eşleşmeyen yolu koru | **Evet**olarak ayarlanırsa, kaynak örüntüden sonraki kalan yol yeni hedef yoluna eklenir. 

## <a name="next-steps"></a>Sonraki adımlar

- [Azure CDN genel bakış](cdn-overview.md)
- [Standart kurallar altyapısı başvurusu](cdn-standard-rules-engine-reference.md)
- [Standart kurallar altyapısındaki koşulları Eşleştir](cdn-standard-rules-engine-match-conditions.md)
- [Standart kurallar altyapısını kullanarak HTTPS 'yi zorla](cdn-standard-rules-engine.md)
