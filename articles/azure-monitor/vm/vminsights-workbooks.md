---
title: Çalışma kitapları ile etkileşimli raporlar VM öngörüleri oluşturma
description: VM öngörüleri için önceden tanımlı ve özel parametreli çalışma kitapları ile karmaşık raporlamayı kolaylaştırın.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 695aa760d554946883f72692f0bed1babad933b1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568809"
---
# <a name="create-interactive-reports-vm-insights-with-workbooks"></a>Çalışma kitapları ile etkileşimli raporlar VM öngörüleri oluşturma

Çalışma kitapları metin, [günlük sorgularını](/azure/data-explorer/kusto/query/), ölçümleri ve parametreleri zengin etkileşimli raporlara birleştirir. Çalışma kitapları aynı Azure kaynaklarına erişimi olan diğer takım üyeleri tarafından düzenlenebilir.

Çalışma kitapları şu senaryolar için yararlıdır:

* Daha önce ilgilendiğiniz ölçümleri bilmiyorsanız sanal makinenizin kullanımını keşfetme: CPU kullanımı, disk alanı, bellek, ağ bağımlılıkları vb. Diğer kullanım analizi araçlarının aksine, çalışma kitapları birden çok görselleştirme ve analizler birleştirerek bu tür bir serbest biçim araştırması için harika hale getirir.
* Son sağlanan VM 'nin nasıl çalıştığını açıklayan, anahtar sayaçları ve diğer günlük olayları için ölçümleri gösterir.
* SANAL makinenizin yeniden boyutlandırma denemenizin sonuçlarını takımınızın diğer üyeleriyle paylaşma. Denemeye ilişkin hedefleri açıklayabileceğiniz için, her bir metriğin hedefin üstünde mi yoksa altında mı olduğunu gösteren açık çağrı durumuyla birlikte denemeyi değerlendirmek için kullanılan her kullanım ölçümünü ve analiz sorgularını gösterebilirsiniz.
* SANAL makinenizin kullanımıyla ilgili bir kesinti oluşmasını, verileri, metin açıklamasını birleştirerek ve gelecekte kesintiye neden olan kesintileri önlemeye yönelik sonraki adımlara ilişkin bir tartışmayı raporlama.

Aşağıdaki tabloda, sanal makine öngörülerinin, başlamanızı sağlamak için içerdiği çalışma kitapları özetlenmektedir.

| Çalışma Kitabı | Description | Kapsam |
|----------|-------------|-------|
| Performans | , Etkinleştirdiğiniz tüm Log Analytics performans sayaçlarından yararlanan tek bir çalışma kitabında En Iyi N liste ve grafik görünümümüzün özelleştirilebilir bir sürümünü sağlar.| Birden çok VM |
| Performans sayaçları | Çok sayıda performans sayacı üzerinde Ilk N grafik görünümü. | Birden çok VM |
| Bağlantılar | Bağlantılar, izlenen sanal makinelerinizden gelen ve giden bağlantıların derinlemesine bir görünümünü sağlar. | Birden çok VM |
| Etkin Bağlantı Noktaları | İzlenen sanal makinelerdeki bağlantı noktalarına ve bunların seçili zaman diliminde etkinliklerine bağlı işlemlerin bir listesini sağlar. | Birden çok VM |
| Açık Bağlantı Noktaları | İzlenen sanal makinelerinizdeki açık bağlantı noktası sayısını ve bu açık bağlantı noktalarıyla ilgili ayrıntıları sağlar. | Birden çok VM |
| Başarısız Bağlantılar | İzlenen sanal makinelerinizdeki başarısız bağlantı sayısını, başarısızlık eğilimi 'ni ve hata yüzdesinin zaman içinde arttığını görüntüleyin. | Birden çok VM |
| Güvenlik ve Denetim | Genel bağlantılarda, kötü amaçlı bağlantılardan ve IP uç noktalarının küresel olarak bulunduğu TCP/IP trafiğinizin analizi.  Tüm özellikleri etkinleştirmek için güvenlik algılamayı etkinleştirmeniz gerekir. | Birden çok VM |
| TCP Trafiği | Bir kılavuzda izlenen sanal makinelerinize ve gönderdikleri, alınmış ve toplam ağ trafiğine yönelik bir derecelendirilmiş rapor ve eğilim çizgisi olarak görüntülenen. | Birden çok VM |
| Trafik Karşılaştırması | Bu çalışma kitapları, tek bir makine veya makine grubu için ağ trafiği eğilimlerini karşılaştırmanızı sağlar. | Birden çok VM |
| Performans | , Etkinleştirdiğiniz tüm Log Analytics performans sayaçlarından yararlanan performans görünümümüzün özelleştirilebilir bir sürümünü sağlar. | Tek VM | 
| Bağlantılar | Bağlantılar, sanal makinenizin gelen ve giden bağlantılarının derinlemesine bir görünümünü sağlar. | Tek VM |
 
## <a name="creating-a-new-workbook"></a>Yeni çalışma kitabı oluşturma

Çalışma kitabı, bağımsız olarak düzenlenebilir grafiklerden, tablolardan, metinden ve giriş denetimlerinden oluşan bölümlerden oluşur. Çalışma kitaplarını daha iyi anlamak için, bir şablon açıp özel bir çalışma kitabı oluşturmaya ilerleyerek başlayalım. 

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. **Sanal makineleri** seçin.

3. Listeden bir VM seçin.

4. VM sayfasında, **izleme** bölümünde **Öngörüler**' i seçin.

5. VM öngörüleri sayfasında **performans** veya **haritalar** sekmesini seçin ve sayfadaki bağlantıdan **çalışma kitaplarını görüntüle** ' yi seçin. Aşağı açılan listeden **Galeriye git**' i seçin.

    ![Çalışma kitabı açılır listesinin ekran görüntüsü](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Bu işlem, başlamanıza yardımcı olması için çalışma kitabı galerisini bir dizi önceden oluşturulmuş çalışma kitabıyla başlatır.

7. **Yeni**' ye tıklayarak yeni bir çalışma kitabı oluşturun.

    ![Çalışma kitabı galerinin ekran görüntüsü](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Çalışma kitabı bölümlerini düzenleme

Çalışma kitaplarında iki mod vardır: **Düzen modu** ve **Okuma modu**. Yeni bir çalışma kitabı ilk kez başlatıldığında, **Düzen modunda** açılır. Bu, başka bir şekilde gizlenen tüm adımlar ve parametreler dahil olmak üzere çalışma kitabının tüm içeriğini gösterir. **Okuma modu** , Basitleştirilmiş bir rapor stili görünümü sunar. Okuma modu, bir rapor oluşturma ile ilgili karmaşıklığın dışında, yalnızca değişiklik için gerektiğinde yalnızca birkaç tıklamayla oluşan bir rapor oluşturmaya yönelik karmaşıklığın ötede olmasını sağlar.

![Azure Izleyici 'deki sanal makineler çalışma kitabı bölümünün, Düzenle denetimleri vurgulanmış şekilde, yeni bir çalışma kitabını görüntüleme modunda gösteren ekran görüntüsü.](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Bir bölümü düzenlemenizi bitirdiğinizde, bölümünün sol alt köşesinde bulunan **Düzenle** ' ye tıklayın.

2. Bir bölümün bir kopyasını oluşturmak için **Bu bölümü Kopyala** simgesine tıklayın. Yinelenen bölümler oluşturmak, önceki yinelemeleri kaybetmeden bir sorgu üzerinde yinelemek için harika bir yoldur.

3. Çalışma kitabındaki bir bölümü yukarı taşımak için **Yukarı taşı** veya **aşağı taşı** simgesine tıklayın.

4. Bir bölümü kalıcı olarak kaldırmak için **Kaldır** simgesine tıklayın.

## <a name="adding-text-and-markdown-sections"></a>Metin ve Markdown bölümleri ekleme

Çalışma kitaplarınıza başlıklar, açıklamalar ve yorum eklemek, bir dizi tablo ve grafiği bir anlatıcı haline dönüştürmenize yardımcı olur. Çalışma kitaplarındaki metin bölümleri, metin biçimlendirme için başlıklar, kalın, italik ve madde işaretli listeler gibi [markı sözdizimini](https://daringfireball.net/projects/markdown/) destekler.

Çalışma kitabınıza bir metin bölümü eklemek için, çalışma kitabının alt kısmındaki veya herhangi bir bölümün altındaki **metin ekle** düğmesini kullanın.

## <a name="adding-query-sections"></a>Sorgu bölümleri ekleme

![Çalışma kitaplarında sorgu bölümü](media/vminsights-workbooks/005-workbook-query-section.png)

Çalışma kitabınıza sorgu bölümü eklemek için, çalışma kitabının alt kısmındaki **Sorgu Ekle** düğmesini ya da herhangi bir bölümün alt kısmını kullanın.

Sorgu bölümleri son derece esnektir ve şunun gibi soruları yanıtlamak için kullanılabilir:

* Ağ trafiğinden bir artış ile aynı süre boyunca CPU kullanımım nasıl?
* Son ayda kullanılabilir disk alanı eğilimi nedir?
* Son iki hafta içinde VM deneyimimin kaç ağ bağlantı hatasından oluşması? 

Ayrıca, çalışma kitabını başlattığınız sanal makine bağlamından sorgulanmayı da sınırlı değilsiniz. Bu kaynaklara erişim iznine sahip olduğunuz sürece, birden fazla sanal makinede sorgulama yapabilirsiniz ve Log Analytics çalışma alanları.

Diğer Log Analytics çalışma alanlarından veya **çalışma alanı** tanımlayıcısı kullanarak belirli bir Application Insights uygulamadan veri eklemek için. Çapraz kaynak sorguları hakkında daha fazla bilgi edinmek için [resmi kılavuza](../logs/cross-workspace-query.md)bakın.

### <a name="advanced-analytic-query-settings"></a>Gelişmiş analitik sorgu ayarları

Her bölümde kendi gelişmiş ayarları vardır. Bu ayarlar, ![ ](media/vminsights-workbooks/006-settings.png) **Parametreler Ekle** düğmesinin sağında bulunan denetim çalışma kitapları bölümü denetimleri Düzenle simgesi aracılığıyla erişilebilir.

![Azure Izleyici 'nin sanal makineler çalışma kitabı bölümündeki Gelişmiş ayarlar iletişim kutusunun ekran görüntüsü. İletişim kutusunu açan simge vurgulanır.](media/vminsights-workbooks/007-settings-expanded.png)

| Ayar | Açıklama |
| ---------------- |:-----|
| Özel genişlik    | Bir öğe rastgele bir boyut oluşturur, böylece grafikleri ve tablolarınızı zengin etkileşimli raporlarda daha iyi düzenlemenizi sağlayabilirsiniz.  |
| Koşullu olarak görünür | Okuma modundayken bir parametreye göre adımları gizlemek için belirtin. |
| Bir parametreyi dışarı aktarma| Kılavuzda veya grafikte seçili bir satırın, sonraki adımların değer değiştirmesine veya görünür hale gelmesine neden olmasını sağlar.  |
| Düzenlenmediğinden sorguyu göster | Okuma modundayken bile grafiğin veya tablonun üzerindeki sorguyu görüntüler.
| Düzenlenmediğinden Analytics 'te aç düğmesini göster | Tek tıklamayla erişime izin vermek için grafiğin sağ köşesine mavi analiz simgesini ekler.|

Bu ayarların çoğu oldukça sezgisel, ancak **bir parametreyi dışarı aktarmayı** anlamak için bu işlevi kullanan bir çalışma kitabını incelemek daha iyidir.

Önceden oluşturulmuş çalışma kitaplarından biri olan **TCP trafiğinden**, bir VM 'den gelen bağlantı ölçümleri hakkında bilgi sağlanır.

Çalışma kitabının ilk bölümü günlük sorgusu verilerini temel alır. İkinci bölüm de günlük sorgu verilerine dayalıdır, ancak ilk tablodaki bir satırı seçmek, grafiklerin içeriğini etkileşimli olarak güncelleştirir:

![Azure Izleyici 'de sanal makineler bölümünün, önceden oluşturulan çalışma kitabının TCP trafiğini gösteren ekran görüntüsü.](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

Bir öğe seçildiğinde, tablonun günlük sorgusunda etkinleştirilen **bir parametre gelişmiş ayarlarını dışarı aktarmak** için davranış mümkündür.

!["Öğe seçildiğinde, bir parametreyi dışarı aktar" seçeneği işaretli bir sanal makineler çalışma kitabı için Gelişmiş ayarlar iletişim kutusunun ekran görüntüsü.](media/vminsights-workbooks/009-settings-export.png)

İkinci günlük sorgusu daha sonra bölüm başlığı ve grafikler tarafından kullanılan bir değer kümesi oluşturmak için bir satır seçildiğinde, bu değerleri kullanır. Hiçbir satır seçilmezse, bölüm başlığını ve grafiklerini gizler. 

Örneğin, ikinci bölümdeki Hidden parametresi, kılavuzda seçili olan satırdaki şu başvuruyu kullanır:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Ölçüm bölümleri ekleme

Ölçüm bölümleri, Azure Izleyici ölçüm verilerini etkileşimli raporlarınıza katmak için size tam erişim sağlar. VM öngörüleri ' nde, önceden oluşturulmuş çalışma kitapları genellikle ölçüm verileri yerine analitik sorgu verileri içerir.  Ölçüm verileriyle çalışma kitapları oluşturmayı tercih edebilirsiniz, böylece her iki özellikten en iyi şekilde tek bir yerde yararlanmanızı sağlayabilirsiniz. Ayrıca, erişiminiz olan aboneliklerdeki kaynaklardan ölçüm verilerini çekme olanağınız da vardır.

Aşağıda, bir çalışma kitabına eklenen ve CPU performansının kılavuz bir görselleştirmesi sağlayan sanal makine verilerinin bir örneği verilmiştir:

![Azure Izleyici 'de bir sanal makine çalışma kitabının ölçümler bölümünün ekran görüntüsü. Her bir sanal makine için CPU performansı grafiksel olarak gösterilir.](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Parametre bölümleri ekleme

Çalışma kitabı parametreleri, sorgu veya metin bölümlerini el ile düzenlemeniz gerekmeden çalışma kitabındaki değerleri değiştirmenize izin verir. Bu, temel analiz sorgu dilini anlamaya gerek gereksinimini ortadan kaldırır ve çalışma kitabı tabanlı raporlamanın potansiyel kitlelerini büyük ölçüde genişletir.

Parametre değerleri, gibi küme ayraçları içine yerleştirerek sorgu, metin veya diğer parametre bölümlerinde değişir ``{parameterName}`` . Parametre adları, JavaScript tanımlayıcıları, alfabetik karakterler veya alt çizgiler, ardından alfasayısal karakterler veya alt çizgiler ile benzer kurallarla sınırlıdır. Örneğin, **a1** 'ye izin verilir, ancak **1a** buna izin verilmez.

Parametreler doğrusal, bir çalışma kitabının üstünden başlayıp daha sonraki adımlara akar.  Daha sonra bir çalışma kitabında bildirildiği parametreler, daha önce belirtilen parametreleri geçersiz kılabilir. Bu Ayrıca, daha önce tanımlanan parametrelerden değerlere erişmek için sorgular kullanan parametrelere izin verir. Bir parametrenin adımının kendisi içinde, parametreler aynı adımda daha önce belirtilen bir parametreye bağlı olabileceği gibi, parametreler de doğrusal, soldan sağa doğru yapılır.
 
Şu anda desteklenen dört farklı parametre türü vardır:

| Parametre | Açıklama |
| ---------------- |:-----|
| Metin    | Kullanıcının bir metin kutusunu düzenlemesine izin verir ve isteğe bağlı olarak varsayılan değeri dolduracak bir sorgu sağlayabilirsiniz. |
| Açılan | Kullanıcının bir değer kümesinden seçmesine izin verir. |
| Zaman aralığı seçici| Kullanıcının önceden tanımlanmış bir zaman aralığı değerleri kümesinden seçmesine izin verir veya özel bir zaman aralığından seçim yapmasına olanak tanır.|
| Kaynak seçici | Kullanıcının çalışma kitabı için seçilen kaynaklardan seçmesine izin verir.|

### <a name="using-a-text-parameter"></a>Metin parametresi kullanma

Metin kutusundaki Kullanıcı türlerinin değeri, kaçış veya tırnak içine alma olmadan doğrudan sorguda yer alır. İhtiyacınız olan değer bir dize ise, sorgunun parametre etrafında tırnak işareti olmalıdır ( **' {Parameter} '** gibi).

Metin parametresi bir metin kutusundaki değerin her yerde kullanılmasına izin verir. Tablo adı, sütun adı, işlev adı, işleç vb. olabilir.  Metin parametre türü, **analiz sorgusundan varsayılan değeri Al** ayarına sahiptir ve bu, çalışma kitabı yazarının bu metin kutusu için varsayılan değeri doldurmak üzere bir sorgu kullanmasına izin verir.

Bir günlük sorgusundan varsayılan değeri kullanırken, varsayılan değer olarak yalnızca ilk satırın ilk değeri (satır 0, sütun 0) kullanılır. Bu nedenle, sorgunuzu yalnızca bir satır ve bir sütun döndürecek şekilde sınırlandırmaya önerilir. Sorgu tarafından döndürülen diğer veriler yok sayılır. 

Sorgunun döndürdüğü değer, doğrudan kaçış veya tırnak işareti olmadan değişir. Sorgu hiçbir satır döndürürse, parametrenin sonucu boş bir dizedir (parametre gerekli değilse) ya da tanımsız (parametre gerekliyse).

### <a name="using-a-drop-down"></a>Açılan bir liste kullanma

Açılan parametre türü, bir veya daha fazla değer seçimine izin veren bir açılan denetim oluşturmanıza olanak sağlar.

Açılan menü, bir günlük sorgusu veya JSON ile doldurulur. Sorgu bir sütun döndürürse, bu sütundaki değerler hem değer hem de açılan denetimdeki etikettir. Sorgu iki sütun döndürürse, ilk sütun değerdir ve ikinci sütun açılan kutuda gösterilen etikettir. Sorgu üç sütun döndürürse, üçüncü sütun bu açılan kutuda varsayılan seçimi göstermek için kullanılır. Bu sütun herhangi bir tür olabilir, ancak en basit değer bool veya sayısal türler kullanmaktır; burada 0, false, 1 ise doğrudur.

Sütun bir dize türü ise, null/boş dize yanlış olarak değerlendirilir ve diğer tüm değerler doğru olarak değerlendirilir. Tek seçim açılan listeleri için, true değeri olan ilk değer varsayılan seçim olarak kullanılır.  Çoklu seçim açılan listeleri için, true değeri olan tüm değerler varsayılan seçili küme olarak kullanılır. Açılır öğelerdeki öğeler, sorgunun satırları döndürdüğü sırada gösterilir. 

Bağlantılara Genel Bakış raporunda bulunan parametrelere göz atalım. **Yön** seçeneğinin yanındaki Düzenle simgesine tıklayın.

![Azure Izleyici 'de rapor parametrelerini ekleme ve düzenlemeyle ilgili bölümün ekran görüntüsü. Yön parametresinin düzenleme simgesi seçilidir.](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Bu işlem **parametre Düzenle** menü öğesini başlatacaktır.

![Parametre Düzenle iletişim kutusunun ekran görüntüsü. Parametre adı Yöntür, parametre türü açılır ve JSON 'dan veri al seçilidir.](media/vminsights-workbooks/012-workbook-edit-parameter.png)

JSON, içerikle doldurulmuş rastgele bir tablo oluşturmanıza olanak sağlar. Örneğin, aşağıdaki JSON açılan kutuda iki değer oluşturur:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Daha uygun bir örnek, bir performans sayaçları kümesinden ada göre seçim yapmak için açılan bir örnek kullanmaktır:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

Sorgu sonuçları şu şekilde görüntüler:

![Performans sayacı açılan kutusu](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

Açılır listeler, etkileşimli raporları özelleştirmeye ve oluşturmaya yönelik inanılmaz güçlü araçlardır.

### <a name="time-range-parameters"></a>Zaman aralığı parametreleri

Açılan parametre türü aracılığıyla kendi özel zaman aralığı parametresini de yapabilirsiniz, ancak aynı esneklik derecesine gerek duymuyorsanız, kutudan çıkar zaman aralığı parametre türünü de kullanabilirsiniz. 

Zaman aralığı parametre türlerinde beş dakikadan son 90 güne kadar geçen 15 varsayılan Aralık vardır. Ayrıca, özel zaman aralığı seçimine izin veren bir seçenek de vardır. Bu, raporun işlecinin, zaman aralığı için açık başlatma ve durdurma değerlerini seçmesine olanak sağlar.

### <a name="resource-picker"></a>Kaynak seçici

Kaynak seçici parametre türü, raporunuzu belirli kaynak türlerine göre kapsam yeteneği sağlar. Kaynak seçici türünü kullanan önceden oluşturulmuş bir çalışma kitabına bir örnek, **performans** çalışma kitabıdır.

![Çalışma alanları açılan kutusu](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Çalışma kitaplarını kaydetme ve ekibinizle paylaşma

Çalışma kitapları, çalışma kitapları galerisine nasıl eriştiğinize bağlı olarak bir Log Analytics çalışma alanı veya bir sanal makine kaynağı içinde kaydedilir. Çalışma kitabı, size özel olan **Raporlarım** bölümüne veya kaynağa erişimi olan herkesin erişebileceği **paylaşılan raporlar** bölümünde kaydedilebilir. Kaynaktaki tüm çalışma kitaplarını görüntülemek için Eylem çubuğundaki **Aç** düğmesine tıklayın.

Şu anda **Raporlarım** bir çalışma kitabını paylaşmak için:

1. Eylem çubuğunda **Aç** ' a tıklayın
2. "..." Düğmesine tıklayın. paylaşmak istediğiniz çalışma kitabının yanındaki düğme
3. **Paylaşılan raporlara taşı**' ya tıklayın.

Bir çalışma kitabını bir bağlantı ile veya e-posta ile paylaşmak için, eylem çubuğunda **paylaşma** ' ya tıklayın. Bağlantı alıcılarının, çalışma kitabını görüntülemek için Azure portal bu kaynağa erişmesi gerektiğini aklınızda bulundurun. Düzenleme yapmak için, alıcıların kaynak için en az katkıda bulunan izinleri olması gerekir.

Bir Azure panosuna çalışma kitabına bir bağlantı sabitlemek için:

1. Eylem çubuğunda **Aç** ' a tıklayın
2. "..." Düğmesine tıklayın. sabitlemek istediğiniz çalışma kitabının yanındaki düğme
3. **Panoya sabitle**' ye tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

- Sınırlamaları ve genel VM performansını belirlemek için bkz. [Azure VM performansını görüntüleme](vminsights-performance.md).

- Bulunan uygulama bağımlılıkları hakkında bilgi edinmek için bkz. [VM Insights haritasını görüntüleme](vminsights-maps.md).
