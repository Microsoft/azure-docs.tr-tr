---
title: Yeni ve güncelleştirilmiş dosyaları artımlı olarak kopyalamak için veri aracı
description: Bir Azure Data Factory oluşturun ve ardından Veri Kopyalama aracını kullanarak LastModifiedDate tabanlı yeni dosyaları artımlı olarak yükleyin.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 25e39d3fc9c56a282714bafb630fee65421fb5ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606676"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Veri Kopyalama aracını kullanarak, LastModifiedDate tabanlı yeni ve değiştirilmiş dosyaları artımlı olarak kopyalama

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu öğreticide, bir veri fabrikası oluşturmak için Azure portal kullanacaksınız. Daha sonra, Azure Blob depolama 'dan Azure Blob depolama alanına yalnızca yeni ve değiştirilen dosyaları artımlı olarak kopyalayan bir işlem hattı oluşturmak için Veri Kopyalama aracını kullanacaksınız. `LastModifiedDate`Hangi dosyaların kopyalanacağını belirleyen tarafından kullanılır.

Buradaki adımları tamamladıktan sonra Azure Data Factory, kaynak depodaki tüm dosyaları tarar, dosya filtresini ' a uygular `LastModifiedDate` ve yalnızca yeni veya son zamandan bu yana güncelleştirilmiş olan dosyaları hedef depoya kopyalar. Data Factory çok sayıda dosyayı taradığında, hala uzun süreleri beklediğinizi unutmayın. Dosya tarama zaman alıcı, ancak kopyalanmış veri miktarı azalır.

> [!NOTE]
> İlk kez Data Factory kullanıyorsanız bkz. [Azure Data Factory'ye giriş](introduction.md).

Bu öğreticide, şu görevleri tamamlayacaksınız:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Veri Kopyalama aracını kullanarak bir işlem hattı oluşturun.
> * İşlem hattı ve etkinlik çalıştırmalarını izleme.

## <a name="prerequisites"></a>Önkoşullar

* **Azure aboneliği**: Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* **Azure depolama hesabı**: kaynak ve havuz veri depoları için BLOB depolama kullanın. Azure depolama hesabınız yoksa [depolama hesabı oluşturma](../storage/common/storage-account-create.md)' daki yönergeleri izleyin.

## <a name="create-two-containers-in-blob-storage"></a>Blob depolamada iki kapsayıcı oluşturma

Aşağıdaki adımları tamamlayarak BLOB depolama alanınızı öğreticiye hazırlayın:

1. **Kaynak** adlı bir kapsayıcı oluşturun. Bu görevi gerçekleştirmek için [Azure Depolama Gezgini](https://storageexplorer.com/)gibi çeşitli araçları kullanabilirsiniz.

2. **Hedef** adlı bir kapsayıcı oluşturun.

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. Soldaki bölmede **Kaynak oluştur**'u seçin. **Tümleştirme**  >  **Data Factory** seçin:

   ![Data Factory seçin](./media/doc-common-process/new-azure-data-factory-menu.png)

2. **Yeni veri fabrikası** sayfasında **Ad** bölümüne **ADFTutorialDataFactory** girin.

   Veri fabrikanızın adı genel olarak benzersiz olmalıdır. Şu hata iletisini alabilirsiniz:

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Yinelenen ad için yeni veri fabrikası hata iletisi.":::

   Ad değeriyle ilgili bir hata iletisi alırsanız, veri fabrikası için farklı bir ad girin. Örneğin, _**adınız**_**ADFTutorialDataFactory** adını kullanın. Data Factory yapıtlarını adlandırma kuralları için bkz. [Data Factory adlandırma kuralları](naming-rules.md).
3. **Abonelik** altında yeni veri fabrikasını oluşturacağınız Azure aboneliğini seçin.
4. **Kaynak grubu** altında şu adımlardan birini uygulayın:

    * **Mevcut olanı kullan** ' ı seçin ve ardından listeden var olan bir kaynak grubunu seçin.

    * **Yeni oluştur** ' u seçin ve kaynak grubu için bir ad girin.
         
    Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/management/overview.md).

5. **Sürüm** bölümünde **V2**'yi seçin.
6. **Konum** bölümünde veri fabrikasının konumunu seçin. Listede yalnızca desteklenen konumlar görüntülenir. Veri fabrikanızın kullandığı veri depoları (örneğin, Azure depolama ve Azure SQL veritabanı) ve işlemler (örneğin, Azure HDInsight) başka konumlarda ve bölgelerde olabilir.
8. **Oluştur**’u seçin.
9. Data Factory oluşturulduktan sonra Data Factory giriş sayfası görüntülenir.
10. Azure Data Factory Kullanıcı arabirimini (UI) ayrı bir sekmede açmak için **yazar & izleyici** kutucuğunu seçin:

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Yazar & Izleyici kutucuğuna sahip Azure Data Factory ana sayfası.":::

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Veri Kopyalama aracını kullanarak işlem hattı oluşturma

1. **Haydi** başlayın sayfasında, veri kopyalama aracını açmak için **veri kopyalama** kutucuğunu seçin:

   ![Veri Kopyalama kutucuğu](./media/doc-common-process/get-started-page.png)

2. **Özellikler** sayfasında, aşağıdaki adımları uygulayın:

    a. **Görev adı** altında **Deltacopyfromblobpipeline** girin.

    b. **Görev temposunda veya görev zamanlaması** altında **zamanlamaya göre düzenli olarak çalıştır**' ı seçin.

    c. **Tetikleyici türü** altında, atlayan **pencere**' yi seçin.

    d. **Yinelenme** altına **15 dakika** girin.

    e. **İleri**’yi seçin.

    Data Factory, belirtilen görev adıyla bir işlem hattı oluşturur.

    ![Veri kopyalama özellikleri sayfası](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. **Kaynak veri deposu** sayfasında, şu adımları uygulayın:

    a. Bağlantı eklemek için  **Yeni bağlantı oluştur** ' u seçin.

    b. Galeriden **Azure Blob depolama** ' yı seçin ve ardından **devam**' ı seçin:

    ![Azure blog depolamayı seçin](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. **Yeni bağlı hizmet (Azure Blob depolama)** sayfasında **depolama hesabı adı** listesinden depolama hesabınızı seçin. Bağlantıyı test edin ve **Oluştur**' u seçin.

    d. Yeni bağlı hizmeti seçin ve ardından **İleri**' yi seçin:

   ![Yeni bağlı hizmeti seçin](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. **Girdi dosyasını veya klasörünü seçin** sayfasında aşağıdaki adımları uygulayın:

    a. **Kaynak** klasöre gözatıp seçin ve **ardından Seç ' i seçin.**

    ![Girdi dosyası veya klasörü seçin](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. **Dosya yükleme davranışı** altında **artımlı yük: LastModifiedDate** öğesini seçin.

    c. **İkili kopya** ' yı seçin ve ardından **İleri**' yi seçin:

     ![Giriş dosyası veya klasör sayfasını seçin](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. **Hedef veri deposu** sayfasında, oluşturduğunuz **AzureBlobStorage** hizmetini seçin. Bu, kaynak veri deposuyla aynı depolama hesabıdır. Sonra **İleri**’yi seçin.

6. **Çıktı dosyasını veya klasörünü seçin** sayfasında aşağıdaki adımları uygulayın:

    a. **Hedef** klasöre gözatıp seçin ve **ardından Seç ' i seçin:**

    ![Çıkış dosyasını veya klasör sayfasını seçin](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. **İleri**’yi seçin.

7. **Ayarlar** sayfasında **İleri**’yi seçin.

8. **Özet** sayfasında, ayarları gözden geçirin ve ardından **İleri**' yi seçin.

    ![Özet sayfası](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. **Dağıtım** sayfasında, işlem hattını (görev) izlemek için **İzleyici**’yi seçin.

    ![Dağıtım sayfası](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Soldaki **İzleyici** sekmesinin otomatik olarak seçildiğine dikkat edin. Uygulama **izleyici** sekmesine geçer. İşlem hattının durumunu görürsünüz. Listeyi yenilemek için **Yenile**’yi seçin. Etkinlik çalıştırma ayrıntılarını görüntülemek veya işlem hattını yeniden çalıştırmak için işlem **hattı adı** altındaki bağlantıyı seçin.

    ![Listeyi yenile ve etkinlik çalıştırma ayrıntılarını görüntüle](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs-1.png)

11. İşlem hattında yalnızca bir etkinlik (kopyalama etkinliği) bulunur, bu nedenle yalnızca bir giriş görürsünüz. Kopyalama işlemiyle ilgili ayrıntılar için **etkınlık adı** sütunundaki **Ayrıntılar** bağlantısını (gözlük simgesi) seçin. Özellikler hakkında daha fazla bilgi için bkz. [kopyalama etkinliğine genel bakış](copy-activity-overview.md).

    ![İşlem hattındaki kopyalama etkinliği](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    BLOB depolama hesabınızda kaynak kapsayıcısında dosya olmadığından, hesaptaki hedef kapsayıcıya kopyalanan herhangi bir dosya görmezsiniz:

    ![Kaynak kapsayıcısında veya hedef kapsayıcıda dosya yok](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Boş bir metin dosyası oluşturun ve **file1.txt** adlandırın. Bu metin dosyasını Depolama hesabınızdaki kaynak kapsayıcıya yükleyin. [Azure Depolama Gezgini](https://storageexplorer.com/)gibi bu görevleri gerçekleştirmek için çeşitli araçları kullanabilirsiniz.

    ![file1.txt oluşturun ve kaynak kapsayıcıya yükleyin](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. İşlem hattı **çalıştırmaları** görünümüne geri dönmek için **tüm işlem hattı çalıştırmaları**' nı seçin ve aynı işlem hattının yeniden otomatik olarak tetiklenmesi için bekleyin.  

14. İkinci işlem hattı çalıştırıldığında, etkinliğin çalışma ayrıntılarını gözden geçirmek için yukarıda bahsedilen adımların aynısını izleyin.  

    Kaynak kapsayıcısından BLOB depolama hesabınızın hedef kapsayıcısına bir dosya (file1.txt) kopyalandığını görürsünüz:

    ![file1.txt, kaynak kapsayıcıdan hedef kapsayıcıya kopyalanmış](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Başka bir boş metin dosyası oluşturun ve **file2.txt** adlandırın. Bu metin dosyasını Blob Depolama hesabınızdaki kaynak kapsayıcıya yükleyin.

16. İkinci metin dosyası için 13. ve 14. adımları yineleyin. Bu işlem hattı çalıştırıldığında, kaynak kapsayıcıdan yalnızca yeni dosyanın (file2.txt) depolama hesabınızın hedef kapsayıcısına kopyalandığını görürsünüz.  

    Ayrıca, dosyaları taramak için [Azure Depolama Gezgini](https://storageexplorer.com/) kullanarak yalnızca bir dosyanın kopyalandığından emin olabilirsiniz:

    ![Azure Depolama Gezgini kullanarak dosyaları tarayın](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Sonraki adımlar
Azure 'da Apache Spark kümesi kullanarak verileri nasıl dönüştürebileceğinizi öğrenmek için aşağıdaki Öğreticiye gidin:

> [!div class="nextstepaction"]
>[Apache Spark kümesi kullanarak buluttaki verileri dönüştürme](tutorial-transform-data-spark-portal.md)
