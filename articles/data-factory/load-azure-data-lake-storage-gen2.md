---
title: Azure Data Lake Storage 2. Nesil'e verileri yükleme
description: Verileri Azure Data Lake Storage 2. kopyalamak için Azure Data Factory kullanma
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 075778e2650d3b1f67447078eb6cb130849bbb35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593790"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Azure Data Factory ile Azure Data Lake Storage 2. verileri yükleme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage 2., [Azure Blob depolamada](../storage/blobs/storage-blobs-introduction.md)yerleşik olan, büyük veri analizi için adanmış bir özellik kümesidir. Hem dosya sistemi hem de nesne depolama paradigmalarına kullanarak verilerinize arabirim oluşturmanızı sağlar.

Azure Data Factory (ADF), tam olarak yönetilen bulut tabanlı bir veri tümleştirme hizmetidir. Bu hizmeti kullanarak Gölü zengin şirket içi ve bulut tabanlı veri mağazalarındaki verilerle doldurabilir ve analiz çözümlerinizi derlerken zamandan tasarruf edebilirsiniz. Desteklenen bağlayıcıların ayrıntılı bir listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats)tablosuna bakın.

Azure Data Factory, genişleme, yönetilen bir veri taşıma çözümü sağlar. ADF 'nin ölçek genişletme mimarisi nedeniyle, verileri yüksek bir aktarım hızına alabilir. Ayrıntılar için bkz. [etkinlik performansını kopyalama](copy-activity-performance.md).

Bu makalede _Amazon Web Services S3 hizmetinden_ _Azure Data Lake Storage 2._'a veri yüklemek için Data Factory veri kopyalama aracının nasıl kullanılacağı gösterilmektedir. Diğer veri deposu türlerinden veri kopyalamak için benzer adımları izleyebilirsiniz.

>[!TIP]
>Azure Data Lake Storage 1. verileri Gen2 'e kopyalamak için [Bu özel izlenecek yolu](load-azure-data-lake-storage-gen2-from-gen1.md)inceleyin.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği: bir Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* Data Lake Storage 2. etkin Azure depolama hesabı: depolama hesabınız yoksa [bir hesap oluşturun](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* Veri içeren bir S3 demetini olan AWS hesabı: Bu makalede, Amazon S3 'ten nasıl veri kopyalanacağı gösterilmektedir. Benzer adımları izleyerek başka veri depoları da kullanabilirsiniz.

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. Sol menüde, **kaynak**  >  **tümleştirmesi**  >  **Data Factory** oluştur ' u seçin:
   
   ![“Yeni” bölmesinde Data Factory seçimi](./media/doc-common-process/new-azure-data-factory-menu.png)

2. **Yeni Veri Fabrikası** sayfasında, aşağıdaki alanlar için değerler girin:
 
    * **Ad**: Azure Data Factory 'niz için genel olarak benzersiz bir ad girin. "Data Factory Name *Yourdatafactoryname* kullanılamıyor" hatasını alırsanız Veri Fabrikası için farklı bir ad girin. Örneğin _**, Name**_**ADFTutorialDataFactory** adını kullanabilirsiniz. Data Factory 'yi yeniden oluşturmayı deneyin. Data Factory yapıtlarını adlandırma kuralları için bkz. [Data Factory adlandırma kuralları](naming-rules.md).
    * **Abonelik**: veri fabrikasının oluşturulacağı Azure aboneliğinizi seçin. 
    * **Kaynak grubu**: açılan listeden var olan bir kaynak grubunu seçin veya **Yeni oluştur** seçeneğini belirleyin ve bir kaynak grubunun adını girin. Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/management/overview.md).  
    * **Sürüm**: **V2**'yi seçin.
    * **Konum**: veri fabrikasının konumunu seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Data Factory tarafından kullanılan veri depoları diğer konumlarda ve bölgelerde olabilir. 

3. **Oluştur**’u seçin.

4. Oluşturma işlemi tamamlandıktan sonra, veri fabrikanıza gidin. Aşağıdaki görüntüde gösterildiği gibi **Data Factory** giriş sayfasını görürsünüz: 
   
   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Yazar & Izleyici kutucuğuna sahip Azure Data Factory ana sayfası.":::

   Veri Tümleştirme Uygulamasını ayrı bir sekmede açmak için **Oluştur ve İzle** kutucuğunu seçin.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. Nesil'e verileri yükleme

1. **Başlayalım** sayfasında, Veri Kopyalama aracını açmak için **Veri Kopyala** kutucuğunu seçin.

2. **Özellikler** sayfasında, **görev adı** alanı Için **CopyFromAmazonS3ToADLS** belirtin ve **İleri**' yi seçin.

    ![Özellikler sayfası](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. **Kaynak veri deposu** sayfasında **+ Yeni bağlantı oluştur**'a tıklayın. Bağlayıcı galerisinden **Amazon S3** ' i seçin ve **devam**' ı seçin.
    
    ![Kaynak veri deposu S3 sayfası](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. **Yeni bağlı hizmet (Amazon S3)** sayfasında, aşağıdaki adımları uygulayın:

   1. **Erişim anahtarı kimlik** değerini belirtin.
   2. Gizli dizi **erişim anahtarı** değerini belirtin.
   3. Ayarları doğrulamak için **Bağlantıyı Sına** ' ya tıklayın ve ardından **Oluştur**' u seçin.

      ![Amazon S3 hesabını belirtin](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
   4. Yeni bir AmazonS3 bağlantısının oluşturulduğunu görürsünüz. **İleri**’yi seçin. 

5. **Giriş dosyasını veya klasörünü seçin** sayfasında, üzerine kopyalamak istediğiniz klasöre ve dosyaya göz atın. Klasör/dosya ' yı seçin ve ardından **Seç**' i seçin.

    ![Girdi dosyasını veya klasörünü seçin](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. **Yinelemeli** ve **ikili kopya** seçeneklerini denetleyerek kopyalama davranışını belirtin. **İleri**’yi seçin.

    ![Ekran görüntüsü, Ikili kopya seçebileceğiniz ve yinelemeli olarak bir giriş dosyası veya klasör seçin öğesini gösterir.](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. **Hedef veri deposu** sayfasında **+ Yeni bağlantı oluştur**' a ve ardından **Azure Data Lake Storage 2.**' yi seçin ve **devam**' ı seçin.

    ![Hedef veri deposu sayfası](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. **Yeni bağlı hizmet (Azure Data Lake Storage 2.)** sayfasında, aşağıdaki adımları uygulayın:

   1. "Depolama hesabı adı" açılan listesinden Data Lake Storage 2. Nesil özellikli hesabınızı seçin.
   2. Bağlantıyı oluşturmak için **Oluştur** ' u seçin. Sonra **İleri**’yi seçin.   

        ![Azure Data Lake Storage 2. hesabı belirtin](./media/load-azure-data-lake-storage-gen2/specify-azure-data-lake-storage.png)

9. **Çıktı dosyasını veya klasörünü seçin** sayfasında, çıkış klasörü adı olarak **Copyfroms3** girin ve **İleri**' yi seçin. ADF, varsa kopyalama sırasında karşılık gelen ADLS 2. dosya sistemi ve alt klasörleri oluşturur.

    ![Ekran görüntüsü girdiğiniz klasör yolunu gösterir.](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. Varsayılan ayarları kullanmak için **Ayarlar** sayfasında **İleri**'yi seçin.

    ![Ayarlar sayfası](./media/load-azure-data-lake-storage-gen2/copy-settings.png)

11. **Özet** sayfasında, ayarları gözden geçirin ve **İleri**' yi seçin.

    ![Özet sayfası](./media/load-azure-data-lake-storage-gen2/copy-summary.png)

12. **Dağıtım** sayfasında, işlem hattını (görev) izlemek için **İzleyici**’yi seçin. 
 
13. İşlem hattı çalıştırması başarıyla tamamlandığında, el ile tetikleyici tarafından tetiklenen bir işlem hattı çalıştırması görürsünüz. Etkinlik ayrıntılarını görüntülemek ve işlem hattını yeniden çalıştırmak için işlem **hattı adı** sütununun altındaki bağlantıları kullanabilirsiniz.

    ![İşlem hattı çalıştırmalarını izleme](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. İşlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görmek için, işlem hattı adı sütununun altındaki **CopyFromAmazonS3ToADLS** bağlantısını seçin. Kopyalama işlemi hakkında daha fazla bilgi için etkınlık adı sütununun altındaki **Ayrıntılar** bağlantısını (eyegözlük simgesi) seçin. Kaynaktan havuza, veri işleme, karşılık gelen süre ile yürütme adımlarına ve kullanılan yapılandırmaya sahip verilerin hacmi gibi ayrıntıları izleyebilirsiniz.
 
    ![Etkinlik çalıştırmalarını izleme](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)
    
    ![Etkinlik çalıştırma ayrıntılarını izle](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

15. Görünümü yenilemek için Yenile’yi seçin. İşlem hattı çalıştırmaları görünümüne dönmek için üstteki **tüm işlem hattı çalıştırmalarını** seçin.

16. Verilerin Data Lake Storage 2. hesabınıza kopyalandığını doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

* [Kopyalama etkinliğine genel bakış](copy-activity-overview.md)
* [Azure Data Lake Storage 2. Nesil bağlayıcısı](connector-azure-data-lake-storage.md)
