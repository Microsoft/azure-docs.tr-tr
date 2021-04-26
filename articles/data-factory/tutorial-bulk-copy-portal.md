---
title: Azure portal kullanarak verileri toplu olarak kopyalama
description: Kaynak veri deposundan verileri toplu olarak hedef veri deposuna kopyalamak için Azure Data Factory ve kopyalama etkinliğini kullanın.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/29/2021
ms.openlocfilehash: bca2158f448f74ba596114fce5d1631249124a92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606744"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory-in-the-azure-portal"></a>Azure portal Azure Data Factory kullanarak birden çok tabloyu toplu olarak kopyalama

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu öğreticide **, Azure SQL veritabanından Azure SYNAPSE Analytics 'e kadar birçok tablo kopyalama** gösterilmektedir. Aynı düzeni diğer kopyalama senaryolarında da uygulayabilirsiniz. Örneğin, tabloları SQL Server/Oracle 'dan Azure SQL veritabanı/Azure SYNAPSE Analytics/Azure Blob 'a kopyalayarak blob 'tan Azure SQL veritabanı tablolarına farklı yollar kopyalarsınız.

> [!NOTE]
> Azure Data Factory kullanmaya yeni başlıyorsanız bkz. [Azure Data Factory'ye giriş](introduction.md).

Yüksek düzeyde, bu öğretici aşağıdaki adımları içerir:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Azure SQL veritabanı, Azure SYNAPSE Analytics ve Azure depolama bağlı hizmetleri oluşturun.
> * Azure SQL veritabanı ve Azure SYNAPSE Analytics veri kümeleri oluşturun.
> * Kopyalanacak tabloları aramak için bir işlem hattı ve gerçek kopyalama işlemini gerçekleştirmek için başka bir işlem hattı oluşturun. 
> * Bir işlem hattı çalıştırması başlatma.
> * İşlem hattı ve etkinlik çalıştırmalarını izleme.

Bu öğreticide Azure portalı kullanılır. Veri fabrikası oluşturmaya yönelik diğer araçlar/SDK’lar hakkında bilgi edinmek için bkz. [Hızlı Başlangıçlar](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Uçtan uca iş akışı
Bu senaryoda, Azure SQL veritabanı 'nda Azure SYNAPSE Analytics 'e kopyalamak istediğiniz birkaç tablo vardır. İş akışının işlem hatlarında gerçekleşen adımlarının mantıksal sırası şöyledir:

![İş akışı](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* İlk işlem hattı, havuz veri depolarına kopyalanması gereken tabloların listesini arar.  Alternatif olarak, havuz veri deposuna kopyalanacak tüm tabloları listeleyen bir meta veri tablosu tutabilirsiniz. İşlem hattı daha sonra veritabanındaki her bir tabloda yinelenen ve veri kopyalama işlemini gerçekleştiren başka bir işlem hattını tetikler.
* İkinci işlem hattı gerçek kopyalama işlemini gerçekleştirir. Tablo listesini bir parametre olarak alır. Listedeki her tablo için, en iyi performans için [BLOB depolama ve PolyBase aracılığıyla hazırlanan kopyayı](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics) kullanarak Azure SQL veritabanı 'ndaki belirli bir tabloyu Azure SYNAPSE Analytics 'te karşılık gelen tabloya kopyalayın. Bu örnekte, ilk işlem hattı tablo listesini bir parametre değeri olarak geçirir. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar
* **Azure depolama hesabı**. Azure Depolama hesabı, toplu kopyalama işleminde hazırlama blob depolama alanı olarak kullanılır. 
* **Azure SQL veritabanı**. Bu veritabanı, kaynak verileri içerir. SQL veritabanı 'nda Adventure Works LT örnek verileriyle bir veritabanı oluşturun ve [Azure SQL veritabanı 'nda veritabanı oluşturun](../azure-sql/database/single-database-create-quickstart.md) makalesini takip edin. Bu öğretici bu örnek veritabanındaki tüm tabloları bir Azure SYNAPSE analiziyle kopyalar.
* **Azure SYNAPSE Analytics**. Bu veri ambarı, SQL Veritabanından kopyalanan verileri tutar. Bir Azure SYNAPSE Analytics çalışma alanınız yoksa, oluşturma adımları için [Azure SYNAPSE Analytics ile çalışmaya başlama](..\synapse-analytics\get-started.md) makalesini inceleyin.

## <a name="azure-services-to-access-sql-server"></a>SQL sunucusuna erişime yönelik Azure hizmetleri

Hem SQL veritabanı hem de Azure SYNAPSE Analytics için, Azure hizmetlerinin SQL Server 'a erişmesine izin verin. Sunucunuz için **Azure hizmetlerinin ve kaynaklarının bu sunucuya erişmesine Izin ver** **ayarının açık olduğundan** emin olun. Bu ayar Data Factory hizmetinin Azure SQL veritabanınızdaki verileri okumasını ve Azure SYNAPSE analizlerinizi veri yazmasını sağlar. 

Bu ayarı doğrulamak ve etkinleştirmek için, sunucunuza > güvenlik > güvenlik duvarları ve sanal ağlar ' a gidin > **Azure hizmetlerinin ve kaynaklarının bu sunucuya erişmesine Izin ver** ayarını **Açık** olarak ayarlayın.

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. **Microsoft Edge** veya **Google Chrome** web tarayıcısını açın. Şu anda Data Factory kullanıcı arabirimi yalnızca Microsoft Edge ve Google Chrome web tarayıcılarında desteklenmektedir.
1. [Azure Portal](https://portal.azure.com) gidin. 
1. Azure Portal menüsünün sol tarafında, **kaynak**  >  **tümleştirmesi**  >  **Data Factory** oluştur ' u seçin. 

   ![“Yeni” bölmesinde Data Factory seçimi](./media/doc-common-process/new-azure-data-factory-menu.png)
1. **Yeni Veri Fabrikası** sayfasında **ad** için **ADFTutorialBulkCopyDF** girin. 
 
   Azure veri fabrikasının adı **genel olarak benzersiz** olmalıdır. Ad alanı için aşağıdaki hatayı görürseniz veri fabrikasının adını değiştirin (örneğin, adınızADFTutorialBulkCopyDF). Data Factory yapıtlarını adlandırma kuralları için [Data Factory - Adlandırma Kuralları](naming-rules.md) makalesine bakın.
  
    ```text
    Data factory name "ADFTutorialBulkCopyDF" is not available
    ```
1. Veri fabrikasını oluşturmak istediğiniz Azure **aboneliğini** seçin. 
1. **Kaynak grubu** için aşağıdaki adımlardan birini yapın:
     
   - **Var olanı kullan**’ı seçin ve ardından açılır listeden var olan bir kaynak grubu belirleyin. 
   - **Yeni oluştur**’u seçin ve bir kaynak grubunun adını girin.   
         
     Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/management/overview.md).  
1. **Sürüm** için **V2**'yi seçin.
1. Data factory için **konum** seçin. Data Factory'nin kullanılabileceği Azure bölgelerinin bir listesi için bir sonraki sayfada ilgilendiğiniz bölgeleri seçin ve **Analytics**'i genişleterek **Data Factory**: [Products available by region](https://azure.microsoft.com/global-infrastructure/services/) (Bölgeye göre kullanılabilir durumdaki ürünler) bölümünü bulun. Veri fabrikası tarafından kullanılan verileri depoları (Azure Depolama, Azure SQL Veritabanı vb.) ve işlemler (HDInsight vb.) başka bölgelerde olabilir.
1. **Oluştur**’a tıklayın.
1. Oluşturma işlemi tamamlandıktan sonra **Data Factory** sayfasına gitmek Için **Kaynağa Git** ' i seçin. 
   
1. Data Factory kullanıcı arabirimi uygulamasını ayrı bir sekmede açmak için **Author & Monitor** (Oluştur ve İzle) kutucuğuna tıklayın.


## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma
Veri depolarınızı ve işlemlerinizi veri fabrikasına bağlamak için bağlı hizmetler oluşturursunuz. Bağlı hizmetler, Data Factory hizmetinin çalışma zamanında veri deposuna bağlanmak için kullandığı bağlantı bilgilerini içerir. 

Bu öğreticide, Azure SQL veritabanı, Azure SYNAPSE Analytics ve Azure Blob depolama veri depolarınızı veri fabrikanıza bağlarsınız. Azure SQL Veritabanı, kaynak veri deposudur. Azure SYNAPSE Analytics, havuz/hedef veri deposudur. Azure Blob depolama, PolyBase kullanılarak veriler Azure SYNAPSE Analytics 'e yüklenmeden önce verileri aşamalandırmaktır. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Kaynak Azure SQL Veritabanı bağlı hizmetini oluşturma
Bu adımda, Azure SQL veritabanı 'nda veritabanınızı veri fabrikasına bağlamak için bağlı bir hizmet oluşturursunuz. 

1. Sol bölmedeki [Yönet sekmesini](./author-management-hub.md) açın.

1. Bağlı hizmetler sayfasında **+ Yeni** ' yi seçerek yeni bir bağlı hizmet oluşturun.

   :::image type="content" source="./media/doc-common-process/new-linked-service.png" alt-text="Yeni bağlı hizmet.":::
1. **New Linked Service** (Yeni Bağlı Hizmet) penceresinde **Azure SQL Veritabanı**’nı seçip **Devam**’a tıklayın. 
1. **Yeni bağlı hizmet (Azure SQL veritabanı)** penceresinde aşağıdaki adımları uygulayın: 

    a. **Ad** için **AzureSqlDatabaseLinkedService** adını girin.

    b. **Sunucu adı** için sunucunuzu seçin
    
    c. **Veritabanı adı** için veritabanınızı seçin. 
    
    d. Veritabanınıza bağlanacak **kullanıcının adını** girin. 
    
    e. Kullanıcının **parolasını** girin. 

    f. Belirtilen bilgileri kullanarak veritabanınızın bağlantısını test etmek için **Bağlantıyı Sına**' ya tıklayın.
  
    örneğin: Bağlı hizmeti kaydetmek için **Oluştur** ' a tıklayın.


### <a name="create-the-sink-azure-synapse-analytics-linked-service"></a>Azure SYNAPSE Analytics bağlı hizmeti havuzunu oluşturma

1. **Bağlantılar** sekmesinde, araç çubuğundaki **+ Yeni** düğmesine tekrar tıklayın. 
1. **Yeni bağlı hizmet** penceresinde **Azure SYNAPSE Analytics**' i seçin ve **devam**' a tıklayın. 
1. **Yeni bağlı hizmet (Azure SYNAPSE Analytics)** penceresinde aşağıdaki adımları uygulayın: 
   
    a. **Ad** için **AzureSqlDWLinkedService** adını girin.
     
    b. **Sunucu adı** için sunucunuzu seçin
     
    c. **Veritabanı adı** için veritabanınızı seçin. 
     
    d. Veritabanınıza bağlanmak için **Kullanıcı adı** girin. 
     
    e. Kullanıcı için **parola** girin. 
     
    f. Belirtilen bilgileri kullanarak veritabanınızın bağlantısını test etmek için **Bağlantıyı Sına**' ya tıklayın.
     
    örneğin: **Oluştur**’a tıklayın.

### <a name="create-the-staging-azure-storage-linked-service"></a>Hazırlama Azure Depolama bağlı hizmetini oluşturma
Bu öğreticide Azure Blob depolamayı daha iyi bir kopyalama performansı için PolyBase’i etkinleştiren geçici bir hazırlama alanı olarak kullanırsınız.

1. **Bağlantılar** sekmesinde, araç çubuğundaki **+ Yeni** düğmesine tekrar tıklayın. 
1. **New Linked Service** (Yeni Bağlı Hizmet) penceresinde **Azure Blob Depolama**’yı seçip **Devam**’a tıklayın. 
1. **Yeni bağlı hizmet (Azure Blob depolama)** penceresinde aşağıdaki adımları uygulayın: 

    a. **Ad** için **AzureStorageLinkedService** adını girin.                                                 
    b. **Depolama hesabı adı** Için **Azure depolama hesabınızı** seçin.
    
    c. **Oluştur**’a tıklayın.

## <a name="create-datasets"></a>Veri kümeleri oluşturma
Bu öğreticide, verilerin depolandığı konumu belirten kaynak ve havuz veri kümelerini oluşturacaksınız. 

**Azuressqldatabasedataset** giriş veri kümesi, **Azuressqldatabaselinkedservice** öğesine başvurur. Bağlı hizmet, veritabanına bağlanmaya yönelik bağlantı dizesini belirtir. Veri kümesi, kaynak verileri içeren veritabanı ve tablonun adını belirtir. 

**Azuresqldwdataset** çıkış veri kümesi **Azuresqldwlinkedservice** öğesine başvurur. Bağlı hizmet, Azure SYNAPSE Analytics 'e bağlanacak bağlantı dizesini belirtir. Veri kümesi, verilerin kopyalandığı hedef veritabanı ve tabloyu belirtir. 

Bu öğreticide, kaynak ve hedef SQL tabloları veri kümesi tanımında sabit kodlanmamıştır. Bunun yerine, ForEach etkinliği tablonun adını çalışma zamanında Kopyalama etkinliğine geçirir. 

### <a name="create-a-dataset-for-source-sql-database"></a>Kaynak SQL Veritabanı için veri kümesi oluşturma

1. Sol bölmeden **Yazar** sekmesini seçin.

1. **+** Sol bölmedeki (artı) öğesini seçin ve ardından **veri kümesi**' ni seçin. 

    ![Yeni veri kümesi menüsü](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. **Yeni veri kümesi** PENCERESINDE **Azure SQL veritabanı**' nı seçin ve ardından **devam**' a tıklayın. 
    
1. **Özellikleri ayarla** penceresinde, **ad**' ın altına **azuressqldatabasedataset** adını girin. **Bağlı hizmet** altında **Azuressqldatabaselinkedservice**' i seçin. Daha sonra, **Tamam**'a tıklayın.

1. **Bağlantı** sekmesine geçin, **tablo** için herhangi bir tablo seçin. Bu tablo işlevsiz bir tablodur. İşlem hattını oluştururken kaynak veri kümesinde bir sorgu belirtirsiniz. Sorgu, veritabanınızdaki verileri ayıklamak için kullanılır. Alternatif olarak, **Düzenle** onay kutusunu tıklatabilir ve tablo adı olarak **dbo. dummyName** yazabilirsiniz. 
 

### <a name="create-a-dataset-for-sink-azure-synapse-analytics"></a>Havuz Azure SYNAPSE Analytics için bir veri kümesi oluşturma 

1. Sol bölmedeki **+ (artı)** düğmesine ve **Veri Kümesi**'ne tıklayın. 
1. **Yeni veri kümesi** penceresinde **Azure SYNAPSE Analytics**' i seçin ve ardından **devam**' a tıklayın.
1. **Özellikleri ayarla** penceresinde, **ad**' ın altına **Azuresqldwdataset** adını girin. **Bağlı hizmet** altında **Azuresqldwlinkedservice** öğesini seçin. Daha sonra, **Tamam**'a tıklayın.
1. **Parametreler** sekmesine geçin, **+ Yeni**'ye tıklayın ve parametre adı olarak **DWTableName** girin. **+ Yeni** ' ye tekrar tıklayın ve parametre adı Için **dwschema** yazın. Bu adı sayfadan kopyalayıp yapıştırırsanız *Dwtablename* ve *dwschema* sonunda **boşluk karakteri** bulunmadığından emin olun. 
1. **Bağlantı** sekmesine geçin, 

    1. **Tablo** için **Düzenle** seçeneğini işaretleyin. İlk giriş kutusunu seçin ve aşağıdaki **dinamik Içerik Ekle** bağlantısına tıklayın. **Dinamik Içerik Ekle** sayfasında, **Parametreler** altındaki **dwschema** ' a tıklayarak üstteki ifade metin kutusunu otomatik olarak doldurur `@dataset().DWSchema` ve ardından **son**' a tıklayın.  
    
        ![Veri kümesi bağlantısı TableName](./media/tutorial-bulk-copy-portal/dataset-connection-tablename.png)

    1. İkinci giriş kutusunu seçin ve aşağıdaki **dinamik Içerik Ekle** bağlantısına tıklayın. **Dinamik Içerik Ekle** sayfasında, **Parametreler** altındaki **dwtablename** öğesine tıklayarak üstteki ifade metin kutusunu otomatik olarak doldurur `@dataset().DWTableName` ve ardından **son**' a tıklayın. 
    
    1. Veri kümesinin **TableName** özelliği, **Dwschema** ve **dwtablename** parametreleri için bağımsız değişken olarak geçirilen değerlere ayarlanır. ForEach etkinliği bir tablo listesi boyunca yinelenir ve birer birer Kopyalama etkinliğine geçirilir. 
    

## <a name="create-pipelines"></a>İşlem hattı oluşturma
Bu öğreticide, iki işlem hattı oluşturursunuz: **IterateAndCopySQLTables** ve **GetTableListAndTriggerCopyData**. 

**Gettablelistandtriggercopydata** işlem hattı iki eylem gerçekleştirir:

* Kopyalanacak tabloların listesini almak için Azure SQL Veritabanı sistem tablosunu arar.
* Gerçek veri kopyalamayı yapmak için **IterateAndCopySQLTables** işlem hattını tetikler.

**Iterateandcopysqltables** işlem hattı, bir tablo listesini parametre olarak alır. Listedeki her tablo için, Azure SQL veritabanındaki tablodaki verileri, hazırlanan Copy ve PolyBase kullanarak Azure SYNAPSE Analytics 'e kopyalar.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>IterateAndCopySQLTables işlem hattını oluşturma

1. Sol bölmede, **+ (artı)** düğmesine ve sonra da **İşlem Hattı**’na tıklayın.

    ![Yeni işlem hattı menüsü](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
 
1. Genel panelinde **Özellikler** altında, **ad** Için **Iterateandcopysqltables** ' ı belirtin. Sonra sağ üst köşedeki Özellikler simgesine tıklayarak paneli daraltın.

1. **Parametreler** sekmesine geçin ve aşağıdaki eylemleri gerçekleştirin: 

    a. **+ Yeni** öğesine tıklayın. 
    
    b. Parametre **adı** Için **tablelist** girin.
    
    c. **Tür** için **Dizi**'yi seçin.

1. **Etkinlikler** araç kutusunda **Yineleme ve Koşullar**’ı genişletin ve **ForEach** etkinliğini sürükleyerek işlem hattı tasarım yüzeyine bırakın. Ayrıca, **Etkinlikler** araç kutusunda etkinlikler için arama yapabilirsiniz. 

    a. En alttaki **Genel** sekmesinde, **Ad** olarak **IterateSQLTables** girin. 

    b. **Ayarlar** sekmesine geçin, **öğeler** için giriş kutusuna tıklayın, ardından aşağıdaki **dinamik içerik Ekle** bağlantısına tıklayın. 

    c. **Dinamik Içerik Ekle** sayfasında, **Sistem değişkenleri** ve **işlevler** bölümlerini daraltın, **Parametreler** altındaki **tablelist** öğesine tıklayarak üstteki ifade metin kutusunu otomatik olarak doldurur `@pipeline().parameter.tableList` . Ardından, **Son**'a tıklayın. 

    ![Foreach parametresi derleyici](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. **Etkinlikler** sekmesine geçin, **foreach** etkinliğine bir alt etkinlik eklemek için **kurşun kalem simgesine** tıklayın.
    
    ![ForEach etkinlik Oluşturucusu](./media/tutorial-bulk-copy-portal/for-each-activity-builder.png)

1. **Etkinlikler** araç kutusunda **Taşı & aktar**' ı genişletin ve **veri kopyalama** etkinliğini ardışık düzen Tasarımcısı yüzeyine sürükleyin. En üstteki içerik haritası menüsüne dikkat edin. **Iterateandcopysqltable** , işlem hattı adıdır ve **ıteratesqltables** , ForEach etkinlik adıdır. Tasarımcı, etkinlik kapsamdadır. ForEach düzenleyicisinden işlem hattı düzenleyicisine geri dönmek için, içerik haritası menüsündeki bağlantıya tıklayabilirsiniz. 

    ![ForEach içinde kopyalama](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)

1. **Kaynak** sekmesine geçin ve aşağıdaki adımları izleyin:

    1. **Kaynak Veri Kümesi** olarak **AzureSqlDatabaseDataset** seçin. 
    1. **Kullanım sorgusu** için **sorgu** seçeneğini belirleyin. 
    1. **Sorgu** giriş kutusuna tıklayın -> aşağıdaki **Dinamik içerik ekle**'yi seçin -> **Sorgu** için aşağıdaki ifadeyi girin -> **Son**'u seçin.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 

1. **Havuz** sekmesine geçin ve aşağıdaki adımları izleyin: 

    1. **Havuz Veri Kümesi** olarak **AzureSqlDWDataset** seçin.
    1. DWTableName parametresinin DEĞERI için giriş kutusuna tıklayın-> aşağıdan **dinamik Içerik Ekle** ' yi seçin, `@item().TABLE_NAME` komut dosyası olarak ifade girin,-> **son**' u seçin.
    1. DWSchema parametresinin DEĞERI için giriş kutusuna tıklayın-> aşağıdan **dinamik Içerik Ekle** ' yi seçin, `@item().TABLE_SCHEMA` komut dosyası olarak ifade girin,-> **son**' u seçin.
    1. Copy yöntemi için **PolyBase**' i seçin. 
    1. **Varsayılan tür kullan** seçeneğini temizleyin. 
    1. Tablo seçeneği için varsayılan ayar "none" dır. Azure SYNAPSE Analytics havuzu 'nda önceden oluşturulmuş tablolar yoksa, **Otomatik tablo oluştur** seçeneğini etkinleştirin, kopyalama etkinliği, kaynak verilere göre sizin için otomatik olarak tablo oluşturur. Ayrıntılar için [Otomatik havuz tabloları oluşturma](copy-activity-overview.md#auto-create-sink-tables)bölümüne bakın. 
    1. **Betiği kopyala** giriş kutusuna tıklayın -> aşağıdaki **Dinamik içerik ekle**'yi seçin -> betik için aşağıdaki ifadeyi girin -> **Son**'u seçin. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Kopyalama havuz ayarları](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)

1. **Ayarlar** sekmesine geçin ve aşağıdaki adımları uygulayın: 

    1. **Hazırlamayı etkinleştir** onay kutusunu seçin.
    1. **Depo Hesabı Bağlı Hizmeti** için **AzureStorageLinkedService** hizmetini seçin.

1. İşlem hattı ayarlarını doğrulamak için üst işlem hattı araç çubuğunda **Doğrula**’ya tıklayın. Doğrulama hatası olmadığından emin olun. İşlem **hattı doğrulama raporunu** kapatmak için çift açılı ayraçları tıklatın **>>** .

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>GetTableListAndTriggerCopyData işlem hattını oluşturma

Bu işlem hattı iki eylem yapar:

* Kopyalanacak tabloların listesini almak için Azure SQL Veritabanı sistem tablosunu arar.
* Gerçek veri kopyalamayı yapmak için "IterateAndCopySQLTables" işlem hattını tetikler.

İşlem hattını oluşturma adımları aşağıda verilmiştir:

1. Sol bölmede, **+ (artı)** düğmesine ve sonra da **İşlem Hattı**’na tıklayın.
1. Genel panelinde **Özellikler** altında, işlem hattının adını **Gettablelistandtriggercopydata** olarak değiştirin. 

1. **Etkinlikler** araç kutusunda **genel**' i genişletin ve **arama** etkinliğini, işlem hattı Tasarımcısı yüzeyine sürükleyip bırakın ve aşağıdaki adımları uygulayın:

    1. **Ad** olarak **LookupTableList** girin. 
    1. **Açıklama** için **Veritabanımın Tablo listesini al** yazın.

1. **Ayarlar** sekmesine geçin ve aşağıdaki adımları uygulayın:

    1. **Kaynak Veri Kümesi** olarak **AzureSqlDatabaseDataset** seçin. 
    1. **Kullanım sorgusu** için **sorgu** ' yı seçin. 
    1. **Sorgu** için aşağıdaki SQL sorgusunu girin.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. **Yalnızca ilk satır** alanının onay işaretini temizleyin.

        ![Arama etkinliği - ayarlar sayfası](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Etkinlik araç kutusu ' ndan işlem **hattı** Tasarımcısı yüzeyine sürükleyip bırakın ve adı **triggercopy** olarak ayarlayın.

1. **Arama** etkinliğini Işlem hattını **Yürüt** etkinliğine **bağlamak** için, arama etkinliğine eklenen **yeşil kutuyu** ardışık düzen yürütme etkinliğinin soluna sürükleyin.

    ![Arama ve İşlem Hattı Yürütme etkinliklerini bağlama](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)

1. İşlem **hattı yürütme** etkinliğinin **Ayarlar** sekmesine geçin ve aşağıdaki adımları uygulayın: 

    1. **Çağrılan işlem hattı** olarak **IterateAndCopySQLTables** seçin. 
    1. **Tamamlanmasını beklemek** için onay kutusunu temizleyin.
    1. **Parametreler** bölümünde, değer ' in altındaki giriş kutusuna tıklayın > aşağıdaki **dinamik içerik Ekle** ' yi seçin > `@activity('LookupTableList').output.value` tablo adı olarak girin-> **son**' u seçin. Sonuç listesini, arama etkinliğinden ikinci işlem hattına giriş olarak ayarlıyoruz. Sonuç listesinde yer alan tabloların içerdiği verilerin hedefe kopyalanması gerekir. 

        ![İşlem hattı yürütme etkinliği - ayarlar sayfası](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)

1. İşlem hattını doğrulamak için araç çubuğundaki **Doğrula**'ya tıklayın. Doğrulama hatası olmadığından emin olun. **İşlem Hattı Doğrulama Raporu**'nu kapatmak için **>>** seçeneğine tıklayın.

1. Varlıkları (veri kümeleri, işlem hatları, vb.) Data Factory hizmetine yayımlamak için pencerenin üst kısmında **Tümünü Yayımla** ' ya tıklayın. Yayımlama başarılı olana kadar bekleyin. 

## <a name="trigger-a-pipeline-run"></a>İşlem hattı çalıştırmasını tetikleme

1. İşlem hattı **Gettablelistandtriggercopydata**' a gidin, üst işlem hattı araç çubuğunda **tetikleyici Ekle** ' ye tıklayın ve sonra **Şimdi Tetikle**' ye tıklayın. 

1. İşlem **hattı çalıştırması** sayfasında çalıştırmayı onaylayın ve ardından **son**' u seçin.

## <a name="monitor-the-pipeline-run"></a>İşlem hattı çalıştırmasını izleme

1. **İzleyici** sekmesine geçin. Çözümünüzde her iki işlem hattı için de çalıştırmaları görene kadar **Yenile** ' ye tıklayın. **Başarılı oldu** durumunu görene kadar listeyi yenilemeye devam edin. 

1. **Gettablelistandtriggercopydata** işlem hattı ile ilişkili etkinlik çalıştırmalarını görüntülemek için işlem hattının işlem hattı adı bağlantısına tıklayın. Bu işlem hattı çalıştırması için iki etkinlik çalıştırması görüyor olmalısınız. 
    ![İşlem hattı çalıştırmasını izleme](./media/tutorial-bulk-copy-portal/monitor-pipeline.png)
1. **Arama** etkinliğinin çıkışını görüntülemek Için, **etkinlik adı** sütununun altındaki etkinliğin yanındaki **Çıkış** bağlantısına tıklayın. **Çıkış** penceresinin ekranı kaplamasını sağlamalı ve pencereyi geri yüklemelisiniz. Gözden geçirdikten sonra, **X** simgesine tıklayarak **Çıkış** penceresini kapatın.

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
1. İşlem **hattı çalıştırmaları** görünümüne geri dönmek için, içerik haritası menüsünün en üstündeki **tüm işlem hattı çalıştırmaları** bağlantısına tıklayın. İşlem hattının etkinlik çalıştırmalarını görüntülemek için **Iterateandcopysqltables** bağlantısına ( **ardışık düzen adı** sütunu altında) tıklayın. **Arama** etkinliği çıkışında her tablo Için bir **kopyalama** etkinliği çalıştırılmış olduğuna dikkat edin. 

1. Verilerin bu öğreticide kullandığınız hedef Azure SYNAPSE analiziyle kopyalandığından emin olun. 

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide aşağıdaki adımları gerçekleştirdiniz: 

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Azure SQL veritabanı, Azure SYNAPSE Analytics ve Azure depolama bağlı hizmetleri oluşturun.
> * Azure SQL veritabanı ve Azure SYNAPSE Analytics veri kümeleri oluşturun.
> * Kopyalanacak tabloları aramak için bir işlem hattı ve gerçek kopyalama işlemini gerçekleştirmek için başka bir işlem hattı oluşturma. 
> * Bir işlem hattı çalıştırması başlatma.
> * İşlem hattı ve etkinlik çalıştırmalarını izleme.

Kaynaktan hedefe verileri artımlı olarak koppyalama hakkında bilgi edinmek için aşağıdaki öğreticiye geçin:
> [!div class="nextstepaction"]
>[Verileri artımlı olarak kopyalama](tutorial-incremental-copy-portal.md)
