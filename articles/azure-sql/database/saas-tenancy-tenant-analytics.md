---
title: Ayıklanan verileri kullanan çapraz kiracı Analizi
description: Tek bir kiracı uygulamasındaki birden çok Azure SQL veritabanından ayıklanan verileri kullanan çapraz kiracı Analizi sorguları.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 98896b5b728a729a29f989b3b9a76f29131af8d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93305979"
---
# <a name="cross-tenant-analytics-using-extracted-data---single-tenant-app"></a>Ayıklanan verileri kullanan çapraz kiracı Analizi-tek kiracılı uygulama
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]
 
Bu öğreticide, tek bir kiracı uygulamasıyla ilgili kapsamlı bir analiz senaryosuna kılavuzluk edersiniz. Bu senaryoda analizler, geliştiricilerin işletmelerin akıllı kararlar vermesine nasıl olanak verebileceğinizi gösterir. Her kiracı veritabanından ayıklanan verileri kullanarak, örnek Wingtip bilet SaaS uygulamasının kullanımı dahil olmak üzere, kiracı davranışına yönelik Öngörüler elde etmek için analiz kullanırsınız. Bu senaryo üç adımdan oluşur: 

1.  Her kiracı veritabanından veri **ayıklayın** ve bir analiz deposuna **yükleyin** .
2.  Analiz işleme için **ayıklanan verileri dönüştürün** .
3.  Karar verme konusunda rehberlik sağlayan yararlı Öngörüler çizmek için **iş zekası** araçlarını kullanın. 

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> - Verileri içine ayıklamak için kiracı Analizi deposu oluşturun.
> - Her kiracı veritabanından analiz deposuna veri ayıklamak için esnek işleri kullanın.
> - Ayıklanan verileri iyileştirin (bir yıldız şemasına yeniden düzenleyin).
> - Analiz veritabanını sorgulayın.
> - Veri görselleştirme için Power BI kullanarak kiracı verilerindeki eğilimleri vurgulayın ve iyileştirmeler için öneri alın.

![Diyagramda Bu makale için kullanılan mimariye genel bir bakış gösterilmektedir.](./media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Çevrimdışı kiracı Analizi kalıbı

Çok kiracılı SaaS uygulamaları, genellikle bulutta depolanan büyük miktarda kiracı verisine sahiptir. Bu veriler, uygulamanızın işlemi ve kullanımı ve Kiracılarınızın davranışı hakkında zengin bir Öngörüler kaynağı sağlar. Bu Öngörüler, uygulama ve platformdaki Özellik geliştirme, kullanılabilirlik iyileştirmeleri ve diğer yatırımları yönlendirebilir.

Tüm veriler yalnızca bir çok kiracılı veritabanında olduğunda tüm kiracılar için verilere erişim basittir. Ancak erişim, büyük olasılıkla binlerce veritabanı üzerinde ölçekli olarak dağıtıldığında daha karmaşıktır. Karmaşıklığın bir yolu ve işlem verilerinde analiz sorgularının etkilerini en aza indirmek, verileri amacı tasarlanan bir analiz veritabanına veya veri ambarına ayıklamaya yönelik bir yoldur.

Bu öğreticide, Wingtip bilet SaaS uygulaması için kapsamlı bir analiz senaryosu sunulmaktadır. İlk olarak, *elastik işler* her kiracı veritabanından veri ayıklamak ve bir analiz deposundaki hazırlama tablolarına yüklemek için kullanılır. Analytics Mağazası bir SQL veritabanı ya da adanmış bir SQL havuzu olabilir. Büyük ölçekli veri ayıklama için [Azure Data Factory](../../data-factory/introduction.md) önerilir.

Ardından, toplanmış veriler bir [yıldız şeması](https://www.wikipedia.org/wiki/Star_schema) tabloları kümesine dönüştürülür. Tablolar bir merkezi olgu tablosu ve ilgili boyut tablolarından oluşur.  Wingtip biletleri için:

- Yıldız şeması içindeki merkezi olgu tablosu bilet verileri içerir.
- Boyut tabloları, Venn, olaylar, müşteriler ve satın alma tarihlerini anlatmaktadır.

Merkezi olgu ve boyut tabloları birlikte verimli analitik işleme sağlar. Bu öğreticide kullanılan yıldız şeması aşağıdaki görüntüde gösterilmiştir:
 
![Mimari Tureoverview](./media/saas-tenancy-tenant-analytics/StarSchema.png)

Son olarak, analitik mağaza, kiracı davranışı ve Wingtip bilet uygulamasının kullanımıyla ilgili öngörüleri vurgulamak için **Power BI** kullanılarak sorgulanır. Şunu içeren sorguları çalıştırırsınız:
 
- Her bir mekanın göreli popülerliğini göster
- Farklı olaylar için bilet satışlarında desenleri vurgulama
- Olaylarını satmaya yönelik farklı havalandırma noktaları için göreli başarıyı göster

Her bir kiracının hizmeti nasıl kullandığını anlamak, hizmeti Monaya çevirme ve kiracının daha başarılı olmasını sağlamak için hizmeti geliştirme seçeneklerini incelemek üzere kullanılır. Bu öğretici, kiracı verilerinden eğli olabilecek Öngörüler türlerine yönelik temel örnekler sağlar.

## <a name="setup"></a>Kurulum

### <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdaki ön koşulların karşılandığından emin olun:

- Kiracı uygulaması başına Wingtip bilet SaaS veritabanı dağıtılır. Beş dakikadan kısa bir süre içinde dağıtmak için bkz [. Wingtip SaaS uygulamasını dağıtma ve araştırma](./saas-dbpertenant-get-started-deploy.md)
- Kiracı betikleri başına Wingtip biletleri SaaS veritabanı ve uygulama [kaynak kodu](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) , GitHub 'dan indirilir. Bkz. indirme yönergeleri. İçindekileri Ayıklamadan önce *ZIP dosyasının engellemesini kaldırmayı* unutmayın. Wingtip bilet SaaS betiklerini indirme ve engellemesini kaldırma adımları için [genel kılavuza](saas-tenancy-wingtip-app-guidance-tips.md) göz atın.
- Power BI Desktop yüklendi. [Power BI Desktop İndirin](https://powerbi.microsoft.com/downloads/)
- Ek kiracıların toplu işi sağlandı, [**kiracılar sağlama öğreticisine**](./saas-dbpertenant-provision-and-catalog.md)bakın.
- Bir iş hesabı ve iş hesabı veritabanı oluşturuldu. [**Şema yönetimi öğreticisindeki**](./saas-tenancy-schema-management.md#create-a-job-agent-database-and-new-job-agent)ilgili adımlara bakın.

### <a name="create-data-for-the-demo"></a>Demo için veri oluşturma

Bu öğreticide, Bilet satış verilerinde analiz yapılır. Geçerli adımda, tüm kiracılara yönelik bilet verileri oluşturun.  Daha sonra bu veriler analiz için ayıklanır. *Daha önce açıklandığı gibi kiracılar toplu işlemini sağladığınızdan emin olun, böylece anlamlı bir veri miktarına sahip olursunuz*. Yeterince büyük miktarda veri, farklı bilet satın alma desenlerinin bir aralığını açığa çıkarır.

1. PowerShell ıSE 'de *. ..\Learning Modules\işlemsel analiz Tics\tenant Analytics\Demo-TenantAnalytics.ps1* açın ve aşağıdaki değeri ayarlayın:
    - **$DemoScenario**  =  **1** tüm havalandırma olaylardaki olaylar için bilet satın al
2. Komut dosyasını çalıştırmak ve her bir mekan her olay için bilet satın alma geçmişi oluşturmak için **F5** tuşuna basın.  Betik on binlerce bilet üretmek için birkaç dakika çalışır.

### <a name="deploy-the-analytics-store"></a>Analiz deposunu dağıtma
Genellikle tüm kiracı verilerini tutan çok sayıda işlem veritabanı vardır. Birçok işlem veritabanından kiracı verilerini tek bir analiz deposuna toplamanız gerekir. Toplama, verilerin verimli bir şekilde sorgulanmalarını sağlar. Bu öğreticide, toplanan verileri depolamak için bir Azure SQL veritabanı kullanılır.

Aşağıdaki adımlarda, **tenantanalytics** adlı analiz deposunu dağıtırsınız. Ayrıca öğreticide daha sonra doldurulmuş önceden tanımlanmış tablolar da dağıtabilirsiniz:
1. PowerShell ıSE 'de *. ..\Learning Modules\işlemsel analiz Tics\tenant Analytics\Demo-TenantAnalytics.ps1* açın 
2. Betikteki $DemoScenario değişkenini analiz deposu seçimiyle eşleşecek şekilde ayarlayın:
    - SQL veritabanı 'nı sütun deposu olmadan kullanmak için **$DemoScenario**  =  **2** ' yi ayarlayın.
    - SQL veritabanı 'nı sütun deposuyla birlikte kullanmak için, **$DemoScenario**  =  **3** ' ü ayarlayın  
3. , Kiracı Analizi deposunu oluşturan demo betiğini ( *Deploy-TenantAnalytics \<XX> . ps1* betiğini çağırır) çalıştırmak için **F5** tuşuna basın. 

Uygulamayı dağıttığınıza ve ilgi çekici kiracı verileriyle doldurduktan sonra, oturum açma = *Geliştirici*, parola = *P \@ ssword1* kullanarak **tenants1-DPT- &lt; user &gt;** ve **Catalog-DPT- &lt; user &gt;** sunucularını bağlamak için [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) kullanın. Daha fazla bilgi için bkz. [giriş öğreticisi](./saas-dbpertenant-wingtip-app-overview.md) .

![SQL Server bağlanmak için gereken bilgileri gösteren ekran görüntüsü.](./media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

Nesne Gezgini, aşağıdaki adımları gerçekleştirin:

1. *Tenants1-DPT- &lt; user &gt;* sunucusunu genişletin.
2. Veritabanları düğümünü genişletin ve kiracı veritabanlarının listesini görüntüleyin.
3. *Katalog-DPT- &lt; Kullanıcı &gt;* sunucusunu genişletin.
4. Analiz deposunu ve jobaccount veritabanını gördiğinizi doğrulayın.

Analiz deposu düğümünü genişleterek SSMS Nesne Gezgini aşağıdaki veritabanı öğelerine bakın:

- Tablo **biletleri** , ham veri ve **olayları** , işlenmemiş veri tutan verileri kiracı veritabanlarından ayıklamalıdır.
- Yıldız şeması tabloları **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** ve **dim_Dates**.
- Saklı yordam, yıldız şeması tablolarını ham veri tablolarından doldurmak için kullanılır.

![SSMS Nesne Gezgini gösterilen veritabanı öğelerinin ekran görüntüsü.](./media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Veri ayıklama 

### <a name="create-target-groups"></a>Hedef grupları oluştur 

Devam etmeden önce, iş hesabını ve jobaccount veritabanını dağıttığınızdan emin olun. Sonraki adımlarda, her bir kiracı veritabanından veri ayıklamak ve verileri analiz deposunda depolamak için esnek Işler kullanılır. Ardından ikinci iş, verileri shreds ve yıldızı-Schema 'daki tablolara depolar. Bu iki iş iki farklı hedef gruba karşı çalışır, yani **Tenantgroup** ve **analiz ticsgroup**. Ayıklama işi, tüm kiracı veritabanlarını içeren TenantGroup 'a karşı çalışır. Ayırma işi, yalnızca analiz deposunu içeren analiz Ticsgroup 'a karşı çalışır. Aşağıdaki adımları kullanarak hedef grupları oluşturun:

1. SSMS 'de, Catalog-DPT-User içindeki **jobaccount** veritabanına bağlanın &lt; &gt; .
2. SSMS 'de *. ..\Learning Modules\işlemsel analiz Tics\tenant Analytics \ TargetGroups. SQL* dosyasını açın 
3. @UserKomut dosyasının en üstündeki değişkeni, `<User>` Wingtip SaaS uygulamasını dağıtırken kullanılan Kullanıcı değeriyle değiştirerek değiştirin.
4. İki hedef grubunu oluşturan betiği çalıştırmak için **F5** tuşuna basın.

### <a name="extract-raw-data-from-all-tenants"></a>Tüm kiracılardan ham verileri Ayıkla

Çok sayıda veri değişikliği *, Bilet ve müşteri* verileri için *olay ve mekme* verilerinden daha sık gerçekleşebilir. Bu nedenle, Bilet ve müşteri verilerini, olay ve mekan verileri ayıklamadan ayrı ve daha sık bir şekilde ayıklamayı düşünün. Bu bölümde, iki ayrı iş tanımlar ve zamanlayabilirsiniz:

- Bilet ve müşteri verilerini ayıklayın.
- Olay ve mekan verilerini ayıklayın.

Her iş verilerini ayıklar ve analiz deposuna gönderir. Ayıklanan verileri analiz yıldızı-şemasına shreds ayrı bir iş vardır.

1. SSMS 'de, Catalog-DPT-User Server 'daki **jobaccount** veritabanına bağlanın &lt; &gt; .
2. SSMS 'de, *. ..\Learning Modules\işletimsel Analtics\tenant Analtics\tenant Analyzer*' ı açın.
3. @UserBetiğin en üstünde değişiklik yapın ve bunu, `<User>` Wingtip SaaS uygulamasını dağıtırken kullanılan kullanıcı adıyla değiştirin 
4. Her kiracı veritabanından bilet ve müşteri verilerini çıkaran işi oluşturan ve çalıştıran betiği çalıştırmak için F5 ' e basın. İş, verileri analiz deposuna kaydeder.
5. Tablonun tüm kiracılardan bilet bilgileriyle doldurulduğundan emin olmak için tenantanalytics veritabanındaki bilet Srawdata tablosunu sorgulayın.

![Ekran görüntüsü, Nesne Gezgini ' de seçili olan bilet Srawdata d b o ile Extractbilet veritabanını gösterir.](./media/saas-tenancy-tenant-analytics/ticketExtracts.png)

Yukarıdaki adımları yineleyin; bu süre dışında, adım 2 ' de **\Extractbilet S.SQL** **dosyasını \Extractvenueyetts.exe SQL** ile değiştirin.

İş başarıyla çalıştırıldığında, analiz deposundaki rawData tablosunun olayları, tüm kiracılardan yeni olaylar ve havalandırma bilgileri ile doldurulur. 

## <a name="data-reorganization"></a>Veri yeniden organizasyonu

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Yıldız-şema tablolarını doldurmak için ayıklanan veriler

Sonraki adım, ayıklanan ham verilerin analiz sorguları için iyileştirilmiş bir tablo kümesine bölünmesi içindir. Yıldız şeması kullanılır. Bir merkezi olgu tablosu bağımsız bilet satış kayıtlarını barındırır. Diğer tablolar, Venn, olaylar ve müşteriler hakkındaki ilgili verilerle doldurulur. Ve zaman boyutu tabloları vardır. 

Öğreticinin bu bölümünde, ayıklanan ham verileri, yıldız şeması tablolarındaki verilerle birleştiren bir iş tanımlar ve çalıştırırsınız. Birleştirme işi tamamlandıktan sonra ham veriler silinir ve tabloları bir sonraki kiracı verileri ayıklama işi tarafından doldurulmaya başlamaya bırakılır.

1. SSMS 'de, Catalog-DPT-User içindeki **jobaccount** veritabanına bağlanın &lt; &gt; .
2. SSMS 'de *. ..\Learning Modules\işletimsel Analtics\tenant Analtics\shredrawextracteddata.exe* dosyasını açın.
3. Analiz deposunda sp_ShredRawExtractedData saklı yordamını çağıran bir işi tanımlamak üzere betiği çalıştırmak için **F5** ' e basın.
4. İşin başarıyla çalışması için yeterli zaman yok.
    - İş durumu için jobs.jobs_execution tablonun **yaşam döngüsü** sütununu kontrol edin. Devam etmeden önce işin **başarılı** olduğundan emin olun. Başarılı bir çalıştırma aşağıdaki grafiğe benzer verileri görüntüler:

![Çöp kutusu](./media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Veri araştırması

### <a name="visualize-tenant-data"></a>Kiracı verilerini görselleştirin

Star şeması tablosundaki veriler, analizinizi için gereken tüm bilet satış verilerini sağlar. Büyük veri kümelerinde eğilimleri görmeyi kolaylaştırmak için, grafiği grafiksel olarak görselleştirmeniz gerekir.  Bu bölümde, ayıkladığınız ve düzenlediğiniz kiracı verilerini işlemek ve görselleştirmek için **Power BI** kullanmayı öğreneceksiniz.

Power BI bağlanmak ve daha önce oluşturduğunuz görünümleri içeri aktarmak için aşağıdaki adımları kullanın:

1. Power BI Desktop 'ı başlatın.
2. Giriş şeridinde **veri al**' ı seçin ve **daha fazla...** seçeneğini belirleyin. menüsünde.
3. **Veri al** PENCERESINDE Azure SQL veritabanı ' nı seçin.
4. Veritabanı oturum açma penceresinde sunucunuzun adını (Katalog-DPT- &lt; user &gt; . Database.Windows.net) girin. **Veri bağlantısı modu** Için **içeri aktar** ' ı seçin ve ardından Tamam ' a tıklayın. 

    ![signınpowerbı](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Sol bölmedeki **veritabanı** ' nı seçin, ardından Kullanıcı adı = *Geliştirici* yazın ve parola = *P \@ ssword1* girin. **Bağlan**'a tıklayın.  

    ![Ekran görüntüsü, bir Kullanıcı adı ve parola girebileceğiniz SQL Server veritabanı iletişim kutusunu gösterir.](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. **Gezgin** bölmesinde, analiz veritabanı altında, yıldız şema tablolarını seçin: fact_Tickets, dim_Events, dim_Venues, dim_Customers ve dim_Dates. Sonra **Yükle**' yi seçin. 

Tebrikler! Verileri başarıyla Power BI yüklendi. Artık kiracılarınız hakkında öngörüler elde etmenize yardımcı olmak için ilginç görselleştirmeleri keşfetmeye başlayabilirsiniz. Ardından analizler tarafından, Wingtip bilet iş ekibine veri odaklı öneriler sağlamanıza nasıl olanak sağlayacağız. Öneriler, iş modelini ve müşteri deneyimini iyileştirmenize yardımcı olabilir.

Bilet satış verilerini çözümleyerek, tüm kullanımlar genelinde kullanımdaki çeşitliliği görebilirsiniz. Her bir mekanın sattığı toplam bilet sayısının çubuk grafiğini çizmek için Power BI ' de aşağıdaki seçenekleri seçin. Bilet oluşturucusunun rastgele çeşitlemesi nedeniyle, sonuçlarınız farklı olabilir.
 
![Ekran görüntüsünde, sağ taraftaki veri görselleştirmesi için bir Power B görselleştirme ve denetimleri gösterilmektedir.](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

Yukarıdaki çizim, her bir mekan tarafından satılan bilet sayısının değiştiğini onaylar. Daha fazla bilet satmaya yönelik havalandırma, hizmetinizi daha az bilet satmaya kıyasla daha fazla şekilde kullanıyor. Burada, farklı kiracı ihtiyaçlarına göre kaynak ayırmayı uyarlamak için bir fırsat olabilir.

Bilet satışlarının zaman içinde nasıl değişeceğini görmek için verileri daha fazla analiz edebilirsiniz. Her gün 60 gün boyunca satılan toplam bilet sayısını çizmek için Power BI ' de aşağıdaki seçenekleri seçin.
 
![Ekran görüntüsü bilet satışı dağılımı başlıklı Power B I görselleştirmesini ve satış gününü gösterir.](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

Yukarıdaki grafikte, bazı havalandırma noktaları için bilet satış ani artış görüntülenir. Bu ani artışlar, bazı havalandırma, sistem kaynaklarının orantısız olarak tüketilme fikrini zorlayacaktır. Şimdiye kadar, ani artışlar meydana geldiğinde açık bir düzende yoktur.

Daha sonra bu en yüksek satış günlerinin önemini daha fazla araştırmak istiyorsunuz. Bilet satışa alındıktan sonra bu tepe noktaları ne zaman oluşur? Gün başına satılan anahtarları çizmek için Power BI ' de aşağıdaki seçenekleri seçin.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

Yukarıdaki çizimde, bazı havalandırma satışları satışın ilk gününde çok sayıda bilet sattığına gösterilmektedir. Bilet Bu havalandırma noktaları üzerinden satışa geldiğinde, bir Mad aceleniz gibi görünüyor. Etkinliğin birkaç kez bu patlaması, diğer kiracılara yönelik hizmeti etkileyebilir.

Bu bu Venn tarafından barındırılan tüm olaylar için bu Mad aceleniz doğru olup olmadığını görmek için verilerin ayrıntılarına gidebilirsiniz. Önceki çizimler bölümünde, contoso Concert salonu 'un çok sayıda bilet sattığını ve contoso 'nun Ayrıca belirli günlerde bilet satışındaki bir ani artış olduğunu gözlemlediniz. Contoso Concert salonu için birikimli bilet satışları çizmek üzere Power BI seçenekleriyle, olayların her biri için satış eğilimlerine odaklanarak oynayın. Tüm olaylar aynı satış düzenine mi uyar?

![ContosoSales](./media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Contoso Concert salonu için önceki çizimde, Mad aceleniz tüm olaylar için gerçekleşmediğinden emin olmak üzere gösterilmektedir. Diğer havalandırma için satış eğilimlerini görmek üzere filtre seçenekleriyle birlikte oynayın.

Bilet satışı desenlerine yönelik Öngörüler, Wingtip biletlerinin iş modellerini iyileştirmesine neden olabilirler. Tüm kiracılar eşit olarak doldurulmak yerine, Wingtip, farklı işlem boyutlarına sahip hizmet katmanlarını göstermelidir. Günde daha fazla Bilet satmayı gerektiren daha büyük havalandırma noktaları, daha yüksek bir hizmet düzeyi sözleşmesi (SLA) ile daha yüksek bir katman sunulamaz. Bu havalandırma kaynakları, veritabanlarının veritabanlarına göre daha yüksek kaynak limitleriyle havuza yerleştirilmesini sağlayabilir. Her hizmet katmanında saatlik satış tahsisi olabilir ve bu da ayırmayı aşmamak için ek ücretler ücretlendirilir. Düzenli olarak elde edilen satışları olan büyük havalandırma noktaları, daha yüksek katmanlardan faydalanır ve Wingtip biletleri, hizmetini daha verimli bir şekilde kullanabilir.

Bu arada, bazı Wingtip bilet müşterileri, hizmet maliyetini yaslamak için yeterli bilet satmaya uğraşır. Belki de bu içgörüler, düşük performanslı havalandırma işlemlerinde bilet satışlarını artırma fırsatına sahiptir. Daha yüksek satış, hizmetin algılanan değerini artırır. Fact_Tickets sağ tıklayıp **Yeni ölçü** seçeneğini belirleyin. **Averagebilet Ssold** adlı yeni ölçü için aşağıdaki ifadeyi girin:

```
AverageTicketsSold = AVERAGEX( SUMMARIZE( TableName, TableName[Venue Name] ), CALCULATE( SUM(TableName[Tickets Sold] ) ) )
```

Göreli başarısını belirlemek için her bir mekan satılan yüzde biletlerini işaretlemek üzere aşağıdaki görselleştirme seçeneklerini belirleyin.

![Ekran görüntüsü, her bir mekan satılan ortalama bilet başlıklı Power B I görselleştirmeyi gösterir.](./media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

Yukarıdaki çizimde, çoğu havalandırma, biletlerinin %80 ' undan daha fazla satışı olsa da, bazılarına çok sayıda oturmuş kadar fazla bilgi veren bir süre sonra gösterilmektedir. Her bir mekan için satılan anahtarların maksimum veya minimum yüzdesini seçmek için değerler ile etrafında bir oynatma yapın.

Daha önce, bu bilet satışlarının tahmin edilebilir desenleri takip etmek için analizinizi daha ayrıntılı olarak fark edersiniz. Bu bulgu, Wingtip biletlerinin, dinamik fiyatlandırma önererek bilet satışlarını artırma konusunda yardımcı olmasına izin verebilir. Bu bul, her olay için bilet satışlarını tahmin etmek üzere makine öğrenimi tekniklerini kullanmayı bir fırsat ortaya çıkarmaktadır. Tahmine dayalı, bilet satışlarında indirim teklif teklifi için de yapılabilir. Power BI Embedded bir olay yönetimi uygulamasıyla tümleştirilebilir. Tümleştirme, öngörülen satışları ve farklı indirimlerin etkisini görselleştirmenize yardımcı olabilir. Uygulama, doğrudan analiz görüntüsüne uygulanan en uygun indirimin Devine sağlanmasına yardımcı olabilir.

WingTip uygulamasındaki kiracı verilerinde eğilimleri gözlemlediniz. Uygulamanın SaaS uygulama satıcıları için iş kararları verebilmesine diğer yollarla uyum sağlayabilirsiniz. Satıcılar kiracılarının ihtiyaçlarına daha iyi bir şekilde erişebilir. Bu öğreticide, işletmelerin veri odaklı kararlar vermesine olanak sağlamak amacıyla kiracı verilerinde analizler gerçekleştirmek için gereken araçlar bulunur.

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

- [Wingtip SaaS uygulaması üzerine inşa edilen ek öğreticiler](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- [Elastik işler](./elastic-jobs-overview.md).
- [Ayıklanan verileri kullanan çapraz kiracı analizi-çok kiracılı uygulama](./saas-multitenantdb-tenant-analytics.md)
