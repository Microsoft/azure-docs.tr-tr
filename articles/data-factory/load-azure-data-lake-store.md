---
title: Azure Data Factory kullanarak Azure Data Lake Storage 1. verileri yükleme
description: Verileri Azure Data Lake Storage 1. kopyalamak için Azure Data Factory kullanma
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: b8e5841e6869bd6f19b07bf71434de809cdcb74f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672727"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure Data Lake Storage 1. verileri yükleme

[Azure Data Lake Storage 1.](../data-lake-store/data-lake-store-overview.md) (daha önce Azure Data Lake Store olarak bilinir), büyük veri analizi iş yükleri için kurumsal çapta bir hiper ölçek deposudur. Data Lake Storage 1., herhangi bir boyut, tür ve Alım hızında veri yakalamanızı sağlar. Veriler, işletimsel ve keşif analizlerinin tek bir yerinde yakalanır.

Azure Data Factory, tam olarak yönetilen bulut tabanlı bir veri tümleştirme hizmetidir. Bu hizmeti kullanarak Gölü verileri mevcut sisteminizdeki verilerle doldurabilir ve analiz çözümlerinizi oluştururken zamandan tasarruf edebilirsiniz.

Azure Data Factory, verileri Data Lake Storage 1. yüklemek için aşağıdaki avantajları sunmaktadır:

* **Kolayca ayarlanabilir**: komut dosyası gerekmeden sezgisel 5 adımlı bir sihirbaz.
* **Zengin veri deposu desteği**: zengin bir şirket içi ve bulut tabanlı veri deposu kümesi için yerleşik destek. Ayrıntılı bir liste için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats)tablosuna bakın.
* **Güvenli ve uyumlu**: veriler HTTPS veya ExpressRoute üzerinden aktarılır. Küresel hizmet varlığı, verilerinizin hiçbir şekilde coğrafi sınır bırakmamasını sağlar.
* **Yüksek performans**: en fazla 1 GB/sn veri yükleme hızı Data Lake Storage 1. Ayrıntılar için bkz. [etkinlik performansını kopyalama](copy-activity-performance.md).

Bu makalede, Data Factory Veri Kopyalama aracının, _Amazon S3 'ten Data Lake Storage 1. 'a veri yüklemek_için nasıl kullanılacağı gösterilmektedir. Diğer veri deposu türlerinden veri kopyalamak için benzer adımları izleyebilirsiniz.

> [!NOTE]
> Daha fazla bilgi için bkz. [Azure Data Factory kullanarak Data Lake Storage 1. veri kopyalama](connector-azure-data-lake-store.md).

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği: bir Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* Data Lake Storage 1. hesabı: bir Data Lake Storage 1. hesabınız yoksa [Data Lake Storage 1. hesabı oluşturma](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account)bölümündeki yönergelere bakın.
* Amazon S3: Bu makalede, Amazon S3 öğesinden verilerin nasıl kopyalanacağı gösterilmektedir. Benzer adımları izleyerek başka veri depoları da kullanabilirsiniz.

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. Sol menüde **kaynak oluştur** > **analiz** > **Data Factory**' yı seçin:
   
   ![“Yeni” bölmesinde Data Factory seçimi](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. **Yeni Veri Fabrikası** sayfasında, aşağıdaki görüntüde gösterilen alanlar için değerler girin: 
      
   ![Yeni veri fabrikası sayfası](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Ad**: Azure Data Factory 'niz için genel olarak benzersiz bir ad girin. "Data Factory Name \"LoadADLSG1Demo\" kullanılamıyor" hatası alırsanız, Veri Fabrikası için farklı bir ad girin. Örneğin _**, Name**_ **ADFTutorialDataFactory**adını kullanabilirsiniz. Data Factory 'yi yeniden oluşturmayı deneyin. Data Factory yapıtlarını adlandırma kuralları için bkz. [Data Factory adlandırma kuralları](naming-rules.md).
    * **Abonelik**: veri fabrikasının oluşturulacağı Azure aboneliğinizi seçin. 
    * **Kaynak grubu**: açılan listeden var olan bir kaynak grubunu seçin veya **Yeni oluştur** seçeneğini belirleyin ve bir kaynak grubunun adını girin. Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/resource-group-overview.md).  
    * **Sürüm**: **v2**'yi seçin.
    * **Konum**: veri fabrikasının konumunu seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Data Factory tarafından kullanılan veri depoları diğer konumlarda ve bölgelerde olabilir. Bu veri depoları Azure Data Lake Storage 1., Azure depolama, Azure SQL veritabanı vb. içerir.

3. **Oluştur**'u seçin.
4. Oluşturma işlemi tamamlandıktan sonra, veri fabrikanıza gidin. Aşağıdaki görüntüde gösterildiği gibi **Data Factory** giriş sayfasını görürsünüz: 
   
   ![Data factory giriş sayfası](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Veri tümleştirme uygulamasını ayrı bir sekmede başlatmak için **yazar & İzleyici** kutucuğunu seçin.

## <a name="load-data-into-data-lake-storage-gen1"></a>Data Lake Storage 1. verileri yükleme

1. **Başlarken** sayfasında, veri kopyalama aracını başlatmak için **veri kopyalama** kutucuğunu seçin: 

   ![Veri Kopyalama aracının kutucuğu](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. **Özellikler** sayfasında, **görev adı** alanı Için **CopyFromAmazonS3ToADLS** belirtin ve **İleri**' yi seçin:

    ![Özellikler sayfası](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. **Kaynak veri deposu** sayfasında **+ Yeni bağlantı oluştur**' a tıklayın:

    ![Kaynak veri deposu sayfası](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    **Amazon S3**' i seçin ve **devam** ' ı seçin.
    
    ![Kaynak veri deposu S3 sayfası](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. **Amazon S3 bağlantısını belirtin** sayfasında aşağıdaki adımları uygulayın: 
   1. **Erişim anahtarı kimlik** değerini belirtin.
   2. Gizli dizi **erişim anahtarı** değerini belirtin.
   3. **Son**’u seçin.
   
      ![Amazon S3 hesabını belirtin](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. Yeni bir bağlantı göreceksiniz. **İleri**’yi seçin.
   
   ![Amazon S3 hesabını belirtin](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. **Girdi dosyasını veya klasörünü seçin** sayfasında, üzerine kopyalamak istediğiniz klasöre ve dosyaya gidin. Klasörü/dosyayı seçin **, Seç '** i seçin ve ardından **İleri**' yi seçin:

    ![Girdi dosyasını veya klasörünü seçin](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. **Dosyaları yinelemeli Kopyala** ve **ikili kopya** (dosya olarak Kopyala) seçeneklerini belirleyerek kopyalama davranışını seçin. **İleri ' yi**seçin:

    ![Çıkış klasörünü belirtin](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. **Hedef veri deposu** sayfasında **+ Yeni bağlantı oluştur**' a ve ardından **Azure Data Lake Storage 1.** ' ı seçip **devam**' ı seçin:

    ![Hedef veri deposu sayfası](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. **Yeni bağlı hizmet (Azure Data Lake Storage 1.)** sayfasında, aşağıdaki adımları uygulayın: 

   1. **Data Lake Store hesap adı**için Data Lake Storage 1. hesabınızı seçin.
   2. **Kiracıyı**belirtip son ' u seçin.
   3. **İleri**’yi seçin.
   
   > [!IMPORTANT]
   > Bu kılavuzda, Data Lake Storage 1. hesabınızın kimliğini doğrulamak için Azure kaynakları için yönetilen bir kimlik kullanırsınız. [Bu yönergeleri](connector-azure-data-lake-store.md#managed-identity)IZLEYEREK Data Lake Storage 1. MSI 'ye uygun izinleri verdiğinizden emin olun.
   
   ![Data Lake Storage 1. hesabı belirtin](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. **Çıktı dosyasını veya klasörünü seçin** sayfasında, çıkış klasörü adı olarak **Copyfroms3** girin ve **İleri**' yi seçin: 

    ![Çıkış klasörünü belirtin](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. **Ayarlar** sayfasında, **İleri**' yi seçin:

    ![Ayarlar sayfası](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. **Özet** sayfasında, ayarları gözden geçirin ve **İleri**' yi seçin:

    ![Özet sayfası](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. **Dağıtım sayfasında**, işlem hattını (görev) Izlemek için **izleyici** ' yi seçin:

    ![Dağıtım sayfası](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Soldaki **İzleyici** sekmesinin otomatik olarak seçildiğine dikkat edin. Eylemler sütunu, etkinlik çalıştırma ayrıntılarını görüntüleme ve **işlem** hattını yeniden çalıştırma bağlantılarını içerir:

    ![İşlem hattı çalıştırmalarını izleme](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Işlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görüntülemek için **Eylemler** sütunundaki **etkinlik çalıştırmalarını görüntüle** bağlantısını seçin. İşlem hattında yalnızca bir etkinlik (kopyalama etkinliği) olduğundan tek bir girdi görürsünüz. İşlem hattı çalıştırmaları görünümüne geri dönmek için üstteki işlem **hatları** bağlantısını seçin. Listeyi yenilemek için **Yenile**’yi seçin. 

    ![Etkinlik çalıştırmalarını izleme](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Her kopyalama etkinliğinin yürütme ayrıntılarını izlemek için, etkinlik izleme görünümündeki **Eylemler** altında **Ayrıntılar** bağlantısını seçin. Kaynaktan havuza, veri işleme, karşılık gelen süre ile yürütme adımlarına ve kullanılan yapılandırmalara kadar olan veri hacmi gibi ayrıntıları izleyebilirsiniz:

    ![Etkinlik çalıştırma ayrıntılarını izle](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Verilerin Data Lake Storage 1. hesabınıza kopyalandığını doğrulayın: 

    ![Çıktıyı Data Lake Storage 1. doğrulama](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Sonraki adımlar

Data Lake Storage 1. desteği hakkında bilgi edinmek için aşağıdaki makaleye ilerleyin: 

> [!div class="nextstepaction"]
>[Azure Data Lake Storage 1. Bağlayıcısı](connector-azure-data-lake-store.md)
