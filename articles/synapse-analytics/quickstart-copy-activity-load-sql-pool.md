---
title: 'Hızlı başlangıç: kopyalama etkinliğini kullanarak adanmış SQL havuzuna veri yüklemek için'
description: Adanmış SQL havuzuna veri yüklemek için Azure SYNAPSE Analytics 'teki işlem hattı kopyalama etkinliğini kullanın.
services: synapse-analytics
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.custom: seo-lt-2019
ms.date: 11/02/2020
ms.openlocfilehash: 8c0ebebd98a34a82b464707b82a650a1825676be
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566265"
---
# <a name="quickstart-load-data-into-dedicated-sql-pool-using-the-copy-activity"></a>Hızlı başlangıç: kopyalama etkinliğini kullanarak adanmış SQL havuzuna veri yükleme

Azure SYNAPSE Analytics, verilerinizi almak, dönüştürmek, modellemek ve analiz etmenize yardımcı olmak için çeşitli analiz motorları sunmaktadır. Adanmış bir SQL havuzu, T-SQL tabanlı işlem ve depolama olanakları sunar. SYNAPSE çalışma alanınızda adanmış bir SQL havuzu oluşturduktan sonra, veriler yüklenebilir, modellenebilir, işlenebilir ve daha hızlı analitik Öngörüler için teslim edilebilir.

Bu hızlı *Başlangıçta Azure SQL veritabanından Azure SYNAPSE Analytics 'e nasıl veri yükleneceğini* öğreneceksiniz. Diğer veri deposu türlerinden veri kopyalamak için benzer adımları izleyebilirsiniz. Bu benzer akış, diğer kaynak ve havuzlar için de veri kopyalama için geçerlidir.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği: bir Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* Azure SYNAPSE çalışma alanı: [hızlı başlangıç: bir Synapse çalışma alanı oluşturma](quickstart-create-workspace.md)yönergelerini takip eden Azure Portal kullanarak bir Synapse çalışma alanı oluşturun.
* Azure SQL veritabanı: Bu öğretici, verileri Azure SQL veritabanı 'nda Adventure Works LT örnek veri kümesinden kopyalar. [Azure SQL veritabanı 'nda örnek veritabanı oluşturma](../azure-sql/database/single-database-create-quickstart.md)bölümündeki YÖNERGELERI Izleyerek SQL veritabanında bu örnek veritabanını oluşturabilirsiniz. Ya da benzer adımları izleyerek başka veri depoları da kullanabilirsiniz.
* Azure depolama hesabı: Azure depolama, kopyalama işleminde *hazırlama* alanı olarak kullanılır. Azure depolama hesabınız yoksa [Depolama hesabı oluşturma](../storage/common/storage-account-create.md) bölümündeki yönergelere bakın.
* Azure SYNAPSE Analytics: adanmış bir SQL havuzunu havuz veri deposu olarak kullanırsınız. Azure SYNAPSE Analytics örneğiniz yoksa, oluşturma adımları için [Özel BIR SQL havuzu oluşturma](quickstart-create-sql-pool-portal.md) konusuna bakın.

### <a name="navigate-to-the-synapse-studio"></a>SYNAPSE Studio 'ya gidin

SYNAPSE çalışma alanınız oluşturulduktan sonra, SYNAPSE Studio 'Yu açmak için iki yol vardır:

* [Azure Portal](https://ms.portal.azure.com/#home)SYNAPSE çalışma alanınızı açın. Başlarken ' in altındaki Open SYNAPSE Studio kartını **Aç** ' ı seçin.
* [Azure SYNAPSE Analytics](https://web.azuresynapse.net/) 'i açın ve çalışma alanınızda oturum açın.

Bu hızlı başlangıçta, örnek olarak "adftest2020" adlı çalışma alanını kullanırız. SYNAPSE Studio giriş sayfasına otomatik olarak gidecektir.

![SYNAPSE Studio giriş sayfası](media/doc-common-process/synapse-studio-home.png)

## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma

Azure SYNAPSE Analytics 'te, bağlı bir hizmet, bağlantı bilgilerinizi diğer hizmetlere tanımladığınız yerdir. Bu bölümde, aşağıdaki iki tür bağlı hizmeti oluşturacaksınız: Azure SQL veritabanı ve Azure Data Lake Storage 2. (ADLS 2.) bağlı hizmetleri.

1. SYNAPSE Studio giriş sayfasında, sol gezinti bölmesinde **Yönet** sekmesini seçin.
1. Dış bağlantılar altında bağlı hizmetler ' i seçin.
  
   ![Yeni bağlı hizmet oluşturma](media/doc-common-process/new-linked-service.png)

1. Bağlı bir hizmet eklemek için **Yeni**' yi seçin.
1. Galeriden **Azure SQL veritabanı** ' nı seçin ve ardından **devam**' ı seçin. Bağlayıcıları filtrelemek için arama kutusuna "SQL" yazabilirsiniz.

   ![Yeni Azure SQL veritabanı bağlı hizmeti oluştur](media/quickstart-copy-activity-load-sql-pool/new-azure-sql-linked-service.png)

1. Yeni bağlı hizmet sayfasında, açılır listeden sunucu adınızı ve DB adını seçin ve Kullanıcı adını ve parolayı belirtin. Ayarları doğrulamak için **Bağlantıyı Sına** ' ya tıklayın ve ardından **Oluştur**' u seçin.

   ![Azure SQL veritabanı bağlı hizmetini yapılandırma](media/quickstart-copy-activity-load-sql-pool/azure-sql-linked-service-configuration.png)

1. 3-4 arası adımları tekrarlayın, ancak Galeri yerine **Azure Data Lake Storage 2.** seçin. Yeni bağlı hizmet sayfasında, açılan listeden depolama hesabınızın adını seçin. Ayarları doğrulamak için **Bağlantıyı Sına** ' ya tıklayın ve ardından **Oluştur**' u seçin. 

   ![Azure Data Lake Storage 2. Yapılandır](media/quickstart-copy-activity-load-sql-pool/adls-gen2-linked-service-configuration.png)
 
## <a name="create-a-pipeline"></a>İşlem hattı oluşturma

İşlem hattı, bir dizi etkinliğin yürütülmesi için mantıksal akışı içerir. Bu bölümde, Azure SQL veritabanı 'ndan adanmış bir SQL havuzuna veri veren bir kopyalama etkinliği içeren bir işlem hattı oluşturacaksınız.

1. **Tümleştirin** sekmesine gidin. Ardışık düzen üstbilgisinin yanındaki artı simgesini seçin ve işlem hattı ' nı seçin.

   ![Yeni işlem hattı oluşturma](media/doc-common-process/new-pipeline.png)

1. *Etkinlikler* bölmesinde *taşıma ve dönüştürme* ' nın altında, verileri ardışık düzen tuvaline **Kopyala** ' yı sürükleyin.
1. Kopyalama etkinliği ' ni seçin ve kaynak sekmesine gidin. Yeni bir kaynak veri kümesi oluşturmak için **Yeni** ' yi seçin.

   ![Kaynak veri kümesi oluşturma](media/quickstart-copy-activity-load-sql-pool/new-source-dataset.png)

1. Veri depolama alanı olarak **Azure SQL veritabanı** ' nı seçin ve **devam**' ı seçin.
1. *Özellikleri ayarla* bölmesinde, önceki adımda oluşturduğunuz Azure SQL veritabanı bağlı hizmetini seçin. 
1. Tablo adı altında, aşağıdaki kopyalama etkinliğinde kullanılacak örnek bir tablo seçin. Bu hızlı başlangıçta, örnek olarak "SalesLT. Customer" tablosunu kullanırız. 

   ![Kaynak veri kümesi özelliklerini ayarlama](media/quickstart-copy-activity-load-sql-pool/source-dataset-properties.png)
1. Bittiğinde **Tamam ' ı** seçin.
1. Kopyalama etkinliğini seçin ve havuz sekmesine gidin. Yeni bir havuz veri kümesi oluşturmak için **Yeni** ' yi seçin.
1. Veri depolduğunuz **Azure SYNAPSE ADANMıŞ SQL havuzunu** seçin ve **devam**' ı seçin.
1. **Özellikleri ayarla** bölmesinde, önceki ADıMDA oluşturduğunuz SQL Analytics havuzunu seçin. Var olan bir tabloya yazıyorsanız *tablo adı* bölümünde açılan listeden seçin. Aksi takdirde, "Düzenle" yi işaretleyin ve yeni tablo adınızı girin. Bittiğinde **Tamam ' ı** seçin.
1. Havuz veri kümesi ayarları için tablo seçenek alanında **Otomatik oluştur** ' u etkinleştirin.

   ![Otomatik oluşturmayı etkinleştir](media/quickstart-copy-activity-load-sql-pool/auto-create-table.png)

1. **Ayarlar** sayfasında, **hazırlamayı etkinleştir** onay kutusunu seçin. Bu seçenek, kaynak verileriniz PolyBase ile uyumlu değilse geçerlidir. **Hazırlama ayarları** bölümünde, hazırlama depolama alanı olarak önceki adımda oluşturduğunuz Azure Data Lake Storage 2. bağlı hizmeti seçin. 

    Depolama, PolyBase kullanılarak Azure SYNAPSE Analytics 'e yüklenmeden önce verileri hazırlamak için kullanılır. Kopyalama işlemi tamamlandıktan sonra, Azure Data Lake Storage 2. içindeki ara veriler otomatik olarak temizlenir.

   ![Hazırlamayı etkinleştirme](media/quickstart-copy-activity-load-sql-pool/staging-linked-service.png)

1. İşlem hattını doğrulamak için araç çubuğunda **Doğrula** ' yı seçin. Sayfanın sağ tarafında işlem hattı doğrulama çıktısının sonucunu görürsünüz. 

## <a name="debug-and-publish-the-pipeline"></a>İşlem hattında hata ayıklama ve işlem hattını yayımlama

İşlem hattınızı yapılandırmayı tamamladıktan sonra, yapılarınızı yayımlamadan önce, her şeyin doğru olduğunu doğrulamak için bir hata ayıklama çalıştırması gerçekleştirebilirsiniz.

1. İşlem hattında hata ayıklamak için araç çubuğunda **Hata Ayıkla**'yı seçin. Pencerenin altındaki **Çıkış** sekmesinde işlem hattı çalıştırmasının durumu görüntülenir. 

   ![İşlem hattında hata ayıklama](media/quickstart-copy-activity-load-sql-pool/debugging-result.png)

1. İşlem hattı başarıyla çalıştırıldığında, üstteki araç çubuğunda **Tümünü Yayımla**' yı seçin. Bu eylem, oluşturduğunuz varlıkları (veri kümeleri ve işlem hatları) SYNAPSE Analytics hizmetinde yayımlar.
1. **Başarıyla yayımlandı** iletisini görene kadar bekleyin. Bildirim iletilerini görmek için sağ üst köşedeki zil düğmesini seçin. 


## <a name="trigger-and-monitor-the-pipeline"></a>İşlem hattını tetikleme ve izleme

Bu bölümde, önceki adımda yayınlanan işlem hattını el ile tetiklersiniz. 

1. Araç çubuğunda **tetikleyici Ekle** ' yi seçin ve sonra **Şimdi Tetikle**' yi seçin. İşlem **hattı çalıştırma** sayfasında **Tamam**' ı seçin.  
1. Sol kenar çubuğunda bulunan **izleyici** sekmesine gidin. El ile tetikleme tarafından tetiklenmiş bir işlem hattı çalıştırması görürsünüz. 
1. İşlem hattı çalıştırması başarıyla tamamlandığında, etkinlik çalıştırma ayrıntılarını görüntülemek veya işlem hattını yeniden çalıştırmak için işlem **hattı adı** sütununun altındaki bağlantıyı seçin. Bu örnekte yalnızca bir etkinlik bulunur, bu nedenle listede yalnızca bir giriş görürsünüz. 
1. Kopyalama işlemi hakkında daha fazla bilgi için **etkinlik adı** sütununun altındaki **Ayrıntılar** bağlantısını (eyegözlük simgesi) seçin. Kaynaktan havuza, veri işleme, karşılık gelen süre ile yürütme adımlarına ve kullanılan yapılandırmalara göre veri hacmi gibi ayrıntıları izleyebilirsiniz.

   ![Etkinlik ayrıntıları](media/quickstart-copy-activity-load-sql-pool/activity-details.png)

1. İşlem hattı çalıştırmaları görünümüne geri dönmek için üstteki **tüm işlem hattı çalıştırmaları** bağlantısını seçin. Listeyi yenilemek için **Yenile**’yi seçin.
1. Verilerinizin adanmış SQL havuzunda doğru yazıldığından emin olun.


## <a name="next-steps"></a>Sonraki adımlar

Azure SYNAPSE Analytics desteği hakkında bilgi edinmek için aşağıdaki makaleye ilerleyin:

> [!div class="nextstepaction"]
> İşlem [hattı ve Etkinlikler](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Bağlayıcıya genel bakış](../data-factory/connector-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Kopyalama etkinliği](../data-factory/copy-activity-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)