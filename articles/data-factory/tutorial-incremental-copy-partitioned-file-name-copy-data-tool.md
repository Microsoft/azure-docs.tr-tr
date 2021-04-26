---
title: Yeni dosyaları, bölümlenmiş dosya adına göre artımlı olarak Kopyala
description: Bir Azure Data Factory oluşturun ve ardından yeni dosyaları yalnızca bölümlenmiş dosya adına göre artımlı olarak yüklemek için Veri Kopyalama aracını kullanın.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 26703920fda8746badf085f96686f922ee250513
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606642"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Veri Kopyalama aracını kullanarak yeni dosyaları saat bölümlenmiş dosya adına göre artımlı olarak kopyalama

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu öğreticide, Azure portalını kullanarak bir veri fabrikası oluşturursunuz. Ardından, yeni dosyaları Azure Blob depolama alanından Azure Blob depolama alanına göre bölümlenmiş dosya adına göre artımlı olarak kopyalayan bir işlem hattı oluşturmak için Veri Kopyalama aracını kullanırsınız.

> [!NOTE]
> Azure Data Factory kullanmaya yeni başlıyorsanız bkz. [Azure Data Factory'ye giriş](introduction.md).

Bu öğreticide, aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Veri Kopyalama aracını kullanarak bir işlem hattı oluşturun.
> * İşlem hattı ve etkinlik çalıştırmalarını izleme.

## <a name="prerequisites"></a>Önkoşullar

* **Azure aboneliği**: Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* **Azure depolama hesabı**: BLOB depolama alanını _kaynak_  ve _Havuz_ veri deposu olarak kullanın. Azure depolama hesabınız yoksa [Depolama hesabı oluşturma](../storage/common/storage-account-create.md) bölümündeki yönergelere bakın.

### <a name="create-two-containers-in-blob-storage"></a>Blob depolamada iki kapsayıcı oluşturma

Bu adımları uygulayarak BLOB depolama alanınızı öğreticiye hazırlayın.

1. **Kaynak** adlı bir kapsayıcı oluşturun.  Kapsayıcıda **2020/03/17/03** olarak bir klasör yolu oluşturun. Boş bir metin dosyası oluşturun ve **file1.txt** olarak adlandırın. Depolama hesabınızda file1.txt **kaynak/2020/03/17/03** klasörüne yükleyin.  Bu görevleri [Azure Depolama Gezgini](https://storageexplorer.com/) gibi çeşitli araçlar kullanarak gerçekleştirebilirsiniz.

    ![dosyaları karşıya yükle](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > Lütfen klasör adını UTC saat ile değiştirin.  Örneğin, geçerli UTC saati 17 Mart 2020 ' de 3:38 ise kaynak/ **2020/03/17/03/** kaynak/{yıl}/{ay}/{gün}/ **{saat}/**} olarak klasör yolunu oluşturabilirsiniz.

2. **Hedef** adlı bir kapsayıcı oluşturun. Bu görevleri [Azure Depolama Gezgini](https://storageexplorer.com/) gibi çeşitli araçlar kullanarak gerçekleştirebilirsiniz.

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. Sol menüde, **kaynak**  >  **tümleştirmesi**  >  **Data Factory** oluştur ' u seçin:

   ![“Yeni” bölmesinde Data Factory seçimi](./media/doc-common-process/new-azure-data-factory-menu.png)

2. **Yeni veri fabrikası** sayfasında **Ad** bölümüne **ADFTutorialDataFactory** girin.

    Veri fabrikanızın adı _genel olarak benzersiz_ olmalıdır. Aşağıdaki hata iletisini alabilirsiniz:

   :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Yinelenen ad için yeni veri fabrikası hata iletisi.":::

   Ad değeriyle ilgili bir hata iletisi alırsanız, veri fabrikası için farklı bir ad girin. Örneğin, _**adınız**_**ADFTutorialDataFactory** adını kullanın. Data Factory yapıtlarını adlandırma kuralları için bkz. [Data Factory adlandırma kuralları](naming-rules.md).
3. Yeni veri fabrikasının oluşturulacağı Azure **aboneliğini** seçin.
4. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:

    a. **Var olanı kullan**’ı seçin ve ardından açılır listeden var olan bir kaynak grubu belirleyin.

    b. **Yeni oluştur**’u seçin ve bir kaynak grubunun adını girin. 
         
    Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/management/overview.md).

5. **Sürüm** bölümünde **V2**'yi seçin.
6. **Konum** bölümünde veri fabrikasının konumunu seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Veri fabrikanız tarafından kullanılan veri depoları (örneğin, Azure Depolama ve SQL Veritabanı) ve işlemler (örneğin, Azure HDInsight) başka konumlarda ve bölgelerde olabilir.
7. **Oluştur**’u seçin.
8. Oluşturma işlemi tamamlandıktan sonra **Data Factory** giriş sayfası görüntülenir.
9. Azure Data Factory kullanıcı arabirimini (UI) ayrı bir sekmede açmak için **Yazar ve İzleyici** kutucuğunu seçin.

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Yazar & Izleyici kutucuğuna sahip Azure Data Factory ana sayfası.":::

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Veri Kopyalama aracını kullanarak işlem hattı oluşturma

1. **Haydi** başlayın sayfasında, veri kopyalama aracını başlatmak için **veri kopyalama** başlığını seçin.

   ![Veri Kopyalama aracının kutucuğu](./media/doc-common-process/get-started-page.png)

2. **Özellikler** sayfasında, aşağıdaki adımları uygulayın:

    a. **Görev adı** altında **Deltacopyfromblobpipeline** girin.

    b. **Görev temposunda veya görev zamanlaması** altında **zamanlamaya göre düzenli olarak çalıştır**' ı seçin.

    c. **Tetikleyici türü** altında, atlayan **pencere**' yi seçin.

    d. **Yinelenme** bölümünde **1 saat** girin.

    e. **İleri**’yi seçin.

    Data Factory kullanıcı arabirimi, belirtilen görev adına sahip bir işlem hattı oluşturur.

    ![Özellikler sayfası](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. **Kaynak veri deposu** sayfasında aşağıdaki adımları tamamlayın:

    a. Bağlantı eklemek için **+ Yeni bağlantı oluştur** ' a tıklayın
    
    b. Galeriden Azure Blob Depolama ' yı seçin ve ardından devam ' ı seçin.
    
    c. **Yeni bağlı hizmet (Azure Blob depolama)** sayfasında, bağlı hizmet için bir ad girin. Azure aboneliğinizi seçin ve **depolama hesabı adı** listesinden depolama hesabınızı seçin. Bağlantıyı test edin ve **Oluştur**' u seçin.

    ![Kaynak veri deposu sayfası](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)

    d. **Kaynak veri deposu** sayfasında yeni oluşturulan bağlı hizmeti seçin ve ardından **İleri**' ye tıklayın.

4. **Girdi dosyasını veya klasörünü seçin** sayfasında aşağıdaki adımları uygulayın:

    a. **Kaynak** kapsayıcısını bulup seçin ve ardından Seç ' i **seçin.**

    ![Ekran görüntüsü girdi dosyası veya klasörü seç iletişim kutusunu gösterir.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)

    b. **Dosya yükleme davranışı** altında **artımlı yükleme: zaman bölümlenmiş klasör/dosya adları**' nı seçin.

    c. Dinamik klasör yolunu **kaynak/{year}/{month}/{Day}/{Hour}/** olarak yazın ve aşağıdaki ekran görüntüsünde gösterildiği gibi biçimi değiştirin. **İkili kopyayı** denetleyin ve **İleri**' ye tıklayın.

    ![Ekran görüntüsü seçili bir klasör ile giriş dosyası veya klasör seç iletişim kutusunu gösterir.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     

5. **Hedef veri deposu** sayfasında, veri kaynağı deposuyla aynı depolama hesabı olan **AzureBlobStorage** öğesini seçin ve ardından **İleri**' ye tıklayın.

    ![Hedef veri deposu sayfası](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png)
6. **Çıktı dosyasını veya klasörünü seçin** sayfasında aşağıdaki adımları uygulayın:

    a. **Hedef** klasöre gidip seçin ve ardından **Seç**' e tıklayın.

    b. Dinamik klasör yolunu **hedef/{Year}/{ay}/{Day}/{Hour}/** olarak yazın ve biçimi şöyle değiştirin:

    ![Ekran görüntüsü çıkış dosyası veya klasörü seç iletişim kutusunu gösterir.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/output-file-name.png)

    c. **İleri**’ye tıklayın.

    ![Ekran görüntüsü, Ileri seçiliyken çıkış dosyası veya klasörü seç iletişim kutusunu gösterir.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)
7. **Ayarlar** sayfasında **İleri**’yi seçin.

8. **Özet** sayfasında ayarları gözden geçirin ve **İleri**’yi seçin.

    ![Özet sayfası](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

9. **Dağıtım** sayfasında, işlem hattını (görev) izlemek için **İzleyici**’yi seçin.
    ![Dağıtım sayfası](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

10. Soldaki **İzleyici** sekmesinin otomatik olarak seçildiğine dikkat edin.  Otomatik olarak tetiklendiğinde işlem hattı çalıştırmasını beklemeniz gerekir (bir saatten sonra). Çalıştığında, etkinlik çalıştırma ayrıntılarını görüntülemek veya işlem hattını yeniden çalıştırmak için **Deltacopyfromblobpipeline** işlem hattı adı bağlantısına tıklayın. Listeyi yenilemek için **Yenile**’yi seçin.

    ![Ekran görüntüsü, işlem hattı çalıştırmaları bölmesini gösterir.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs-1.png)
11. İşlem hattında yalnızca bir etkinlik (kopyalama etkinliği) olduğundan tek bir girdi görürsünüz. **Kaynak** ve **hedef** sütunlarının sütun genişliğini ayarla (gerekirse) daha fazla ayrıntı görüntülemek için kaynak dosyayı (file1.txt) *kaynak/2020/03/17/03/* ile *Destination/2020/03/17/* 03/ile aynı dosya adına kopyalanmış olduğunu görebilirsiniz. 

    ![Ekran görüntüsü, işlem hattı çalıştırma ayrıntılarını gösterir.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    Ayrıca, Azure Depolama Gezgini (dosyaları taramak için) öğesini kullanarak da aynı şekilde doğrulayabilirsiniz https://storageexplorer.com/) .

    ![Ekran görüntüsü, hedef için işlem hattı çalıştırma ayrıntılarını gösterir.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)

12. **file2.txt** yeni adla başka bir boş metin dosyası oluşturun. file2.txt dosyasını Depolama hesabınızdaki **kaynak/2020/03/17/04** klasörüne yükleyin. Bu görevleri [Azure Depolama Gezgini](https://storageexplorer.com/) gibi çeşitli araçlar kullanarak gerçekleştirebilirsiniz.

    > [!NOTE]
    > Yeni bir klasör yolunun oluşturulması gerektiğini fark edebilirsiniz. Lütfen klasör adını UTC saat ile değiştirin.  Örneğin, geçerli UTC saati 4:20 ile 11.17., 2020 ise, **{year}/{month}/{Day}/{Hour}/** kuralına göre **kaynak/2020/03/17/04** /klasör yolunu oluşturabilirsiniz.

13. İşlem **hattı çalıştırmaları** görünümüne geri dönmek Için, tüm işlem hattı **çalıştırmaları**' nı seçin ve aynı işlem hattının bir saatten sonra otomatik olarak yeniden tetiklenmesi için bekleyin.  

    ![Ekran görüntüsü, bu sayfaya dönmek için tüm işlem hattı çalıştırmaları bağlantısını gösterir.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. İkinci işlem hattı çalıştırıldığında, yeni **Deltacopyfromblobpipeline** bağlantısını seçin ve ayrıntıları gözden geçirmek için aynısını yapın. Kaynak dosyayı (file2.txt)  **kaynak/2020/03/17/04/**  ile **Destination/2020/03/17/04** /ile aynı dosya adına kopyalanmış olduğunu görürsünüz. Ayrıca, Azure Depolama Gezgini ( https://storageexplorer.com/) **hedef** kapsayıcıdaki dosyaları taramak için) öğesini kullanarak da doğrulayabilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar
Azure üzerinde bir Spark kümesi kullanarak veri dönüştürme hakkında bilgi edinmek için aşağıdaki öğreticiye geçin:

> [!div class="nextstepaction"]
>[Bulutta Spark kümesi kullanarak verileri dönüştürme](tutorial-transform-data-spark-portal.md)
