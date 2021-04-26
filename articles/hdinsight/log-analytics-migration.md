---
title: Azure HDInsight için Log Analytics verilerini geçirme
description: Azure Izleyici tümleştirmesinde yapılan değişiklikler ve yeni tabloları kullanmaya yönelik en iyi uygulamalar hakkında bilgi edinin.
ms.service: hdinsight
ms.topic: how-to
ms.author: ali
author: AliciaLiMicrosoft
ms.date: 04/19/2021
ms.openlocfilehash: 6659b515ee2d25a4b9136ccfac4cc3444e491438
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741811"
---
# <a name="log-analytics-migration-guide-for-azure-hdinsight-clusters"></a>Azure HDInsight kümeleri için Log Analytics geçiş kılavuzu

 Azure HDInsight, kurumsal özellikli, yönetilen bir küme hizmetidir. Bu hizmet, Azure üzerinde Apache Spark, Hadoop, HBase ve Kafka gibi açık kaynaklı analiz çerçeveleri çalıştırır. Azure HDInsight, müşterilerin büyük veri analizi uygulamalarını daha iyi yönetmesine olanak tanımak için diğer Azure hizmetleriyle tümleşiktir.

Log Analytics, günlük sorgularını düzenlemek ve çalıştırmak için Azure portal bir araç sağlar. Sorgular, Azure Izleyici günlükleri tarafından toplanan verilerden gelir ve sonuçları etkileşimli olarak analiz eder. Müşteriler, belirli ölçütlerle eşleşen kayıtları almak için Log Analytics sorguları kullanabilir. Eğilimleri belirlemek, desenleri çözümlemek ve verileri hakkında Öngörüler sağlamak için sorguları da kullanabilirler.

Azure HDInsight, 2017 içinde Log Analytics ile tümleştirmeyi etkinleştirdi. HDInsight müşterileri, HDInsight kümelerini izlemek ve kümelerdeki günlükleri sorgulamak için bu özelliği hızlı bir şekilde benimsemiştir. Bu özelliğin benimseme artışı arttıkça, müşteriler tümleştirme hakkında geri bildirim sağlamıştır:

- Müşteriler hangi günlüklerin depolanacağına karar vermez ve tüm günlüklerin depolanması pahalı hale gelebilir.
- Geçerli HDInsight şemaları günlükleri tutarlı adlandırma kurallarına uygun değildir ve bazı tablolar tekrarda tekrarlanmıyor.
- Müşteriler, bir hazır panonun, HDInsight kümelerinin KPI 'sini kolayca izlemesini ister.
- Müşteriler basit sorgular çalıştırmak için Log Analytics atmalıdır.

## <a name="solution-overview"></a>Çözüme genel bakış

Azure HDInsight ekibi, müşteri geri bildirimlerine göz önünde bulundurarak Azure Izleyici ile tümleştirmeyi yatırılmış. Bu tümleştirme şunları sunar:

- Müşterilerin Log Analytics çalışma alanındaki yeni bir tablo kümesi. Yeni tablolar yeni bir Log Analytics işlem hattı aracılığıyla dağıtılır.
- Daha yüksek güvenilirlik
- Daha hızlı günlük teslimi
- Kaynak tabanlı tablo gruplama ve varsayılan sorgular

## <a name="benefits-of-the-new-azure-monitor-integration"></a>Yeni Azure Izleyici tümleştirmesinin avantajları

Bu belge, Azure Izleyici tümleştirmesinde yapılan değişiklikleri özetler ve yeni tabloları kullanmak için en iyi uygulamaları sağlar.

Yeniden **tasarlanan şemalar**: yeni Azure izleyici tümleştirmesinin şema biçimlendirmesi daha iyi düzenlenmiştir ve anlaşılması kolay. Eski şemalarda mümkün olduğunca fazla belirsizlik kaldırmak için iki buçuk daha az şema vardır.

**Seçmeli günlüğe kaydetme (yakında serbest bırakılıyor)**: Log Analytics aracılığıyla kullanılabilir Günlükler ve ölçümler vardır. İzleme maliyetlerinde tasarruf etmenize yardımcı olmak için yeni bir seçmeli günlük özelliği yayınlarız. Farklı günlükleri ve ölçüm kaynaklarını açmak ve kapatmak için bu özelliği kullanın. Bu özellikle, yalnızca kullandığınız kadar ödeme yapmanız gerekir.

**Küme portalı tümleştirmesini günlüğe kaydeder**: **Günlükler** bölmesi HDInsight kümesi portalında yenidir. Kümeye erişimi olan herkes, küme kaynağının kayıt gönderdiği tabloları sorgulamak için bu bölmeye gidebilir. Kullanıcıların belirli bir küme kaynağı için kayıtları görmek üzere artık Log Analytics çalışma alanına erişmesi gerekmez.

**Öngörüler kümesi Portal tümleştirmesi**: **Öngörüler** bölmesi, HDInsight küme portalı için de yenidir. Yeni Azure Izleyici tümleştirmesini etkinleştirdikten **sonra, içgörüler bölmesini seçebilirsiniz** ve küme türüne özgü bir kullanıma hazır Günlükler ve ölçüm panosu sizin için otomatik olarak doldurulur. Bu panolar, önceki Azure çözümlerimizden daha sonra kurtarıldı. Bu kişiler, kümenizin performansı ve sistem durumu hakkında ayrıntılı bilgiler sunar.

**Ölçekli** içgörüler: farklı aboneliklerde kümelerinizin sistem durumunu ve performansını Izlemek Için **Azure izleyici** portalındaki yeni yerinde **Öngörüler** çalışma kitabını kullanabilirsiniz.

## <a name="customer-scenarios"></a>Müşteri senaryoları

Aşağıdaki bölümlerde, müşterilerin yeni Azure Izleyici tümleştirmesini farklı senaryolarda nasıl kullanabileceği açıklanır. [Yeni Azure izleyici Tümleştirmesini Etkinleştirme](#activate-a-new-azure-monitor-integration) bölümünde, yeni Azure izleyici Tümleştirmesini Etkinleştirme ve kullanma işlemlerinin nasıl yapılacağı özetlenmektedir. [Azure izleyici 'den yeni Azure Izleyici tümleştirmesi bölümüne geçiş](#migrate-to-the-new-azure-monitor-integration) , eski Azure izleyici tümleştirmesine bağlı olan kullanıcılar için ek bilgiler içerir.

> [!NOTE]
> Yalnızca gecikmiş-Eylül 2020 ve sonrasında oluşturulan kümeler yeni Azure Izleme tümleştirmesi için uygun değildir.

## <a name="activate-a-new-azure-monitor-integration"></a>Yeni bir Azure Izleyici tümleştirmesini etkinleştirme 

> [!NOTE]
> Yeni tümleştirmeyi etkinleştirmeden önce erişiminiz olan bir abonelikte oluşturulmuş bir Log Analytics çalışma alanınızın olması gerekir. Log Analytics çalışma alanı oluşturma hakkında daha fazla bilgi için, bkz. [Azure portal Log Analytics çalışma alanı oluşturma](../azure-monitor/learn/quick-create-workspace.md).

Kümenizin Portal sayfasına gidip, **izleme** bölümüne ulaşana kadar sol taraftaki menüyü aşağı kaydırarak yeni tümleştirmeyi etkinleştirin. **İzleme** bölümünde izleme **tümleştirmesi**' ni seçin. Ardından, **Etkinleştir** ' i seçin ve günlüklerinizin gönderilmesini istediğiniz Log Analytics çalışma alanını seçebilirsiniz. Çalışma alanınızı seçtikten sonra **Kaydet** ' i seçin. 

### <a name="access-the-new-tables"></a>Yeni tablolara erişin

Yeni tablolara erişmek için kullanabileceğiniz iki yol vardır. 

### <a name="approach-1"></a>Yaklaşım 1:
Yeni tablolara erişmenin ilk yolu Log Analytics çalışma alanıdır. 

1. Tümleştirmeyi etkinleştirdiğinizde seçtiğiniz Log Analytics çalışma alanına gidin. 
2. Ekranın sol tarafındaki menüde aşağı kaydırın ve **Günlükler**' i seçin. Bir Günlükler sorgu Düzenleyicisi, çalışma alanındaki tüm tabloların listesi ile açılır. 
3. Tablolar **çözüme** göre gruplandırılmışsa, yenı HDI tabloları **Günlükler yönetimi** bölümünde bulunur. 
4. Tabloları **kaynak türüne** göre gruplandırdıysanız, tablolar aşağıdaki görüntüde gösterildiği gibi **HDInsight kümeleri** bölümünün altındadır. 

> [!NOTE]
> Bu işlem, eski tümleştirmede günlüklere nasıl erişildiğini açıklar. Bunun için kullanıcının çalışma alanına erişimi olması gerekir.

### <a name="approach-2"></a>Yaklaşım 2:

Yeni tablolara erişmenin ikinci yolu, küme portalı erişimi kullanmaktır.
 
1. Kümenizin Portal sayfasına gidin ve **izleme** bölümünü görene kadar sol taraftaki menüyü aşağı kaydırın. Bu bölümde, **Günlükler** bölmesini görürsünüz. 
2. **Günlükleri** seç ve Günlükler sorgu Düzenleyicisi görüntülenir. Düzenleyici, küme kaynağıyla ilişkili tüm günlükleri içerir. Tümleştirme etkinleştirildiğinde günlükleri Log Analytics çalışma alanına gönderdiniz. Bu Günlükler kaynak tabanlı erişim (RBAC) sağlar. RBAC ile, kümeye erişimi olan ancak çalışma alanına yönelik olmayan kullanıcılar kümeyle ilişkili günlükleri görebilir.

Karşılaştırma için aşağıdaki ekran görüntülerinde eski tümleştirme çalışma alanı görünümü ve yeni tümleştirme çalışma alanı görünümü gösterilmektedir:

**Eski Tümleştirme çalışma alanı görünümü**

  :::image type="content" source="./media/log-analytics-migration/legacy-integration-workspace-view.png"  lightbox="./media/log-analytics-migration/legacy-integration-workspace-view.png" alt-text="Eski Tümleştirme çalışma alanı görünümünü gösteren ekran görüntüsü." border="false":::

**Yeni tümleştirme çalışma alanı görünümü**

  :::image type="content" source="./media/log-analytics-migration/new-integration-workspace-view.png" lightbox="./media/log-analytics-migration/new-integration-workspace-view.png" alt-text="Yeni tümleştirme çalışma alanı görünümünü gösteren ekran görüntüsü." border="false":::

### <a name="use-the-new-tables"></a>Yeni tabloları kullanma

Bu tümleştirmeler, yeni tabloları kullanmanıza yardımcı olabilir:

#### <a name="default-queries-to-use-with-new-tables"></a>Yeni tablolarla kullanılacak varsayılan sorgular

Günlüklerinizin sorgu düzenleyicisinde, tablolar listesinin üzerindeki **sorguları** Dönüştür ' ü belirleyin. Sorguları **kaynak türüne** göre gruplandırtığınızdan ve **HDInsight kümeleri** dışında bir kaynak türü için ayarlanmış filtre olmadığından emin olun. Aşağıdaki görüntüde sonuçların **kaynak türüne** göre gruplandırılırken nasıl gösterdiği ve **HDInsight kümeleri** için filtrelenmiş gösterilmektedir. Yalnızca bir tane seçin ve Günlükler sorgu düzenleyicisinde görünür. Sorgunun başarıyla çalışması için kümenin adı gibi bazı bilgileri girmenizi gerektirdiğinden sorgulara dahil olan açıklamaları okuduğunuzdan emin olun.

:::image type="content" source="./media/log-analytics-migration/default-query-results-grouped-resource-type.png" alt-text="Varsayılan sorgu sonuçları gruplandırılmış kaynak türünü gösteren ekran görüntüsü." border="true":::


#### <a name="create-your-own-queries"></a>Kendi sorgularınızı oluşturun

Günlükler sorgu düzenleyicisinde kendi sorgularınızı girebilirsiniz. Yeni tabloların çoğunda yeni ve siyah zemin şemaları olduğundan, eski tablolarda kullanılan sorgular yeni tablolarda geçerli olmayacaktır. Varsayılan sorgular, yeni tablolardaki sorguları şekillendirmeye yönelik harika başvurulardır.

#### <a name="insights"></a>Insights

Öngörüler, [Azure çalışma kitapları](../azure-monitor/platform/workbooks-overview.md)kullanılarak oluşturulan, kümeye özgü görselleştirme panolardır. Bu panolar, kümenizin nasıl çalıştığı hakkında ayrıntılı grafikler ve görselleştirmeler sunar. Panolar her küme türü, YARN, sistem ölçümleri ve bileşen günlükleri için bölümler vardır. Kümenizin portalda kümenizin sayfasını ziyaret ederek, **izleme** bölümüne giderek ve **Öngörüler** bölmesini seçerek kümenizin panosuna erişebilirsiniz. Yeni tümleştirmeyi etkinleştirdiyseniz pano otomatik olarak yüklenir. Günlükleri sorgulayıp grafiklerin yüklenmesi için birkaç saniye bekleyin.

:::image type="content" source="./media/log-analytics-migration/visualization-dashboard.png" lightbox="./media/log-analytics-migration/visualization-dashboard.png" alt-text="Görselleştirme panosunu gösteren ekran görüntüsü.":::

#### <a name="custom-azure-workbooks"></a>Özel Azure çalışma kitapları

Özel grafiklerle ve görselleştirmelerle kendi Azure çalışma kitaplarınızı oluşturabilirsiniz. Kümenizin portal sayfasında, **izleme** bölümüne gidin ve soldaki menüden **çalışma kitapları** bölmesini seçin. Boş bir şablon kullanmaya başlayabilir ya da **HDInsight kümeleri** bölümünün altındaki şablonlardan birini kullanabilirsiniz. Her küme türü için bir şablon vardır. Varsayılan HDInsight öngörülerinin sağlamadığı belirli özelleştirmeleri kaydetmek istiyorsanız şablonlar yararlı olur. HDInsight Insights 'taki yeni özellikler için istekleri bir şeyler olmadığını düşünüyorsanız ücretsiz olarak gönderebilirsiniz.

#### <a name="at-scale-workbooks-for-new-azure-monitor-integrations"></a>Yeni Azure Izleyici tümleştirmeleri için ölçekli çalışma kitapları

Kümeleriniz için çok bölgeli bir izleme deneyimi almak üzere yeni ölçekli çalışma kitabımızı kullanın. Ölçekli çalışma kitabımız, sizin kümelerinizde izleme işlem hattının etkin olduğunu gösterir. Çalışma kitabı ayrıca aynı anda birden çok kümenin durumunu denetlemek için basit bir yol sağlar. Bu çalışma kitabını görüntülemek için:

1. Azure portal giriş sayfasından **Azure izleyici** sayfasına gidin
2. **Azure izleyici** sayfasında, **Öngörüler** bölümünde **Öngörüler merkezi** ' ni seçin.
3. **Analiz** bölümünün altında **HDInsight kümeleri** ' ni seçin.

   :::image type="content" source="./media/log-analytics-migration/at-scale-workbook.png" lightbox="./media/log-analytics-migration/at-scale-workbook.png" alt-text="Ölçekli çalışma kitabını gösteren ekran görüntüsü." border="false":::

#### <a name="alerts"></a>Uyarılar

Günlük sorgu düzenleyicisinde kümelerinize ve çalışma alanlarınıza özel uyarılar ekleyebilirsiniz. Kümelerinizi veya çalışma alanı portalından **Günlükler** bölmesini seçerek Günlükler sorgu Düzenleyicisi ' ne gidin. Bir sorgu çalıştırın ve ardından aşağıdaki ekran görüntüsünde gösterildiği gibi **Yeni uyarı kuralı** ' nı seçin. Daha fazla bilgi için [Uyarıları yapılandırma](../azure-monitor/platform/alerts-log.md)hakkında makalesini okuyun.

:::image type="content" source="./media/log-analytics-migration/new-rule-alert.png" alt-text="Yeni kural uyarısını gösteren ekran görüntüsü." border="false":::

## <a name="migrate-to-the-new-azure-monitor-integration"></a>Yeni Azure Izleyici tümleştirmesine geçiş

Klasik Azure Izleyici tümleştirmesini kullanıyorsanız, yeni Azure Izleyici tümleştirmesine geçiş yaptıktan sonra yeni tablo biçimlerinde bazı ayarlamalar yapmanız gerekir.

Yeni Azure Izleyici tümleştirmesini etkinleştirmek için [yeni Azure izleyici Tümleştirmesini Etkinleştirme](#activate-a-new-azure-monitor-integration) bölümünde açıklanan adımları izleyin.

### <a name="run-queries-in-log-analytics"></a>Sorguları Log Analytics içinde Çalıştır

Yeni tablo biçimi öncekinden farklı olduğundan, yeni tablolarımızı kullanabilmeniz için sorgularınızın yeniden çalışması gerekir. Yeni Azure Izleyici tümleştirmesini etkinleştirdikten sonra, eski sorgularda kullanılan alanları belirlemek için tablolara ve şemalara gözatabilmeniz gerekir.

Panolarınızı ve sorgularınızı geçirmek için kullanmanız gereken yeni alanları hızlı bir şekilde bulmanıza yardımcı olması için eski tablo ile yeni tabloya bir [eşleme tablosu](#appendix-table-mapping) sağlıyoruz.

**Varsayılan sorgular**: yeni tabloların yaygın durumlar için nasıl kullanılacağını gösteren varsayılan sorgular oluşturduk. Varsayılan sorgular, her tabloda hangi bilgilerin kullanılabildiğini de gösterir. Varsayılan sorgulara, bu makaledeki [Yeni tablolarla kullanılacak varsayılan sorgular](#default-queries-to-use-with-new-tables) bölümündeki yönergeleri izleyerek erişebilirsiniz.

### <a name="update-dashboards-for-hdinsight-clusters"></a>HDInsight kümeleri için panoları güncelleştirme

HDInsight kümelerinizi izlemek için birden çok Pano oluşturduysanız, yeni Azure Izleyici tümleştirmesini etkinleştirdikten sonra tablonun arkasındaki sorguyu ayarlamanız gerekir. Tablo adı veya alan adı yeni tümleştirmede değişebilir, ancak eski tümleştirmede sahip olduğunuz tüm bilgiler dahildir.

Panoların arkasındaki sorguyu güncelleştirmek için eski tablo/şema ile yeni tablo/şema arasındaki [eşleme tablosuna](#appendix-table-mapping) bakın.

#### <a name="out-of-box-dashboards"></a>Hazır panolar 

Ayrıca, yerleşik panoları her ikisi de küme düzeyinde geliştirdik. Her grafiğin sağ üst kısmında, bilgileri üreten temel sorguyu görmenizi sağlayan bir düğme vardır. Grafik, yeni tabloların etkin bir şekilde nasıl sorgulanılabileceğini öğrenmek için harika bir yoldur. [Yeni Azure izleyici tümleştirmeleri için](#at-scale-workbooks-for-new-azure-monitor-integrations) içgörüler ve ölçekli çalışma kitaplarında bulacağınız yönergeleri izleyerek kullanıma hazır panolara erişebilirsiniz [](#insights) .

### <a name="use-an-hdinsight-at-scale-monitoring-dashboard"></a>HDInsight üzerinde ölçeklendirme izleme panosu kullanma

HDInsight Spark Izleme ve HDInsight etkileşimli Izleme gibi HDInsight kümeleri için kullanıma hazır izleme panosu kullanıyorsanız, size Azure Izleyici portalında aynı özellikleri sağlamaya çalışıyoruz.

Azure Izleyici 'de bir HDInsight kümeleri seçeneği olduğunu göreceksiniz.

   :::image type="content" source="./media/log-analytics-migration/hdinsight-azure-monitor.png" lightbox="./media/log-analytics-migration/hdinsight-azure-monitor.png" alt-text="Azure Izleyici 'de HDInsight seçeneğini gösteren ekran görüntüsü." border="false":::

Azure Izleyici portalının içgörüler hub 'ı, birden çok HDInsight kümesini tek bir yerde izleme yeteneği sağlar. Kümeleri, iş yükü türüne göre düzenliyoruz, bu nedenle Spark, HBase ve Hive gibi türler görürsünüz. Artık birden çok panoya gitmek yerine, bu görünümdeki tüm HDInsight kümelerinizi izleyebilirsiniz.

> [!NOTE]
> Daha fazla bilgi için bu makaledeki [](#insights) [yeni Azure izleyici tümleştirmeleri için Öngörüler ve ölçekli çalışma kitaplarına](#at-scale-workbooks-for-new-azure-monitor-integrations) bakın.

## <a name="enable-both-integrations-to-accelerate-the-migration"></a>Geçişi hızlandırmak için her iki tümleştirmeleri etkinleştirin

Hem klasik hem de yeni Azure Izleyici tümleştirmelerini, her iki tümleştirmede de yeni Azure Izleyici tümleştirmesine hızla geçiş yapmak için uygun bir kümede etkinleştirebilirsiniz. Yeni tümleştirme, PARÇAAL-Eylül 2020 ' den sonra oluşturulan tüm kümeler için kullanılabilir.

Bu şekilde, kullanmakta olduğunuz sorgulara yönelik yan yana karşılaştırmayı kolayca yapabilirsiniz.

### <a name="enabling-the-classic-integration"></a>Klasik tümleştirmeyi etkinleştirme

PARÇAAL-Eylül 2020 sonrasında oluşturulmuş bir küme kullanıyorsanız, kümenizin portalında yeni Portal deneyimini görürsünüz. Yeni işlem hattını etkinleştirmek için [yeni Azure izleyici Tümleştirmesini Etkinleştirme](#activate-a-new-azure-monitor-integration) bölümünde özetlenen adımları izleyebilirsiniz. Bu kümede klasik tümleştirmeyi etkinleştirmek için, kümenizin Portal sayfasına gidin. Küme Portalı sayfanızın sol tarafındaki menünün **izleme** bölümünde **tümleştirme bölmesini izle** ' yi seçin. **HDInsight kümeleri tümleştirmesi (klasik) Için Azure Izleyicisini Yapılandır**' ı seçin. Klasik Azure Izleme tümleştirmesini etkinleştirmek ve devre dışı bırakmak için kullanabileceğiniz iki durumlu bir yan bağlam görüntülenir. 

> [!NOTE]
> Küme portalınızın günlükleri ve öngörüleri sayfasında, klasik tümleştirmede herhangi bir günlük veya ölçüm görmezsiniz. Bu konumlarda yalnızca yeni tümleştirme günlükleri ve ölçümleri mevcut olacaktır.

   :::image type="content" source="./media/log-analytics-migration/hdinsight-classic-integration.png" alt-text="Klasik tümleştirmeye erişim bağlantısını gösteren ekran görüntüsü." border="false":::

Klasik Azure Izleyici tümleştirmesiyle yeni kümeler oluşturmak, 1 Temmuz 2021 ' den sonra kullanılamaz.

## <a name="release-and-support-timeline"></a>Yayın ve destek zaman çizelgesi

- Klasik Azure Izleme tümleştirmesi, 15 Ekim 2021 ' den sonra kullanılamayacaktır. Bu tarihten sonra klasik Azure Izleme tümleştirmesini etkinleştiremezsiniz.
- Mevcut klasik Azure izleme tümleştirmeleri çalışmaya devam edecektir. Klasik Azure Izleme tümleştirmesi için sınırlı destek olacaktır. 
  - Müşteriler destek bileti gönderdikten sonra sorunlar araştırılır.
  - Çözüm görüntü değişikliği gerektiriyorsa, müşteriler yeni tümleştirmeye geçmelidir.
  - Kritik güvenlik sorunları hariç, klasik Azure Izleme tümleştirme kümelerini yamayacağız.

## <a name="appendix-table-mapping"></a>Ek: tablo eşleme

Aşağıdaki grafiklerde, klasik Azure Izleme tümleştirmesinden yeni bir tane olan tablo eşlemeleri gösterilmektedir. **Iş yükü** sütunu, her bir tablonun hangi iş yüküne ilişkilendirildiğini açıklar. **Yeni** tablo satırı yeni tablonun adını gösterir. **Açıklama** satırı, bu tabloda kullanılabilecek günlüklerin/ölçümlerin türünü açıklar. **Eski tablo** satırı, klasik Azure izleyici tümleştirmesinin, verileri artık **Yeni tablo** satırında listelenen tabloda yer alacak olan tüm tabloların bir listesidir.

> [!NOTE]
> Bazı tablolar yenidir ve eski tabloları temel değildir.

## <a name="general-workload-tables"></a>Genel iş yükü tabloları

| Yeni Tablo | Ayrıntılar |
| --- | --- |
| HDInsightAmbariSystemMetrics | <ul><li>**Açıklama**: Bu tablo, ambarı 'ndan toplanan sistem ölçümlerini içerir. Ölçümler artık yalnızca iki headnode yerine kümedeki her düğümden (kenar düğümleri hariç) gelir. Her ölçüm artık bir sütundur ve her ölçüm kayıt başına rapor edilir.</li><li>**Eski tablo**: ölçümler \_ CPU \_ yaklaşık \_ CL, ölçümler \_ CPU \_ sistemi \_ CL, ölçümler \_ CPU \_ Kullanıcı \_ CL, ölçüm \_ belleği \_ önbellek \_ CL, ölçüm \_ belleği \_ takas CL, ölçüm belleği \_ \_ \_ Toplam \_ clölçümleri \_ bellek \_ arabelleği \_ CL, ölçüm \_ yüklemesi \_ 1dk \_ CL, ölçüm \_ yükleme \_ CPU \_ CL, ölçüm \_ yükleme \_ düğümleri CL, ölçümler \_ \_ Yük \_ procs \_ CL \_ \_ \_ \_ \_ \_ , ölçümler ağı</li></ul>|
| HDInsightAmbariClusterAlerts | <ul><li>**Açıklama**: Bu tablo, kümedeki her düğümden alınan (kenar düğümleri hariç) her bir düğümdeki ambarı küme uyarılarını içerir. Her uyarı bu tablodaki bir kayıttır.</li><li>**Eski tablo**: ölçüm \_ kümesi \_ uyarıları \_ CL</li></ul>|
| HDInsightSecurityLogs | <ul><li>**Açıklama**: Bu tablo, ambarı denetim ve kimlik doğrulama günlüklerinden kayıtları içerir.</li><li>**Eski tablo**: günlük \_ ambarı \_ Denetim \_ CL, günlük \_ kimlik doğrulaması \_ CL</li></ul>|
| HDInsightRangerAuditLogs | <ul><li>**Açıklama**: Bu tablo, ESP kümeleri Için Ranger denetim günlüğünden tüm kayıtları içerir.</li><li>**Eski tablo**: Ranger \_ Denetim \_ günlükleri \_ CL</li></ul>|
| HDInsightGatewayAuditLogs \_ CL | <ul><li>**Açıklama**: Bu tablo, ağ geçidi düğümlerinin denetim bilgilerini içerir. Eski tablolar sütunundaki tabloyla aynı biçimdedir. **Hala özel Günlükler bölümünde bulunur.**</li><li>**Eski tablo**: günlük \_ ağ geçidi \_ Denetim \_ CL</li></ul>|

## <a name="spark-workload"></a>Spark iş yükü

> [!NOTE]
> Spark uygulamayla ilgili tablolar, Spark iş yükleriniz hakkında daha ayrıntılı bilgi sağlayacak şekilde 11 Yeni Spark tablolarıyla (HDInsightSpark * ile başlayarak) değiştirilmiştir.


| Yeni Tablo | Ayrıntılar |
| --- | --- |
| HDInsightSparkLogs | <ul><li>**Açıklama**: Bu tablo Spark ve ilgili bileşeni ile ilgili tüm günlükleri Içerir: Livy ve Jupyter.</li><li>**Eski tablo**: günlük \_ Livy, \_ CL, log \_ Jupyter \_ CL, günlük \_ Spark \_ CL, log \_ sparkappsexecutors \_ CL, log \_ sparkappsdrivers \_ CL</li></ul>|
| HDInsightSparkApplicationEvents | <ul><li>**Açıklama**: Bu tablo, gönderim ve tamamlanma zamanı, uygulama kimliği ve AppName gibi Spark uygulamalarına yönelik olay bilgilerini içerir. Uygulamaların başlatıldığı ve tamamlandığı sırada izlenmesi yararlı olur. </li></ul>|
| HDInsightSparkBlockManagerEvents | <ul><li>**Açıklama**: Bu tablo, Spark 'ın blok yöneticisiyle ilgili olay bilgilerini içerir. Yürütücü bellek kullanımı gibi bilgileri içerir.</li></ul>|
| HDInsightSparkEnvironmentEvents | <ul><li>**Açıklama**: Bu tablo, bir uygulamanın yürütüldüğü ortamla ilgili olay bilgilerini, Spark dağıtım modu, ana ve yürütücü hakkındaki bilgileri içerir.</li></ul>|
| HDInsightSparkExecutorEvents | <ul><li>**Açıklama**: Bu tablo, bir uygulama tarafından Için Spark yürütücü kullanımı hakkındaki olay bilgilerini içerir.</li></ul>|
| HDInsightSparkExtraEvents | <ul><li>**Açıklama**: Bu tablo, başka bir Spark tablosuna sığmayan olay bilgilerini içerir. </li></ul>|
| HDInsightSparkJobEvents | <ul><li>**Açıklama**: Bu tablo, başlangıç ve bitiş zamanları, sonucu ve ilişkili aşamaları dahil olmak üzere Spark işleri hakkında bilgiler içerir.</li></ul>|
| HDInsightSparkSqlExecutionEvents | <ul><li>**Açıklama**: Bu tablo, Spark SQL sorguları hakkında plan bilgilerini ve açıklamalarını ve başlangıç ve bitiş zamanlarını içeren olay bilgilerini içerir.</li></ul>|
| HDInsightSparkStageEvents | <ul><li>**Açıklama**: Bu tablo, başlangıç ve tamamlanma zamanları, hata durumu ve ayrıntılı yürütme bilgileri de dahil olmak üzere Spark aşamaları için olay bilgilerini içerir.</li></ul>|
| HDInsightSparkStageTaskAccumulables | <ul><li>**Açıklama**: Bu tablo, aşamalar ve görevler için performans ölçümlerini içerir.</li></ul>|
| HDInsightTaskEvents | <ul><li>**Açıklama**: Bu tablo başlangıç ve tamamlanma saati, ilişkili aşamalar, yürütme durumu ve görev türü gibi Spark görevlerine yönelik olay bilgilerini içerir.</li></ul>|
| HDInsightJupyterNotebookEvents | <ul><li>**Açıklama**: Bu tablo, Jupyter Not defterleri için olay bilgilerini içerir.</li></ul>|

## <a name="hadoopyarn-workload"></a>Hadoop/YARN iş yükü

| Yeni Tablo | Ayrıntılar |
| --- | --- |
| HDInsightHadoopAndYarnMetrics | <ul><li>**Açıklama**: Bu tablo HADOOP ve Yarn ÇERÇEVELERINDEN JMX ölçümlerini içerir. Eski özel günlük tablolarıyla aynı JMX ölçümlerini ve önemli kabul ettiğimiz daha fazla ölçümü içerir. Zaman çizelgesi sunucusu, düğüm Yöneticisi ve Iş geçmişi Sunucu ölçümlerini ekledik. Kayıt başına bir ölçüm içerir.</li><li>**Eski tablo**: ölçümler \_ ResourceManager \_ kümeölçümleri \_ CL, ölçümler \_ ResourceManager \_ JVM \_ CL, ölçümler \_ ResourceManager \_ kuyruğu \_ kök \_ CL, ölçümler \_ ResourceManager \_ kuyruğu \_ kök \_ joblauncher \_ CL, ölçüm \_ ResourceManager \_ kuyruğu \_ kök \_ varsayılan \_ CL, ölçümler \_ ResourceManager \_ kuyruğu \_ kök \_ thriftsvr \_ CL</li></ul>|
| HDInsightHadoopAndYarnLogs | <ul><li>**Açıklama**: Bu tablo HADOOP ve Yarn çerçevelerinden oluşturulan tüm günlükleri içerir.</li><li>**Eski tablo**: günlük \_ mrjobsummary \_ CL, log \_ ResourceManager \_ CL, günlük \_ timelineserver \_ CL, log \_ NodeManager \_ CL</li></ul>|

 
## <a name="hivellap-workload"></a>Hive/LLAP iş yükü 

| Yeni Tablo | Ayrıntılar |
| --- | --- |
| HDInsightHiveAndLLAPMetrics | <ul><li>**Açıklama**: Bu tablo HIVE ve LLAP ÇERÇEVELERINDEN JMX ölçümlerini içerir. Eski özel günlük tabloları ile aynı JMX ölçümlerini içerir. Kayıt başına bir ölçüm içerir.</li><li>**Eski tablo**: LLAP \_ ölçümleri \_ hiveserver2 \_ CL, LLAP \_ ölçümleri \_ HS2 \_ ölçümleri \_ alt systemllap \_ ölçümleri \_ JVM \_ CL, LLAP \_ ölçümleri \_ LLAP \_ Daemon \_ bilgi \_ CL, LLAP \_ ölçümleri \_ arkadaş \_ ayırıcı \_ bilgi \_ CL, LLAP \_ ölçümleri \_ Deamon \_ JVM \_ CL, LLAP \_ ölçümleri \_ GÇ \_ CL, LLAP \_ ölçümleri \_ yürütücü \_ ölçümleri \_ CL, LLAP \_ ölçümleri \_ metricssystem \_ stats \_ CL, LLAP \_ ölçümleri \_ önbellek \_ CL</li></ul>|
| HDInsightHiveAndLLAPLogs | <ul><li>**Açıklama**: Bu tablo HIVE, LLAP ve ilgili bileşenlerinden oluşturulan günlükleri Içerir: WebHCat ve Zeppelin.</li><li>**Eski tablo**: günlük \_ hivemetasyürüme \_ CL log \_ hiveserver2 \_ CL, log \_ hiveserve2interactive \_ CL, log \_ webhcat \_ CL, log \_ Zeppelin \_ ppppelin \_ CL</li></ul>|


## <a name="kafka-workload"></a>Kafka iş yükü

| Yeni Tablo | Ayrıntılar |
| --- | --- |
| HDInsightKafkaMetrics | <ul><li>**Açıklama**: Bu tablo Kafka adresinden JMX ölçümlerini içerir. Eski özel günlük tablolarıyla aynı JMX ölçümlerini ve önemli kabul ettiğimiz daha fazla ölçümü içerir. Kayıt başına bir ölçüm içerir.</li><li>**Eski tablo**: ölçümler \_ Kafka \_ CL</li></ul>|
| HDInsightKafkaLogs | <ul><li>**Açıklama**: Bu tablo, Kafka aracılarından oluşturulan tüm günlükleri içerir.</li><li>**Eski tablo**: log \_ kafkaserver \_ CL, log \_ kafkacontroller \_ CL</li></ul>|

## <a name="hbase-workload"></a>HBase iş yükü

| Yeni Tablo | Ayrıntılar |
| --- | --- |
| HDInsightHBaseMetrics | <ul><li>**Açıklama**: Bu tablo HBase 'den JMX ölçümlerini içerir. Eski şema sütununda listelenen tablolardaki tüm JMX ölçümlerini içerir. Eski tabloların aksine, her satır bir ölçüm içerir.</li><li>**Eski tablo**: ölçümler \_ regionserver \_ CL, ölçümler \_ regionserver \_ Wal \_ CL, ölçümler \_ regionserver \_ IPC \_ CL, ölçümler \_ regionserver \_ işletim sistemi \_ CL, ölçümler \_ regionserver \_ çoğaltma \_ CL, ölçümler \_ restserver \_ CL, ölçümler \_ restserver \_ JVM \_ CL, ölçümler \_ hmaster \_ atamakaydı Yöneticisi \_ CL, ölçümler hmaster IPC CL, ölçümler hmaser OS \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ CL, ölçümler hmaster, ölçümler hmaster FS CL</li></ul>|
| HDInsightHBaseLogs | <ul><li>**Açıklama**: Bu tablo HBase 'den ve ilgili bileşenlerinden Günlükler Içerir: Phoenix ve bir.</li><li>**Eski tablo**: günlük \_ regionserver \_ CL, log \_ restserver \_ CL, log \_ phoenixserver \_ CL, log \_ hmaster \_ CL, log \_ hdfsnamencode \_ CL, günlük \_ çöp \_ toplayıcısı \_ CL</li></ul>|

## <a name="storm-workload"></a>Fırtınası iş yükü

| Yeni Tablo | Ayrıntılar |
| --- | --- |
| HDInsightStormMetrics | <ul><li>**Açıklama**: Bu tablo, eski tablolar bölümündeki tablolarla aynı JMX ölçümlerini içerir. Satırları her kayıt için bir ölçüm içerir.</li><li>**Eski tablo**: ölçümler \_ stormnımbus \_ CL, ölçümler \_ stormgözetmen \_ CL</li></ul>|
| HDInsightStormTopologyMetrics | <ul><li>**Açıklama**: Bu tablo, fırtınası için topoloji düzeyi ölçümlerini içerir. Bu, eski tablolar bölümünde listelenen tabloyla aynı şekildir.</li><li>**Eski tablo**: ölçüm \_ stormrest \_ CL</li></ul>|
| HDInsightStormLogs | <ul><li>**Açıklama**: Bu tablo, fırtınası 'dan oluşturulan tüm günlükleri içerir.</li><li>**Eski tablo**: log \_ Gözetmen \_ CL, log \_ Nimbus \_ CL</li></ul>|

## <a name="oozie-workload"></a>Oozie iş yükü

| Yeni Tablo | Ayrıntılar |
| --- | --- |
| HDInsightOozieLogs | <ul><li>**Açıklama**: Bu tablo, Oozie çerçevesinden oluşturulan tüm günlükleri içerir.</li><li>**Eski tablo**: günlük \_ Oozie \_ CL</li></ul>|

## <a name="next-steps"></a>Sonraki adımlar
[HDInsight kümelerini izlemek için Azure Izleyici günlüklerini sorgulama](hdinsight-hadoop-oms-log-analytics-use-queries.md)
