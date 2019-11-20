---
title: Analitik sorguları kiracı veritabanlarına karşı çalıştırma
description: Azure SQL veritabanı, SQL veri ambarı, Azure Data Factory veya Power BI ayıklanan verileri kullanan çapraz kiracı Analizi sorguları.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: MightyPen, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 4791cd3a6b6f72c5d9ee4ca828d66b0d361f356c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816777"
---
# <a name="explore-saas-analytics-with-azure-sql-database-sql-data-warehouse-data-factory-and-power-bi"></a>Azure SQL veritabanı, SQL veri ambarı, Data Factory ve Power BI SaaS analizlerini keşfet

Bu öğreticide, uçtan uca bir analiz senaryosuna kılavuzluk edersiniz. Bu senaryoda, kiracı verileri üzerindeki analizler, akıllı kararlar almak için yazılım satıcılarının nasıl güçlendirin olduğunu gösterir. Her kiracı veritabanından ayıklanan verileri kullanarak, örnek Wingtip bilet SaaS uygulamasının kullanımı dahil olmak üzere, kiracı davranışına yönelik Öngörüler elde etmek için analiz kullanırsınız. Bu senaryo üç adımdan oluşur: 

1.  Her kiracı veritabanından bir analiz deposuna, bu durumda bir SQL veri ambarı 'na **veri ayıklayın** .
2.  **Ayıklanan verileri** analiz Işleme için iyileştirin.
3.  Karar verme konusunda rehberlik sağlayan yararlı Öngörüler çizmek için **Iş zekası** araçlarını kullanın. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> - Yükleme için kiracı Analizi deposu oluşturun.
> - Her kiracı veritabanından analiz veri ambarına veri ayıklamak için Azure Data Factory (ADF) kullanın.
> - Ayıklanan verileri iyileştirin (bir yıldız şemasına yeniden düzenleyin).
> - Analytics veri ambarını sorgulayın.
> - Veri görselleştirme için Power BI kullanarak kiracı verilerindeki eğilimleri vurgulayın ve iyileştirmeler için öneri alın.

![Mimari Tureoverview](media/saas-tenancy-tenant-analytics/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Ayıklanan kiracı verileri üzerinde analiz

SaaS uygulamaları, bulutta büyük miktarda kiracı verisi tutabilir. Bu veriler, uygulamanızın işlemi ve kullanımı ve Kiracılarınızın davranışı hakkında zengin bir Öngörüler kaynağı sağlayabilir. Bu Öngörüler, uygulama ve platformdaki Özellik geliştirmeyi, kullanılabilirlik geliştirmelerini ve diğer yatırımları yönlendirebilir.

Tüm veriler yalnızca bir çok kiracılı veritabanında olduğunda tüm kiracılar için verilere erişim basittir. Ancak, binlerce veritabanı üzerinde ölçeklendirmeye dağıtıldığında erişim daha karmaşıktır. Karmaşıklığın bir yolu, verileri bir analiz veritabanına veya sorgu için bir veri ambarına ayıklamaya yöneliktir.

Bu öğreticide, Wingtip bilet uygulaması için uçtan uca bir analiz senaryosu sunulmaktadır. İlk olarak, [Azure Data Factory (ADF)](../data-factory/introduction.md) , her bir kiracı veritabanından bilet satışları ve ilgili verileri ayıklamak için düzenleme aracı olarak kullanılır. Bu veriler, bir analiz deposundaki hazırlama tablolarına yüklenir. Analytics Mağazası bir SQL veritabanı veya SQL veri ambarı olabilir. Bu öğretici, analiz deposu olarak [SQL veri ambarı](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) 'nı kullanır.

Sonra ayıklanan veriler dönüştürülür ve bir [yıldız-şema](https://www.wikipedia.org/wiki/Star_schema) tabloları kümesine yüklenir. Tablolar bir merkezi olgu tablosu ve ilgili boyut tablolarından oluşur:

- Yıldız şeması içindeki merkezi olgu tablosu bilet verileri içerir.
- Boyut tabloları, Venn, olaylar, müşteriler ve satın alma tarihleri hakkındaki verileri içerir.

Merkezi ve boyut tabloları birlikte verimli analitik işleme sağlar. Bu öğreticide kullanılan yıldız şeması aşağıdaki görüntüde görüntülenir:
 
![Mimari Tureoverview](media/saas-tenancy-tenant-analytics/starschematables.JPG)

Son olarak, yıldız şeması tabloları sorgulanır. Sorgu sonuçları, kiracı davranışı ve uygulamanın kullanımıyla ilgili öngörüleri vurgulamak için Power BI kullanılarak görsel olarak görüntülenir. Bu yıldız şeması ile, şu şekilde kullanıma sunan sorguları çalıştırırsınız:

- Bilet ve bu mekden kim satın alan.
- Bilet satışında desenler ve eğilimler.
- Her bir mekanın göreli popülerliği.

Bu öğretici, Wingtip bilet verilerinden glecan temel Öngörüler örnekleri sunar. Her bir girişimin hizmeti nasıl kullandığını anlamak, Wingtip bilet satıcısının, örneğin daha fazla veya daha az etkin havalandırma amacıyla hedeflenmiş farklı hizmet planlarını düşündüğünü sağlayabilir. 

## <a name="setup"></a>Kurulum

### <a name="prerequisites"></a>Ön koşullar

> [!NOTE]
> Bu öğretici, şu anda sınırlı bir önizlemede olan Azure Data Factory özelliklerini kullanır (bağlı hizmet parametrelemesi). Bu öğreticiyi yapmak istiyorsanız, abonelik KIMLIĞINIZI [burada](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxrVywox1_tHk9wgd5P8SVJUNlFINjNEOElTVFdMUEREMjVVUlJCUDdIRyQlQCN0PWcu)belirtin. Aboneliğiniz etkinleştirildikten hemen sonra size bir onay göndereceğiz.

Bu öğreticiyi tamamlamak için aşağıdaki ön koşulların karşılandığından emin olun:
- Kiracı uygulaması başına Wingtip bilet SaaS veritabanı dağıtılır. Beş dakikadan kısa bir süre içinde dağıtmak için bkz. [Wingtip SaaS uygulamasını dağıtma ve araştırma](saas-dbpertenant-get-started-deploy.md).
- Kiracı betikleri başına Wingtip biletleri SaaS veritabanı ve uygulama [kaynak kodu](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) , GitHub 'dan indirilir. Bkz. indirme yönergeleri. İçindekileri Ayıklamadan önce *ZIP dosyasının engellemesini kaldırmayı* unutmayın.
- Power BI Desktop yüklendi. [Power BI Desktop indirin](https://powerbi.microsoft.com/downloads/).
- Ek kiracıların toplu işi sağlandı, [**kiracılar sağlama öğreticisine**](saas-dbpertenant-provision-and-catalog.md)bakın.

### <a name="create-data-for-the-demo"></a>Demo için veri oluşturma

Bu öğreticide, Bilet satış verilerine ilişkin analizler incelenmektedir. Bu adımda, tüm kiracılar için bilet verileri oluşturursunuz. Sonraki bir adımda bu veriler Analize çıkarılır. Farklı bilet satın alma desenlerinin bir aralığını açığa çıkarmak için yeterli veri bulundurabilmeniz için _kiracılar toplu işlemini sağladığınızdan emin olun_ (daha önce açıklandığı gibi).

1. PowerShell ıSE 'de *. ..\Learning Modules\işletimsel Analtics\tenant Analytics DW\Demo-TenantAnalyticsDW.ps1*açın ve aşağıdaki değeri ayarlayın:
    - **$DemoScenario** tüm havalandırma ve olaylar için = **1** satın alma bileti
2. Komut dosyasını çalıştırmak ve tüm havalandırma noktaları için bilet satın alma geçmişi oluşturmak için **F5** tuşuna basın. 20 kiracılar ile, betik on binlerce bilet üretir ve 10 dakika veya daha uzun sürebilir.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>SQL veri ambarı, Data Factory ve BLOB depolama dağıtma 
Wingtip bilet uygulamasında kiracının işlem verileri birçok veritabanına dağıtılır. Azure Data Factory (ADF), veri ambarına bu verilerin ayıklanma, yükleme ve dönüştürme (ELT) işlemlerini düzenlemek için kullanılır. ADF, verileri SQL veri ambarı 'na en verimli şekilde yüklemek için verileri ara blob dosyalarına ayıklar ve sonra verileri veri ambarına yüklemek için [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) 'i kullanır.   

Bu adımda, öğreticide kullanılan ek kaynakları dağıtırsınız: _tenantanalytics_ADLı bir SQL veri ambarı, _dbtodwload-\<Kullanıcı\>_ adlı bir Azure Data Factory ve Wingtiphazırlama adlı bir Azure depolama hesabı.  _Kullanıcı\>\<_ . Depolama hesabı, ayıklanan veri dosyalarını, veri ambarına yüklenmeden önce blob olarak geçici olarak saklamak için kullanılır. Bu adım Ayrıca veri ambarı şemasını dağıtır ve ELT sürecini düzenleyen ADF işlem hatlarını tanımlar.
1. PowerShell ıSE 'de, *. ..\Learning Modules\işletimsel Analtics\tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* ve set ' i açın:
    - **$DemoScenario** = **2** kiracı Analizi veri ambarı, BLOB depolama ve Veri Fabrikası dağıtma 
1. Tanıtım betiğini çalıştırmak ve Azure kaynaklarını dağıtmak için **F5** tuşuna basın. 

Şimdi dağıttığınız Azure kaynaklarını gözden geçirin:
#### <a name="tenant-databases-and-analytics-store"></a>Kiracı veritabanları ve analiz deposu
**Tenants1-DPT-&lt;user&gt;** ve **Catalog-dpt-&lt;Kullanıcı&gt;** sunucularına bağlanmak için [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) kullanın. &lt;Kullanıcı&gt;, uygulamayı dağıtırken kullanılan değerle değiştirin. Login = *Developer* ve Password = *P\@ssword1*kullanın. Daha fazla bilgi için bkz. [giriş öğreticisi](saas-dbpertenant-wingtip-app-overview.md) .

![SSMS 'den SQL veritabanı sunucusuna bağlanma](media/saas-tenancy-tenant-analytics/ssmsSignIn.JPG)

Nesne Gezgini:

1. *Tenants1-DPT-&lt;kullanıcı&gt;* sunucusunu genişletin.
1. Veritabanları düğümünü genişletin ve kiracı veritabanlarının listesini görüntüleyin.
1. *Katalog-DPT-&lt;kullanıcı&gt;* sunucusunu genişletin.
1. Aşağıdaki nesneleri içeren analiz deposunu görmediğinizi doğrulayın:
    1. Tablolar **raw_Tickets**, **raw_Customers**, **raw_Events** ve **raw_Venues** kiracı veritabanlarından ham ayıklanan verileri tutar.
    1. Yıldız şeması tabloları **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**ve **dim_Dates**.
    1. **Sp_transformExtractedData** saklı yordam, verileri dönüştürmek ve yıldız şema tablolarına yüklemek için kullanılır.

![DWtables 'lar](media/saas-tenancy-tenant-analytics/DWtables.JPG)

#### <a name="blob-storage"></a>Blob depolama
1. [Azure portalında](https://ms.portal.azure.com), uygulamayı dağıtmak için kullandığınız kaynak grubuna gidin. **Wingtiphazırlama\<user\>** adlı bir depolama hesabının eklendiğini doğrulayın.

   ![DWtables 'lar](media/saas-tenancy-tenant-analytics/adf-staging-storage.PNG)

1. Mevcut nesneleri araştırmak için **\<wingtiphazırlama kullanıcı\>** depolama hesabı ' na tıklayın.
1. **Bloblar** kutucuğuna tıklayın
1. Kapsayıcı **ConfigFile** öğesine tıklayın
1. **ConfigFile** 'ın **tableconfig. JSON**adlı bir JSON dosyası içerdiğini doğrulayın. Bu dosya, kaynak ve hedef tablo adlarını, sütun adlarını ve izleyici sütun adını içerir.

#### <a name="azure-data-factory-adf"></a>Azure Data Factory (ADF)
Kaynak grubundaki [Azure portalında](https://ms.portal.azure.com) , _dbtodwload-\<user\>_ adlı Azure Data Factory eklendiğini doğrulayın. 

 ![adf_portal](media/saas-tenancy-tenant-analytics/adf-data-factory-portal.png)

Bu bölüm, oluşturulan veri fabrikasını araştırır. Data Factory 'yi başlatmak için aşağıdaki adımları izleyin:
1. Portalda, **dbtodwload-\<kullanıcı\>** adlı veri fabrikasına tıklayın.
2. Data Factory tasarımcısını ayrı bir sekmede başlatmak için **& İzleyici** kutucuğuna yaz ' a tıklayın. 

## <a name="extract-load-and-transform-data"></a>Verileri ayıklama, yükleme ve dönüştürme
Azure Data Factory, verilerin ayıklanmasını, yüklenmesini ve dönüştürülmesini düzenlemek için kullanılır. Bu öğreticide, kiracı veritabanlarının her biri için dört farklı SQL görünümünden Veri ayıkladığınızda: **Rawbiletleri**, **rawcustomers**, **Rawevents**ve **rawvenlar**. Bu görünümler, mekan kimliği içerir, bu sayede veri ambarındaki her bir yerden verileri ayırt edebilirsiniz. Veriler, veri ambarındaki ilgili hazırlama tablolarına yüklenir: **raw_Tickets**, **raw_customers**, **raw_Events** ve **raw_Venue**. Daha sonra saklı bir yordam ham verileri dönüştürür ve yıldız şema tablolarını **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**ve **dim_Dates**doldurur.

Önceki bölümde, Veri Fabrikası dahil olmak üzere gerekli Azure kaynaklarını dağıtıp başlatıttınız. Dağıtılan veri fabrikası, kiracı verilerini ayıklamak, yüklemek ve dönüştürmek için gereken işlem hatlarını, veri kümelerini, bağlı hizmetleri vb. içerir. Daha sonra bu nesneleri araştıralım ve sonra verileri kiracı veritabanlarından veri ambarına taşımak için işlem hattını tetikleyelim.

### <a name="data-factory-pipeline-overview"></a>Data Factory ardışık düzenine genel bakış
Bu bölüm, veri fabrikasında oluşturulan nesneleri araştırır. Aşağıdaki şekilde, bu öğreticide kullanılan ADF işlem hattının genel iş akışı açıklanmaktadır. İşlem hattını daha sonra araştırmayı tercih ediyorsanız ve önce sonuçları görmek isterseniz, işlem **hattı çalıştırmasını tetikleyen**bir sonraki bölüme atlayın.

![adf_overview](media/saas-tenancy-tenant-analytics/adf-data-factory.PNG)

Genel Bakış sayfasında sol paneldeki **Yazar** sekmesine geçin ve üç işlem [hattı](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) ve oluşturulan üç [veri kümesi](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) olduğunu gözlemleyin.
![adf_author](media/saas-tenancy-tenant-analytics/adf_author_tab.JPG)

İç içe geçmiş üç işlem hattı şunlardır: SQLDBToDW, DBCopy ve TableCopy.

İşlem **hattı 1-SQLDBToDW** , Katalog veritabanında depolanan kiracı veritabanlarının adlarını arar (tablo adı: [__ShardManagement]. [ ShardsGlobal]) ve her kiracı veritabanı için **Dbcopy** işlem hattını yürütür. Tamamlandıktan sonra, belirtilen **sp_TransformExtractedData** saklı yordam şeması yürütülür. Bu saklı yordam, hazırlama tablolarında yüklenen verileri dönüştürür ve yıldız şema tablolarını doldurur.

İşlem **hattı 2-DBCopy** , BLOB depolama alanında depolanan bir yapılandırma dosyasından kaynak tablolarının ve sütunların adlarını arar.  Daha sonra **Tablecopy** işlem hattı dört tablonun her biri için çalıştırılır: bilet olguları, Customerolgu, Eventfdavranır ve venueolguları. **[Foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** etkinliği tüm 20 veritabanları için paralel olarak yürütülür. ADF, en fazla 20 döngü yinelemesine paralel olarak çalıştırılmasına izin verir. Daha fazla veritabanı için birden çok işlem hattı oluşturmayı düşünün.    

**Ardışık düzen 3-TableCopy** , değiştirilmiş veya güncelleştirilmiş satırları BELIRLEMEK Için SQL veritabanı 'nda (_rowversion_) satır sürüm numaralarını kullanır. Bu etkinlik, kaynak tablolardan satırları ayıklamak için başlangıç ve bitiş satırı sürümünü arar. Her bir kiracı veritabanında depolanan **Copytracker** tablosu, her çalıştırmada her bir kaynak tablosundan ayıklanan son satırı izler. Yeni veya değiştirilmiş satırlar veri ambarındaki ilgili hazırlama tablolarına kopyalanır: **raw_Tickets**, **raw_Customers**, **raw_Venues**ve **raw_Events**. Son olarak, son satır sürümü bir sonraki ayıklama için ilk satır sürümü olarak kullanılacak **Copytracker** tablosuna kaydedilir. 

Ayrıca, veri fabrikasını kaynak SQL veritabanlarına, hedef SQL veri ambarına ve ara BLOB depolama alanına bağlayan üç parametreli bağlantılı hizmet vardır. **Yazar** sekmesinde, aşağıdaki görüntüde gösterildiği gibi bağlı hizmetleri araştırmak için **Bağlantılar** ' a tıklayın:

![adf_linkedservices](media/saas-tenancy-tenant-analytics/linkedservices.JPG)

Üç bağlantılı hizmete karşılık gelen, işlem hattı etkinliklerinde giriş veya çıkış olarak kullandığınız verilere başvuran üç veri kümesi vardır. Kullanılan bağlantıları ve parametreleri gözlemlemek için veri kümelerinin her birini inceleyin. _AzureBlob_ , kaynak ve hedef tabloları ve sütunları içeren yapılandırma dosyasına ve her bir kaynaktaki İzleyici sütununa işaret eder.
  
### <a name="data-warehouse-pattern-overview"></a>Veri ambarı düzenine genel bakış
SQL veri ambarı, kiracı verilerinde toplamayı gerçekleştirmek için analiz deposu olarak kullanılır. Bu örnekte, verileri SQL veri ambarı 'na yüklemek için PolyBase kullanılır. Ham veriler, yıldız şema tablolarına dönüştürülmüş satırları izlemek için bir kimlik sütunu olan hazırlama tablolarına yüklenir. Aşağıdaki görüntüde yükleme deseninin gösterildiği gösterilmektedir: ![loadingmodel](media/saas-tenancy-tenant-analytics/loadingpattern.JPG)

Yavaş değişen boyut (SCD) tür 1 boyut tabloları bu örnekte kullanılır. Her boyutun bir kimlik sütunu kullanılarak tanımlanmış bir vekil anahtarı vardır. En iyi uygulama olarak, tarih boyut tablosu zaman kazanmak için önceden doldurulur. Diğer boyut tabloları için, SELECT olarak bir CREATE TABLE... (CTAS) deyimleri, yedek anahtarlarla birlikte, varolan değiştirilmiş ve değiştirilmemiş satırları içeren geçici bir tablo oluşturmak için kullanılır. Bu işlem IDENTITY_INSERT = ON ile yapılır. Yeni satırlar daha sonra tabloya IDENTITY_INSERT = OFF ile eklenir. Kolayca geri alma için, mevcut boyut tablosu yeniden adlandırılır ve geçici tablo yeni boyut tablosu olacak şekilde yeniden adlandırılır. Her çalıştırmadan önce eski boyut tablosu silinir.

Boyut tabloları olgu tablosundan önce yüklenir. Bu sıralama, her bir gelen olgu için, başvurulan tüm boyutların zaten var olmasını sağlar. Gerçekler yüklenirken, her bir boyut için iş anahtarı eşleştirilir ve ilgili vekil anahtarlar her bir olgusuna eklenir.

Dönüştürmenin son adımı, işlem hattının bir sonraki yürütülmesi için hazırlık verilerini siler.
   
### <a name="trigger-the-pipeline-run"></a>İşlem hattı çalıştırmasını tetikleme
Tüm kiracı veritabanları için tüm ayıklama, yükleme ve dönüştürme işlem hattını çalıştırmak için aşağıdaki adımları izleyin:
1. ADF Kullanıcı arabiriminin **Yazar** sekmesinde sol bölmeden **Sqldbtodw** işlem hattı ' nı seçin.
1. **Tetikle** ' e tıklayın ve çekilme menüsünde **Şimdi Tetikle**' ye tıklayın. Bu eylem ardışık düzeni hemen çalıştırır. Bir üretim senaryosunda, bir zamanlamaya göre verileri yenilemek için işlem hattını çalıştırmaya yönelik bir zaman tablosu tanımlarsınız.
  ![adf_trigger](media/saas-tenancy-tenant-analytics/adf_trigger.JPG)
1. İşlem **hattı çalıştırma** sayfasında **son**' a tıklayın.
 
### <a name="monitor-the-pipeline-run"></a>İşlem hattı çalıştırmasını izleme
1. ADF Kullanıcı arabiriminde, sol taraftaki menüden **izleyici** sekmesine geçin.
1. SQLDBToDW işlem hattının durumu **başarılı**olana kadar **Yenile** ' ye tıklayın.
  ![adf_monitoring](media/saas-tenancy-tenant-analytics/adf_monitoring.JPG)
1. SSMS ile veri ambarına bağlanın ve bu tablolarda verilerin yüklendiğini doğrulamak için yıldız şema tablolarını sorgulayın.

İşlem hattı tamamlandığında, olgu tablosu tüm havalandırma noktaları için bilet satış verilerini barındırır ve boyut tabloları ilgili havalandırma noktaları, olaylar ve müşteriler ile doldurulur.

## <a name="data-exploration"></a>Veri araştırması

### <a name="visualize-tenant-data"></a>Kiracı verilerini görselleştirin

Yıldız-şema içindeki veriler, analizinizi için gereken tüm bilet satış verilerini sağlar. Verilerin görselleştirilmesi, büyük veri kümelerinde eğilimleri görmenizi kolaylaştırır. Bu bölümde, veri ambarındaki kiracı verilerini işlemek ve görselleştirmek için **Power BI** kullanırsınız.

Power BI bağlanmak ve daha önce oluşturduğunuz görünümleri içeri aktarmak için aşağıdaki adımları kullanın:

1. Power BI Desktop 'ı başlatın.
2. Giriş şeridinde **veri al**' ı seçin ve **daha fazla...** seçeneğini belirleyin. menüsünde.
3. **Veri al** PENCERESINDE **Azure SQL veritabanı**' nı seçin.
4. Veritabanı oturum açma penceresinde sunucunuzun adını (**Katalog-DPT-&lt;User&gt;. Database.Windows.net**) girin. **Veri bağlantısı modu**Için **içeri aktar** ' ı seçin ve ardından **Tamam**' a tıklayın. 

    ![oturum açma-Power BI](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Sol bölmedeki **veritabanı** ' nı seçin, ardından Kullanıcı adı = *Geliştirici*yazın ve Password = *P\@ssword1*girin. **Bağlan**'a tıklayın.  

    ![veritabanı-oturum açma](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. **Gezgin** bölmesinde, analiz veritabanı altında, yıldız şema tablolarını seçin: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** ve **dim_Dates**. Sonra **Yükle**' yi seçin. 

Tebrikler! Verileri başarıyla Power BI yüklendi. Artık kiracılarınız hakkında içgörüler elde etmek için ilginç görselleştirmeler keşfedebilirsiniz. Analiz 'in Wingtip bilet iş ekibine veri odaklı bazı öneriler sağlayabilmesine yol açalım. Öneriler, iş modelini ve müşteri deneyimini iyileştirmenize yardımcı olabilir.

Bilet genelinde kullanım çeşitlemesini görmek için bilet satış verilerini çözümleyerek başlayın. Her bir mekan satılan toplam bilet sayısının çubuk grafiğini çizmek için Power BI gösterilen seçenekleri seçin. (Bilet oluşturucusunda rastgele varyasyon nedeniyle, sonuçlarınız farklı olabilir.)
 
![Totalbilet sbyvenlar](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues-DW.PNG)

Yukarıdaki çizim, her bir mekan tarafından satılan bilet sayısının değiştiğini onaylar. Daha fazla bilet satmaya yönelik havalandırma, hizmetinizi daha az bilet satmaya kıyasla daha fazla şekilde kullanıyor. Burada, farklı kiracı ihtiyaçlarına göre kaynak ayırmayı uyarlamak için bir fırsat olabilir.

Bilet satışlarının zaman içinde nasıl değişeceğini görmek için verileri daha fazla analiz edebilirsiniz. Her gün 60 gün boyunca satılan toplam bilet sayısını çizmek için Power BI aşağıdaki görüntüde gösterilen seçenekleri seçin.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate-DW.PNG)

Yukarıdaki grafikte, bazı havalandırma için bilet satışı ani artış gösterilmektedir. Bu ani artışlar, bazı havalandırma, sistem kaynaklarının orantısız olarak tüketilme fikrini zorlayacaktır. Şimdiye kadar, ani artışlar meydana geldiğinde açık bir düzende yoktur.

Daha sonra bu en yüksek satış günlerinin önemini araştıralım. Bilet satışa alındıktan sonra bu tepe noktaları ne zaman oluşur? Günde satılan anahtarları çizmek için Power BI aşağıdaki görüntüde gösterilen seçenekleri seçin.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay-DW.PNG)

Bu çizimde, bazı havalandırma satışları satışın ilk gününde çok sayıda bilet satmakta. Bilet Bu havalandırma noktaları üzerinden satışa geldiğinde, bir Mad aceleniz gibi görünüyor. Etkinliğin birkaç kez bu patlaması, diğer kiracılara yönelik hizmeti etkileyebilir.

Bu bu Venn tarafından barındırılan tüm olaylar için bu Mad aceleniz doğru olup olmadığını görmek için verilerin ayrıntılarına gidebilirsiniz. Önceki çizimler bölümünde contoso Concert salonu 'un birçok bilet sattığı ve contoso 'nun Ayrıca belirli günlerde bilet satışlarında ani bir artış olduğu gördük. Contoso Concert salonu için birikimli bilet satışları çizmek üzere Power BI seçenekleriyle, olayların her biri için satış eğilimlerine odaklanarak oynayın. Tüm olaylar aynı satış düzenine mi uyar? Aşağıdaki gibi bir çizim üretmeyi deneyin.

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Her olay için Contoso Concert salonu için zaman içinde birikimli bilet satışları bu şekilde çiziyorsa, Mad aceleniz tüm olaylar için gerçekleşmiyor. Diğer havalandırma noktaları için satış eğilimlerini araştırmak üzere filtre seçenekleriyle birlikte oynayın.

Bilet satışı desenlerine yönelik Öngörüler, Wingtip biletlerinin iş modellerini iyileştirmesine neden olabilirler. Tüm kiracılar eşit olarak doldurulmak yerine, Wingtip, farklı işlem boyutlarına sahip hizmet katmanlarını göstermelidir. Günde daha fazla Bilet satmayı gerektiren daha büyük havalandırma noktaları, daha yüksek bir hizmet düzeyi sözleşmesi (SLA) ile daha yüksek bir katman sunulamaz. Bu havalandırma kaynakları, veritabanlarının veritabanlarına göre daha yüksek kaynak limitleriyle havuza yerleştirilmesini sağlayabilir. Her hizmet katmanında saatlik satış tahsisi olabilir ve bu da ayırmayı aşmamak için ek ücretler ücretlendirilir. Düzenli olarak elde edilen satışları olan büyük havalandırma noktaları, daha yüksek katmanlardan faydalanır ve Wingtip biletleri, hizmetini daha verimli bir şekilde kullanabilir.

Bu arada, bazı Wingtip bilet müşterileri, hizmet maliyetini yaslamak için yeterli bilet satmaya uğraşır. Belki de bu içgörüler, düşük performanslı havalandırma işlemlerinde bilet satışlarını artırma fırsatına sahiptir. Daha yüksek satış, hizmetin algılanan değerini artırır. Fact_Tickets sağ tıklayıp **Yeni ölçü**seçeneğini belirleyin. **Averagebilet Ssold**adlı yeni ölçü için aşağıdaki ifadeyi girin:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Göreli başarısını belirlemek için her bir mekan satılan yüzde biletlerini işaretlemek üzere aşağıdaki görselleştirme seçeneklerini belirleyin.

![Avgbilet sbyvenlar](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues-DW.PNG)

Yukarıdaki çizimde, çoğu venlin biletlerinin %80 ' inden daha fazla satışı olmasına rağmen bazıları, her birinin oturmasını doldurmakla uğraşmaya uğraştırılmakta. Her bir mekan için satılan anahtarların maksimum veya minimum yüzdesini seçmek için değerler ile etrafında bir oynatma yapın.

## <a name="embedding-analytics-in-your-apps"></a>Uygulamalarınıza analiz ekleme 
Bu öğreticide, yazılım satıcısının kiracılar hakkında daha iyi şekilde anlaşılmasını sağlamak için kullanılan çapraz kiracı analizine odaklanılmıştır. Analiz, _kiracıların_işlerini daha etkili bir şekilde yönetmesine yardımcı olmak için de öngörüler sağlayabilir. 

Wingtip bilet örneğinde, daha önce bilet satışlarının tahmin edilebilir desenleri takip etmek üzere olduğunu fark edersiniz. Bu öngörü, Bilet satışlarını artırma konusunda yardımcı olmak için kullanılabilir. Olayların bilet satışlarını tahmin etmek için makine öğrenimi tekniklerini de kullanmak bir fırsat olabilir. Fiyat değişikliklerinin etkileri de modellenebilir ve bu da indirimlerin tahmin edilebileceği etkiyi sağlar. Power BI Embedded, satılan toplam lisans ve düşük satış etkinliklerinden gelir üzerinden indirimlerin etkisi dahil olmak üzere bir olay yönetimi uygulamasıyla tümleştirilebilir. Power BI Embedded sayesinde, gerçekten de görselleştirme deneyimindeki indirimle, anahtarı bilet fiyatlarına uygulayarak tümleştirebilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Kiracı analizi için bir yıldız şeması ile doldurulmuş bir SQL veri ambarı dağıtın.
> * Her kiracı veritabanından analiz veri ambarına veri ayıklamak için Azure Data Factory kullanın.
> * Ayıklanan verileri iyileştirin (bir yıldız şemasına yeniden düzenleyin).
> * Analytics veri ambarını sorgulayın. 
> * Tüm kiracılar genelinde verilerdeki eğilimleri görselleştirmek için Power BI kullanın.

Tebrikler!

## <a name="additional-resources"></a>Ek kaynaklar

- [Wingtip SaaS uygulaması üzerine inşa edilen ek öğreticiler](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
