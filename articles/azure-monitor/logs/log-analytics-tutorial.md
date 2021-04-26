---
title: Log Analytics öğreticisi
description: Bu öğreticiden Azure Izleyici 'de Log Analytics özelliklerini kullanarak bir günlük sorgusu çalıştırın ve sonuçlarını Azure portal analiz edin.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/07/2020
ms.openlocfilehash: a5788e5ac64c1c74a06c94c5634a05315ee098b8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799301"
---
# <a name="log-analytics-tutorial"></a>Log Analytics öğreticisi
Log Analytics, Azure Izleyici günlükleri tarafından toplanan verilerden günlük sorgularını düzenlemek ve çalıştırmak ve sonuçları etkileşimli olarak analiz etmek için Azure portal bir araçtır. Belirli ölçütlere uyan kayıtları almak, eğilimleri belirlemek, desenleri analiz etmek ve verilerinize çeşitli Öngörüler sağlamak için Log Analytics sorguları kullanabilirsiniz. 

Bu öğretici Log Analytics arabiriminde size rehberlik eder, bazı temel sorgularla başlamanıza yardımcı olur ve sonuçlarla nasıl çalışacağınızı gösterir. Şunları öğreneceksiniz:

> [!div class="checklist"]
> * Günlük veri şemasını anlama
> * Basit sorgular yazma ve çalıştırma ve sorgular için zaman aralığını değiştirme
> * Sorgu sonuçlarını filtreleme, sıralama ve gruplama
> * Sorgu sonuçlarının görsellerini görüntüleme, değiştirme ve paylaşma
> * Sorguları ve sonuçları yükleme, dışarı aktarma ve kopyalama

> [!IMPORTANT]
> Bu öğretici, sorgunun kendisiyle çalışmak yerine bir sorgu oluşturmak ve çalıştırmak için Log Analytics özelliklerini kullanır. Bir sorgu oluşturmak ve başka bir örnek sorgu kullanmak için Log Analytics özelliklerden yararlanabilirsiniz. Sorguların sözdizimini öğrenmeye ve sorgunun kendisini doğrudan düzenlemeyle çalışmaya başladığınızda [kusto sorgu dili öğreticisine](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)gidin. Bu öğretici, bu öğreticide öğrentireceğinizi pek çok özellikten yararlanarak Log Analytics düzenleyebileceğiniz ve çalıştırabileceğiniz birkaç örnek sorgu üzerinde yol gösterir.


## <a name="prerequisites"></a>Önkoşullar
Bu öğretici, örnek sorguları destekleyen çok sayıda örnek veri içeren [Log Analytics tanıtım ortamını](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)kullanır. Ayrıca kendi Azure aboneliğinizi de kullanabilirsiniz, ancak aynı tablolarda verileriniz olmayabilir.

## <a name="open-log-analytics"></a>Log Analytics açın
[Log Analytics demo ortamını](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade) açın veya aboneliğinizdeki Azure Izleyici menüsünden **Günlükler** ' i seçin. Bu, ilk kapsamı, sorgunuzun bu çalışma alanındaki tüm verilerden seçim olacağı bir Log Analytics çalışma alanına ayarlar. Azure kaynağının menüsünden **Günlükler** ' i seçerseniz, kapsam yalnızca bu kaynaktaki kayıtlar olarak ayarlanır. Kapsam hakkındaki ayrıntılar için bkz. [günlük sorgu kapsamı](./scope.md) .

Kapsamı ekranın sol üst köşesinde görüntüleyebilirsiniz. Kendi ortamınızı kullanıyorsanız, farklı bir kapsam seçme seçeneği görürsünüz, ancak bu seçenek tanıtım ortamında kullanılamaz.

[![Sorgu kapsamı](media/log-analytics-tutorial/scope.png)](media/log-analytics-tutorial/scope.png#lightbox)

## <a name="table-schema"></a>Tablo şeması
Ekranın sol tarafında, geçerli kapsamda kullanılabilir olan tabloları incelemenize olanak sağlayan **Tablolar** sekmesi bulunur. Bunlar varsayılan olarak **çözüme** göre gruplandırılır, ancak gruplamalarını değiştirirsiniz veya filtrelemenizi sağlayabilirsiniz. 

**Günlük yönetimi** çözümünü genişletin ve **AzureActivity** tablosunu bulun. Şemasını görüntülemek için tabloyu genişletebilir veya onunla ilgili ek bilgileri göstermek için adının üzerine gelin. 

[![Tablolar görünümü](media/log-analytics-tutorial/table-details.png)](media/log-analytics-tutorial/table-details.png#lightbox)

Her bir tabloyu ve sütunlarını belgeleyen Tablo başvurusuna gitmek için **daha fazla bilgi** ' ye tıklayın. Tablodaki birkaç son kayda hızlı bir bakış sağlamak için **Verileri Önizle** ' ye tıklayın. Bu, bir sorguyu gerçekten çalıştırmadan önce beklediğiniz verilerin olduğundan emin olmak için yararlı olabilir.

[![Örnek veriler](media/log-analytics-tutorial/sample-data.png)](media/log-analytics-tutorial/sample-data.png#lightbox)

## <a name="write-a-query"></a>Bir sorgu yazın
Şimdi de **AzureActivity** tablosunu kullanarak bir sorgu yazalım. Sorgu penceresine eklemek için adına çift tıklayın. Ayrıca, doğrudan pencereye yazabilir ve hatta geçerli kapsamdaki ve KQL komutlarında bulunan tabloların adlarını tamamlamaya yardımcı olacak IntelliSense ' i kullanabilirsiniz.

Bu, yazdığımız en basit sorgudur. Yalnızca bir tablodaki tüm kayıtları döndürür. **Çalıştır** düğmesine tıklayarak veya imleç sorgu metninde herhangi bir yere konumlanarak SHIFT + enter tuşlarına basarak çalıştırın.

[![Sorgu sonuçları](media/log-analytics-tutorial/query-results.png)](media/log-analytics-tutorial/query-results.png#lightbox)

Sonuçlara sahip olduğumuz hakkında bilgi alabilirsiniz. Sorgu tarafından döndürülen kayıt sayısı sağ alt köşede görüntülenir. 

## <a name="filter"></a>Filtre

Döndürülen kayıt sayısını azaltmak için sorguya bir filtre ekleyelim. Sol bölmedeki **filtre** sekmesini seçin. Bu, sonuçları filtrelemek için kullanabileceğiniz sorgu sonuçlarında farklı sütunlar gösterir. Bu sütunlardaki en üstteki değerler bu değere sahip kayıt sayısıyla birlikte görüntülenir. **CategoryValue** ' ın altında **Yönetim** ' e tıklayın ve ardından **& Çalıştır**' ı uygulayın 

[![Sorgu bölmesi](media/log-analytics-tutorial/query-pane.png)](media/log-analytics-tutorial/query-pane.png#lightbox)

Sorguya, seçtiğiniz değere sahip bir **WHERE** ifadesinin eklendiği. Sonuçlar artık, kayıt sayısının azaldığına bakmak için yalnızca bu değere sahip kayıtları içerir.

[![Filtre uygulanan sorgu sonuçları](media/log-analytics-tutorial/query-results-filter-01.png)](media/log-analytics-tutorial/query-results-filter-01.png#lightbox)


## <a name="time-range"></a>Zaman aralığı
Log Analytics çalışma alanındaki tüm tablolar, **TimeGenerated** adlı ve kaydın oluşturulduğu zaman olan bir sütuna sahiptir. Tüm sorguların, sonuçları bu Aralık içinde **TimeGenerated** değeri olan kayıtlarla sınırlayan bir zaman aralığı vardır. Zaman aralığı, sorguda veya ekranın en üstündeki seçiciyle ayarlanabilir.

Varsayılan olarak, sorgu son 24 saat için kayıtlar formunu döndürür. **Zaman aralığı** açılan listesini seçin ve **7 güne** değiştirin. Sonuçları döndürmek için yeniden **Çalıştır** ' a tıklayın. Sonuçların döndürüldüğünü görebilirsiniz, ancak sonuçların tümünü görmedik ve burada bir ileti sunuyoruz. Bunun nedeni Log Analytics en fazla 30.000 kayıt döndürebildiğinden ve sorgumuz bundan daha fazla kayıt döndürdüğünden. 

[![Zaman aralığı](media/log-analytics-tutorial/query-results-max.png)](media/log-analytics-tutorial/query-results-max.png#lightbox)


## <a name="multiple-query-conditions"></a>Birden çok sorgu koşulu
Başka bir filtre koşulu ekleyerek sonuçlarımızı daha fazla azaltalım. Bir sorgu tam olarak istediğiniz kayıt kümesini hedeflemek için herhangi bir sayıda filtre içerebilir. **ActivityStatusValue** altında **Success** ' ı seçin ve **Çalıştır & Uygula**' ya tıklayın. 

[![Sorgu sonuçları birden çok filtre](media/log-analytics-tutorial/query-results-filter-02.png)](media/log-analytics-tutorial/query-results-filter-02.png#lightbox)


## <a name="analyze-results"></a>Sonuçları analiz etme
Sorgu yazma ve çalıştırmanıza yardımcı olmaya ek olarak, Log Analytics sonuçlarla çalışmaya yönelik özellikler sağlar. Tüm sütunlarının değerlerini görüntülemek için bir kayıt genişleterek başlayın.

[![Kaydı Genişlet](media/log-analytics-tutorial/expand-record.png)](media/log-analytics-tutorial/expand-record.png#lightbox)

Sonuçları sütuna göre sıralamak için herhangi bir sütunun adına tıklayın. Filtre koşulunu sağlamak için yanındaki filtre simgesine tıklayın. Bu, sorgu yeniden çalıştırıldığında bu filtrenin temizlenmesi dışında, sorgunun kendine bir filtre koşulu eklemeye benzer. Bir kayıt kümesini etkileşimli çözümlemenin bir parçası olarak hızlıca çözümlemek istiyorsanız bu yöntemi kullanın.

Örneğin, kayıtları tek bir arayanla sınırlamak için **Callerıpaddress** sütununda bir filtre ayarlayın. 

[![Sorgu sonuçları filtresi](media/log-analytics-tutorial/query-results-filter.png)](media/log-analytics-tutorial/query-results-filter.png#lightbox)

Sonuçları filtrelemek yerine, kayıtları belirli bir sütuna göre gruplandırabilirsiniz. Yeni oluşturduğunuz filtrenin işaretini kaldırın ve sonra **Grup sütunları** kaydırıcısını açın. 

[![Sütunları Gruplandır](media/log-analytics-tutorial/query-results-group-columns.png)](media/log-analytics-tutorial/query-results-group-columns.png#lightbox)

Şimdi **Callerıpaddress** sütununu gruplandırma satırına sürükleyin. Sonuçlar artık bu sütuna göre düzenlenmiştir ve analiz etmenize yardımcı olmak için her bir grubu daraltabilirsiniz.

[![Sorgu sonuçları gruplandırılmış](media/log-analytics-tutorial/query-results-grouped.png)](media/log-analytics-tutorial/query-results-grouped.png#lightbox)

## <a name="work-with-charts"></a>Grafiklerle çalışma
Bir grafikte görüntüleyebilmemiz için sayısal verileri kullanan bir sorguya göz atalım. Sorgu oluşturmak yerine örnek bir sorgu seçeceğiz.

Sol bölmedeki **sorgular** ' a tıklayın. Bu bölmede sorgu penceresine ekleyebileceğiniz örnek sorgular yer alır. Kendi çalışma alanınızı kullanıyorsanız, birden fazla kategoride birçok sorgunuz olması gerekir, ancak demo ortamını kullanıyorsanız yalnızca tek bir **Log Analytics çalışma alanı** kategorisi görebilirsiniz. Kategorideki sorguları görüntülemek için bunu genişletin.

**Yanıt koduna göre Istek sayısı** adlı sorguya tıklayın. Bu, sorguyu sorgu penceresine ekler. Yeni sorgunun sıfırdan boş bir satırla ayrıldığına dikkat edin. KQL 'deki bir sorgu boş bir satır ile karşılaştığında sona erer, bu nedenle bunlar ayrı sorgular olarak görülür. 

[![Yeni sorgu](media/log-analytics-tutorial/example-query.png)](media/log-analytics-tutorial/example-query.png#lightbox)

Geçerli sorgu, imlecin bulunduğu bir sorgudur. İlk sorgunun vurgulandığını ve geçerli sorgu olduğunu görebilirsiniz. Yeni sorgudaki herhangi bir yere tıklayarak seçin ve ardından **Çalıştır düğmesine tıklayarak çalıştırın.**

[![Sorgu sonuçları grafiği](media/log-analytics-tutorial/example-query-output-chart.png)](media/log-analytics-tutorial/example-query-output-chart.png#lightbox)

Bu çıkışın son sorgu gibi bir tablo yerine bir grafik olduğuna dikkat edin. Bunun nedeni, örnek sorgusunun sonda bir [render](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) komutu kullanmaörneğidir. Grafik ile birlikte çalışmak için başka bir türe değiştirme gibi çeşitli seçenekler olduğuna dikkat edin.

Sorgunun çıkışını tablo olarak görüntülemek için **sonuçları** seçmeyi deneyin. 

[![Sorgu sonuçları tablosu](media/log-analytics-tutorial/example-query-output-table.png)](media/log-analytics-tutorial/example-query-output-table.png#lightbox)



## <a name="next-steps"></a>Sonraki adımlar

Log Analytics nasıl kullanacağınızı öğrendiğinizden, günlük sorgularını kullanma öğreticisini tamamlayabilirsiniz.
> [!div class="nextstepaction"]
> [Azure Izleyici günlük sorgularını yazma](get-started-queries.md)
