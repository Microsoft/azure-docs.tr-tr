---
title: Azure Data Factory kullanarak Azure SQL veri ambarı 'na veri yükleme
description: Azure SQL veri ambarı 'na veri kopyalamak için Azure Data Factory kullanma
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: jingwang
ms.openlocfilehash: 538751b1e93dfec66c35ea3768bde603c198df32
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672756"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure SQL veri ambarı 'na veri yükleme

[Azure SQL veri ambarı](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) , hem ilişkisel hem de ilişkisel olmayan büyük hacimli verileri işleyebilen bulut tabanlı, genişleme bir veritabanıdır. SQL veri ambarı, kurumsal veri ambarı iş yükleri için en iyi duruma getirilmiş, yüksek düzeyde paralel işleme (MPP) mimarisi üzerine kurulmuştur. Depolama ve işlem ölçeğini bağımsız olarak ölçeklendirmeye yönelik esnekliğe sahip bulut esnekliği sunar.

Azure Data Factory kullandığınızda Azure SQL veri ambarı 'nı kullanmaya artık hiç olmadığı kadar kolay. Azure Data Factory, tam olarak yönetilen bulut tabanlı bir veri tümleştirme hizmetidir. Hizmeti kullanarak bir SQL veri ambarını mevcut sisteminizdeki verilerle doldurabilir ve analiz çözümlerinizi oluştururken zamandan tasarruf edebilirsiniz.

Azure Data Factory Azure SQL veri ambarı 'na veri yüklemek için aşağıdaki avantajları sunmaktadır:

* **Kolayca ayarlanabilir**: komut dosyası gerekmeden sezgisel 5 adımlı bir sihirbaz.
* **Zengin veri deposu desteği**: zengin bir şirket içi ve bulut tabanlı veri deposu kümesi için yerleşik destek. Ayrıntılı bir liste için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats)tablosuna bakın.
* **Güvenli ve uyumlu**: veriler HTTPS veya ExpressRoute üzerinden aktarılır. Küresel hizmet varlığı, verilerinizin hiçbir şekilde coğrafi sınır bırakmamasını sağlar.
* **PolyBase kullanarak benzersiz olmayan performans**: PolyBase, VERILERI Azure SQL veri ambarı 'na taşımanın en etkili yoludur. Azure Blob depolama ve Data Lake Store dahil olmak üzere tüm veri deposu türlerinden yüksek yük hızları elde etmek için hazırlama blobu özelliğini kullanın. (PolyBase, Azure Blob depolamayı ve varsayılan olarak Azure Data Lake Store destekler.) Ayrıntılar için bkz. [etkinlik performansını kopyalama](copy-activity-performance.md).

Bu makalede, _Azure SQL veritabanından Azure SQL veri ambarı 'na veri yüklemek_için Data Factory veri kopyalama aracının nasıl kullanılacağı gösterilmektedir. Diğer veri deposu türlerinden veri kopyalamak için benzer adımları izleyebilirsiniz.

> [!NOTE]
> Daha fazla bilgi için bkz. [Azure Data Factory kullanarak Azure SQL veri ambarı 'na veya buradan veri kopyalama](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği: bir Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* Azure SQL veri ambarı: veri ambarı, SQL veritabanından kopyalandığı verileri tutar. Azure SQL veri ambarınız yoksa, [SQL veri ambarı oluşturma](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md)' daki yönergelere bakın.
* Azure SQL veritabanı: Bu öğretici, Adventure Works LT örnek verileriyle bir Azure SQL veritabanından veri kopyalar. [Azure SQL veritabanı oluşturma](../sql-database/sql-database-get-started-portal.md)bölümündeki yönergeleri IZLEYEREK bir SQL veritabanı oluşturabilirsiniz. 
* Azure depolama hesabı: Azure depolama, toplu kopyalama işleminde _hazırlama_ blobu olarak kullanılır. Azure depolama hesabınız yoksa [Depolama hesabı oluşturma](../storage/common/storage-quickstart-create-account.md) bölümündeki yönergelere bakın.

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. Sol taraftaki menüden > **kaynak oluştur** ' u seçin **veri ve analiz** > **Data Factory**: 
   
   ![“Yeni” bölmesinde Data Factory seçimi](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. **Yeni Veri Fabrikası** sayfasında, aşağıdaki görüntüde gösterilen alanlar için değerler girin:
      
   ![Yeni veri fabrikası sayfası](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **Ad**: Azure Data Factory 'niz için genel olarak benzersiz bir ad girin. "Data Factory Name \"LoadSQLDWDemo\" kullanılamıyor hatası alırsanız, Veri Fabrikası için farklı bir ad girin. Örneğin _**, Name**_ **ADFTutorialDataFactory**adını kullanabilirsiniz. Data Factory 'yi yeniden oluşturmayı deneyin. Data Factory yapıtlarını adlandırma kuralları için bkz. [Data Factory adlandırma kuralları](naming-rules.md).
    * **Abonelik**: veri fabrikasının oluşturulacağı Azure aboneliğinizi seçin. 
    * **Kaynak grubu**: açılan listeden var olan bir kaynak grubunu seçin veya **Yeni oluştur** seçeneğini belirleyin ve bir kaynak grubunun adını girin. Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/resource-group-overview.md).  
    * **Sürüm**: **v2**'yi seçin.
    * **Konum**: veri fabrikasının konumunu seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Data Factory tarafından kullanılan veri depoları diğer konumlarda ve bölgelerde olabilir. Bu veri depoları Azure Data Lake Store, Azure depolama, Azure SQL veritabanı vb. içerir.

3. **Oluştur**'u seçin.
4. Oluşturma işlemi tamamlandıktan sonra, veri fabrikanıza gidin. Aşağıdaki görüntüde gösterildiği gibi **Data Factory** giriş sayfasını görürsünüz:
   
   ![Data factory giriş sayfası](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Veri tümleştirme uygulamasını ayrı bir sekmede başlatmak için **yazar & İzleyici** kutucuğunu seçin.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Azure SQL Veri Ambarı’na veri yükleme

1. **Başlarken** sayfasında, veri kopyalama aracını başlatmak için **veri kopyalama** kutucuğunu seçin:

   ![Veri Kopyalama aracının kutucuğu](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
1. **Özellikler** sayfasında, **görev adı** alanı Için **Copyfromsqltosqldw** ' i belirtin ve **İleri**' yi seçin:

    ![Özellikler sayfası](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. **Kaynak veri deposu** sayfasında, aşağıdaki adımları izleyin:

    a. **+ Yeni bağlantı oluştur**' a tıklayın:

    ![Kaynak veri deposu sayfası](./media/load-azure-sql-data-warehouse/new-source-linked-service.png)

    b. Galeriden **Azure SQL veritabanı** ' nı seçin ve **devam**' ı seçin. Bağlayıcıları filtrelemek için arama kutusuna "SQL" yazabilirsiniz.

    ![Azure SQL veritabanını seçme](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. **Yeni bağlı hizmet** sayfasında, açılır listeden sunucu ADıNıZı ve DB adını seçin ve Kullanıcı adını ve parolayı belirtin. Ayarları doğrulamak için **Bağlantıyı Sına** ' ya tıklayın ve ardından **son**' u seçin.
   
    ![Azure SQL veritabanını yapılandırma](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Kaynak olarak yeni oluşturulan bağlantılı hizmeti seçin ve **İleri**'ye tıklayın.

    ![Kaynak olarak bağlantılı hizmeti seçin](./media/load-azure-sql-data-warehouse/select-source-linked-service.png)

1. **Verilerin kopyalanacağı tabloları seçin veya özel bir sorgu sayfası kullanın** sayfasında, tabloları filtrelemek Için **SalesLT** girin. Kopyaya ait tüm tabloları kullanmak için **(Tümünü Seç)** kutusunu seçin ve ardından **İleri**' yi seçin: 

    ![Kaynak tabloları Seç](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. **Hedef veri deposu** sayfasında, aşağıdaki adımları izleyin:

    a. Bağlantı eklemek için **+ Yeni bağlantı oluştur**'a tıklayın

    ![Havuz veri deposu sayfası](./media/load-azure-sql-data-warehouse/new-sink-linked-service.png)

    b. Galeriden **Azure SQL veri ambarı** ' nı seçin ve **İleri**' yi seçin.

    ![Azure SQL DW 'yi seçin](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. **Yeni bağlı hizmet** sayfasında, açılır listeden sunucu ADıNıZı ve DB adını seçin ve Kullanıcı adını ve parolayı belirtin. Ayarları doğrulamak için **Bağlantıyı Sına** ' ya tıklayın ve ardından **son**' u seçin.
   
    ![Azure SQL DW 'yi yapılandırma](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Kaynak olarak yeni oluşturulan bağlantılı havuz hizmetini seçin ve **İleri**'ye tıklayın.

    ![Havuza bağlı hizmeti seçin](./media/load-azure-sql-data-warehouse/select-sink-linked-service.png)

1. **Tablo eşleme** sayfasında, içeriği gözden geçirin ve **İleri**' yi seçin. Akıllı tablo eşleştirmesi görüntülenir. Kaynak tablolar, tablo adlarına göre hedef tablolarla eşleştirilir. Hedefte bir kaynak tablosu yoksa, Azure Data Factory varsayılan olarak aynı ada sahip bir hedef tablo oluşturur. Ayrıca, bir kaynak tabloyu var olan bir hedef tabloyla eşleyebilirsiniz. 

   > [!NOTE]
   > SQL veri ambarı havuzu için otomatik tablo oluşturma, SQL Server veya Azure SQL veritabanı kaynak olduğunda geçerlidir. Verileri başka bir kaynak veri deposundan kopyalarsanız, veri kopyasını yürütmeden önce şemayı Azure SQL veri ambarı 'nda önceden oluşturmanız gerekir.

   ![Tablo eşleme sayfası](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. **Şema eşleme** sayfasında, içeriği gözden geçirin ve **İleri**' yi seçin. Akıllı tablo eşleme, sütun adını temel alır. Tabloları otomatik olarak oluşturalım Data Factory, kaynak ve hedef mağazalar arasında uyumsuzluklar olduğunda veri türü dönüştürme gerçekleşebilir. Kaynak ve hedef sütun arasında desteklenmeyen bir veri türü dönüştürmesi varsa, karşılık gelen tablonun yanında bir hata mesajı görürsünüz.

    ![Şema eşleme sayfası](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. **Ayarlar** sayfasında, aşağıdaki adımları izleyin:

    a. **Hazırlama ayarları** bölümünde, yeni bir hazırlama depolaması Için **+ Yeni** öğesine tıklayın. Depolama, PolyBase kullanılarak SQL veri ambarı 'na yüklenmeden önce verileri hazırlamak için kullanılır. Kopyalama işlemi tamamlandıktan sonra, Azure Storage 'daki geçici veriler otomatik olarak temizlenir. 

    ![Hazırlama yapılandırma](./media/load-azure-sql-data-warehouse/configure-staging.png)

    b. **Yeni bağlı hizmet** sayfasında depolama hesabınızı seçin ve **son**' u seçin.
   
    ![Azure Storage 'ı yapılandırma](./media/load-azure-sql-data-warehouse/configure-blob-storage.png)

    c. **Gelişmiş ayarlar** bölümünde, **türü varsayılan kullan** seçeneğinin Işaretini kaldırın ve ardından **İleri**' yi seçin.

    ![PolyBase 'i yapılandırma](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. **Özet** sayfasında, ayarları gözden geçirin ve **İleri**' yi seçin:

    ![Özet sayfası](./media/load-azure-sql-data-warehouse/summary-page.png)
1. **Dağıtım sayfasında**, işlem hattını (görev) Izlemek için **izleyici** ' yi seçin:

    ![Dağıtım sayfası](./media/load-azure-sql-data-warehouse/deployment-page.png)
1. Soldaki **İzleyici** sekmesinin otomatik olarak seçildiğine dikkat edin. Eylemler sütunu, etkinlik çalıştırma ayrıntılarını görüntüleme ve **işlem** hattını yeniden çalıştırma bağlantılarını içerir: 

    ![İşlem hattı çalıştırmalarını izleme](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)
1. Işlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görüntülemek için **Eylemler** sütunundaki **etkinlik çalıştırmalarını görüntüle** bağlantısını seçin. İşlem hattı çalıştırmaları görünümüne geri dönmek için üstteki işlem **hatları** bağlantısını seçin. Listeyi yenilemek için **Yenile**’yi seçin. 

    ![Etkinlik çalıştırmalarını izleme](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Her kopyalama etkinliğinin yürütme ayrıntılarını izlemek için, etkinlik izleme görünümündeki **Eylemler** altında **Ayrıntılar** bağlantısını seçin. Kaynaktan havuza, veri işleme, karşılık gelen süre ile yürütme adımlarına ve kullanılan yapılandırmalara kadar olan veri hacmi gibi ayrıntıları izleyebilirsiniz:

    ![Etkinlik çalıştırma ayrıntılarını izle](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Sonraki adımlar

Azure SQL veri ambarı desteği hakkında bilgi edinmek için aşağıdaki makaleye ilerleyin: 

> [!div class="nextstepaction"]
>[Azure SQL veri ambarı Bağlayıcısı](connector-azure-sql-data-warehouse.md)
