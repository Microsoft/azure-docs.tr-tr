---
title: Ölçüm Danışmanı 'nda veri akışlarını yönetme
titleSuffix: Azure Cognitive Services
description: Ölçüm Danışmanı 'na eklediğiniz veri akışlarını yönetmeyi öğrenin.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: mbullwin
ms.openlocfilehash: fb6eaf44967732d3a41ea92b0896540a40f694e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96184731"
---
# <a name="how-to-manage-your-data-feeds"></a>Nasıl yapılır: veri akışlarınızı yönetme

Ölçüm Danışmanı 'nda eklendi veri akışlarınızı yönetme hakkında bilgi edinin. Bu makale, ölçüm Danışmanı 'nda veri akışlarını yönetme konusunda size rehberlik eder.

## <a name="edit-a-data-feed"></a>Veri akışını düzenleme

> [!NOTE]
> Bir veri akışı oluşturulduktan sonra aşağıdaki Ayrıntılar değiştirilemez. 
> * Veri akışı KIMLIĞI
> * Oluşturma Zamanı
> * Boyut
> * Kaynak Türü
> * Ayrıntı düzeyi

Yalnızca bir veri akışı yöneticisinin üzerinde değişiklik yapmasına izin verilir. 

Bir veri akışını duraklatmak veya yeniden etkinleştirmek için:

1. Veri akışı listesi sayfasında, veri akışında gerçekleştirmek istediğiniz işleme tıklayın.

2. Veri akışı ayrıntıları sayfasında **durum** düğmesi düğmesine tıklayın.

Bir veri akışını silmek için: 

1. Veri akışı listesi sayfasında, veri akışında **Sil** ' e tıklayın.

2. Veri akışı ayrıntıları sayfasında **Sil**' e tıklayın.

Başlangıç saatini değiştirirken şemayı yeniden doğrulamanız gerekir. **Parametreleri Düzenle** öğesini kullanarak değiştirebilirsiniz.

##  <a name="backfill-your-data-feed"></a>Veri akışınızı geri doldurma

Başarısız bir alımı onarmak veya mevcut verileri geçersiz kılmak için zaman damgasında bir anında alma tetiklemesi için  **geri doldur** düğmesini seçin.
- Başlangıç saati dahil değildir.
- Bitiş saati özeldir.
- Anomali algılama yalnızca seçili aralıkta yeniden tetikleniyor.

:::image type="content" source="../media/datafeeds/backfill-datafeed.png" alt-text="Veri akışını geri doldurma":::

## <a name="manage-permission-of-a-data-feed"></a>Veri akışı iznini yönetme

Çalışma alanı erişimi, kimlik doğrulaması için Azure Active Directory kullanan ölçüm Danışmanı kaynağı tarafından denetlenir. Farklı bir izin denetimi katmanı ölçüm verilerine uygulanır.

Ölçüm Danışmanı farklı veri akışlarındaki farklı kişi gruplarına izin vermenizi sağlar. İki tür rol vardır: 

- Yönetici: değiştirme ve silme dahil olmak üzere bir veri akışını yönetmek için kimin tam izinleri vardır.
- Görüntüleyici: veri akışı salt okunurdur bir görünüme kimin erişimi vardır.
 

## <a name="advanced-settings"></a>Gelişmiş ayarlar

Yeni bir veri akışı oluştururken, veri akışı ayrıntısı sayfasında değiştirilebilen birkaç isteğe bağlı gelişmiş ayar vardır.

### <a name="ingestion-options"></a>Alma seçenekleri

* Alım **zaman aralığı**: varsayılan olarak, veriler belirtilen ayrıntı düzeyine göre yapılır. Örneğin, *günlük* zaman damgasına sahip bir ölçüm, zaman damgasından sonra bir gün sonra alınır. Boşluğu, *pozitif* bir sayıyla alma süresini geciktirmek veya *negatif* bir sayıyla ilerlemek için kullanabilirsiniz.

* **En fazla eşzamanlılık**: veri kaynağınız sınırlı eşzamanlılık destekliyorsa bu parametreyi ayarlayın. Aksi takdirde varsayılan ayarda bırakın.

* **Yeniden denemeyi durdur**: veri alımı başarısız olduysa, bir süre içinde otomatik olarak yeniden dener. Dönemin başlangıcı, ilk veri alımı gerçekleştiği zaman olur. Dönemin uzunluğu, ayrıntı düzeyine göre tanımlanır. Varsayılan değerin (-1) bırakılması durumunda değer, aşağıdaki şekilde ayrıntı düzeyine göre belirlenir.
    
    | Ayrıntı düzeyi       | Sonra yeniden denemeyi durdur           |
    | :------------ | :--------------- |
    | Günlük, özel (>= 1 gün), haftalık, aylık, yıllık     | 7 gün          |
    | Saatlik, özel (< 1 gün)       | 72 saat |

* **Min yeniden deneme aralığı**: kaynaktan veri çekme işlemi yeniden denendiğinde en düşük aralığı belirtebilirsiniz. Varsayılan değer (-1) ayrılırsanız, yeniden deneme aralığı aşağıdaki şekilde ayrıntı düzeyine göre belirlenir.
    
    | Ayrıntı düzeyi       | En az yeniden deneme aralığı           |
    | :------------ | :--------------- |
    | Günlük, özel (>= 1 gün), haftalık, aylık     | 30 dakika          |
    | Saatlik, özel (< 1 gün)      | 10 dakika |
    | Yıllık | 1 gün          |
 
### <a name="fill-gap-when-detecting"></a>Algılayarak boşluğu doldur: 

> [!NOTE]
> Bu ayar veri kaynağınızı etkilemez ve portalda görüntülenecek veri grafiklerini etkilemez. Otomatik doldurma yalnızca anomali algılama sırasında gerçekleşir.

Bazı zaman serileri sürekli değildir. Eksik veri noktaları olduğunda, ölçüm Danışmanı daha iyi doğruluk için, anomali algılama için belirtilen değeri kullanır.
Seçenekler şunlardır: 

* Önceki gerçek veri noktasındaki değeri kullanma. Bu varsayılan olarak kullanılır.
* Belirli bir değer kullanma.

### <a name="action-link-template"></a>Eylem bağlantısı şablonu: 

Eylem bağlantısı şablonları,,,, ve yer tutucularından oluşan, işlem yapılabilir http url 'lerini önceden tanımlamak için kullanılır `%datafeed` `%metric` `%timestamp` `%detect_config` `%tagset` . Ayrıntıya gitmek için bir anomali veya olaydan belirli bir URL 'ye yönlendirmek üzere şablonu kullanabilirsiniz.

:::image type="content" source="../media/action-link-template.png" alt-text="Eylem bağlantısı şablonu" lightbox="../media/action-link-template.png":::

Eylem bağlantısını doldurduktan sonra olay listesinin eylem seçeneğinde **eylem bağlantısına git ' e** tıklayın ve olay ağacının sağ tıklama menüsü ' ne tıklayın. Eylem bağlantı şablonundaki yer tutucuları, anomali veya olay için karşılık gelen değerlerle değiştirin.

| Yer tutucu | Örnekler | Açıklama |
| ---------- | -------- | ------- |
| `%datafeed` | - | Veri akışı KIMLIĞI |
| `%metric` | - | Ölçüm KIMLIĞI |
| `%detect_config` | - | Yapılandırma KIMLIĞINI Algıla |
| `%timestamp` | - | Kalıcı bir olayın anomali veya bitiş saatinin zaman damgası |
| `%tagset` | `%tagset`, <br> `[%tagset.get("Dim1")]`, <br> `[ %tagset.get("Dim1", "filterVal")]` | Bir olayın bir anomali veya en üst anomali boyut değerleri.   <br> , `filterVal` Köşeli ayraç içinde eşleşen değerleri filtrelemek için kullanılır.   |

Örnekler

* Eylem bağlantısı şablonu ise `https://action-link/metric/%metric?detectConfigId=%detect_config` ,
  * Eylem bağlantısı, `https://action-link/metric/1234?detectConfigId=2345` ölçüm kapsamındaki bozukluklar veya olaylara gider `1234` ve yapılandırmayı algılar `2345` .

* Eylem bağlantısı şablonu ise `https://action-link?[Dim1=%tagset.get('Dim1','')&][Dim2=%tagset.get('Dim2','')]` , 
    * İşlem bağlantısı `https://action-link?Dim1=Val1&Dim2=Val2` anomali olduğunda olacaktır `{ "Dim1": "Val1", "Dim2": "Val2" }` . 
    * `https://action-link?Dim2=Val2` `{ "Dim1": "", "Dim2": "Val2" } ` `[Dim1=***&]` Boyut değeri boş dize için atlandığından, işlem bağlantısı anomali ne zaman olur. 

* Eylem bağlantısı şablonu ise `https://action-link?filter=[Name/Dim1 eq '%tagset.get('Dim1','')' and ][Name/Dim2 eq '%tagset.get('Dim2','')']` , 
    * İşlem bağlantısı `https://action-link?filter=Name/Dim1 eq 'Val1' and Name/Dim2 eq 'Val2'` anomali olduğunda `{ "Dim1": "Val1", "Dim2": "Val2" }` , 
    * `https://action-link?filter=Name/Dim2 eq 'Val2'` `{ "Dim1": "", "Dim2": "Val2" }` Boyut değeri boş dize için, anomali ne zaman `[Name/Dim1 eq '***' and ]` atlandığından eylem bağlantısı olur. 
   
### <a name="data-feed-not-available-alert-settings"></a>"Veri akışı kullanılamıyor" uyarı ayarları

Veri akışı alma işlemi başladığında belirtilen yetkisiz kullanım süresi içinde kaynaktan veri alınmadığından, veri akışı kullanılamaz olarak kabul edilir. Bu durumda bir uyarı tetiklenir.

Bir uyarı yapılandırmak için önce [bir kanca oluşturmanız](alerts.md#create-a-hook) gerekir. Uyarılar, yapılandırılan kanca aracılığıyla gönderilir.

* **Yetkisiz kullanım süresi**: yetkisiz kullanım süresi ayarı, hiçbir veri noktası belirtilmemişse bir uyarının ne zaman gönderileceğini tespit etmek için kullanılır. Başvuru noktası ilk alma zamanı ' dır. Alma işlemi başarısız olursa, ölçüm Danışmanı ayrıntı düzeyi tarafından belirtilen düzenli bir aralıkta çalışmaya devam eder. Yetkisiz kullanım süresinin ötesinde başarısız olmaya devam ederse, bir uyarı gönderilir.

* **Otomatik erteleme**: Bu seçenek sıfıra ayarlandığında, *kullanılamayan* her zaman damgası bir uyarı tetikler. Sıfırdan farklı bir ayar belirtildiğinde, *mevcut olmayan* ilk zaman damgasından sonraki sürekli zaman damgaları belirtilen ayara göre tetiklenmez.

## <a name="next-steps"></a>Sonraki adımlar
- [Ölçümleri yapılandırma ve ince ayar algılama yapılandırması](configure-metrics.md)
- [Geri bildirimi kullanarak anomali algılamayı ayarlama](anomaly-feedback.md)
- [Bir olayı tanılayın](diagnose-incident.md).
