---
title: Analiz sorguları çalıştırma
description: Çok kiracılı bir uygulamadaki birden çok Azure SQL veritabanı veritabanından ayıklanan verileri kullanan çapraz kiracı Analizi sorguları.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: anjangsh,billgib,genemi
ms.date: 09/19/2018
ms.openlocfilehash: 067afd09f942b8062825553a3cf90f715e8d3938
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822141"
---
# <a name="cross-tenant-analytics-using-extracted-data---multi-tenant-app"></a>Ayıklanan verileri kullanan çapraz kiracı analizi-çok kiracılı uygulama
 
Bu öğreticide, çok kiracılı bir uygulama için kapsamlı bir analiz senaryosuna yol göstereceğiz. Bu senaryoda analizler, geliştiricilerin işletmelerin akıllı kararlar vermesine nasıl olanak verebileceğinizi gösterir. Parçalı veritabanından ayıklanan verileri kullanarak, örnek Wingtip bilet SaaS uygulamasının kullanımları dahil olmak üzere kiracı davranışına yönelik Öngörüler elde etmek için analiz kullanırsınız. Bu senaryo üç adımdan oluşur: 

1.  Her kiracı veritabanından bir analiz deposuna **veri ayıklayın** .
2.  **Ayıklanan verileri** analiz Işleme için iyileştirin.
3.  Karar verme konusunda rehberlik sağlayan yararlı Öngörüler çizmek için **Iş zekası** araçlarını kullanın. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> - Verileri içine ayıklamak için kiracı Analizi deposu oluşturun.
> - Her kiracı veritabanından analiz deposuna veri ayıklamak için esnek işleri kullanın.
> - Ayıklanan verileri iyileştirin (bir yıldız şemasına yeniden düzenleyin).
> - Analiz veritabanını sorgulayın.
> - Veri görselleştirme için Power BI kullanarak kiracı verilerindeki eğilimleri vurgulayın ve iyileştirmeler için öneri alın.

![Mimari Tureoverview](media/saas-multitenantdb-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Çevrimdışı kiracı Analizi kalıbı

Geliştirdiğiniz SaaS uygulamalarının bulutta depolanan büyük miktarda kiracı verisi erişimi vardır. Veriler, uygulamanızın işlemi ve kullanımı ve kiracıların davranışı hakkında zengin bir Öngörüler kaynağı sağlar. Bu Öngörüler, uygulama ve platformdaki Özellik geliştirme, kullanılabilirlik iyileştirmeleri ve diğer yatırımları yönlendirebilir.

Tüm veriler yalnızca bir çok kiracılı veritabanında olduğunda tüm kiracılar için verilere erişim basittir. Ancak erişim, binlerce veritabanı genelinde ölçeklendirmeye dağıtıldığında daha karmaşıktır. Karmaşıklığın bir yolu, verileri bir analiz veritabanına veya bir veri ambarına ayıklamaya yönelik bir yoldur. Ardından, tüm kiracıların bilet verilerinden Öngörüler toplamak için veri ambarını sorgulayın.

Bu öğreticide, bu örnek SaaS uygulaması için kapsamlı bir analiz senaryosu sunulmaktadır. İlk olarak, elastik işler her kiracı veritabanından veri ayıklanmasını zamanlamak için kullanılır. Veriler bir analiz deposuna gönderilir. Analytics Mağazası bir SQL veritabanı veya SQL veri ambarı olabilir. Büyük ölçekli veri ayıklama için [Azure Data Factory](../data-factory/introduction.md) yapılır.

Ardından, toplanmış veriler bir [yıldız şeması](https://www.wikipedia.org/wiki/Star_schema) tabloları kümesine ayrılabilir. Tablolar bir merkezi olgu tablosu ve ilgili boyut tablolarından oluşur:

- Yıldız şeması içindeki merkezi olgu tablosu bilet verileri içerir.
- Boyut tabloları, Venn, olaylar, müşteriler ve satın alma tarihleri hakkındaki verileri içerir.

Merkezi ve boyut tabloları birlikte verimli analitik işleme sağlar. Bu öğreticide kullanılan yıldız şeması aşağıdaki görüntüde görüntülenir:
 
![StarSchema](media/saas-multitenantdb-tenant-analytics/StarSchema.png)

Son olarak, yıldız şeması tabloları sorgulanır. Sorgu sonuçları, kiracı davranışı ve uygulamanın kullanımıyla ilgili öngörüleri vurgulamak için görsel olarak görüntülenir. Bu yıldız şeması ile, aşağıdaki gibi öğeleri bulmaya yardımcı olan sorguları çalıştırabilirsiniz:

- Bilet ve bu mekden kim satın alan.
- Aşağıdaki alanlardaki gizli desenler ve eğilimler:
    - Bilet satışları.
    - Her bir mekanın göreli popülerliği.

Her kiracının hizmeti nasıl kullandığını anlamak, ihtiyaçlarını karşılamak için hizmet planları oluşturma fırsatı sağlar. Bu öğreticide, kiracı verilerinden eğli olabilecek temel Öngörüler örnekleri sağlanmaktadır.

## <a name="setup"></a>Kurulum

### <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlamak için aşağıdaki ön koşulların karşılandığından emin olun:

- Wingtip bilet SaaS çok kiracılı veritabanı uygulaması dağıtılır. Beş dakikadan kısa bir süre içinde dağıtmak için bkz [. Wingtip biletleri SaaS çok kiracılı veritabanı uygulaması dağıtma ve araştırma](saas-multitenantdb-get-started-deploy.md)
- Wingtip SaaS betikleri ve uygulama [kaynak kodu](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub 'dan indirilir. İçindekileri Ayıklamadan önce *ZIP dosyasının engellemesini kaldırmayı* unutmayın. Wingtip bilet SaaS betiklerini indirme ve engellemesini kaldırma adımları için [genel kılavuza](saas-tenancy-wingtip-app-guidance-tips.md) göz atın.
- Power BI Desktop yüklendi. [Power BI Desktop indir](https://powerbi.microsoft.com/downloads/)
- Ek kiracıların toplu işi sağlandı, [**kiracılar sağlama öğreticisine**](saas-multitenantdb-provision-and-catalog.md)bakın.
- Bir iş Aracısı ve iş Aracısı veritabanı oluşturuldu. [**Şema yönetimi öğreticisindeki**](saas-multitenantdb-schema-management.md#create-a-job-agent-database-and-new-job-agent)ilgili adımlara bakın.

### <a name="create-data-for-the-demo"></a>Demo için veri oluşturma

Bu öğreticide, Bilet satış verilerinde analiz yapılır. Geçerli adımda, tüm kiracılara yönelik bilet verileri oluşturun.  Daha sonra bu veriler analiz için ayıklanır. *Daha önce açıklandığı gibi kiracılar toplu işlemini sağladığınızdan emin olun, böylece anlamlı bir veri miktarına sahip olursunuz*. Yeterince büyük miktarda veri, farklı bilet satın alma desenlerinin bir aralığını açığa çıkarır.

1. **PowerShell ISE**'de *. ..\Learning Modules\işletimsel Analtics\tenant Analytics\Demo-TenantAnalytics.ps1*açın ve aşağıdaki değeri ayarlayın:
    - **$DemoScenario** tüm havalandırma ve olaylar için = **1** satın alma bileti
2. Komut dosyasını çalıştırmak ve her bir mekan her olay için bilet satın alma geçmişi oluşturmak için **F5** tuşuna basın.  Betik on binlerce bilet üretmek için birkaç dakika çalışır.

### <a name="deploy-the-analytics-store"></a>Analiz deposunu dağıtma
Genellikle tüm kiracı verilerini tutan çok sayıda hareketsel birleştirilmiş veritabanı vardır. Parçalı veritabanındaki kiracı verilerini tek bir analiz deposunda toplamanız gerekir. Toplama, verilerin verimli bir şekilde sorgulanmalarını sağlar. Bu öğreticide, toplanan verileri depolamak için bir Azure SQL veritabanı veritabanı kullanılır.

Aşağıdaki adımlarda, **tenantanalytics**adlı analiz deposunu dağıtırsınız. Ayrıca öğreticide daha sonra doldurulmuş önceden tanımlanmış tablolar da dağıtabilirsiniz:
1. PowerShell ıSE 'de *. ..\Learning Modules\işletimsel Analtics\tenant Analytics\Demo-TenantAnalytics.ps1* açın 
2. Betikteki $DemoScenario değişkenini, analiz deposu seçimiyle eşleşecek şekilde ayarlayın. Öğrenme amacıyla, columnstore olmadan SQL veritabanı önerilir.
    - SQL veritabanı 'nı columnstore olmadan kullanmak için **$DemoScenario** = **2** ' yi ayarlayın.
    - SQL veritabanını columnstore ile kullanmak için, **$DemoScenario** = **3** ' ü ayarlayın  
3. , Kiracı Analizi deposunu oluşturan tanıtım betiğini ( *Deploy-TenantAnalytics\<XX >. ps1* betiğini çağırır) çalıştırmak için **F5** tuşuna basın. 

Uygulamayı dağıttığınıza ve ilgi çekici kiracı verileriyle doldurduktan sonra, [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) kullanarak **tenants1-MT-\<User\>** ve **Katalog-MT-\<Kullanıcı\>** sunucularını şu şekilde bağlayın Login = *Geliştirici*, Password = *P\@ssword1*.

![Mimari Tureoverview](media/saas-multitenantdb-tenant-analytics/ssmsSignIn.png)

Nesne Gezgini, aşağıdaki adımları gerçekleştirin:

1. *Tenants1-MT-\<kullanıcı\>* sunucusunu genişletin.
2. Veritabanları düğümünü genişletin ve birden çok kiracı içeren *tenants1* veritabanı ' na bakın.
3. *Katalog-MT-\<kullanıcı\>* sunucusunu genişletin.
4. Analiz deposunu ve jobaccount veritabanını gördiğinizi doğrulayın.

Analiz deposu düğümünü genişleterek SSMS Nesne Gezgini aşağıdaki veritabanı öğelerine bakın:

- Tablo **biletleri** , ham veri ve **olayları** , işlenmemiş veri tutan verileri kiracı veritabanlarından ayıklamalıdır.
- Yıldız şeması tabloları **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**ve **dim_Dates**.
- **Sp_ShredRawExtractedData** saklı yordamı, yıldız şeması tablolarını ham veri tablolarından doldurmak için kullanılır.

![tenantAnalytics](media/saas-multitenantdb-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Veri ayıklama 

### <a name="create-target-groups"></a>Hedef grupları oluştur 

Devam etmeden önce, iş hesabını ve jobaccount veritabanını dağıttığınızdan emin olun. Sonraki adımlarda, parçalı kiracılar veritabanından veri ayıklamak ve verileri analiz deposunda depolamak için esnek Işler kullanılır. Ardından ikinci iş, verileri shreds ve yıldızı-Schema 'daki tablolara depolar. Bu iki iş iki farklı hedef gruba karşı çalışır, yani **Tenantgroup** ve **analiz ticsgroup**. Ayıklama işi, tüm kiracı veritabanlarını içeren TenantGroup 'a karşı çalışır. Ayırma işi, yalnızca analiz deposunu içeren analiz Ticsgroup 'a karşı çalışır. Aşağıdaki adımları kullanarak hedef grupları oluşturun:

1. SSMS 'de, Katalog-MT-\<User\>içindeki **jobaccount** veritabanına bağlanın.
2. SSMS 'de *. ..\Learning Modules\işlemsel analiz Tics\tenant Analytics \ TargetGroups. SQL* dosyasını açın 
3. Komut dosyasının en üstündeki @User değişkenini değiştirerek, `<User>` değiştirerek, Wingtip bilet SaaS çok kiracılı veritabanı uygulamasını dağıtırken kullanılan Kullanıcı değeriyle değiştirin.
4. İki hedef grubunu oluşturan betiği çalıştırmak için **F5** tuşuna basın.

### <a name="extract-raw-data-from-all-tenants"></a>Tüm kiracılardan ham verileri Ayıkla

İşlemler, *bilet ve müşteri* verileri için *olay ve mekme* verilerinden daha sık gerçekleşebilir. Bu nedenle, Bilet ve müşteri verilerini, olay ve mekan verileri ayıklamadan ayrı ve daha sık bir şekilde ayıklamayı düşünün. Bu bölümde, iki ayrı iş tanımlar ve zamanlayabilirsiniz:

- Bilet ve müşteri verilerini ayıklayın.
- Olay ve mekan verilerini ayıklayın.

Her iş verilerini ayıklar ve analiz deposuna gönderir. Ayıklanan verileri analiz yıldızı-şemasına shreds ayrı bir iş vardır.

1. SSMS 'de, Katalog-MT-\<User\> Server 'daki **jobaccount** veritabanına bağlanın.
2. SSMS 'de, *. ..\Learning Modules\işletimsel Analtics\tenant Analtics\tenant Analyzer*' ı açın.
3. Betiğin en üstünde @User değiştirin ve `<User>`, Wingtip bilet SaaS çok kiracılı veritabanı uygulamasını dağıtırken kullanılan kullanıcı adıyla değiştirin. 
4. Her kiracı veritabanından bilet ve müşteri verilerini çıkaran işi oluşturan ve çalıştıran betiği çalıştırmak için **F5** ' e basın. İş, verileri analiz deposuna kaydeder.
5. Tablonun tüm kiracılardan bilet bilgileriyle doldurulduğundan emin olmak için tenantanalytics veritabanındaki bilet Srawdata tablosunu sorgulayın.

![ticketExtracts](media/saas-multitenantdb-tenant-analytics/ticketExtracts.png)

Yukarıdaki adımları yineleyin; bu süre dışında, adım 2 ' de **\Extractbilet S.SQL** **dosyasını \Extractvenueyetts.exe SQL** ile değiştirin.

İş başarıyla çalıştırıldığında, analiz deposundaki rawData tablosunun olayları, tüm kiracılardan yeni olaylar ve havalandırma bilgileri ile doldurulur. 

## <a name="data-reorganization"></a>Veri yeniden organizasyonu

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Yıldız-şema tablolarını doldurmak için ayıklanan veriler

Sonraki adım, ayıklanan ham verilerin analiz sorguları için iyileştirilmiş bir tablo kümesine bölünmesi içindir. Yıldız şeması kullanılır. Bir merkezi olgu tablosu bağımsız bilet satış kayıtlarını barındırır. Boyut tabloları, Venn, olaylar, müşteriler ve satın alma tarihleri hakkında verilerle doldurulur. 

Öğreticinin bu bölümünde, ayıklanan ham verileri, yıldız şeması tablolarındaki verilerle birleştiren bir iş tanımlar ve çalıştırırsınız. Birleştirme işi tamamlandıktan sonra ham veriler silinir ve tabloları bir sonraki kiracı verileri ayıklama işi tarafından doldurulmaya başlamaya bırakılır.

1. SSMS 'de, Katalog-MT-\<User\>içindeki **jobaccount** veritabanına bağlanın.
2. SSMS 'de *. ..\Learning Modules\işletimsel Analtics\tenant Analtics\shredrawextracteddata.exe*dosyasını açın.
3. Analiz deposunda sp_ShredRawExtractedData saklı yordamını çağıran bir işi tanımlamak üzere betiği çalıştırmak için **F5** ' e basın.
4. İşin başarıyla çalışması için yeterli zaman yok.
    - İş için işler. jobs_execution tablosunun **yaşam döngüsü** sütununu kontrol edin. Devam etmeden önce işin **başarılı** olduğundan emin olun. Başarılı bir çalıştırma aşağıdaki grafiğe benzer verileri görüntüler:

![shreddingJob](media/saas-multitenantdb-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Veri araştırması

### <a name="visualize-tenant-data"></a>Kiracı verilerini görselleştirin

Star şeması tablosundaki veriler, analizinizi için gereken tüm bilet satış verilerini sağlar. Büyük veri kümelerinde eğilimleri görmeyi kolaylaştırmak için, grafiği grafiksel olarak görselleştirmeniz gerekir.  Bu bölümde, ayıkladığınız ve düzenlediğiniz kiracı verilerini işlemek ve görselleştirmek için **Power BI** kullanmayı öğreneceksiniz.

Power BI bağlanmak ve daha önce oluşturduğunuz görünümleri içeri aktarmak için aşağıdaki adımları kullanın:

1. Power BI Desktop 'ı başlatın.
2. Giriş şeridinde **veri al**' ı seçin ve **daha fazla...** seçeneğini belirleyin. menüsünde.
3. **Veri al** PENCERESINDE Azure SQL veritabanı ' nı seçin.
4. Veritabanı oturum açma penceresinde sunucunuzun adını (Catalog-MT-\<User\>. database.windows.net) girin. **Veri bağlantısı modu**Için **içeri aktar** ' ı seçin ve ardından Tamam ' a tıklayın. 

    ![Powerbııgın](media/saas-multitenantdb-tenant-analytics/powerBISignIn.PNG)

5. Sol bölmedeki **veritabanı** ' nı seçin, ardından Kullanıcı adı = *Geliştirici*yazın ve Password = *P\@ssword1*girin. **Bağlan**'a tıklayın.  

    ![Databasesignın](media/saas-multitenantdb-tenant-analytics/databaseSignIn.PNG)

6. **Gezgin** bölmesinde, analiz veritabanı altında, yıldız şema tablolarını seçin: fact_Tickets, dim_Events, dim_Venues, dim_Customers ve dim_Dates. Sonra **Yükle**' yi seçin. 

Tebrikler! Verileri başarıyla Power BI yüklendi. Artık kiracılarınız hakkında öngörüler elde etmenize yardımcı olmak için ilginç görselleştirmeleri keşfetmeye başlayabilirsiniz. Ardından analizler tarafından, Wingtip bilet iş ekibine veri odaklı öneriler sağlamanıza nasıl olanak sağlayacağız. Öneriler, iş modelini ve müşteri deneyimini iyileştirmenize yardımcı olabilir.

Bilet satış verilerini çözümleyerek, tüm kullanımlar genelinde kullanımdaki çeşitliliği görebilirsiniz. Her bir mekanın sattığı toplam bilet sayısının çubuk grafiğini çizmek için Power BI ' de aşağıdaki seçenekleri seçin. Bilet oluşturucusunun rastgele çeşitlemesi nedeniyle, sonuçlarınız farklı olabilir.
 
![Totalbilet sbyvenlar](media/saas-multitenantdb-tenant-analytics/TotalTicketsByVenues.PNG)

Yukarıdaki çizim, her bir mekan tarafından satılan bilet sayısının değiştiğini onaylar. Daha fazla bilet satmaya yönelik havalandırma, hizmetinizi daha az bilet satmaya kıyasla daha fazla şekilde kullanıyor. Burada, farklı kiracı ihtiyaçlarına göre kaynak ayırmayı uyarlamak için bir fırsat olabilir.

Bilet satışlarının zaman içinde nasıl değişeceğini görmek için verileri daha fazla analiz edebilirsiniz. Her gün 60 gün boyunca satılan toplam bilet sayısını çizmek için Power BI ' de aşağıdaki seçenekleri seçin.
 
![SaleVersusDate](media/saas-multitenantdb-tenant-analytics/SaleVersusDate.PNG)

Yukarıdaki grafikte, bazı havalandırma noktaları için bilet satış ani artış görüntülenir. Bu ani artışlar, bazı havalandırma, sistem kaynaklarının orantısız olarak tüketilme fikrini zorlayacaktır. Şimdiye kadar, ani artışlar meydana geldiğinde açık bir düzende yoktur.

Daha sonra bu en yüksek satış günlerinin önemini daha fazla araştırmak istiyorsunuz. Bilet satışa alındıktan sonra bu tepe noktaları ne zaman oluşur? Gün başına satılan anahtarları çizmek için Power BI ' de aşağıdaki seçenekleri seçin.

![SaleDayDistribution](media/saas-multitenantdb-tenant-analytics/SaleDistributionPerDay.PNG)

Yukarıdaki çizimde, bazı havalandırma satışları satışın ilk gününde çok sayıda bilet sattığına gösterilmektedir. Bilet Bu havalandırma noktaları üzerinden satışa geldiğinde, bir Mad aceleniz gibi görünüyor. Etkinliğin birkaç kez bu patlaması, diğer kiracılara yönelik hizmeti etkileyebilir.

Bu bu Venn tarafından barındırılan tüm olaylar için bu Mad aceleniz doğru olup olmadığını görmek için verilerin ayrıntılarına gidebilirsiniz. Önceki çizimler bölümünde, contoso Concert salonu 'un çok sayıda bilet sattığını ve contoso 'nun Ayrıca belirli günlerde bilet satışındaki bir ani artış olduğunu gözlemlediniz. Contoso Concert salonu için birikimli bilet satışları çizmek üzere Power BI seçenekleriyle, olayların her biri için satış eğilimlerine odaklanarak oynayın. Tüm olaylar aynı satış düzenine mi uyar?

![ContosoSales](media/saas-multitenantdb-tenant-analytics/EventSaleTrends.PNG)

Contoso Concert salonu için önceki çizimde, Mad aceleniz tüm olaylar için gerçekleşmediğinden emin olmak üzere gösterilmektedir. Diğer havalandırma için satış eğilimlerini görmek üzere filtre seçenekleriyle birlikte oynayın.

Bilet satışı desenlerine yönelik Öngörüler, Wingtip biletlerinin iş modellerini iyileştirmesine neden olabilirler. Tüm kiracılar eşit olarak doldurulmak yerine, Wingtip, farklı işlem boyutlarına sahip hizmet katmanlarını göstermelidir. Günde daha fazla Bilet satmayı gerektiren daha büyük havalandırma noktaları, daha yüksek bir hizmet düzeyi sözleşmesi (SLA) ile daha yüksek bir katman sunulamaz. Bu havalandırma kaynakları, veritabanlarının veritabanlarına göre daha yüksek kaynak limitleriyle havuza yerleştirilmesini sağlayabilir. Her hizmet katmanında saatlik satış tahsisi olabilir ve bu da ayırmayı aşmamak için ek ücretler ücretlendirilir. Düzenli olarak elde edilen satışları olan büyük havalandırma noktaları, daha yüksek katmanlardan faydalanır ve Wingtip biletleri, hizmetini daha verimli bir şekilde kullanabilir.

Bu arada, bazı Wingtip bilet müşterileri, hizmet maliyetini yaslamak için yeterli bilet satmaya uğraşır. Belki de bu içgörüler, anahtar satışlarına yönelik çağrı satışlarını artırma fırsatına sahiptir. Daha yüksek satış, hizmetin algılanan değerini artırır. Fact_Tickets sağ tıklayıp **Yeni ölçü**seçeneğini belirleyin. **Averagebilet Ssold**adlı yeni ölçü için aşağıdaki ifadeyi girin:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Göreli başarısını belirlemek için her bir mekan satılan yüzde biletlerini işaretlemek üzere aşağıdaki görselleştirme seçeneklerini belirleyin.

![Analiz Ticsgörünümleri](media/saas-multitenantdb-tenant-analytics/AvgTicketsByVenues.PNG)

Yukarıdaki çizimde, çoğu havalandırma, biletlerinin %80 ' undan daha fazla satışı olsa da, bazılarına çok sayıda oturmuş kadar fazla bilgi veren bir süre sonra gösterilmektedir. Her bir mekan için satılan anahtarların maksimum veya minimum yüzdesini seçmek için değerler ile etrafında bir oynatma yapın.

Daha önce, bu bilet satışlarının tahmin edilebilir desenleri takip etmek için analizinizi daha ayrıntılı olarak fark edersiniz. Bu bulgu, Wingtip biletlerinin, dinamik fiyatlandırma önererek bilet satışlarını artırma konusunda yardımcı olmasına izin verebilir. Bu bulma, her olay için bilet satışlarını tahmin etmek üzere makine öğrenimi tekniklerini kullanmayı bir fırsat ortaya çıkarmaktadır. Tahmine dayalı, bilet satışlarında indirim teklif teklifi için de yapılabilir. Power BI Embedded bir olay yönetimi uygulamasıyla tümleştirilebilir. Tümleştirme, öngörülen satışları ve farklı indirimlerin etkisini görselleştirmenize yardımcı olabilir. Uygulama, doğrudan analiz görüntüsüne uygulanan en uygun indirimin Devine sağlanmasına yardımcı olabilir.

Wingtip bilet SaaS çok kiracılı veritabanı uygulamasındaki kiracı verilerinde eğilimleri gözlemlediniz. Uygulamanın SaaS uygulama satıcıları için iş kararları verebilmesine diğer yollarla uyum sağlayabilirsiniz. Satıcılar kiracılarının ihtiyaçlarına daha iyi bir şekilde erişebilir. Bu öğreticide, işletmelerin veri odaklı kararlar vermesine olanak sağlamak amacıyla kiracı verilerinde analizler gerçekleştirmek için gereken araçlar bulunur.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> - Önceden tanımlanmış yıldız şeması tabloları ile bir kiracı Analizi veritabanı dağıtıldı
> - Tüm kiracı veritabanından veri ayıklamak için kullanılan elastik işler
> - Ayıklanan verileri, analiz için tasarlanan bir yıldız şeması içindeki tablolarda birleştirme
> - Analiz veritabanını sorgulama 
> - Kiracı verilerindeki eğilimleri gözlemlemek için veri görselleştirme Power BI kullanın 

Tebrikler!

## <a name="additional-resources"></a>Ek kaynaklar

[Wingtip SaaS uygulaması üzerine inşa edilen ek öğreticiler](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials). 
- [Elastik işler](elastic-jobs-overview.md).
- [Ayıklanan verileri kullanan çapraz kiracı Analizi-tek kiracılı uygulama](saas-tenancy-tenant-analytics.md) 