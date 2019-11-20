---
title: Azure Izleyici 'yi kullanmaya başlama Log Analytics | Microsoft Docs
description: Bu makalede, sorguları yazmak için Azure portal Log Analytics kullanmaya yönelik bir öğretici sunulmaktadır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 07/19/2019
ms.openlocfilehash: 1117ebbb8d2c3b133156c6b63a0ab13185f9f4a5
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933043"
---
# <a name="get-started-with-log-analytics-in-azure-monitor"></a>Azure Izleyici 'de Log Analytics kullanmaya başlama

> [!NOTE]
> En az bir sanal makineden veri topluyorsanız, kendi ortamınızda bu alıştırma aracılığıyla çalışabilirsiniz. Daha sonra, çok sayıda örnek veri içeren [tanıtım ortamımuzu](https://portal.loganalytics.io/demo)kullanın.

Bu öğreticide, Azure Izleyici günlük sorgularını yazmak için Azure portal Log Analytics nasıl kullanacağınızı öğreneceksiniz. Şu şekilde nasıl yapılacağını öğretir:

- Basit bir sorgu yazmak için Log Analytics kullanma
- Verilerinizin şemasını anlayın
- Sonuçları filtrele, Sırala ve Gruplandır
- Zaman aralığı Uygula
- Grafik oluşturma
- Sorguları kaydetme ve yükleme
- Sorguları dışarı ve paylaşma

Günlük sorguları yazma hakkında bir öğretici için bkz. [Azure izleyici 'de günlük sorgularını kullanmaya başlama](get-started-queries.md).<br>
Günlük sorguları hakkında daha fazla bilgi için bkz. [Azure izleyici 'de günlük sorgularına genel bakış](log-query-overview.md).

## <a name="meet-log-analytics"></a>Log Analytics tanışın
Log Analytics, Azure Izleyici günlük sorgularını yazmak ve yürütmek için kullanılan bir web aracıdır. Azure Izleyici menüsünde **Günlükler** ' i seçerek açın. Yeni bir boş sorgu ile başlar.

![Giriş sayfası](media/get-started-portal/homepage.png)

## <a name="firewall-requirements"></a>Güvenlik duvarı gereksinimleri
Log Analytics kullanmak için tarayıcınızda aşağıdaki adreslere erişim gerekir. Tarayıcınız bir güvenlik duvarı üzerinden Azure portal erişiyorsa, bu adreslere erişimi etkinleştirmeniz gerekir.

| Kullanılmamışsa | IP | Bağlantı Noktaları |
|:---|:---|:---|
| portal.loganalytics.io | Dinamik | 80.443 |
| api.loganalytics.io | Dinamik | 80.443 |
| docs.loganalytics.io | Dinamik | 80.443 |

## <a name="basic-queries"></a>Temel sorgular
Sorgular terimleri aramak, eğilimleri belirlemek, desenleri analiz etmek ve verilerinize göre birçok diğer öngörü sağlamak için kullanılabilir. Temel bir sorgu ile başlayın:

```Kusto
Event | search "error"
```

Bu sorgu, herhangi bir özellikte _hata_ koşulu içeren kayıtlar için _olay_ tablosunu arar.

Sorgular, bir tablo adı ya da [arama](/azure/kusto/query/searchoperator) komutu ile başlayabilir. Yukarıdaki örnek, olay tablosundan tüm kayıtları alan tablo adı _olayı_ile başlar. Kanal (|) karakteri komutları ayırır, bu nedenle ilk birinin çıktısı aşağıdaki komutun girişi olarak görev yapar. Tek bir sorguya dilediğiniz sayıda komut ekleyebilirsiniz.

Aynı sorguyu yazmak için başka bir yol şöyle olacaktır:

```Kusto
search in (Event) "error"
```

Bu örnekte, arama _olay_ tablosuna kapsamlıdır ve bu tablodaki tüm kayıtlar, _hataya_ilişkin **arama** yapılır.

## <a name="running-a-query"></a>Sorgu çalıştırma
**Çalıştır** düğmesine tıklayarak veya **SHIFT + enter**tuşlarına basarak bir sorgu çalıştırın. Çalıştırılacak kodu ve döndürülen verileri belirleyen aşağıdaki ayrıntıları göz önünde bulundurun:

- Satır sonları: tek bir kesme, sorgunuzu okumayı daha kolay hale getirir. Birden çok satır sonu, ayrı sorgulara bölünür.
- İmleç: imlecinizi yürütmek için imleci sorgunun içine yerleştirin. Geçerli sorgu boş bir satır bulunana kadar kod olarak kabul edilir.
- Zaman aralığı- _son 24 saatin_ zaman aralığı varsayılan olarak ayarlanır. Farklı bir Aralık kullanmak için, zaman seçiciyi kullanın veya sorgunuza açık bir zaman aralığı filtresi ekleyin.


## <a name="understand-the-schema"></a>Şemayı anlama
Şema, mantıksal bir kategori altında görsel olarak gruplanmış bir tablo koleksiyonudur. Kategorilerden bazıları izleme çözümlerinden biridir. _Logmanagement_ kategorisi, Windows ve Syslog olayları, performans verileri ve aracı sinyalleri gibi yaygın verileri içerir.

![Şema](media/get-started-portal/schema.png)

Her tabloda, veriler sütun adının yanındaki simgelerle gösterildiği gibi farklı veri türlerine sahip sütunlarda düzenlenir. Örneğin, ekran görüntüsünde gösterilen _olay_ tablosu, metin, bir sayı olan _EventCategory_ ve tarih/saat olan _TimeGenerated_ _gibi sütunlar_ içerir.

## <a name="filter-the-results"></a>Sonuçları filtrele
_Olay_ tablosundaki her şeyi alarak başlayın.

```Kusto
Event
```

Log Analytics otomatik olarak kapsam sonuçları:

- Zaman aralığı: varsayılan olarak, sorgular son 24 saat ile sınırlıdır.
- Sonuç Sayısı: sonuçlar maksimum 10.000 kayıtla sınırlıdır.

Bu sorgu çok genel ve yararlı olacak çok fazla sonuç döndürüyor. Sonuçları Tablo öğelerinden ya da sorguya açıkça bir filtre ekleyerek filtreleyebilirsiniz. Tablo öğeleri aracılığıyla sonuçları filtreleme, mevcut sonuç kümesi için geçerlidir, ancak sorgunun bir filtresi yeni bir filtrelenmiş sonuç kümesi döndürür ve bu nedenle daha doğru sonuçlar üretebilir.

### <a name="add-a-filter-to-the-query"></a>Sorguya bir filtre ekleyin
Her kaydın solunda bir ok vardır. Belirli bir kaydın ayrıntılarını açmak için bu oka tıklayın.

Görüntülenecek "+" ve "-" simgelerinin sütun adının üstüne gelin. Yalnızca aynı değere sahip kayıtları döndürecek bir filtre eklemek için, "+" işaretine tıklayın. Kayıtları bu değerle dışlamak için "-" düğmesine tıklayın ve ardından sorguyu yeniden çalıştırmak için **Çalıştır** ' a tıklayın.

![Sorguya filtre Ekle](media/get-started-portal/add-filter.png)

### <a name="filter-through-the-table-elements"></a>Tablo öğelerine göre filtrele
Şimdi _hata_önem derecesine sahip olaylara odaklanalım. Bu, _Eventlevelname_adlı bir sütunda belirtilmiştir. Bu sütunu görmek için sağa kaydırmanız gerekir.

Sütun başlığının yanındaki filtre simgesine tıklayın ve açılır pencerede metin _hatasıyla_ _başlayan_ değerleri seçin:

![Filtrele](media/get-started-portal/filter.png)


## <a name="sort-and-group-results"></a>Sonuçları Sırala ve Gruplandır
Sonuçlar, son 24 saat içinde oluşturulan SQL Server yalnızca hata olaylarını içerecek şekilde daha dar bir şekilde kullanıma sunulmuştur. Ancak, sonuçlar herhangi bir şekilde sıralanmaz. Sonuçları örneğin _zaman damgası_ gibi belirli bir sütuna göre sıralamak için sütun başlığına tıklayın. Bir tıklama, ikinci tıklama azalan sırada sıralama yaparken artan düzende sıralar.

![Sütunları Sırala](media/get-started-portal/sort-column.png)

Sonuçları düzenlemenin başka bir yolu da gruplara göre yapılır. Sonuçları belirli bir sütuna göre gruplandırmak için, sütun başlığını diğer sütunların üstüne sürüklemeniz yeterlidir. Alt gruplar oluşturmak için diğer sütunları üst çubuğun yanı sıra sürükleyin.

![Gruplar](media/get-started-portal/groups.png)

## <a name="select-columns-to-display"></a>Görüntülenecek sütunları seçin
Sonuçlar tablosu genellikle çok sayıda sütun içerir. Döndürülen sütunlardan bazılarının varsayılan olarak görüntülenmediğini ya da görüntülenen bazı sütunları kaldırmak istediğinizi görebilirsiniz. Gösterilecek sütunları seçmek için sütunlar düğmesine tıklayın:

![Sütun seçin](media/get-started-portal/select-columns.png)


## <a name="select-a-time-range"></a>Bir zaman aralığı seçin
Varsayılan olarak, Log Analytics _son 24 saatlik_ zaman aralığını uygular. Farklı bir Aralık kullanmak için zaman seçici aracılığıyla başka bir değer seçin ve **Çalıştır**' a tıklayın. Önceden ayarlanmış değerlere ek olarak, sorgunuz için mutlak bir Aralık seçmek üzere _özel zaman aralığı_ seçeneğini de kullanabilirsiniz.

![Saat seçici](media/get-started-portal/time-picker.png)

Özel bir zaman aralığı seçerken, seçilen değerler UTC biçiminde olur ve bu da yerel saat diliminize göre farklılık gösteren bir değer olabilir.

Sorgu, _TimeGenerated_için açıkça bir filtre içeriyorsa, saat seçici başlığı _sorgu olarak ayarlanır_. Çakışmayı engellemek için el ile seçim devre dışı bırakılacak.


## <a name="charts"></a>grafik
Sonuçları bir tabloya döndürmenin yanı sıra, sorgu sonuçları görsel biçimlerde sunulabilir. Örnek olarak aşağıdaki sorguyu kullanın:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Varsayılan olarak, sonuçlar bir tabloda görüntülenir. Sonuçları bir grafik görünümünde görmek için _grafik_ ' e tıklayın:

![Çubuk grafik](media/get-started-portal/bar-chart.png)

Sonuçlar yığılmış çubuk grafiğinde gösterilir. _Yığılmış sütun_ ' a tıklayın ve sonuçların başka bir görünümünü göstermek için _pasta_ ' ı seçin:

![Pasta grafik](media/get-started-portal/pie-chart.png)

Görünümün x ve y eksenleri gibi farklı özellikleri veya gruplama ve bölme tercihleri, denetim çubuğundan el ile değiştirilebilir.

Ayrıca, işleme işlecini kullanarak sorguda tercih edilen görünümü ayarlayabilirsiniz.

### <a name="smart-diagnostics"></a>Akıllı Tanılamalar
Bir zaman grafiğinde, verilerinizde ani bir ani artış veya adım varsa, satırda vurgulanan bir nokta görebilirsiniz. Bu, _akıllı tanılamayı_ ani değişikliğin filtreleneceği özelliklerin bir bileşimini tanımladığını gösterir. Filtre hakkında daha fazla ayrıntı almak için noktaya tıklayın ve filtrelenmiş sürümü görüntüleyin. Bu, değişikliğin ne olduğunu belirlemenize yardımcı olabilir:

![Akıllı Tanılamalar](media/get-started-portal/smart-diagnostics.png)

## <a name="pin-to-dashboard"></a>Panoya sabitle
Bir diyagramı veya tabloyu paylaşılan Azure Panolarınızın birine sabitlemek için Sabitle simgesine tıklayın. Bu simgenin, aşağıdaki ekran görüntüsünden farklı Log Analytics penceresinin en üstüne taşındığını unutmayın.

![Panoya sabitle](media/get-started-portal/pin-dashboard.png)

Belirli basitleştirmeleri bir panoya sabitlemeyi yaptığınızda bir grafiğe uygulanır:

- Tablo sütunları ve satırları: bir tabloyu panoya sabitlemek için dört veya daha az sütunu olmalıdır. Yalnızca ilk yedi satır görüntülenir.
- Zaman kısıtlaması: sorgular son 14 güne göre otomatik olarak sınırlandırılır.
- Bin sayısı kısıtlaması: çok sayıda farklı bölme içeren bir grafik görüntülediğinizde, daha az doldurulmuş depo gözleri otomatik olarak tek bir _diğer_ bin içine gruplandırılır.

## <a name="save-queries"></a>Sorguları Kaydet
Faydalı bir sorgu oluşturduktan sonra, bunu kaydetmek veya başkalarıyla paylaşmak isteyebilirsiniz. **Kaydet** simgesi üst çubukta bulunur.

Sorgu sayfasının tamamını ya da tek bir sorguyu işlev olarak kaydedebilirsiniz. İşlevler, diğer sorgular tarafından da başvurulabilen sorgulardır. Bir sorguyu işlev olarak kaydetmek için, diğer sorgular tarafından başvuruluyorsa bu sorguyu çağırmak için kullanılan ad olan bir işlev diğer adı sağlamanız gerekir.

![İşlevi Kaydet](media/get-started-portal/save-function.png)

>[!NOTE]
>Aşağıdaki karakterler desteklenir-kaydedilen sorguyu kaydederken veya düzenlenirken **ad** alanında `a–z, A–Z, 0-9, -, _, ., <space>, (, ), |`.

Log Analytics sorguları her zaman seçili bir çalışma alanına kaydedilir ve bu çalışma alanının diğer kullanıcılarıyla paylaşılır.

## <a name="load-queries"></a>Sorguları yükle
Sorgu Gezgini simgesi sağ üst alandır. Bu, tüm kaydedilmiş sorguları kategoriye göre listeler. Ayrıca, belirli sorguları gelecekte hızlı bir şekilde bulmak için sık kullanılanlar olarak işaretlemenize de olanak sağlar. Kayıtlı bir sorguyu geçerli pencereye eklemek için çift tıklayın.

![Sorgu Gezgini](media/get-started-portal/query-explorer.png)

## <a name="export-and-share-as-link"></a>Dışarı ve farklı paylaşma bağlantısı
Log Analytics birkaç dışarı aktarma yöntemini destekler:

- Excel: sonuçları bir CSV dosyası olarak kaydedin.
- Power BI: sonuçları Power BI dışarı aktarın. Ayrıntılar için bkz. [Azure izleyici günlük verilerini Içeri aktarma Power BI](../../azure-monitor/platform/powerbi.md) .
- Bağlantı paylaşma: sorgunun kendisi, aynı çalışma alanına erişimi olan diğer kullanıcılar tarafından gönderilebileceği ve çalıştırılabilen bir bağlantı olarak paylaşılabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure izleyici günlük sorgularını yazma](get-started-queries.md)hakkında daha fazla bilgi edinin.
