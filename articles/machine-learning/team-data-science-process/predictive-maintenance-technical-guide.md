---
title: Aerospace ekibi veri bilimi Işlemi için tahmine dayalı bakım kılavuzu
description: Aerospace, yardımcı programlar ve ulaşım içindeki tahmine dayalı bakım için çözüm şablonuna yönelik teknik kılavuz.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 3edeee8f41c806c90f32208c0c4f174c76ba38d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321995"
---
# <a name="technical-guide-to-the-solution-template-for-predictive-maintenance-in-aerospace"></a>Aerospace 'de tahmine dayalı bakım için çözüm şablonuna yönelik teknik kılavuz

> [!Important]
> Bu makale kullanımdan kaldırılmıştır. Aerospace 'de tahmine dayalı bakım hakkında tartışma hala geçerlidir, ancak geçerli bilgiler için [Iş kitlelerine yönelik çözüme genel bakış](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace)bölümüne bakın.


Çözüm şablonları, E2E demo oluşturma sürecini hızlandırmak için tasarlanmıştır. Dağıtılan bir şablon, gerekli bileşenlerle aboneliğinizi sağlar ve bunlar arasındaki ilişkileri oluşturur. Ayrıca, çözüm şablonunu dağıttıktan sonra yerel makinenize indirip yüklediğiniz bir veri Oluşturucu uygulamasındaki örnek verilerle veri işlem hattına da sahiptir. Oluşturucunun verileri, veri işlem hattını kapatır ve daha sonra Power BI panosunda görselleştirilebilen makine öğrenimi tahminleri oluşturmaya başlar.

Dağıtım işlemi, çözüm kimlik bilgilerinizi ayarlamak için çeşitli adımlarda size rehberlik eder. Dağıtım sırasında sağladığınız çözüm adı, Kullanıcı adı ve parola gibi kimlik bilgilerini kaydettiğinizden emin olun. 


Bu makalenin amaçları şunlardır:
- Aboneliğinizde sağlanan başvuru mimarisini ve bileşenlerini açıklama.
- Örnek verilerin kendi verilerinize nasıl değiştirileceğini gösterir. 
- Çözüm şablonunun nasıl değiştirileceğini gösterir.  

## <a name="overview"></a>Genel Bakış
![Tahmine dayalı bakım mimarisi](./media/predictive-maintenance-technical-guide/predictive-maintenance-architecture.png)

Çözümü dağıttığınızda, Olay Hub 'ı, Stream Analytics, HDInsight, Data Factory ve Machine Learning dahil olmak üzere Azure hizmetlerini etkinleştirir. Mimari diyagramı, Aerospace çözüm şablonu için tahmine dayalı bakımın nasıl oluşturulduğunu gösterir. Bu Azure portal hizmetleri, çözüm dağıtımıyla oluşturulan çözüm şablonu diyagramında (örneğin, ilgili işlem hattı etkinlikleri çalıştırmak için gerekli olan ve daha sonra silinmeleri gerektiğinde isteğe bağlı olarak sağlanan HDInsight hariç) tıklatarak inceleyebilirsiniz.
[Diyagramın tam boyutlu bir sürümünü](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png)indirin.

Aşağıdaki bölümlerde çözüm bölümleri açıklanır.

## <a name="data-source-and-ingestion"></a>Veri kaynağı ve alımı
### <a name="synthetic-data-source"></a>Yapay veri kaynağı
Bu şablon için, kullanılan veri kaynağı, başarılı dağıtımdan sonra yerel olarak çalıştırdığınız indirilen bir masaüstü uygulamasından oluşturulur.

Bu uygulamayı indirme ve yükleme yönergelerini bulmak için, çözüm şablonu diyagramında, tahmine dayalı bakım veri Oluşturucu olan ilk düğümü seçin. Yönergeler, Özellikler çubuğunda bulunur. Bu uygulama, çözüm akışının geri kalanında kullanılan veri noktaları veya olaylar ile [Azure Event hub](#azure-event-hub) hizmetini besler. Bu veri kaynağı, [turbofan altyapı performansında azalma benzetim veri kümesi](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan)kullanılarak [NASA veri deposundaki](https://c3.nasa.gov/dashlink/resources/139/) genel kullanıma açık verilerden türetilir.

Olay oluşturma uygulaması, Azure Olay Hub 'ını yalnızca bilgisayarınızda yürütüldüğü sırada doldurur.  

### <a name="azure-event-hub"></a>Azure Event Hub  
[Azure Olay Hub 'ı](https://azure.microsoft.com/services/event-hubs/) hizmeti, yapay veri kaynağı tarafından belirtilen girişin alıcıdır.

## <a name="data-preparation-and-analysis"></a>Veri hazırlama ve çözümleme  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
[Azure Olay Hub](#azure-event-hub) 'ı hizmetinden gelen giriş akışında neredeyse gerçek zamanlı analiz sağlamak için [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) kullanın. Daha sonra, [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) hizmeti tarafından işlenmek üzere tüm ham gelen olayları [Azure depolama](https://azure.microsoft.com/services/storage/) hizmeti 'ne arşivleyerek [Power BI](https://powerbi.microsoft.com) panosu üzerinde yayımlayın.

### <a name="hdinsight-custom-aggregation"></a>HDInsight özel toplama
Azure Stream Analytics kaynağı kullanılarak arşivlenen ham olaylara toplamalar sağlamak için HDInsight kullanarak [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) betikleri (Azure Data Factory tarafından düzenlenmiş) çalıştırın.

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning hizmeti](https://azure.microsoft.com/services/machine-learning/) ile alınan girişleri kullanarak belirli bir uçak altyapısının kalan kullanım ömrü (rul) için tahminleri yapın (Azure Data Factory tarafından düzenlenmiş). 

## <a name="data-publishing"></a>Veri yayımlama
### <a name="azure-sql-database"></a>Azure SQL Veritabanı
[Power BI](https://powerbi.microsoft.com) panosunda tüketilen Azure Machine Learning tarafından alınan tahminleri depolamak IÇIN [Azure SQL veritabanı](https://azure.microsoft.com/services/sql-database/) 'nı kullanın.

## <a name="data-consumption"></a>Veri tüketimi
### <a name="power-bi"></a>Power BI
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)tarafından sunulan toplamalar ve uyarıların yanı sıra [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)kullanılarak üretilmiş [Azure SQL veritabanında](https://azure.microsoft.com/services/sql-database/) depolanan rul öngörülerini içeren bir panoyu göstermek için [Power BI](https://powerbi.microsoft.com) kullanın.

## <a name="how-to-bring-in-your-own-data"></a>Kendi verilerinizi getirme
Bu bölümde, kendi verilerinizi Azure 'a nasıl getirebileceğinizi ve bu mimariye getirdiğiniz veriler için hangi alanların değişiklik gerektirdiğini açıklanmaktadır.

Veri kümeniz, bu çözüm şablonu için kullanılan [turbofan motor düşme simülasyonu veri kümesi](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) tarafından kullanılan veri kümesiyle eşleşiyor olabilir. Verilerinizin ve gereksinimlerin nasıl değiştirileceği, bu şablonu kendi verilerinize göre nasıl değiştireceğiniz konusunda çok önemlidir. 

Aşağıdaki bölümler, yeni bir veri kümesi kullanılmaya başlandığında değişiklik gerektiren şablonun parçalarını tartışır.

### <a name="azure-event-hub"></a>Azure Event Hub
Azure Olay Hub 'ı geneldir; veriler hub 'a CSV veya JSON biçiminde gönderilebilir. Azure Olay Hub 'ında özel bir işlem gerçekleşmez, ancak kendisine beslendiği verileri anlamanız önemlidir.

Bu belge, verilerinizin nasıl alınacağını tanımlamaz, ancak olay hub 'ı API 'Lerini kullanarak olayları veya verileri bir Azure Olay Hub 'ına kolayca gönderebilirsiniz.

### <a name="azure-stream-analytics"></a><a name="azure-stream-analytics-1"></a>Azure Stream Analytics
Veri akışlarından okuyup verileri istediğiniz sayıda kaynağa aktararak neredeyse gerçek zamanlı analiz sağlamak için Azure Stream Analytics kaynağını kullanın.

Aerospace çözüm şablonu için tahmine dayalı bakım için, Azure Stream Analytics sorgusu dört ayrı konuma çıkış ile Azure Event hub hizmetinden olayları kullanan dört alt sorgudan oluşur. Bu çıktılar üç Power BI veri kümesinden ve bir Azure depolama konumundan oluşur.

Azure Stream Analytics sorgu şu şekilde bulunabilir:

* Azure portal bağlanma
* ![Çözüm dağıtıldığında oluşturulan Stream Analytics işleri Stream Analytics simgesini bulma ](./media/predictive-maintenance-technical-guide/icon-stream-analytics.png) (*Örneğin*, tahmine dayalı bakım çözümü için **maintenancesa02asapbi** ve **maintenancesa02asablob** )
* Seçiminde
  
  * Sorgu girişini görüntülemek için ***girişler***
  * Sorgunun kendisini görüntülemek için ***sorgu***
  * Farklı çıkışları görüntülemek için ***çıkışlar***

Azure Stream Analytics sorgu oluşturma hakkındaki bilgiler, MSDN 'deki [Stream Analytics sorgu başvurusunda](/stream-analytics-query/stream-analytics-query-language-reference) bulunabilir.

Bu çözümde sorgular, bu çözüm şablonunun bir parçası olarak sunulan bir Power BI panosuna gelen veri akışı hakkında neredeyse gerçek zamanlı analiz bilgilerini içeren üç veri kümesini çıktı. Gelen veri biçimi hakkında örtülü bilgi olduğundan, bu sorgular veri biçiminizdeki şekilde değiştirilmeli.

İkinci Stream Analytics iş **maintenancesa02asablob** sorgusu, tüm [Olay Hub](https://azure.microsoft.com/services/event-hubs/) olaylarını [Azure Storage](https://azure.microsoft.com/services/storage/) 'a çıkarır ve bu nedenle, tam olay bilgileri depoya akışa alınarak veri biçiminizden bağımsız olarak hiçbir değişiklik gerektirmez.

### <a name="azure-data-factory"></a>Azure Data Factory
[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) hizmeti verilerin hareketini ve işlenmesini düzenler. Aerospace için tahmine dayalı bakım çözüm şablonunda Veri Fabrikası, çeşitli teknolojiler kullanarak verileri taşımak ve işlemek için üç işlem [hattı](../../data-factory/concepts-pipelines-activities.md) oluşur.  Çözüm dağıtımıyla oluşturulan çözüm şablonu diyagramının altındaki Data Factory düğümünü açarak veri fabrikanıza erişin. Veri kümelerinizin altındaki hatalar veri Oluşturucu başlatılmadan önce dağıtılmakta olan veri fabrikasından kaynaklanır. Bu hatalar yoksayılabilir ve veri fabrikasının çalışmasını engellemez.

![Data Factory veri kümesi hataları](./media/predictive-maintenance-technical-guide/data-factory-dataset-error.png)

Bu bölümde [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)bulunan gerekli işlem [hatları ve Etkinlikler](../../data-factory/concepts-pipelines-activities.md) açıklanmaktadır. Çözümün Diyagram görünümü aşağıda verilmiştir.

![Azure Data Factory](./media/predictive-maintenance-technical-guide/azure-data-factory.png)

Bu fabrikaya ait işlem hatlarından ikisi, verileri bölümlemek ve toplamak için kullanılan [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) betikleri içerir. Not edildiğinde, betikler kurulum sırasında oluşturulan [Azure Storage](https://azure.microsoft.com/services/storage/) hesabında bulunur. Bu konum şunlardır: maintenancesascript \\ \\ Script \\ \\ Hive \\ \\ (veya https://[çözümünüz adınız]. blob. Core. Windows. net/maintenancesascript).

[Azure Stream Analytics](#azure-stream-analytics-1) sorgulara benzer şekilde, [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) betiklerinin gelen veri biçimi hakkında örtülü bilgileri vardır ve veri biçiminizdeki olarak değiştirilmesi gerekir.

#### <a name="aggregateflightinfopipeline"></a>*Aggregateflightınfopipeline*
Bu [işlem hattı](../../data-factory/concepts-pipelines-activities.md) , [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) işi sırasında [Azure depolama](https://azure.microsoft.com/services/storage/) 'ya yerleştirilen verileri bölümlemek için [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) betiği çalıştıran bir [HDInsightLinkedService](/previous-versions/azure/dn893526(v=azure.100)) kullanan tek bir Activity- [hdınsighthive](../../data-factory/transform-data-using-hadoop-hive.md) etkinliği içerir.

Bu bölümlendirme görevinin [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) betiği, ***Aggregateflightınfo. HQL*** ' dir

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Bu işlem [hattı](../../data-factory/concepts-pipelines-activities.md) , son sonucu bu çözüm şablonuyla ilişkili [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) denemenizin puanlanması olan çeşitli etkinlikleri içerir.

Dahil edilen etkinlikler şunlardır:

* [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) denemesi için gereken toplamaları ve özellik mühendisliğini gerçekleştirmek üzere [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) betiği çalıştıran bir [HDInsightLinkedService](/previous-versions/azure/dn893526(v=azure.100)) kullanarak etkinlik [hdınsighthive](../../data-factory/transform-data-using-hadoop-hive.md) .
  Bu bölümlendirme görevinin [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) betiği, ***hazırlık emlinput. HQL***' dir.
* [Hdınsighthive](../../data-factory/transform-data-using-hadoop-hive.md) etkinlikten sonuçları [AzureMLBatchScoring](/previous-versions/azure/dn894009(v=azure.100)) etkinliğinin eriştiği tek bir [Azure Storage](https://azure.microsoft.com/services/storage/) blobuna taşınan [kopyalama](/previous-versions/azure/dn835035(v=azure.100)) etkinliği.
* [AzureMLBatchScoring](/previous-versions/azure/dn894009(v=azure.100)) etkinliği, sonuçları tek bir [Azure Storage](https://azure.microsoft.com/services/storage/) blobuna yerleştirerek [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) denemeyi çağırır.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Bu işlem [hattı](../../data-factory/concepts-pipelines-activities.md) , ***MLScoringPipeline*** [Azure Machine Learning](#azure-machine-learning) denemesinin sonuçlarını çözüm şablonu YÜKLEMESININ bir parçası olarak sağlanan [Azure SQL veritabanına](https://azure.microsoft.com/services/sql-database/) kopyalayan tek bir etkinlik ( [kopyalama](/previous-versions/azure/dn835035(v=azure.100)) etkinliği) içerir.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Bu çözüm şablonu için kullanılan [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) deneme, uçak altyapısının kalan yararlı ömrünü (rul) sağlar. Deneme, tüketilen veri kümesine özeldir ve üzerinde getirilen verilere özgü değişiklik veya değişiklik yapılmasını gerektirir.

## <a name="monitor-progress"></a>Ilerlemeyi izleme
Veri Oluşturucu başlatıldıktan sonra, işlem hattı, veri fabrikası tarafından verilen komutları takip eden bir şekilde çalışmaya başlar ve çözümünüzün farklı bileşenleri eyleme açılır. İşlem hattını izlemenin iki yolu vardır.

* Stream Analytics işlerden biri ham gelen verileri blob depolamaya yazar. Çözümü başarıyla dağıttığınızdan çözümünüzün BLOB depolama bileşeni ' ne tıklayın ve ardından sağ bölmede aç ' a tıkladığınızda, sizi [Azure Portal](https://portal.azure.com/)götürür. Bir kez daha sonra Bloblar ' a tıklayın. Sonraki panelde, kapsayıcıların bir listesini görürsünüz. **Maintenancesadata**' ye tıklayın. Sonraki panelde **rawData** klasörüdür. RawData klasörünün içinde Hour = 17 ve Hour = 18 gibi adlara sahip klasörler bulunur. Bu klasörlerin varlığı, bilgisayarınızda ham verilerin oluşturulduğunu ve BLOB depolama alanında depolandığını belirtir. CSV dosyalarını bu klasörlerde MB olarak sınırlı boyutlarda görmeniz gerekir.
* İşlem hattının son adımı, verileri (örneğin, makine öğrenimine yönelik tahminler) SQL veritabanı 'na yazmaktır. Verilerin SQL veritabanı 'nda görünmesi için en fazla üç saat beklemeniz gerekebilir. SQL veritabanınızda kullanılabilir veri miktarını izlemenin bir yolu [Azure Portal](https://portal.azure.com/). Sol panelde SQL VERITABANLARıNı bulun :::image type="icon" source="./media/predictive-maintenance-technical-guide/icon-SQL-databases.png" border="false"::: ve tıklayın. Daha sonra veritabanınızın **pmaintenancedb** bulun ve üzerine tıklayın. Alt kısımdaki sonraki sayfada Yönet ' e tıklayın.
   
    ![Yönet simgesi](./media/predictive-maintenance-technical-guide/icon-manage.png)
   
    Burada, yeni sorgu ' ya tıklayıp satır sayısı için sorgulama yapabilirsiniz (örneğin, PMResult ' ın SELECT COUNT (*)). Veritabanınız büyüdükçe, tablodaki satır sayısı artmalıdır.

## <a name="power-bi-dashboard"></a>Power BI Panosu

Azure Machine Learning (soğuk yol) ile Azure Stream Analytics verilerinizi (sık kullanılan yol) ve toplu tahmin sonuçlarını görselleştirmek için bir Power BI panosu ayarlayın.

### <a name="set-up-the-cold-path-dashboard"></a>Soğuk yol panosunu ayarlama
Soğuk yol verileri ardışık düzeninde, amaç, her uçak altyapısının bir uçuş süresini (Cycle) tamamladıktan sonra tahmine dayalı RUL (kalan yararlı ömür) elde etmektir. Tahmin sonucu, son 3 saat boyunca bir uçuş süresi biten uçak altyapılarını tahmin etmek için 3 saatte bir güncelleştirilir.

Power BI, tahmin sonuçlarının depolandığı, veri kaynağı olarak bir Azure SQL veritabanına bağlanır. 

Not: 
1.    Çözümünüzü dağıtmaya yönelik olarak, veritabanında 3 saat içinde bir tahmin görüntülenecektir. Oluşturucu indirimiyle birlikte gelen pbix dosyası, Power BI panosunu hemen oluşturabilmeniz için bazı çekirdek verileri içerir. 
2.    Bu adımda önkoşul, ücretsiz yazılım [Power BI Desktop](/power-bi/fundamentals/desktop-get-the-desktop)'ı indirip yüklemektir.

Aşağıdaki adımlarda, dosya, görselleştirme için verileri (örneğin, tahmin sonuçları) içeren çözüm dağıtımı sırasında en sonunda bulunan SQL veritabanı 'na nasıl bağlayaöğreneceksiniz.

1. Veritabanı kimlik bilgilerini alın.
   
   Sonraki adımlara geçmeden önce **veritabanı sunucu adı, veritabanı adı, Kullanıcı adı ve parola** gerekir. Bunları nasıl bulacağınızı size kılavuzluk eden adımlar aşağıda verilmiştir.
   
   * Çözüm şablonu diyagramınızdaki **' Azure SQL veritabanı '** yeşil ' i etkinleştirdikten sonra, tıklayın ve sonra **' Aç '** düğmesine tıklayın.
   * Azure portal sayfasını görüntüleyen yeni bir tarayıcı sekmesi/penceresi görürsünüz. Sol panelde **' kaynak grupları '** seçeneğine tıklayın.
   * Çözümü dağıtmak için kullanmakta olduğunuz aboneliği seçin ve ardından **' YourSolutionName \_ ResourceGroup '** öğesini seçin.
   * Yeni açılan panelde,  :::image type="icon" source="./media/predictive-maintenance-technical-guide/icon-sql.png" border="false"::: veritabanınıza erişmek için simgeye tıklayın. Veritabanınızın adı bu simgenin yanında (örneğin, **' pmaintenancedb '**) ve **veritabanı sunucu adı** sunucu adı özelliği altında listelenir ve **YourSolutionName.Database.Windows.net** şuna benzer görünmelidir.
   * Veritabanınızın **Kullanıcı adı** ve **parolası** , çözümün dağıtımı sırasında daha önce kaydedilen Kullanıcı adı ve parola ile aynıdır.
2. Power BI Desktop ile soğuk yol rapor dosyasının veri kaynağını güncelleştirin.
   
   * Oluşturucu dosyasını indirdiğiniz ve sıkıştırmışın klasörde, **PowerBI \\ PredictiveMaintenanceAerospace. pbix** dosyasına çift tıklayın. Dosyayı açtığınızda herhangi bir uyarı iletisi görürseniz, bunları yoksayın. Dosyanın üst kısmında **' sorguları Düzenle '** seçeneğine tıklayın.
     
     ![Sorguları Düzenle](./media/predictive-maintenance-technical-guide/edit-queries.png)
   * **RemainingUsefulLife** ve **pmresult** olmak üzere iki tablo görürsünüz. İlk tabloyu seçin ve ![ ](./media/predictive-maintenance-technical-guide/icon-query-settings.png) sağ **' sorgu ayarları '** panelinde **' uygulanan adımlar '** altında bulunan **' kaynak '** ' ın yanındaki sorgu ayarları simgesine tıklayın. Görüntülenen tüm uyarı iletilerini yoksayın.
   * Açılan pencerede, **' sunucu '** ve **' veritabanı '** ' nı kendi sunucu ve veritabanı adlarınızla değiştirin ve ardından **' Tamam 'a** tıklayın. Sunucu adı için 1433 numaralı bağlantı noktasını (**YourSolutionName.Database.Windows.net, 1433**) belirttiğinizden emin olun. Veritabanı alanını **pmaintenancedb** olarak bırakın. Ekranda görünen uyarı iletilerini yoksayın.
   * Sonraki açılan pencerede, sol bölmede (**Windows** ve **veritabanı**) iki seçenek görürsünüz. ' **Veritabanı '** seçeneğine tıklayın, **' Kullanıcı adı '** ve **' parola '** (çözümü ilk dağıtırken girdiğiniz Kullanıcı adı ve parola) ile bir Azure SQL veritabanı oluşturduğunuzda girin. ***Bu ayarların hangi düzeye uygulanacağını seçin*** bölümünde, veritabanı düzeyi seçeneğini işaretleyin. Ardından **' Bağlan**'a tıklayın.
   * İkinci tablo **Pmresult** ' a tıklayın, ardından ![ ](./media/predictive-maintenance-technical-guide/icon-navigation.png) sağ **' sorgu ayarları '** panelinde **' uygulanan adımlar '** altında bulunan **' kaynak '** ' ın yanındaki Gezinti simgesi ' ne tıklayın ve sunucu ve veritabanı adlarını yukarıdaki adımlarda olduğu gibi güncelleştirin ve Tamam ' a tıklayın.
   * Önceki sayfaya geri döndüğünüzde pencereyi kapatın. Bir ileti görüntülenir ve **Uygula**' ya tıklayın. Son olarak, değişiklikleri kaydetmek için **Kaydet** düğmesine tıklayın. Power BI dosyanız artık sunucuyla bağlantı kurdu. Görselleştirmeleriniz boşsa, göstergelerin sağ üst köşesindeki silgi simgesine tıklayarak tüm verileri görselleştirmek için görselleştirmelerin seçimlerini temizlediğinizden emin olun. Görselleştirmelerde yeni verileri yansıtmak için Yenile düğmesini kullanın. Başlangıçta, Veri Fabrikası her 3 saatte bir yenilenmek üzere zamanlandığında yalnızca görselleştirmelerinizde çekirdek verileri görürsünüz. 3 saat sonra, verileri yenilediğinizde görselleştirmelerinizde yansıtılan yeni tahminleri görürsünüz.
3. Seçim [Power BI çevrimiçi](https://www.powerbi.com/)olarak soğuk yol panosunu yayımlayın. Bu adımın Power BI bir hesabı (veya bir iş veya okul hesabı) olması gerekir.
   
   * Daha sonra "Power BI başarılı yayımlama!" ile görüntülenen bir pencere görüntülenirken " **Yayımla** " ve birkaç saniye ' ne tıklayın. yeşil bir onay işareti ile. Aşağıdaki bağlantıya tıklayın. Power BI içinde PredictiveMaintenanceAerospace. pbix dosyasını açın. Ayrıntılı yönergeleri bulmak için bkz. [Power BI Desktop yayımlama](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Yeni bir pano oluşturmak için: **+** sol bölmedeki **panolar** bölümünün yanındaki işaretine tıklayın. Bu yeni Pano için "tahmine dayalı bakım tanıtımı" adını girin.
   * Raporu açtığınızda, ![ ](./media/predictive-maintenance-technical-guide/icon-pin.png) Tüm görselleştirmeleri panonuza sabitlemek için Sabitle simgesine tıklayın. Ayrıntılı yönergeleri bulmak için bkz. bir [panodaki kutucuğu bir Power BI panoya sabitleme](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Pano sayfasına gidin ve görselleştirmelerinizin boyutunu ve konumunu ayarlayıp başlıklarını düzenleyin. Kutucuklarınızın nasıl düzenleneceği hakkında ayrıntılı yönergeler için bkz. [kutucuğu düzenleme--yeniden boyutlandırma, taşıma, yeniden adlandırma, sabitleme, silme, köprü ekleme](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Burada, kendisine sabitlenmiş bazı soğuk yol görselleştirmeleri içeren örnek bir pano verilmiştir.  Veri oluşturucuyu ne kadar süreyle çalıştırdığınıza bağlı olarak, Görselleştirmelerde yaptığınız numaralar farklı olabilir.
     <br/>
     ![Son görünüm](./media/predictive-maintenance-technical-guide/final-view.png)
     <br/>
   * Verilerin yenilenmesini zamanlamak için, farenizi **PredictiveMaintenanceAerospace** veri kümesinin üzerine getirin, ![ üç nokta simgesine ](./media/predictive-maintenance-technical-guide/icon-elipsis.png) ve sonra **yenilemeyi zamanla**' yı seçin.
     <br/>
     > [!NOTE]
     > Bir uyarı iletisi görürseniz, **kimlik bilgilerini düzenle** ' ye tıklayın ve veritabanı kimlik bilgilerinizin adım 1 ' de açıklananlarla aynı olduğundan emin olun.
     <br/>
     ![Yenileme zamanlama](./media/predictive-maintenance-technical-guide/schedule-refresh.png)
     <br/>
   * **Yenilemeyi zamanla** bölümünü genişletin. "Verilerinizi güncel tutun" seçeneğini etkinleştirin.
     <br/>
   * Yenilemeyi gereksinimlerinize göre zamanlayın. Daha fazla bilgi edinmek için bkz. [Power BI veri yenileme](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Etkin yol panosunu ayarla
Aşağıdaki adımlarda, çözüm dağıtımı sırasında oluşturulan Stream Analytics işlerden veri çıkışının nasıl görselleştirilecek gösterilmektedir. Aşağıdaki adımları gerçekleştirmek için [Power BI çevrimiçi](https://www.powerbi.com/) bir hesap gereklidir. Hesabınız yoksa, [bir hesap oluşturabilirsiniz](https://powerbi.microsoft.com/pricing).

1. Azure Stream Analytics (ASA) içinde Power BI çıkışı ekleyin.
   
   * Azure Stream Analytics işinizin çıkışını Power BI panonuz olarak ayarlamak için [Azure Stream Analytics & Power BI: veri akışı verilerinin gerçek zamanlı görünürlüğü için analiz panosu](../../stream-analytics/stream-analytics-power-bi-dashboard.md) ' nda bulunan yönergeleri izlemeniz gerekir.
   * ASA sorgusunda **aircraftmonitor**, **aircraftalert** ve **flightsbyhour**'un üç çıkışı vardır. Sorgu sekmesine tıklayarak sorguyu görüntüleyebilirsiniz. Bu tabloların her birine karşılık gelen bir çıktı eklemeniz gerekir. İlk çıktıyı eklediğinizde (**aircraftmonitor**) **Çıkış diğer** adının, **veri kümesi adının** ve **tablo adının** aynı (**aircraftmonitor**) olduğundan emin olun. **Uçak uyarısı** ve **flightsbyhour** için çıkış eklemek için adımları tekrarlayın. Üç çıkış tablosunu ekledikten ve ASA işini başlattıktan sonra, bir onay iletisi almalısınız ("iş maintenancesa02asapbi başarıyla Stream Analytics başlatılıyor").
2. [Power BI çevrimiçi](https://www.powerbi.com) olarak oturum açın
   
   * Çalışma alanım 'daki sol panel veri kümeleri bölümünde ***veri kümesi** _ adlar _ * aircraftmonitor * *, **aircraftalert** ve **flightsbyhour** görünmelidir. Bu, önceki adımda Azure Stream Analytics gönderdiğiniz akış verileri. Veri kümesi **flightsbyhour** , arkasındaki SQL sorgusunun doğası nedeniyle diğer iki veri kümesiyle aynı anda görüntülenmeyebilir. Ancak, bir saatten sonra gösterilmesi gerekir.
   * ***Görsel öğeler*** bölmesinin açık olduğundan ve ekranın sağ tarafında gösterildiğinden emin olun.
3. Veriler Power BI alındıktan sonra, akış verilerini görselleştirmeyi başlatabilirsiniz. Aşağıda, kendisine sabitlenmiş etkin yol görselleştirmeleri içeren örnek bir pano verilmiştir. Uygun veri kümelerine göre başka Pano kutucukları oluşturabilirsiniz. Veri oluşturucuyu ne kadar süreyle çalıştırdığınıza bağlı olarak, Görselleştirmelerde yaptığınız numaralar farklı olabilir.

    ![Pano görünümü](media/predictive-maintenance-technical-guide/dashboard-view.png)

1. Yukarıdaki kutucuktan birini oluşturmak için bazı adımlar vardır: "algılayıcı 11 ' in Fleet görünümü-eşik 48,26" kutucuğu:
   
   * Sol panel veri kümeleri bölümünde veri kümesi **aircraftmonitor** öğesine tıklayın.
   * **Çizgi grafiği** simgesine tıklayın.
   * **Görsel öğeler** bölmesinde "eksen" altında görünmesi için **alanlar** bölmesinde **işlenen** ' e tıklayın.
   * \_Her ikisi de "Values" altında görünmesini sağlamak için "S11" ve "S11 Alert" öğesine tıklayın. **S11** ve **S11 \_ uyarısı**' nın yanındaki küçük oka tıklayın, "Sum" ı "Average" olarak değiştirin.
   * Üstteki **Kaydet** ' e tıklayın ve raporu "aircraftmonitor" olarak adlandırın. "Aircraftmonitor" adlı rapor, sol taraftaki **Gezgin** bölmesinde bulunan **raporlar** bölümünde gösterilir.
   * Bu çizgi grafiğin sağ üst köşesinde bulunan **görsel sabitle** simgesine tıklayın. Bir panoyu seçmeniz için "panoya sabitle" penceresi gösterilebilir. "Tahmine dayalı bakım tanıtımı" öğesini seçin ve ardından "sabitle" ye tıklayın.
   * Fare işaretçisini panoda bu kutucuğun üzerine getirin, başlığını "en sağ üst köşedeki" Düzenle "simgesine tıklayarak başlığını" bir süre içinde ortalama olarak "ESET, algılayıcı 11 ile 48,26 arasında"

## <a name="delete-your-solution"></a>Çözümünüzü silme
Veri oluşturucunun çalıştığı şekilde çözümü etkin bir şekilde kullanmadığınız durumlarda veri oluşturucuyu durdurduğunuzdan emin olun ve daha yüksek maliyetler uygulanır. Bunu kullanmıyorsanız çözümü silin. Çözümünüzü sildiğinizde, çözümü dağıttığınızda aboneliğinizde sağlanan tüm bileşenler silinir. Çözümü silmek için, çözüm şablonunun sol panelinde çözümünüz adına tıklayın ve ardından **Sil**' e tıklayın.

## <a name="cost-estimation-tools"></a>Maliyet tahmini araçları
Aşağıdaki iki araç, aboneliğinizdeki bir Aerospace çözüm şablonu için tahmine dayalı bakım 'nin çalıştırılmasına dahil edilen toplam maliyetleri daha iyi anlamanıza yardımcı olmak için kullanılabilir:

* [Microsoft Azure Cost Estimator Aracı (çevrimiçi)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure Cost Estimator Aracı (Masaüstü)](https://www.microsoft.com/download/details.aspx?id=43376)