---
title: Azure Izleyici çalışma kitapları ile etkileşimli raporlar oluşturma | Microsoft docs
description: Önceden oluşturulmuş ve özel parametreli çalışma kitapları ile karmaşık raporlamayı kolaylaştırın
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: bbbf511286c63acf4a939e0a0e7d9c3dc9efa75b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899384"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Azure Izleyici çalışma kitapları ile etkileşimli raporlar oluşturma

Çalışma kitapları metin, [analiz sorguları](https://docs.microsoft.com/azure/application-insights/app-insights-analytics), Azure ölçümleri ve parametreleri zengin etkileşimli raporlarla birleştirir. Çalışma kitapları aynı Azure kaynaklarına erişimi olan diğer takım üyeleri tarafından düzenlenebilir.

Çalışma kitapları, şunun gibi senaryolar için yararlıdır:

* Daha önce ilgilendiğiniz ölçümleri bilmiyorsanız uygulamanızın kullanımını keşfetme: Kullanıcı sayısı, bekletme oranları, dönüştürme ücretleri vb. Diğer kullanım analizi araçlarının aksine, çalışma kitapları birden çok görselleştirme ve analizler birleştirerek bu tür bir serbest biçim araştırması için harika hale getirir.
* Yeni yayınlanan bir özelliğin nasıl çalıştığını, önemli etkileşimler ve diğer ölçümler için Kullanıcı sayısını göstererek takımınıza açıklayan.
* Uygulamanızdaki bir A/B denemesinin sonuçlarını takımınızın diğer üyeleriyle paylaşma. Denemeye ilişkin hedefleri açıklayabileceğiniz için, her bir metriğin hedefin üstünde mi yoksa altında mı olduğunu gösteren açık çağrı durumuyla birlikte denemeyi değerlendirmek için kullanılan her kullanım ölçümünü ve analitik sorguyu gösterebilirsiniz.
* Uygulamanızın kullanımıyla ilgili bir kesinti etkisini raporlama, verileri, metin açıklamasını birleştirme ve gelecekte kesintiye neden olan kesintileri önlemeye yönelik sonraki adımların bir tartışmasını bildirme.

## <a name="starting-with-a-template-or-saved-workbook"></a>Şablon veya kaydedilmiş çalışma kitabından başlayarak

Çalışma kitabı, bağımsız olarak düzenlenebilir grafiklerden, tablolardan, metinden ve giriş denetimlerinden oluşan bölümlerden oluşur. Çalışma kitaplarını daha iyi anlamak için en iyisi bir tane açılır. 

Sol taraftaki menüden, uygulamanızın Application Insights deneyiminin içinden **çalışma kitapları** ' nı seçin.

![Çalışma kitaplarına gezinmenin ekran görüntüsü](./media/usage-workbooks/001-workbooks.png)

Bu işlem, başlamanıza yardımcı olacak çok sayıda önceden oluşturulmuş çalışma kitabı içeren bir çalışma kitabı Galerisi başlatır.

![Çalışma kitabı galerinin ekran görüntüsü](./media/usage-workbooks/002-workbook-gallery.png)

**Hızlı başlangıç**başlığı altında bulunan **varsayılan şablonla**başlayacağız.

![Çalışma kitabı galerinin ekran görüntüsü](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Çalışma kitabı bölümlerini düzenleme, yeniden düzenleme, kopyalama ve silme

Çalışma kitaplarında iki mod vardır: **Düzen modu**ve **Okuma modu**. Varsayılan çalışma kitabı ilk başlatıldığında, **Düzen modunda**açılır. Bu, başka bir şekilde gizlenen tüm adımlar ve parametreler dahil olmak üzere çalışma kitabının tüm içeriğini gösterir. **Okuma modu** , Basitleştirilmiş bir rapor stili görünümü sunar. Bu, rapor oluşturma ile ilgili karmaşıklığın, yalnızca değişiklik için gerektiğinde yalnızca birkaç tıklamaya sahip olmaya devam etmenize olanak sağlar.

![Application Insights çalışma kitapları bölümü Düzenle denetimleri](./media/usage-workbooks/editing-controls-new.png)

1. Bir bölümü düzenlemenizi bitirdiğinizde, bölümünün sol alt köşesinde bulunan **Düzenle** ' ye tıklayın.

2. Bir bölümün bir kopyasını oluşturmak için **Bu bölümü Kopyala** simgesine tıklayın. Yinelenen bölümler oluşturmak, önceki yinelemeleri kaybetmeden bir sorgu üzerinde yinelemek için harika bir yoldur.

3. Çalışma kitabındaki bir bölümü yukarı taşımak için **Yukarı taşı** veya **aşağı taşı** simgesine tıklayın.

4. Bir bölümü kalıcı olarak kaldırmak için **Kaldır** simgesine tıklayın.

## <a name="adding-text-and-markdown-sections"></a>Metin ve Markaşağı bölümleri ekleme

Çalışma kitaplarınıza başlıklar, açıklamalar ve yorum eklemek, bir dizi tablo ve grafiği bir anlatıcı haline dönüştürmenize yardımcı olur. Çalışma kitaplarındaki metin bölümleri, metin biçimlendirme için başlıklar, kalın, italik ve madde işaretli listeler gibi [markı sözdizimini](https://daringfireball.net/projects/markdown/) destekler.

Çalışma kitabınıza bir metin bölümü eklemek için, çalışma kitabının alt kısmındaki veya herhangi bir bölümün altındaki **metin ekle** düğmesini kullanın.

## <a name="adding-query-sections"></a>Sorgu bölümleri ekleme

![Çalışma kitaplarında sorgu bölümü](./media/usage-workbooks/analytics-section-new.png)

Çalışma kitabınıza sorgu bölümü eklemek için, çalışma kitabının alt kısmındaki **Sorgu Ekle** düğmesini ya da herhangi bir bölümün alt kısmını kullanın.

Sorgu bölümleri son derece esnektir ve şunun gibi soruları yanıtlamak için kullanılabilir:

* Siteniz, kullanımda olan bir reddetme ile aynı zaman diliminde kaç özel durum oluşturur?
* Bazı sayfaları görüntüleyen kullanıcılar için sayfa yükleme sürelerinin dağılımı nedir?
* Kaç Kullanıcı sitenizde bazı sayfa kümesini görüntüledi, ancak başka bir sayfa kümesi değil mi? Bu, sitenizin işlevselliğinin farklı alt kümelerini kullanan Kullanıcı kümelerine sahip olup olmadığını anlamak için yararlı olabilir (`join` işlecini [kusto sorgu dilinde](/azure/kusto/query/)`kind=leftanti` değiştiricisiyle kullanın).

Ayrıca, yalnızca çalışma kitabını başlattığınız uygulamanın bağlamından sorgulama sınırlı değildir. Bu kaynaklara erişim iznine sahip olduğunuz sürece, birden fazla Application Insights izlenen uygulama ve Log Analytics çalışma alanı genelinde sorgulama yapabilirsiniz.

Ek dış Application Insights kaynaklarından sorgulamak için **uygulama** tanımlayıcısı ' nı kullanın.

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

Bu sorgu, istekleri üç farklı uygulamadan birleştirilüdir. App01 adlı bir uygulama, app02 adlı bir uygulama ve yerel Application Insights kaynağından gelen istekler.

Dış Log Analytics çalışma alanından verileri çekmek için **çalışma alanı** tanımlayıcısı kullanın.

Çapraz kaynak sorguları hakkında daha fazla bilgi edinmek için [resmi kılavuza](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search)bakın.

### <a name="advanced-analytic-query-settings"></a>Gelişmiş analitik sorgu ayarları

Her bölümde kendi gelişmiş ayarları bulunur, bu ayarlar ![Application Insights çalışma kitapları bölümü Düzenle](./media/usage-workbooks/005-settings.png) **parametre Ekle** düğmesinin sağında bulunur.

![Application Insights çalışma kitapları bölümü Düzenle denetimleri](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Özel genişlik**    | Bunu bir öğe için rastgele bir boyut haline getirmek üzere ayarlayın; böylece grafikleri ve tablolarınızı zengin etkileşimli raporlarda daha iyi düzenlemenizi sağlayabilirsiniz.  |
   | **Koşullu olarak görünür** | Okuma modundayken bir parametreye göre adımları gizlemek için bunu kullanın. |
   | **Bir parametreyi dışarı aktarma**| Bu, kılavuz veya grafikteki seçili bir satırın sonraki adımların değer değiştirmesine veya görünür hale gelmesine neden olmasını sağlar.  |
   | **Düzenlenmediğinden sorguyu göster** | Bu, okuma modundayken bile grafiğin veya tablonun üzerindeki sorguyu görüntüler.
   | **Düzenlenmediğinden Analytics 'te aç düğmesini göster** | Bu, tek tıklamayla erişime izin vermek için grafiğin sağ köşesine mavi analiz simgesini ekler.|

Bu ayarların çoğu oldukça sezgisel, ancak **bir parametreyi dışarı aktarmayı** anlamak için bu işlevi kullanan bir çalışma kitabını incelemek daha iyidir.

Önceden oluşturulmuş çalışma kitaplarından biri, etkin kullanıcılar hakkında bilgiler sağlar.

Çalışma kitabının ilk bölümü analitik sorgu verilerine dayalıdır:

![Application Insights çalışma kitapları bölümü Düzenle denetimleri](./media/usage-workbooks/003-active-users.png)

İkinci bölüm de analitik sorgu verilerine dayalıdır, ancak ilk tablodaki bir satırı seçmek, grafiğin içeriğini etkileşimli olarak güncelleştirir:

![Application Insights çalışma kitapları bölümü Düzenle denetimleri](./media/usage-workbooks/004-active-users-trend.png)

 Bu, **bir öğe seçildiğinde** , tablonun analiz sorgusunda etkinleştirilen bir parametre gelişmiş ayarlarını dışarı aktarmak için kullanılabilir.

![Application Insights çalışma kitapları bölümü Düzenle denetimleri](./media/usage-workbooks/007-settings-export.png)

İkinci analiz sorgusu daha sonra bir satır seçildiğinde, bu değerleri kullanır. Hiçbir satır seçilmezse, varsayılan değerleri temsil eden satırı varsayılan olarak alır. 

```
let start = startofday(ago({TimeRange} + {Metric}));
union customEvents, pageViews
| where timestamp >= start
| where name in ({Activities}) or '*' in ({Activities}) or ('%' in ({Activities}) and itemType == 'pageView') or ('#' in ({Activities}) and itemType == 'customEvent')
{OtherFilters}
| where '{Filter}' == '' or '{Filter}' == '🔸 Overall' or {AnalyzeBy} == replace('🔹 ', '', '{Filter}')
| evaluate activity_engagement(user_Id, timestamp, start, now(), 1d, {Metric})
| where timestamp >= startofday(ago({TimeRange}))
| project timestamp, ["Active User"] = dcount_activities_outer
| render timechart 
```

## <a name="adding-metrics-sections"></a>Ölçüm bölümleri ekleme

Ölçüm bölümleri, Azure Izleyici ölçüm verilerini etkileşimli raporlarınıza katmak için size tam erişim sağlar. Önceden oluşturulmuş çalışma kitaplarının çoğu hem analitik sorgu verileri hem de ölçüm verileri içerir ve her iki özellikten en iyi şekilde tek bir yerde yararlanmanızı sağlar. Ayrıca, erişiminiz olan aboneliklerdeki kaynaklardan ölçüm verilerini çekme olanağınız da vardır.

Aşağıda, bir çalışma kitabına eklenen ve CPU performansının kılavuz bir görselleştirmesi sağlayan sanal makine verilerinin bir örneği verilmiştir:

![Application Insights çalışma kitapları bölümü Düzenle denetimleri](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Parametre bölümleri ekleme

Çalışma kitabı parametreleri, sorgu veya metin bölümlerini el ile düzenlemeniz gerekmeden çalışma kitabındaki değerleri değiştirmenize izin verir.  Bu, temel analiz sorgu dilini anlamaya gerek gereksinimini ortadan kaldırır ve çalışma kitabı tabanlı raporlamanın potansiyel kitlelerini büyük ölçüde genişletir.

Parametre değerleri, ``{parameterName}`` gibi küme ayraçları içine yerleştirerek sorgu, metin veya diğer parametre bölümlerinde yer alır.  Parametre adları, JavaScript tanımlayıcıları, temelde alfabetik karakterler veya alt çizgiler, sonra alfasayısal karakterler veya alt çizgiler ile benzer kurallarla sınırlandırılmıştır. Örneğin, **a1** 'ye izin verilir, ancak **1a** buna izin verilmez.

Parametreler doğrusal, bir çalışma kitabının üstünden başlayıp daha sonraki adımlara akar.  Daha sonra bir çalışma kitabında bildirildiği parametreler, daha önce tanımlananlar tarafından geçersiz kılınabilir.  Bu Ayrıca, daha sonra tanımlanan parametrelerden değerlere erişmek için sorgular kullanan parametrelere izin verir.  Bir parametrenin adımının kendisi içinde, parametreler aynı adımda daha önce belirtilen bir parametreye bağlı olabileceği gibi, parametreler de doğrusal, soldan sağa doğru yapılır.
 
Şu anda desteklenen dört farklı parametre türü vardır:

  |         |          |
   | ---------------- |:-----|
   | **Metin**    | Kullanıcı bir metin kutusunu düzenler ve isteğe bağlı olarak varsayılan değeri dolduracak bir sorgu sağlayabilirsiniz. |
   | **Açılır liste** | Kullanıcı bir değer kümesinden seçim yapmak için kullanılır. |
   | **Zaman aralığı seçici**| Kullanıcı önceden tanımlanmış bir zaman aralığı değerleri kümesinden seçim yapmak veya özel bir zaman aralığından seçim yapmak için kullanılır.|
   | **Kaynak seçici** | Kullanıcı çalışma kitabı için seçilen kaynaklardan seçim yapmak için kullanılır.|

### <a name="using-a-text-parameter"></a>Metin parametresi kullanma

Metin kutusundaki Kullanıcı türlerinin değeri, kaçış veya tırnak içine alma olmadan doğrudan sorguda değiştirilmiştir. İhtiyacınız olan değer bir dize ise, sorgunun parametre etrafında tırnak işareti olmalıdır ( **' {Parameter} '** gibi).

Bu, bir metin kutusundaki değerin her yerde kullanılmasına izin verir. Tablo adı, sütun adı, işlev adı, işleç vb. olabilir.

Metin parametre türü, **analiz sorgusundan varsayılan değeri Al**ayarına sahiptir ve bu, çalışma kitabı yazarının bu metin kutusu için varsayılan değeri doldurmak üzere bir sorgu kullanmasına izin verir.

Bir analiz sorgusundan varsayılan değer kullanılırken, varsayılan değer olarak yalnızca ilk satırın ilk değeri (satır 0, sütun 0) kullanılır. Bu nedenle, sorgunuzu yalnızca bir satır ve bir sütun döndürecek şekilde sınırlandırmaya önerilir. Sorgu tarafından döndürülen diğer veriler yok sayılır. 

Sorgunun döndürdüğü değer, doğrudan kaçış veya tırnak işareti olmadan değişir. Sorgu hiçbir satır döndürürse, parametrenin sonucu boş bir dizedir (parametre gerekli değilse) ya da tanımsız (parametre gerekliyse).

### <a name="using-a-dropdown"></a>Açılan menü kullanma

Açılan parametre türü, bir veya daha fazla değer seçimine izin veren bir açılan denetim oluşturmanıza olanak sağlar.

Açılan menü, bir analiz sorgusuyla doldurulur. Sorgu bir sütun döndürürse, bu sütundaki değerler hem **değeri** hem de açılan denetimdeki **etikettir** . Sorgu iki sütun döndürürse, ilk sütun **değerdir**ve ikinci sütun, açılan menüde gösterilen **etikettir** .  Sorgu üç sütun döndürürse, bu açılan menüdeki varsayılan seçimi göstermek için 3. sütun kullanılır.  Bu sütun herhangi bir tür olabilir, ancak en basit değer bool veya sayısal türler kullanmaktır; burada 0, false, 1 ise doğrudur.

 Sütun bir dize türü ise, null/boş dize yanlış olarak değerlendirilir ve diğer tüm değerler doğru olarak değerlendirilir. Tek seçim açılan listeleri için, true değeri olan ilk değer varsayılan seçim olarak kullanılır.  Çoklu seçim açılan listeleri için, true değeri olan tüm değerler varsayılan seçili küme olarak kullanılır. Açılan menüdeki öğeler, sorgunun satırları döndürdüğü sırada gösterilir. 

Etkin kullanıcılar raporunda bulunan parametrelere bakalım. **Timerange**' ın yanındaki Düzenle simgesine tıklayın.

![Application Insights çalışma kitapları bölümü Düzenle denetimleri](./media/usage-workbooks/009-time-range.png)

Bu işlem parametre Düzenle menü öğesini başlatır:

![Application Insights çalışma kitapları bölümü Düzenle denetimleri](./media/usage-workbooks/010-time-range-edit.png)

Sorgu, rastgele bir tablo oluşturmanıza olanak sağlayan bir **DataTable** adlı analiz sorgu dilinin bir özelliğini kullanır. Örneğin, aşağıdaki analiz sorgusu:

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Sonucu üretir:

![Application Insights çalışma kitapları bölümü Düzenle denetimleri](./media/usage-workbooks/011-data-table.png)

Daha uygun bir örnek, bir ülke/bölge kümesinden ada göre seçim yapmak için bir açılan menü kullanmaktır:

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

Sorgu sonuçları şu şekilde görüntüler:

![Ülke açılan kutusu](./media/usage-workbooks/012-country-dropdown.png)

Açılan araçlar, etkileşimli raporları özelleştirmeye ve oluşturmaya yönelik inanılmaz güçlü araçlardır.

### <a name="time-range-parameters"></a>Zaman aralığı parametreleri

Açılan parametre türü aracılığıyla kendi özel zaman aralığı parametresini de yapabilirsiniz, ancak aynı esneklik derecesine gerek duymuyorsanız, kutudan çıkar zaman aralığı parametre türünü de kullanabilirsiniz. 

Zaman aralığı parametre türlerinde beş dakikadan son 90 güne kadar geçen 15 varsayılan Aralık vardır. Ayrıca, özel zaman aralığı seçimine izin veren bir seçenek de vardır. Bu, raporun işlecinin, zaman aralığı için açık başlatma ve durdurma değerlerini seçmesine olanak sağlar.

### <a name="resource-picker"></a>Kaynak seçici

Kaynak seçici parametre türü, raporunuzu belirli kaynak türlerine göre kapsam yeteneği sağlar. Kaynak seçici türünü kullanan önceden oluşturulmuş çalışma kitabının bir örneği, **hata öngörüleri** çalışma kitabıdır.

![Ülke açılan kutusu](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Çalışma kitaplarını ekibinizle kaydetme ve paylaşma

Çalışma kitapları, sizin veya Application Insights kaynağına erişimi olan herkes tarafından erişilebilen **paylaşılan raporlar** bölümünde veya sizin için özel **Raporlarım** bölümünde veya Application Insights bir kaynak içinde kaydedilir. Kaynaktaki tüm çalışma kitaplarını görüntülemek için Eylem çubuğundaki **Aç** düğmesine tıklayın.

Şu anda **Raporlarım**bir çalışma kitabını paylaşmak için:

1. Eylem çubuğunda **Aç** ' a tıklayın
2. "..." Düğmesine tıklayın. paylaşmak istediğiniz çalışma kitabının yanındaki düğme
3. **Paylaşılan raporlara taşı**' ya tıklayın.

Bir çalışma kitabını bir bağlantı ile veya e-posta ile paylaşmak için, eylem çubuğunda **paylaşma** ' ya tıklayın. Bağlantı alıcılarının, çalışma kitabını görüntülemek için Azure portal bu kaynağa erişmesi gerektiğini aklınızda bulundurun. Düzenleme yapmak için, alıcıların kaynak için en az katkıda bulunan izinleri olması gerekir.

Bir Azure panosuna çalışma kitabına bir bağlantı sabitlemek için:

1. Eylem çubuğunda **Aç** ' a tıklayın
2. "..." Düğmesine tıklayın. sabitlemek istediğiniz çalışma kitabının yanındaki düğme
3. **Panoya sabitle**' ye tıklayın.

## <a name="contributing-workbook-templates"></a>Katkıda bulunan çalışma kitabı şablonları

Bir başar çalışma kitabı şablonu oluşturdunuz ve bunu topluluk ile paylaşmak istiyor musunuz? Daha fazla bilgi edinmek için [GitHub](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)deponuzu ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar
- Kullanım deneyimlerini etkinleştirmek için [özel olaylar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) veya [sayfa görünümleri](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)göndermeye başlayın.
- Özel olayları veya sayfa görünümlerini zaten gönderirseniz, kullanıcıların hizmetinizi nasıl kullandığını öğrenmek için kullanım araçları ' nı araştırın.
    - [Kullanıcılar, Oturumlar, Etkinlikler](../../azure-monitor/app/usage-segmentation.md)
    - [Huniler](../../azure-monitor/app/usage-funnels.md)
    - [Bekletme](../../azure-monitor/app/usage-retention.md)
    - [Kullanıcı Akışları](../../azure-monitor/app/usage-flows.md)
    - [Kullanıcı bağlamı Ekle](../../azure-monitor/app/usage-send-user-context.md)