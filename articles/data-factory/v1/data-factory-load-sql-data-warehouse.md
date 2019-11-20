---
title: Terabaytlık verileri SQL veri ambarı 'na yükleme
description: Azure Data Factory ile 15 dakika altında Azure SQL veri ambarı 'na 1 TB veri yüklenebileceğinizi gösterir.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 05dcff2276a799b1debc76e4f85fbbac6606eb59
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682541"
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>1 TB 'yi, 15 dakikadan kısa bir süre içinde Azure SQL veri ambarı 'na yükleyin Data Factory
> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [Data Factory kullanarak Azure SQL veri ambarı 'na veri kopyalama](../connector-azure-sql-data-warehouse.md).


[Azure SQL veri ambarı](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) , hem ilişkisel hem de ilişkisel olmayan büyük hacimli verileri işleyebilen bulut tabanlı, genişleme bir veritabanıdır.  Yüksek düzeyde paralel işleme (MPP) mimarisi üzerinde geliştirilen SQL veri ambarı, kurumsal veri ambarı iş yükleri için optimize edilmiştir.  Depolama ve işlem ölçeğini bağımsız olarak ölçeklendirmeye yönelik esnekliğe sahip bulut esnekliği sunar.

Azure SQL veri ambarı 'nı kullanmaya başlamak artık **Azure Data Factory**kullanmaktan çok daha kolay.  Azure Data Factory, bir SQL veri ambarını mevcut sisteminizdeki verilerle doldurmak ve SQL veri ambarını değerlendirirken ve analizlerinizi oluştururken değerli zamandan tasarruf etmek için kullanılabilen, tam olarak yönetilen bulut tabanlı bir veri tümleştirme hizmetidir. çözümlere. Azure Data Factory kullanarak Azure SQL veri ambarı 'na veri yükleme hakkında önemli avantajlar aşağıda verilmiştir:

* **Kolayca ayarlanır**: komut dosyası gerekmeden 5 adımlı sezgisel sihirbaz.
* **Zengin veri deposu desteği**: zengin bir şirket içi ve bulut tabanlı veri deposu kümesi için yerleşik destek.
* **Güvenli ve uyumlu**: veriler HTTPS veya ExpressRoute üzerinden aktarılır ve küresel hizmet varlığı, verilerinizin hiçbir şekilde Coğrafi sınırın dışına ayrılmamasını sağlar
* **PolyBase kullanarak benzersiz olmayan performans** : PolyBase kullanarak Azure SQL veri ambarı 'na veri taşımanın en verimli yolu vardır. Hazırlama blobu özelliğini kullanarak, PolyBase 'in varsayılan olarak desteklediği Azure Blob depolama alanının yanı sıra tüm veri deposu türlerinden yüksek yük hızları elde edebilirsiniz.

Bu makalede, Azure Blob depolama 'dan Azure SQL veri ambarı 'na 1,2 GB/sn 'lik aktarım hızına göre 1 TB 'lık verileri yüklemek için Data Factory kopyalama Sihirbazı 'Nın nasıl kullanılacağı gösterilmektedir.

Bu makalede, kopyalama Sihirbazı 'nı kullanarak verileri Azure SQL veri ambarı 'na taşımaya yönelik adım adım yönergeler sağlanmaktadır.

> [!NOTE]
>  Azure SQL veri ambarı 'na/verilerine veri taşıma Data Factory özellikleri hakkında genel bilgi için, bkz. [Azure Data Factory makalesini kullanarak verileri Azure SQL veri ambarı 'Na taşıma](data-factory-azure-sql-data-warehouse-connector.md) .
>
> Visual Studio, PowerShell, vb. kullanarak işlem hatları da oluşturabilirsiniz. Bkz. [öğretici: Azure blobundan Azure SQL veritabanı 'na veri kopyalama](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) Azure Data Factory ' de kopyalama etkinliğini kullanmaya yönelik adım adım yönergeler içeren hızlı bir yol.  
>
>

## <a name="prerequisites"></a>Ön koşullar
* Azure Blob depolama: Bu deneme TPC-H test veri kümesini depolamak için Azure Blob depolama (GRS) kullanır.  Azure depolama hesabınız yoksa [depolama hesabı oluşturma hakkında](../../storage/common/storage-quickstart-create-account.md)bilgi edinin.
* [TPC-h](http://www.tpc.org/tpch/) verileri: test veri kümesi olarak TPC-h kullanacağız.  Bunu yapmak için, veri kümesini oluşturmanıza yardımcı olan TPC-H araç seti `dbgen` kullanmanız gerekir.  [TPC araçlarından](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) `dbgen` kaynak kodu indirebilir ve kendiniz derleyebilir ya da derlenmiş Ikiliyi [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TPCHTools)'dan indirebilirsiniz.  10 dosya genelindeki `lineitem` tablo için 1 TB düz dosya oluşturmak üzere aşağıdaki komutlarla dbgen. exe ' yi çalıştırın:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * ...
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Şimdi oluşturulan dosyaları Azure Blob 'a kopyalayın.  ADF kopyalamayı kullanarak nasıl yapılacağını öğrenmek için [Azure Data Factory kullanarak şirket içi bir dosya sistemine veri taşıma](data-factory-onprem-file-system-connector.md) bölümüne bakın.    
* Azure SQL veri ambarı: Bu deneme, 6.000 DWUs ile oluşturulan Azure SQL veri ambarı 'na veri yükler

    SQL veri ambarı veritabanı oluşturma hakkında ayrıntılı yönergeler için [Azure SQL veri ambarı oluşturma](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) bölümüne bakın.  PolyBase kullanarak SQL veri ambarı 'na en iyi olası yük performansını elde etmek için, performans ayarında 6.000 DWUs olan maksimum veri ambarı birimi (DWU) sayısını seçtik.

  > [!NOTE]
  > Azure Blobundan yüklerken veri yükleme performansı, SQL veri ambarı 'nda yapılandırdığınız DWU sayısıyla doğrudan orantılıdır:
  >
  > 1\.000 DWU SQL veri ambarı 'na 1 TB yükleme, 87 dakika sürer (~ 200 MBps aktarım hızı) 1 TB 'yi 2.000 DWU SQL veri ambarı 'na yükleme, 1 TB 'yi 46 DWU SQL veri ambarı 'na yükleme 14 dakika sürer (~ 380 GBps aktarım hızı)
  >
  >

    6\.000 DWUs ile bir SQL veri ambarı oluşturmak için performans kaydırıcısını hemen sağa taşıyın:

    ![Performans kaydırıcı](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    6\.000 DWUs ile yapılandırılmayan mevcut bir veritabanı için Azure portal kullanarak ölçeklendirebilirsiniz.  Azure portal veritabanında veritabanına gidin ve aşağıdaki görüntüde gösterilen **genel bakış** panelinde bir **Ölçek** düğmesi vardır:

    ![Ölçek düğmesi](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    **Ölçek** düğmesine tıklayarak aşağıdaki paneli açın, kaydırıcıyı en büyük değere taşıyın ve **Kaydet** düğmesine tıklayın.

    ![Ölçek iletişim kutusu](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Bu deneme `xlargerc` kaynak sınıfını kullanarak verileri Azure SQL veri ambarı 'na yükler.

    En iyi performansı elde etmek için, kopyalamanın `xlargerc` kaynak sınıfına ait olan bir SQL veri ambarı kullanıcısı kullanılarak gerçekleştirilmesi gerekir.  Bunun nasıl yapılacağını, [bir Kullanıcı kaynak sınıfı örneğini değiştirme](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md)hakkında bilgi edinin.  
* Şu DDL ifadesini çalıştırarak Azure SQL veri ambarı veritabanında hedef tablo şeması oluşturun:

    ```SQL  
    CREATE TABLE [dbo].[lineitem]
    (
        [L_ORDERKEY] [bigint] NOT NULL,
        [L_PARTKEY] [bigint] NOT NULL,
        [L_SUPPKEY] [bigint] NOT NULL,
        [L_LINENUMBER] [int] NOT NULL,
        [L_QUANTITY] [decimal](15, 2) NULL,
        [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
        [L_DISCOUNT] [decimal](15, 2) NULL,
        [L_TAX] [decimal](15, 2) NULL,
        [L_RETURNFLAG] [char](1) NULL,
        [L_LINESTATUS] [char](1) NULL,
        [L_SHIPDATE] [date] NULL,
        [L_COMMITDATE] [date] NULL,
        [L_RECEIPTDATE] [date] NULL,
        [L_SHIPINSTRUCT] [char](25) NULL,
        [L_SHIPMODE] [char](10) NULL,
        [L_COMMENT] [varchar](44) NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    ```
  Önkoşul adımları tamamlandığında kopyalama Sihirbazı 'nı kullanarak kopyalama etkinliğini yapılandırmaya artık hazırsınız.

## <a name="launch-copy-wizard"></a>Kopyalama Sihirbazı'nı başlatma
1. [Azure Portal](https://portal.azure.com)’da oturum açın.
2. Sol üst köşedeki **kaynak oluştur ' a** tıklayın, **Intelligence + Analytics**' e tıklayın ve **Data Factory**' ye tıklayın.
3. **Yeni Data Factory** bölmesinde:

   1. **Ad**Için **Loadıntosqldwdatafactory** girin.
       Azure veri fabrikasının adı genel olarak benzersiz olmalıdır. Şu hatayı alırsanız: **"Loadıntosqldwdatafactory" Veri Fabrikası adı kullanılamıyor**, veri fabrikasının adını değiştirin (örneğin, Yournameloadıntosqldwdatafactory) ve yeniden oluşturmayı deneyin. Data Factory yapıtlarının adlandırma kuralları için [Data Factory - Adlandırma Kuralları](data-factory-naming-rules.md) konusuna bakın.  
   2. Azure **aboneliğinizi** seçin.
   3. Kaynak Grubu için aşağıdaki adımlardan birini uygulayın:
      1. Var olan bir kaynak grubu seçmek için **Var olanı kullan**’ı seçin.
      2. Bir kaynak grubunun adını girmek için **Yeni oluştur**’u seçin.
   4. Veri fabrikası için bir **konum** seçin.
   5. Dikey pencerenin alt kısmındaki **Panoya sabitle** onay kutusunu seçin.  
   6. **Oluştur**'a tıklayın.
4. Oluşturma işlemi tamamlandıktan sonra, aşağıdaki görüntüde gösterildiği gibi **Data Factory** dikey penceresini görürsünüz:

   ![Data factory giriş sayfası](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. Data Factory giriş sayfasında **Veri kopyala** kutucuğuna tıklayarak **Kopyalama Sihirbazı**’nı başlatın.

   > [!NOTE]
   > Web tarayıcısının "Yetkilendiriliyor..." durumunda takıldığını görürseniz **Üçüncü taraf tanımlama bilgilerini ve site verilerini engelle** ayarını devre dışı bırakın/işaretini kaldırın (ya da) etkin durumda bırakıp **login.microsoftonline.com** için bir özel durum oluşturun ve ardından sihirbazı yeniden başlatmayı deneyin.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>1\. Adım: veri yükleme zamanlamasını yapılandırma
İlk adım, veri yükleme zamanlamasını yapılandırmaktır.  

**Özellikler** sayfasında:

1. **Görev adı** Için **Copyfromblobtoazuressqldatawarehouse** girin
2. **Şimdi Çalıştır** seçeneğini belirleyin.   
3. **İleri**’ye tıklayın.  

    ![Kopyalama Sihirbazı-Özellikler sayfası](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>2\. Adım: kaynağı yapılandırma
Bu bölümde, 1 TB TPC-H satır öğesi dosyalarını içeren Azure Blob kaynağını yapılandırma adımları gösterilmektedir.

1. Veri deposu olarak **Azure Blob depolama** ' yı seçin ve **İleri**' ye tıklayın.

    ![Kopyalama Sihirbazı-kaynak sayfası seçin](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Azure Blob depolama hesabının bağlantı bilgilerini doldurup **İleri**' ye tıklayın.

    ![Kopyalama Sihirbazı-kaynak bağlantı bilgileri](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. TPC-H satır öğesi dosyalarını içeren **klasörü** seçin ve **İleri**' ye tıklayın.

    ![Kopyalama Sihirbazı-giriş klasörü seç](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. **İleri**'ye tıklandıktan sonra, dosya biçimi ayarları otomatik olarak algılanır.  Sütun sınırlayıcısı ', ' varsayılan virgülden değil ' | ' olduğundan emin olmak için işaretleyin.  Verileri önizledikten sonra **İleri** ' ye tıklayın.

    ![Kopyalama Sihirbazı-dosya biçimi ayarları](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>3\. Adım: hedefi yapılandırma
Bu bölümde, Azure SQL veri ambarı veritabanında hedef: `lineitem` tablosunun nasıl yapılandırılacağı gösterilmektedir.

1. Hedef depo olarak **Azure SQL veri ambarı** ' nı seçin ve **İleri**' ye tıklayın.

    ![Kopyalama Sihirbazı-hedef veri deposunu seçin](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Azure SQL veri ambarı için bağlantı bilgilerini girin.  `xlargerc` rolün üyesi olan kullanıcıyı belirttiğinizden emin olun (ayrıntılı yönergeler için **Önkoşullar** bölümüne bakın) ve **İleri**' ye tıklayın.

    ![Kopyalama Sihirbazı-hedef bağlantı bilgileri](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Hedef tabloyu seçin ve **İleri**' ye tıklayın.

    ![Kopyalama Sihirbazı-tablo eşleme sayfası](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Şema eşleme sayfasında "Sütun eşlemeyi Uygula" seçeneğini işaretsiz bırakın ve **İleri**' ye tıklayın.

## <a name="step-4-performance-settings"></a>4\. Adım: performans ayarları

**PolyBase 'e Izin ver** varsayılan olarak denetlenir.  **İleri**’ye tıklayın.

![Kopyalama Sihirbazı-şema eşleme sayfası](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>5\. Adım: yükleme sonuçlarını dağıtma ve izleme
1. Dağıtmak için **son** düğmesine tıklayın.

    ![Kopyalama Sihirbazı-Özet sayfası](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Dağıtım tamamlandıktan sonra kopyalama çalıştırmasının ilerlemesini izlemek için `Click here to monitor copy pipeline` ' a tıklayın. **Etkinlik pencereleri** listesinde oluşturduğunuz kopyalama işlem hattını seçin.

    ![Kopyalama Sihirbazı-Özet sayfası](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Kaynak verileri okuma ve hedefe yazma, süre ve çalıştırmaya yönelik ortalama aktarım hızı dahil olmak üzere sağ panelde **etkinlik penceresi Gezgini** ' nde kopyalama çalıştırması ayrıntılarını görüntüleyebilirsiniz.

    Aşağıdaki ekran görüntüsünden görebileceğiniz gibi, 1 TB 'yi Azure Blob depolama alanından SQL veri ambarı 'na kopyalamak 14 dakika sürer ve 1,22 GBps aktarım hızı etkin bir şekilde elde edilebilir!

    ![Kopyalama Sihirbazı-başarılı iletişim kutusu](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>En iyi uygulamalar
Azure SQL veri ambarı veritabanınızı çalıştırmaya yönelik birkaç en iyi yöntem aşağıda verilmiştir:

* KÜMELENMIŞ bir COLUMNSTORE DIZININE yüklerken daha büyük bir kaynak sınıfı kullanın.
* Daha verimli birleşimler için, varsayılan hepsini bir kez deneme dağıtımı yerine bir SELECT sütunuyla karma dağılımı kullanmayı düşünün.
* Daha hızlı yük hızları için, geçici veriler için yığın kullanmayı düşünün.
* Azure SQL veri ambarı 'nı yüklemeyi tamamladıktan sonra İstatistikler oluşturun.

Ayrıntılar için bkz. [Azure SQL veri ambarı Için en iyi uygulamalar](../../sql-data-warehouse/sql-data-warehouse-best-practices.md) .

## <a name="next-steps"></a>Sonraki adımlar
* [Data Factory kopyalama Sihirbazı](data-factory-copy-wizard.md) -Bu makalede kopyalama Sihirbazı hakkında ayrıntılar sağlanmaktadır.
* [Kopyalama etkinliği performansı ve ayarlama Kılavuzu](data-factory-copy-activity-performance.md) -Bu makale, başvuru performansı ölçümleri ve ayarlama Kılavuzu ' nu içerir.
