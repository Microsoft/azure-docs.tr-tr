---
title: Azure Izleyici 'de günlük sorgularını kullanmaya başlama | Microsoft Docs
description: Bu makalede, Azure Izleyici 'de günlük sorguları yazmaya başlama hakkında bir öğretici sunulmaktadır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/24/2019
ms.openlocfilehash: d0e19c8483321189cb38a4eebdbf7b2cb89785ef
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933033"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Azure Izleyici 'de günlük sorgularını kullanmaya başlama

> [!NOTE]
> En az bir sanal makineden veri topluyorsanız, kendi ortamınızda bu alıştırma aracılığıyla çalışabilirsiniz. Daha sonra, çok sayıda örnek veri içeren [tanıtım ortamımuzu](https://portal.loganalytics.io/demo)kullanın.

Bu öğreticide, Azure Izleyici 'de günlük sorgularını yazmayı öğreneceksiniz. Şu şekilde nasıl yapılacağını öğretir:

- Sorgu yapısını anlama
- Sorgu sonuçlarını Sırala
- Sorgu sonuçlarını filtrele
- Bir zaman aralığı belirtin
- Sonuçlara hangi alanların ekleneceğini seçin
- Özel alanları tanımlama ve kullanma
- Toplam ve Grup sonuçları

Azure portal Log Analytics kullanmaya yönelik bir öğretici için bkz. [Azure izleyici 'yi kullanmaya başlama Log Analytics](get-started-portal.md).<br>
Azure Izleyici 'de günlük sorguları hakkında daha fazla bilgi için bkz. [Azure izleyici 'de günlük sorgularına genel bakış](log-query-overview.md).

Aşağıdaki öğreticinin video sürümüyle birlikte izleyin:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="writing-a-new-query"></a>Yeni sorgu yazma
Sorgular, bir tablo adı veya *Search* komutuyla başlayabilir. Sorgu için açık bir kapsam tanımladığından ve hem sorgu performansını hem de sonuçların uygunluğunu geliştirdiğinden, bir tablo adıyla başlamanız gerekir.

> [!NOTE]
> Azure Izleyici tarafından kullanılan kusto sorgu dili, büyük/küçük harfe duyarlıdır. Dil anahtar sözcükleri genellikle küçük harf olarak yazılmıştır. Bir sorgudaki tablo veya sütun adlarını kullanırken, şema bölmesinde gösterildiği gibi doğru durumu kullandığınızdan emin olun.

### <a name="table-based-queries"></a>Tablo tabanlı sorgular
Azure Izleyici, tablolardaki günlük verilerini, her biri birden çok sütundan oluşan şekilde düzenler. Tüm tablolar ve sütunlar, analiz portalında Log Analytics şema bölmesinde gösterilir. İlgilendiğiniz bir tabloyu tanımlayabilir ve ardından bir veri bölümüne göz atın:

```Kusto
SecurityEvent
| take 10
```

Yukarıda gösterilen sorgu, belirli bir sırada *Securityevent* tablosundan 10 sonuç döndürüyor. Bu, bir tabloya bir bakışta göz atma ve yapısını ve içeriğini anlamak için çok yaygın bir yoldur. Nasıl oluşturulduğunu inceleyelim:

* Sorgu *Securityevent* adlı tablo adıyla başlar-bu bölüm, sorgunun kapsamını tanımlar.
* Kanal (|) karakteri komutları ayırır, bu nedenle aşağıdaki komutun girişinde ilk bir çıkış oluşur. Herhangi bir sayıda yöneltilen öğe ekleyebilirsiniz.
* Kanalı takip eden, tablodan belirli sayıda rastgele kayıt döndüren **Al** komutu olur.

Gerçekten de geçerli olmaya devam edecek `| take 10` eklemeden bile sorguyu çalıştırabiliyoruz, ancak en fazla 10.000 sonuç döndürebilir.

### <a name="search-queries"></a>Arama sorguları
Arama sorguları daha az yapılandırılmıştır ve sütunlarında belirli bir değeri içeren kayıtları bulmaya yönelik genellikle daha uygundur:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

Bu sorgu, "şifreleme" tümceciğini içeren kayıtlar için *Securityevent* tablosunu arar. Bu kayıtlardan 10 kayıt döndürülür ve görüntülenir. `in (SecurityEvent)` parçasını atlıyoruz ve yalnızca `search "Cryptographic"`çalıştırırsanız, arama *Tüm* tabloların üzerine giderek daha uzun sürer ve daha az etkili olur.

> [!WARNING]
> Arama sorguları genellikle tablo tabanlı sorgulardan daha yavaştır, çünkü daha fazla veri işleyebilir. 

## <a name="sort-and-top"></a>Sırala ve üst
Birkaç kayıt elde etmek **yararlı olmakla çalışırken** sonuçlar seçilir ve belirli bir sırada görüntülenir. Sıralı bir görünüm almak için, tercih edilen sütuna göre **sıralayabilirsiniz** :

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

Bu çok fazla sonuç döndürebilse de biraz zaman alabilir. Yukarıdaki sorgu *Tüm* securityevent tablosunu TimeGenerated sütununa göre sıralar. Analiz portalı, görüntüyü yalnızca 10.000 kaydı gösterecek şekilde sınırlandırır. Bu yaklaşım en uygun değildir.

Yalnızca en son 10 kaydı almanın en iyi yolu, **en üstte**yer alan ve tüm tabloyu sunucu tarafında sıralayan ve en üstteki kayıtları döndüren ilk kullanmaktır:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

Azalan varsayılan sıralama sıralamasıdır ve genellikle **DESC** bağımsız değişkenini atlarız. Çıktı şöyle görünür:

![İlk 10](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Burada: bir koşula göre filtreleme
Filtreler, kendi adlarıyla gösterildiği gibi, verileri belirli bir koşula göre filtreleyin. Bu, sorgu sonuçlarını ilgili bilgilerle sınırlamanın en yaygın yoludur.

Bir sorguya filtre eklemek için **WHERE** işlecini ve ardından bir veya daha fazla koşulu kullanın. Örneğin, aşağıdaki sorgu yalnızca _Level_ eşittir _8_olan *securityevent* kayıtlarını döndürür:

```Kusto
SecurityEvent
| where Level == 8
```

Filtre koşullarını yazarken aşağıdaki ifadeleri kullanabilirsiniz:

| İfadeler | Açıklama | Örnek |
|:---|:---|:---|
| == | Eşitliği denetle<br>(büyük/küçük harfe duyarlı) | `Level == 8` |
| =~ | Eşitliği denetle<br>(büyük/küçük harf duyarsız) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| ! =, < > | Eşitsizlik denetimi<br>(her iki ifade de aynıdır) | `Level != 4` |
| *ve* *veya* | Koşullar arasında gerekli| `Level == 16 or CommandLine != ""` |

Birden çok koşula göre filtrelemek için, **ve**şunları kullanabilirsiniz:

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

veya **birden çok öğeyi** diğerinin bir sonraki kanalı:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Değerler farklı türlerde olabilir, bu nedenle doğru türde karşılaştırma gerçekleştirmek için bunları atamalısınız. Örneğin, SecurityEvent *düzeyi* sütunu dize türündedir, bu yüzden sayısal işleçleri kullanabilmeniz için önce *int* veya *Long*gibi sayısal bir türe dönüştürmeniz gerekir: `SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Bir zaman aralığı belirtin

### <a name="time-picker"></a>Saat seçici
Saat Seçici, Çalıştır düğmesinin yanında bulunur ve yalnızca son 24 saatin kayıtlarını sorgulıyoruz. Bu, tüm sorgulara uygulanan varsayılan zaman aralığıdır. Son saatin yalnızca kayıtlarını almak için _son saat_ ' i seçin ve sorguyu yeniden çalıştırın.

![Saat Seçici](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Sorgudaki zaman filtresi
Ayrıca sorguya bir zaman filtresi ekleyerek kendi zaman aralığınızı tanımlayabilirsiniz. Zaman filtresini tablo adından hemen sonra yerleştirmeniz en iyisidir: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

Yukarıdaki zaman filtresinde `ago(30m)` "30 dakika önce" anlamına gelir, bu nedenle bu sorgu yalnızca son 30 dakikadan kayıtları döndürür. Diğer zaman birimleri gün (2B), dakika (25m) ve saniye (10 s).


## <a name="project-and-extend-select-and-compute-columns"></a>Proje ve genişletme: sütunları seçme ve hesaplama
Sonuçlara eklenecek belirli sütunları seçmek için **projeyi** kullanın:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

Yukarıdaki örnek bu çıktıyı oluşturur:

![Sorgu projesi sonuçları](media/get-started-queries/project.png)

Ayrıca, sütunları yeniden adlandırmak ve yenilerini tanımlamak için **projeyi** de kullanabilirsiniz. Aşağıdaki örnek, aşağıdakileri yapmak için projesini kullanır:

* Yalnızca *bilgisayar* ve *TimeGenerated* orijinal sütunları seçin.
* *Etkinlik* sütununu *eventdetails*olarak yeniden adlandırın.
* *EventCode*adlı yeni bir sütun oluşturun. Alt **dize ()** Işlevi, etkinlik alanından yalnızca ilk dört karakteri almak için kullanılır.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**Extend** , sonuç kümesindeki tüm özgün sütunları tutar ve ek olanları tanımlar. Aşağıdaki sorgu, *EventCode* sütununu eklemek için **Extend** kullanır. Bu sütunun tablonun sonunda görüntülenmeyebilir, bu durumda, bir kaydın ayrıntılarını görüntülemek için genişletmeniz gerekir.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>Özetleme: toplam satır grupları
Bir veya daha fazla sütuna göre kayıt gruplarını tanımlamak için **özetleme** 'yi kullanın ve bunlara toplamaları uygulayın. **Özetlemenin** en yaygın kullanımı, her bir gruptaki sonuçların sayısını döndüren *sayıdır*.

Aşağıdaki sorgu, son saatin tüm *performans* kayıtlarını inceler, bunları *ObjectName*'e göre gruplandırır ve her bir gruptaki kayıtları sayar: 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Bazen grupları birden çok boyuta göre tanımlamak mantıklı olur. Bu değerlerin her benzersiz birleşimi ayrı bir grubu tanımlar:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Diğer bir yaygın kullanım, her grupta matematiksel veya istatistiksel hesaplamalar gerçekleştirkullanmaktır. Örneğin, aşağıdaki her bilgisayar için Ortalama *CounterValue değerini* hesaplar:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Ne yazık ki, farklı performans sayaçlarını birlikte karmalıyoruz, bu sorgunun sonuçları anlamsız bir şekilde düşüktür. Bunu daha anlamlı hale getirmek için, her bir *CounterName* ve *bilgisayar*birleşimi için ortalamayı ayrı olarak hesaplamalıdır:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Bir zaman sütunuyla özetleme
Gruplandırma sonuçları ayrıca bir zaman sütununa veya başka bir sürekli değere göre de yapılabilir. Yalnızca benzersiz değerler olduğundan `by TimeGenerated` özetleme, zaman aralığı boyunca her tek milisaniyelik için gruplar oluşturur. 

Sürekli değerleri temel alan gruplar oluşturmak için, **bin**kullanarak aralığı yönetilebilir birimlere bölmek en iyisidir. Aşağıdaki sorgu, belirli bir bilgisayardaki boş belleği (*kullanılabilir MBayt*) ölçen *performans* kayıtlarını analiz eder. Son 7 günde her bir 1 saatlik dönemin ortalama değerini hesaplar:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

Çıktıyı daha anlaşılır hale getirmek için, zaman içinde kullanılabilir belleği gösteren bir zaman grafiği olarak görüntülemeyi seçersiniz:

![Zaman içinde sorgu belleği](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Sonraki adımlar

- [Arama sorguları yazma](search-queries.md) hakkında bilgi edinin
