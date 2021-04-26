---
title: Veri akışınızı ölçüm Danışmanı 'na ekleme
titleSuffix: Azure Cognitive Services
description: Veri akışlarınızı ölçüm danışmanına ekleme ile çalışmaya başlama.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: fe3b87c733f54d8bd52c4d973977e3c8cbfefe19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "92043230"
---
# <a name="how-to-onboard-your-metric-data-to-metrics-advisor"></a>Nasıl yapılır: ölçüm verileri ölçüm Danışmanı 'na ekleme

Verilerinizi ölçüm danışmanına ekleme hakkında bilgi edinmek için bu makaleyi kullanın. 

## <a name="data-schema-requirements-and-configuration"></a>Veri şeması gereksinimleri ve yapılandırması

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

## <a name="avoid-loading-partial-data"></a>Kısmi veri yüklemekten kaçının

Kısmi veriler, ölçüm danışmanı ve veri kaynağında depolanan veriler arasındaki tutarsızlıklara neden olur. Bu durum, ölçüm Danışmanı veri çekmesini tamamladıktan sonra veri kaynağı güncelleştirildiği zaman gerçekleşebilir. Ölçüm Danışmanı yalnızca belirli bir veri kaynağından verileri bir kez çeker.

Örneğin, bir ölçüm, izleme için ölçüm Danışmanı 'na eklendi. Ölçüm Danışmanı, ölçüm verilerini zaman damgasına başarıyla yönlendirmektedir ve üzerinde anomali algılama gerçekleştirir. Ancak, bu belirli zaman damgasına ait ölçüm verileri veri alındıktan sonra yenilenmişse. Yeni veri değeri alınmayacak.

Tutarsızlıkları azaltmak için geçmiş verileri (daha sonra açıklanmıştır) yeniden [doldurmaya](manage-data-feeds.md#backfill-your-data-feed) çalışırsanız, ancak bu zaman noktaları için Uyarılar zaten tetikleniyorsa bu yeni anomali uyarıları tetiklemez. Bu işlem sisteme ek iş yükü ekleyebilir ve otomatik değildir.

Kısmi verilerin yüklenmesini önlemek için iki yaklaşım önerilir:

* Tek bir işlemde veri oluşturma:

    Aynı zaman damgasında bulunan tüm boyut birleşimlerinin ölçüm değerlerinin, veri kaynağında tek bir işlemde depolandığından emin olun. Yukarıdaki örnekte, tüm veri kaynaklarından gelen veriler hazırlanana kadar bekleyin ve ardından tek bir işlemde ölçüm Danışmanı 'na yükleyin. Ölçüm Danışmanı, veriler başarılı bir şekilde (veya kısmen) alınana kadar veri akışını düzenli olarak yoklayabilirler.

* Alma **zamanı fark** parametresi için uygun bir değer ayarlayarak veri alımı geciktin:

    Veri akışınız için alma **zaman aralığı** parametresini, veriler tam olarak hazırlanana kadar ertelemeyi geciktirmek için ayarlayın. Bu, Azure Tablo depolaması gibi işlemleri desteklemeyen bazı veri kaynakları için yararlı olabilir. Ayrıntılar için [Gelişmiş ayarlar](manage-data-feeds.md#advanced-settings) ' a bakın.

## <a name="add-a-data-feed-using-the-web-based-workspace"></a>Web tabanlı çalışma alanını kullanarak veri akışı ekleme

Ölçüm Danışmanı portalınıza kaydolduktan ve çalışma alanınızı seçtikten sonra **Başlarken**' e tıklayın. Sonra, çalışma alanının ana sayfasında, sol menüden **veri akışı Ekle** ' ye tıklayın.

### <a name="add-connection-settings"></a>Bağlantı ayarları ekle

Daha sonra, zaman serisi veri kaynağınızı bağlamak için bir parametre kümesi girirsiniz. 
* **Kaynak türü**: zaman serisi verilerinizin depolandığı veri kaynağının türü.
* **Ayrıntı düzeyi**: zaman serisi verilerinizde ardışık veri noktaları arasındaki Aralık. Şu anda ölçüm Danışmanı şunları destekler: yıllık, aylık, haftalık, günlük, saatlik ve özel. Özelleştirme seçeneğinin desteklediği en düşük Aralık 60 saniyedir.
  * **Saniye**: *Granularityname* 'in *özelleştirmeye* ayarlandığı saniye sayısı.
* Verilerin alınması **(UTC)**: veri alımı için temel başlangıç zamanı. *Startoffsetınseconds* genellikle veri tutarlılığı sağlamak üzere bir konum eklemek için kullanılır.

Ardından, veri kaynağı için bağlantı bilgilerini ve verileri gerekli şemaya dönüştürmek için kullanılan özel sorguları belirtmeniz gerekir. Diğer alanlarla ilgili ayrıntılı bilgi edinmek ve farklı türlerdeki veri kaynaklarını bağlamak için bkz. [farklı veri kaynaklarından veri akışları ekleme](../data-feeds-from-different-sources.md).

[!INCLUDE [query requirements](../includes/query-requirements.md)]

### <a name="verify-and-get-schema"></a>Şemayı doğrula ve al

Bağlantı dizesi ve sorgu dizesi ayarlandıktan sonra, bağlantıyı doğrulamak için **Doğrula ve şemayı al** ' ı seçin ve veri kaynağınızdan veri şemanızı sağlamak için sorguyu çalıştırın. Normalde veri kaynağı bağlantınıza bağlı olarak birkaç saniye sürer. Bu adımda bir hata oluşursa şunları onaylayın:

* Bağlantı dizeniz ve sorgunuz doğru.
* Güvenlik Duvarı ayarları varsa, ölçüm Danışmanı örneğiniz veri kaynağına bağlanabilir.

### <a name="schema-configuration"></a>Şema yapılandırması

Veri şeması yüklendikten sonra uygun alanları seçin.

Bir veri noktasının zaman damgası atlanırsa, ölçüm Danışmanı veri noktasının yerine kullanıldığı zaman damgasını kullanır. Her veri akışı için zaman damgası olarak en fazla bir sütun belirtebilirsiniz. Bir sütunun zaman damgası olarak belirtimeyeceğini belirten bir ileti alırsanız sorgu veya veri kaynağınızı denetleyin ve sorgu sonucunda yalnızca önizleme verilerinde değil birden çok zaman damgası olup olmadığını kontrol edin. Veri alımı gerçekleştirirken, ölçüm Danışmanı, her seferinde yalnızca bir öbek (örneğin, bir gün, ayrıntı düzeyine göre bir saat) yalnızca bir zaman serisi verileri alabilir.

|Seçim  |Açıklama  |Notlar  |
|---------|---------|---------|
| **Görünen ad** | Özgün sütun adı yerine çalışma alanınızda görüntülenecek ad. | |
|**İlişkin**     | Bir veri noktasının zaman damgası. Atlanırsa, ölçüm Danışmanı veri noktasının yerine kullanıldığı zaman damgasını kullanır. Her veri akışı için zaman damgası olarak en fazla bir sütun belirtebilirsiniz.        | İsteğe bağlı. En fazla bir sütun ile belirtilmelidir. Bir **sütunu alırsanız zaman damgası hatası olarak belirtilemez** , yinelenen zaman damgaları için sorgunuzu veya veri kaynağınızı denetleyin.      |
|**Measure**     |  Veri akışındaki sayısal değerler. Her veri akışı için birden çok ölçü belirtebilirsiniz, ancak en az bir sütun ölçü olarak seçilmelidir.        | En az bir sütunla belirtilmelidir.        |
|**Boyut**     | Kategorik değerler. Farklı değerlerin bir birleşimi belirli bir tek boyutlu zaman serisini tanımlar, örneğin: ülke, dil, kiracı. Boyut olarak sıfır veya daha fazla sütun seçebilirsiniz. Note: dize olmayan bir sütunu boyut olarak seçerken dikkatli olun. | İsteğe bağlı.        |
|**Yoksay**     | Seçili sütunu yoksayın.        | İsteğe bağlı. Aşağıdaki metni inceleyin.       |

Sütunları yoksaymak istiyorsanız, sorgunuzu veya veri kaynağınızı bu sütunları hariç tutmak için güncelleştirmenizi öneririz. Sütunları **Yoksay** ' ı ve sonra da belirli sütunlarda **Yoksay** ' ı kullanarak sütunları yoksayabilirsiniz. Bir sütun bir boyut olmalıdır ve yanlışlıkla *yoksayıldı* olarak ayarlandıysa, ölçüm Danışmanı kısmi verileri geri alabilir. Örneğin, Sorgunuzdaki verilerin aşağıdaki gibi olduğunu varsayalım:

| Satır KIMLIĞI | Timestamp | Ülke | Dil | Gelir |
| --- | --- | --- | --- | --- |
| 1 | 2019/11/10 | Çin | ZH-CN | 10000 |
| 2 | 2019/11/10 | Çin | EN-US | 1000 |
| 3 | 2019/11/10 | ABD | ZH-CN | 12000 |
| 4 | 2019/11/11 | ABD | EN-US | 23000 |
| ... | ...| ... | ... | ... |

*Ülke* bir boyuttur ve *dil* *yoksayıldı* olarak ayarlandıysa, ilk ve ikinci satır aynı boyutlara sahip olur. Ölçüm Danışmanı, iki satırdaki bir değeri rastgele kullanacaktır. Ölçüm Danışmanı bu durumda satırları toplamayacak.

### <a name="automatic-roll-up-settings"></a>Otomatik toplama ayarları

> [!IMPORTANT]
> Kök neden analizini ve diğer tanılama yeteneklerini etkinleştirmek istiyorsanız **otomatik toplama ayarlarının** yapılandırılması gerekir. Etkinleştirildikten sonra otomatik toplama ayarları değiştirilemez.

Ölçüm Danışmanı, alma sırasında her boyutta otomatik olarak toplama işlemi gerçekleştirebilir (örneğin SUM, MAX, MIN), ardından kök örnek olay çözümlemede ve diğer tanılama özelliklerinde kullanılacak bir hiyerarşi oluşturur. 

Aşağıdaki senaryoları göz önünde bulundurun:

* *Verilerim için toplama analizini içermesi gerekmiyor.*

    Ölçüm Danışmanı toplaması 'nı kullanmanıza gerek yoktur.

* *Verilerim zaten toplandı ve boyut değeri şu şekilde gösteriliyor: NULL veya boş (varsayılan), yalnızca boş, diğerleri.*

    Bu seçenek, satırlar zaten toplandığından, ölçüm Danışmanı 'Nın verileri toplaması gerektiği anlamına gelir. Örneğin, *yalnızca null*' ı seçerseniz, aşağıdaki örnekteki ikinci veri satırı, tüm ülkelerin ve *en-US* dilinin bir toplamı olarak görülecektir. *ülke* için boş bir değer olan dördüncü veri satırı, tamamlanmamış verileri gösterebilen sıradan bir satır olarak görülecektir.
    
    | Ülke | Dil | Gelir |
    |---------|----------|--------|
    | Çin   | ZH-CN    | 10000  |
    | DEĞER  | EN-US    | 999999 |
    | ABD      | EN-US    | 12000  |
    |         | EN-US    | 5000   |

* *Sum/max/min/AVG/Count değerini hesaplayarak, verileri toplamak için ölçüm Danışmanı gerekir <some string>*

    Cosmos DB veya Azure Blob depolama gibi bazı veri kaynakları, *grupla* veya *küp* gibi belirli hesaplamaları desteklemez. Ölçüm Danışmanı, alma sırasında otomatik olarak bir veri küpü oluşturmak için toplama seçeneği sunar.
    Bu seçenek, seçtiğiniz algoritmayı kullanarak toplaması hesaplamak için ölçüm Danışmanı gerekir ve ölçüm Danışmanı 'nda toplaması temsil etmek için belirtilen dizeyi kullanırsınız. Bu, veri kaynağınızdaki verilerin hiçbirini değiştirmez.
    Örneğin, boyut (ülke, bölge) ile satış ölçümlerini temsil eden bir zaman serisi kümesi olduğunu varsayalım. Belirli bir zaman damgası için aşağıdaki gibi görünebilir:


    | Ülke       | Region           | Sales |
    |---------------|------------------|-------|
    | Kanada        | Alberta          | 100   |
    | Kanada        | British Columbia | 500   |
    | Birleşik Devletler | Montana          | 100   |


    *Sum* Ile otomatik toplamayı etkinleştirdikten sonra, ölçüm Danışmanı boyut birleşimlerini hesaplar ve veri alımı sırasında ölçümleri toplayın. Sonuç şu olabilir:

    | Ülke       | Region           | Sales |
    | ------------ | --------------- | ---- |
    | Kanada        | Alberta          | 100   |
    | NULL          | Alberta          | 100   |
    | Kanada        | British Columbia | 500   |
    | NULL          | British Columbia | 500   |
    | Birleşik Devletler | Montana          | 100   |
    | NULL          | Montana          | 100   |
    | NULL          | NULL             | 700   |
    | Kanada        | NULL             | 600   |
    | Birleşik Devletler | NULL             | 100   |

    `(Country=Canada, Region=NULL, Sales=600)` Kanada 'daki (tüm bölgelerde) satış toplamı 600 ' dir.

    SQL dilinde dönüştürme aşağıda verilmiştir.

    ```mssql
    SELECT
        dimension_1,
        dimension_2,
        ...
        dimension_n,
        sum (metrics_1) AS metrics_1,
        sum (metrics_2) AS metrics_2,
        ...
        sum (metrics_n) AS metrics_n
    FROM
        each_timestamp_data
    GROUP BY
        CUBE (dimension_1, dimension_2, ..., dimension_n);
    ```

    Otomatik toplama özelliğini kullanmadan önce aşağıdakileri göz önünde bulundurun:

    * Verilerinizi toplamak için *Sum* kullanmak istiyorsanız, ölçümlerinizin her boyutta eklenebilir olduğundan emin olun. *Toplamasız* ölçümlere ilişkin bazı örnekler aşağıda verilmiştir:
      * Kesir tabanlı ölçümler. Bu, oran, yüzde vb. içerir. Örneğin, tüm ülkenin istihdam oranını hesaplamak için her bir durumun işe alınma oranını eklememelisiniz.
      * Boyutta örtüşme. Örneğin, spor gibi kişilerin sayısını hesaplamak için her bir spor 'a kişi sayısını eklememelisiniz, çünkü aralarında bir çakışma olduğundan, bir kişi birden çok spor gibi olabilir.
    * Tüm sistemin sistem durumunu sağlamak için küp boyutu sınırlı olur. Şu anda sınır 1.000.000 ' dir. Verileriniz bu sınırı aşarsa, bu zaman damgası için alma başarısız olur.

## <a name="advanced-settings"></a>Gelişmiş ayarlar

Verileri, özel bir şekilde (örneğin, alma sapmasını veya eşzamanlılık belirtmek gibi) etkinleştiren çeşitli gelişmiş ayarlar vardır. Daha fazla bilgi için, veri akışı yönetimi makalesindeki [Gelişmiş ayarlar](manage-data-feeds.md#advanced-settings) bölümüne bakın.

## <a name="specify-a-name-for-the-data-feed-and-check-the-ingestion-progress"></a>Veri akışı için bir ad belirtin ve alma ilerlemesini denetleyin
 
Çalışma alanınızda görüntülenecek olan veri akışı için özel bir ad verin. Sonra **Gönder**' e tıklayın. Veri akışı ayrıntıları sayfasında, durum bilgilerini görüntülemek için alma ilerleme çubuğunu kullanabilirsiniz.

:::image type="content" source="../media/datafeeds/ingestion-progress.png" alt-text="Alma ilerleme çubuğu" lightbox="../media/datafeeds/ingestion-progress.png":::


Alma hatası ayrıntılarını denetlemek için: 

1. **Ayrıntıları göster**' e tıklayın.
2. **Durum** ' a tıkladıktan sonra **başarısız** veya **hata**' ı seçin.
3. Başarısız bir alımı üzerine gelin ve görüntülenen Ayrıntılar iletisini görüntüleyin.

:::image type="content" source="../media/datafeeds/check-failed-ingestion.png" alt-text="Başarısız alımı denetle":::

*Hatalı* durum, bu veri kaynağı için alma işleminin daha sonra yeniden deneneceği anlamına gelir.
Bir *hata* durumu, ölçüm Danışmanı 'nın veri kaynağı için yeniden denenmeyeceğini gösterir. Verileri yeniden yüklemek için el ile doldurma/yeniden yükleme tetiklemeniz gerekir.

Ayrıca, **Ilerlemeyi Yenile**' ye tıklayarak alma işleminin ilerlemesini yeniden yükleyebilirsiniz. Veri alma işlemi tamamlandıktan sonra ölçümler ' e tıklayıp anomali algılama sonuçlarını kontrol edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
- [Veri akışlarınızı yönetme](manage-data-feeds.md)
- [Farklı veri kaynakları için yapılandırma](../data-feeds-from-different-sources.md)
- [Ölçümleri yapılandırma ve ince ayar algılama yapılandırması](configure-metrics.md)
