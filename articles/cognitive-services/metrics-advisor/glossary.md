---
title: Ölçüm Danışmanı sözlüğü
titleSuffix: Azure Cognitive Services
description: Ölçüm Danışmanı hizmeti için önemli fikirler ve kavramlar
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 6c461983053a145dfda58b9e3d26b39db0c339e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "92893429"
---
# <a name="metrics-advisor-glossary-of-common-vocabulary-and-concepts"></a>Ortak Sözlük ve kavramların ölçüm Danışmanı sözlüğü

Bu belgede, ölçüm Danışmanı 'nda kullanılan teknik terimler açıklanmaktadır. Hizmeti kullanırken karşılaşabileceğiniz yaygın kavramlar ve nesneler hakkında bilgi edinmek için bu makaleyi kullanın.

## <a name="data-feed"></a>Veri akışı

> [!NOTE]
> Birden çok ölçüm aynı veri kaynağını ve hatta aynı veri akışını paylaşabilir.

Veri akışı, Cosmos DB veya bir SQL Server gibi veri kaynağınızdan alınan ölçüm danışmanı. Bir veri akışı şu satırları içerir:
* zaman damgaları
* sıfır veya daha fazla boyut
* bir veya daha fazla ölçü. 

## <a name="metric"></a>Metric

Ölçüm, belirli bir iş sürecinin durumunu izlemek ve değerlendirmek için kullanılan, ölçülebilir bir ölçüdür. Bu, boyutlara bölünen birden çok zaman serisi değerinin bir birleşimi olabilir. Örneğin, bir *Web durumu* ölçümü *Kullanıcı sayısı* ve *en-US pazarında* boyut içerebilir.

## <a name="dimension"></a>Boyut

Bir boyut bir veya daha fazla kategorik değer. Bu değerlerin birleşimi, belirli bir ayırt eden zaman serisini belirler, örneğin: ülke, dil, kiracı vb.

## <a name="multi-dimensional-metric"></a>Çok boyutlu ölçüm

Çok boyutlu ölçüm nedir? İki örnek kullanalım. 

**Bir işletmenin gelirleri**

İşletmenizin gelirine ilişkin verileriniz olduğunu varsayalım. Zaman serisi verileriniz şuna benzer görünebilir:

| Timestamp | Kategori | Pazara | Gelir |
| ----------|----------|--------|----- |
| 2020-6-1 | Yemek | ABD | 1000 |
| 2020-6-1 | Giyim | ABD | 2000 |
| 2020-6-2 | Yemek | Birleşik Krallık | 800 | 
| ...      | ...  |... | ... |

Bu örnekte, *Kategori* ve *Pazar* boyutlardır. *Gelir* , farklı kategorilere ve/veya pazarlara dilimlenebilir ve ayrıca toplanabilecek ana performans GÖSTERGESIDIR (KPI). Örneğin, tüm pazarlar için *yiyecek* geliri.
 
**Karmaşık bir uygulama için hata sayıları**

Bir uygulamada günlüğe kaydedilen hataların sayısı için verileriniz olduğunu varsayalım. Zaman serisi verileriniz şuna benzer görünebilir:

| Timestamp | Uygulama bileşeni | Region | Hata sayısı |
| ----------|----------|--------|----- |
| 2020-6-1 | Çalışan veritabanı | BATı AB | 9000 |
| 2020-6-1 | İleti kuyruğu | DOĞU ABD | 1000 |
| 2020-6-2 | İleti kuyruğu | DOĞU ABD | 8000| 
| ...      | ...           | ...     |  ...|

Bu örnekte, *uygulama bileşeni* ve *bölgesi* boyutlardır. *Hata sayısı* , farklı kategorilere ve/veya pazarlara dilimlenebilir ve ayrıca TOPLANABILECEK olan KPI 'dir. Örneğin, tüm bölgelerde *ileti sırasının* hata sayısı.

## <a name="measure"></a>Measure

Ölçü, temel veya birime özgü bir terimdir ve ölçümünün ölçülebilir bir değeridir.

## <a name="time-series"></a>Time series (Zaman serisi)

Zaman serisi, kronolojik sırada dizine alınmış (veya listelenmiş veya grafiği oluşturulmuş) bir veri noktaları dizisidir. En yaygın olarak, zaman serisi birbirini izleyen ve zaman içinde eşit aralıklı olarak alınan bir dizidir. Bu, Ayrık zamanlı verilerin bir sırasıdır.

Ölçüm Danışmanı ' nda, belirli bir boyut birleşiminde bir ölçümün değerlerine bir seri denir.

## <a name="granularity"></a>Ayrıntı düzeyi

Ayrıntı düzeyi veri kaynağında veri noktalarının ne sıklıkta oluşturulacağını belirtir. Örneğin, günlük, saatlik.

## <a name="start-time"></a>Başlangıç saati

Başlangıç zamanı, ölçüm Danışmanı 'nın veri kaynağınızdan veri almaya başlamasını istediğiniz süredir. Veri kaynağınız belirtilen başlangıç saatinde verileri içermelidir.

## <a name="confidence-boundaries"></a>Güven sınırları

> [!NOTE]
> Güven sınırları, anormallikleri bulmak için kullanılan tek ölçüm değildir. Bu sınırın dışındaki veri noktalarının, algılama modeli tarafından normal olarak işaretlenmesi mümkündür. 

Ölçüm Danışmanı 'nda güvenirlik sınırları, kullanılan algoritmanın duyarlılığını temsil eder ve aşırı hassas anormallikleri filtrelemek için kullanılır. Web portalında, güvenirlik sınırları saydam mavi bant olarak görünür. Bantdaki tüm noktaları normal noktaları olarak kabul edilir.

Ölçüm Danışmanı, kullanılan algoritmaların duyarlılığını ayarlamak için araçlar sağlar. Bkz. [nasıl yapılır: ölçümleri yapılandırma ve daha fazla bilgi için yapılandırmayı algılama hakkında ince ayar](how-tos/configure-metrics.md) .

![Güven sınırları](media/confidence-bounds.png)


## <a name="hook"></a>Kancalı

Ölçüm Danışmanı gerçek zamanlı uyarılar oluşturmanızı ve bunlara abone olmanızı sağlar. Bu uyarılar Internet üzerinden [bir kanca kullanılarak](how-tos/alerts.md)gönderilir.

## <a name="anomaly-incident"></a>Anomali olay

Bir algılama yapılandırması ölçümlere uygulandıktan sonra, içindeki her bir serinin bir anomali olduğunda olaylar üretilir. Büyük veri kümelerinde bu durum aşırı olabilir, bu nedenle ölçüm danışmanı bir ölçüm içindeki anormal dizileri bir olaya gruplandırır. Ayrıca hizmet önem derecesini değerlendirir ve [olayı tanılamaya](how-tos/diagnose-incident.md)yönelik araçlar sağlar.

### <a name="incident-tree"></a>Olay ağacı

Ölçüm Danışmanı 'nda ölçümler üzerinde anomali algılama uygulayabilirsiniz, ardından ölçüm Danışmanı tüm boyut birleşimlerinin tüm zaman dizilerini otomatik olarak izler. Her türlü anomali algılandığında, ölçüm Danışmanı olayları olaylarla toplar.
Bir olay oluştuktan sonra, ölçüm Danışmanı, katkıda bulunan anomali hiyerarşisine sahip bir olay ağacı sağlar ve en büyük etkiye sahip olanları belirler. Her olay, ağacın üst düğümü olan bir kök neden anomali içerir.

### <a name="anomaly-grouping"></a>Anomali gruplama

Ölçüm Danışmanı, benzer desenlerle ilgili zaman serisini bulma yeteneği sağlar. Ayrıca, diğer boyutlardaki etkiyle ilgili daha ayrıntılı öngörüler sağlayabilir ve anormallikleri ilişkilendirebilir.

### <a name="time-series-comparison"></a>Zaman serisi karşılaştırması

Tek bir görselleştirmede eğilimleri karşılaştırmak için birden çok zaman serisi seçebilirsiniz. Bu, ilgili seriyi görüntülemek ve karşılaştırmak için açık ve öngörülü bir yol sağlar.

## <a name="detection-configuration"></a>Algılama yapılandırması

>[!Note]
>Algılama konfigürasyonları yalnızca tek bir ölçüm içinde uygulanır.

Ölçüm Danışmanı web portalında, bir ölçüm görüntülenirken sol bölmede bir algılama yapılandırması (duyarlılık, otomatik erteleme ve yön gibi) listelenir. Parametreler ayarlanabilir ve bu ölçüm içindeki tüm seriler için uygulanabilir.

Her zaman serisi için bir algılama yapılandırması gerekir ve zaman serisinde bir noktanın bir anomali olup olmadığını belirler. Ölçüm Danışmanı, verileri ilk kez eklediğinizde tüm ölçüm için varsayılan bir yapılandırma ayarlayacaktır. 

Ayrıca, bir dizi grubuna veya belirli bir gruba ayarlama parametreleri uygulayarak yapılandırmayı geliştirebilirsiniz. Bir zaman serisine yalnızca bir yapılandırma uygulanır:
* Belirli bir seriye uygulanan yapılandırmalara bir grubun yapılandırmalarının üzerine yazılacak
* Bir grubun yapılandırması, tüm ölçüme uygulanan yapılandırmaların üzerine yazar.

Ölçüm Danışmanı birçok [algılama yöntemi](how-tos/configure-metrics.md#anomaly-detection-methods)sağlar ve bunları mantıksal işleçler kullanarak birleştirebilirsiniz.

### <a name="smart-detection"></a>Akıllı algılama

Birden çok makine öğrenimi algoritması kullanarak anomali algılama.

**Duyarlılık**: anomali algılama toleransını ayarlamak için sayısal bir değer. Görsel olarak değer arttıkça, zaman serisi etrafında üst ve alt sınır daha dar olur.

### <a name="hard-threshold"></a>Sabit eşik

Üst veya alt sınırların dışındaki değerler düşüktür.

**Min**: alt sınır

**Maksimum**: üst sınır

### <a name="change-threshold"></a>Değişiklik eşiği

Bu noktanın bir anomali olup olmadığını anlamak için önceki nokta değerini kullanın.

**Değişiklik yüzdesi**: önceki noktaya kıyasla, değişikliğin yüzdesi bu parametreden daha büyükse geçerli nokta bir anomali olur.

**Noktaları üzerinde Değiştir**: kaç noktaya geri bakacağız.

### <a name="common-parameters"></a>Ortak Parametreler

**Yön**: bir nokta, yalnızca sapma yönü *yukarı*, *aşağı* veya *her ikisinde* meydana geldiğinde bir anomali olur.

**Şu kadar geçerli anomali değil**: bir veri noktası yalnızca belirtilen önceki noktaların yüzdesi de bozukluklar olduğunda bir anomali olur.

## <a name="alert-settings"></a>Uyarı ayarları

Uyarı ayarları, hangi anormalilerin bir uyarı tetikleyeceğini tespit etmelidir. Birden çok uyarıyı farklı ayarlarla ayarlayabilirsiniz. Örneğin, daha düşük iş etkisi ve daha fazla önem derecesine sahip olan bozukluklar için bir uyarı oluşturabilirsiniz.

Ayrıca, ölçümler arasında bir uyarı oluşturabilirsiniz. Örneğin, yalnızca iki belirtilen ölçüm bozukluklar varsa tetiklenen bir uyarı.

### <a name="alert-scope"></a>Uyarı kapsamı

Uyarı kapsamı, uyarının uygulandığı kapsamı ifade eder. Dört seçenek vardır:

**Tüm serilerin anormallikleri**: tüm seriler içindeki tüm serilerde bulunan uyarılar tetiklenecek.

**Seri grubundaki bozukluklar**: uyarılar yalnızca seri grubunun belirli boyutlarında olan bozukluklar için tetiklenecektir. Belirtilen boyutların sayısı, toplam sayı boyutlarından daha küçük olmalıdır.

**Sık kullanılan serideki bozukluklar**: uyarılar yalnızca sık kullanılanlar olarak eklenen bozukluklar için tetiklenecektir. Her algılama yapılandırması için sık kullanılan olarak bir seri grubu seçebilirsiniz.

**Tüm serilerin Ilk n ' de bozukluklar**: uyarılar yalnızca ilk n serideki bozukluklar için tetiklenecektir. Hesaba eklenecek zaman damgası sayısını ve uyarı göndermek için kaç anomali olması gerektiğini belirtmek için parametreleri ayarlayabilirsiniz.

### <a name="severity"></a>Önem derecesi

Önem derecesi, ölçüm Danışmanı 'nın, *yüksek*, *Orta* ve *düşük* dahil olmak üzere olayın önem derecesini anlatmak için kullandığı bir düzeydir.

Şu anda ölçüm Danışmanı, uyarı önem derecesini ölçmek için aşağıdaki faktörleri kullanır:
1. Değer oranı ve ölçümde anormal sayı oranı.
1. Anormalilerin güvenilirliği.
1. Sık kullanılan ayarlarınız önem derecesine de katkıda bulunur.

### <a name="auto-snooze"></a>Otomatik erteleme

Bazı bozukluklar özellikle küçük ayrıntı düzeyi ölçümler için geçici sorunlardır. Belirli sayıda zaman noktası için bir uyarıyı *uyku* olarak bırakabilirsiniz. Bu belirtilen sayıda noktaya ait anomali bulunursa, hiçbir uyarı tetiklenmeyecektir. Otomatik erteleme davranışı, ölçüm düzeyi veya seri düzeyinde ayarlanabilir.

Erteleme davranışı ölçüm düzeyi veya seri düzeyinde ayarlanabilir.

## <a name="data-feed-settings"></a>Veri akışı ayarları

### <a name="ingestion-time-offset"></a>Alım zaman aralığı

Varsayılan olarak, veriler ayrıntı düzeyine ( *günlük*) göre yapılır. Pozitif bir tam sayı kullanarak, verilerin içeri alımını belirtilen değere erteleyebilirsiniz. Negatif bir sayı kullanarak alımı belirtilen değere göre ilerletebilirsiniz.

### <a name="max-ingestion-per-minute"></a>Dakika başına en fazla alma

Veri kaynağınız sınırlı eşzamanlılık destekliyorsa bu parametreyi ayarlayın. Aksi takdirde varsayılan ayarları bırakın.

### <a name="stop-retry-after"></a>Sonra yeniden denemeyi durdur

Veri alımı başarısız olduysa, ölçüm danışmanı bir süre sonra otomatik olarak yeniden dener. Dönemin başlangıcı, ilk veri alımı oluşma zamanı olur. Yeniden deneme döneminin uzunluğu ayrıntı düzeyine göre tanımlanır. Varsayılan değeri ( `-1` ) kullanırsanız, yeniden deneme süresi ayrıntı düzeyine göre belirlenir:

| Ayrıntı düzeyi       | Sonra yeniden denemeyi durdur           |
| :------------ | :--------------- |
| Günlük, özel (>= 1 gün), haftalık, aylık, yıllık     | 7 gün          |
| Saatlik, özel (< 1 gün)       | 72 saat |

### <a name="min-retry-interval"></a>Min yeniden deneme aralığı

Kaynaktan veri çekme işlemi yeniden denendiğinde en düşük aralığı belirtebilirsiniz. Varsayılan değeri ( `-1` ) kullanırsanız, yeniden deneme aralığı ayrıntı düzeyine göre belirlenir:

| Ayrıntı düzeyi       | En az yeniden deneme aralığı           |
| :------------ | :--------------- |
| Günlük, özel (>= 1 gün), haftalık, aylık     | 30 dakika          |
| Saatlik, özel (< 1 gün)      | 10 dakika |
| Yıllık | 1 gün          |

### <a name="grace-period"></a>Yetkisiz kullanım süresi

> [!Note]
> Yetkisiz kullanım süresi, düzenli alma zamanında başlar ve belirtilen alma süresi kaydırmasına göre belirlenir.
    
Yetkisiz kullanım süresi, ölçüm Danışmanı 'Nın veri kaynağından veri getirmeye devam ettiği, ancak herhangi bir uyarı tetikleneceği zaman dilimi olur. Yetkisiz kullanım süresinden sonra veri belirtilmemişse, bir *veri akışı kullanılamaz* uyarısı tetiklenir.

### <a name="snooze-alerts-in"></a>Erteleme uyarıları

Bu seçenek sıfıra ayarlandığında, *kullanılamayan* her zaman damgası bir uyarı tetikleyecektir. Sıfırdan farklı bir değere ayarlandığında, hiçbir veri getirilmediği takdirde, *mevcut olmayan* uyarı sayısı ertelendi olur.

## <a name="data-feed-permissions"></a>Veri akışı izinleri

Veri akışı izinlerini yönetmek için iki rol vardır: *yönetici* ve *Görüntüleyici*. 

* Bir *yönetici* , veri akışı ve içindeki ölçümler üzerinde tam denetime sahiptir. Bunlar, veri akışını etkinleştirebilir, duraklatabilir, silebilir ve akışlar ve yapılandırmalarda güncelleştirmeler yapabilir. Bir *yönetici* genellikle ölçümlerin sahibidir.

* Bir *Görüntüleyici* , veri akışını veya ölçümlerini görüntüleyebilir, ancak değişiklik yapamaz. 

## <a name="next-steps"></a>Sonraki adımlar
- [Ölçüm danışmanına genel bakış](overview.md)
- [Web portalını kullanma](quickstarts/web-portal.md)