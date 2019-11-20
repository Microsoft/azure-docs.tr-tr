---
title: Veri Kopyalama aracını kullanarak, LastModifiedDate tabanlı yeni ve değiştirilmiş dosyaları artımlı olarak kopyalama
description: Bir Azure Data Factory oluşturun ve ardından Veri Kopyalama aracını kullanarak LastModifiedDate tabanlı yeni dosyaları artımlı olarak yükleyin.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/24/2019
ms.openlocfilehash: 09a9fa4515913470c86bbafe293add007a3117ea
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683469"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Veri Kopyalama aracını kullanarak, LastModifiedDate tabanlı yeni ve değiştirilmiş dosyaları artımlı olarak kopyalama

Bu öğreticide, bir veri fabrikası oluşturmak için Azure portal kullanacaksınız. Daha sonra, yalnızca yeni ve değiştirilmiş dosyaları Azure Blob Storage 'dan Azure Blob depolama 'ya **LastModifiedDate** göre artımlı olarak kopyalayan bir işlem hattı oluşturmak için veri kopyalama aracını kullanacaksınız.

Bu şekilde, ADF tüm dosyaları kaynak deposundan tarar, dosya filtresini LastModifiedDate göre uygular ve yeni ve güncelleştirilmiş dosyayı yalnızca hedef depoya son kez bu yana kopyalar.  ADF 'nin çok büyük miktarlarda dosya taramasına izin verseniz, ancak yalnızca birkaç dosyayı hedefe kopyalayabiliyorsanız, dosya taramanın de uzun süre boyunca zaman harcadığını unutmayın.   

> [!NOTE]
> Azure Data Factory kullanmaya yeni başlıyorsanız bkz. [Azure Data Factory'ye giriş](introduction.md).

Bu öğreticide, aşağıdaki görevleri yerine getirmeniz gerekir:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Veri Kopyalama aracını kullanarak bir işlem hattı oluşturun.
> * İşlem hattı ve etkinlik çalıştırmalarını izleme.

## <a name="prerequisites"></a>Ön koşullar

* **Azure aboneliği**: Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* **Azure depolama hesabı**: BLOB depolama alanını _kaynak_ ve _Havuz_ veri deposu olarak kullanın. Azure depolama hesabınız yoksa [Depolama hesabı oluşturma](../storage/common/storage-quickstart-create-account.md) bölümündeki yönergelere bakın.

### <a name="create-two-containers-in-blob-storage"></a>Blob depolamada iki kapsayıcı oluşturma

Bu adımları uygulayarak BLOB depolama alanınızı öğreticiye hazırlayın.

1. **Kaynak**adlı bir kapsayıcı oluşturun. Bu görevi gerçekleştirmek için [Azure Depolama Gezgini](https://storageexplorer.com/)gibi çeşitli araçları kullanabilirsiniz.

2. **Hedef**adlı bir kapsayıcı oluşturun. 

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. Sol taraftaki menüden > **kaynak oluştur** ' u seçin **veri ve analiz** > **Data Factory**: 
   
   ![“Yeni” bölmesinde Data Factory seçimi](./media/doc-common-process/new-azure-data-factory-menu.png)

2. **Yeni veri fabrikası** sayfasında **Ad** bölümüne **ADFTutorialDataFactory** girin. 
 
   Veri fabrikanızın adı _genel olarak benzersiz_ olmalıdır. Aşağıdaki hata iletisini alabilirsiniz:
   
   ![Yeni veri fabrikası hata iletisi](./media/doc-common-process/name-not-available-error.png)

   Ad değeriyle ilgili bir hata iletisi alırsanız, veri fabrikası için farklı bir ad girin. Örneğin, _**adınız**_ **ADFTutorialDataFactory** adını kullanın. Data Factory yapıtlarını adlandırma kuralları için bkz. [Data Factory adlandırma kuralları](naming-rules.md).
3. Yeni veri fabrikasını oluşturacağınız Azure **aboneliğini** seçin. 
4. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:
     
    * **Var olanı kullan**’ı seçin ve ardından açılır listeden var olan bir kaynak grubu belirleyin.

    * **Yeni oluştur**’u seçin ve bir kaynak grubunun adını girin. 
         
    Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/resource-group-overview.md).

5. **Sürüm**altında **v2**' yi seçin.
6. **Konum** bölümünde veri fabrikasının konumunu seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Veri fabrikanızın kullandığı veri depoları (örneğin, Azure depolama ve SQL veritabanı) ve işlemler (örneğin, Azure HDInsight) başka konumlarda ve bölgelerde olabilir.
7. **Panoya sabitle**’yi seçin. 
8. **Oluştur**'u seçin.
9. Panoda, işlem durumunu görmek için **dağıtma Data Factory** kutucuğuna bakın.

    ![Data Factory kutucuğu dağıtılıyor](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Oluşturma işlemi tamamlandıktan sonra **Data Factory** giriş sayfası görüntülenir.
   
    ![Data factory giriş sayfası](./media/doc-common-process/data-factory-home-page.png)
11. Azure Data Factory Kullanıcı arabirimini (UI) ayrı bir sekmede açmak için **yazar & İzleyici** kutucuğunu seçin. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Veri Kopyalama aracını kullanarak işlem hattı oluşturma

1. **Haydi** başlayın sayfasında, veri kopyalama aracını açmak için **veri kopyalama** başlığını seçin. 

   ![Veri Kopyalama aracının kutucuğu](./media/doc-common-process/get-started-page.png)
   
2. **Özellikler** sayfasında, aşağıdaki adımları uygulayın:

    a. **Görev adı**altında **Deltacopyfromblobpipeline**girin.

    b. **Görev temposunda** veya **görev zamanlaması**altında **zamanlamaya göre düzenli olarak çalıştır**' ı seçin.

    c. **Tetikleyici türü**altında, atlayan **pencere**' yi seçin.
    
    d. **Yinelenme**altına **15 dakika**girin. 
    
    e. **İleri**’yi seçin. 
    
    Data Factory kullanıcı arabirimi, belirtilen görev adına sahip bir işlem hattı oluşturur. 

    ![Özellikler sayfası](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. **Kaynak veri deposu** sayfasında aşağıdaki adımları tamamlayın:

    a. Bağlantı eklemek için **+ Yeni bağlantı oluştur**' u seçin.
    
    ![Kaynak veri deposu sayfası](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Galeriden **Azure Blob depolama** ' yı seçin ve ardından **devam**' ı seçin.
    
    ![Kaynak veri deposu sayfası](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. **Yeni bağlı hizmet** sayfasında **depolama hesabı adı** listesinden depolama hesabınızı seçin ve ardından **son**' u seçin.
    
    ![Kaynak veri deposu sayfası](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Yeni oluşturulan bağlı hizmeti seçin ve ardından **İleri**' yi seçin. 
    
   ![Kaynak veri deposu sayfası](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. **Girdi dosyasını veya klasörünü seçin** sayfasında aşağıdaki adımları uygulayın:
    
    a. **Kaynak** klasöre gidip seçin ve **ardından Seç ' i seçin.**
    
    ![Girdi dosyası veya klasörü seçin](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. **Dosya yükleme davranışı**altında **artımlı yük: LastModifiedDate**öğesini seçin.
    
    ![Girdi dosyası veya klasörü seçin](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. **İkili kopyayı** denetleyin ve **İleri ' yi**seçin.
    
     ![Girdi dosyası veya klasörü seçin](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. **Hedef veri deposu** sayfasında **AzureBlobStorage**' yi seçin. Bu, kaynak veri deposuyla aynı depolama hesabıdır. Sonra **İleri**’yi seçin.

    ![Hedef veri deposu sayfası](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. **Çıktı dosyasını veya klasörünü seçin** sayfasında aşağıdaki adımları uygulayın:
    
    a. **Hedef** klasöre gözatıp seçin ve ardından Seç ' i **seçin.**
    
    ![Çıktı dosyasını veya klasörünü seçin](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. **İleri**’yi seçin.
    
     ![Çıktı dosyasını veya klasörünü seçin](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. **Ayarlar** sayfasında **İleri**’yi seçin. 

    ![Ayarlar sayfası](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. **Özet** sayfasında, ayarları gözden geçirin ve ardından **İleri**' yi seçin.

    ![Özet sayfası](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. **Dağıtım** sayfasında, işlem hattını (görev) izlemek için **İzleyici**’yi seçin.

    ![Dağıtım sayfası](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Soldaki **İzleyici** sekmesinin otomatik olarak seçildiğine dikkat edin. **Eylemler** sütunu, etkinlik çalıştırması ayrıntılarını görüntüleme ve işlem hattını yeniden çalıştırma bağlantılarını içerir. Listeyi yenilemek için **Yenile** ' yi seçin ve **Eylemler** sütunundaki **etkinlik çalıştırmalarını görüntüle** bağlantısını seçin. 

    ![Listeyi yenile ve etkinlik çalıştırmalarını görüntüle seçeneğini belirleyin](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. İşlem hattında yalnızca bir etkinlik (kopyalama etkinliği) bulunur, bu nedenle yalnızca bir giriş görürsünüz. Kopyalama işlemiyle ilgili ayrıntılar için **Eylemler** sütunundaki **Ayrıntılar** bağlantısını (gözlük simgesi) seçin. 

    ![Kopyalama etkinliği ardışık düzen içinde](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    BLOB depolama hesabınızda **kaynak** kapsayıcıda bir dosya olmadığından, BLOB depolama hesabınızda **hedef** kapsayıcıya bir dosya kopyalamadığını göremezsiniz.
    
    ![Kaynak kapsayıcısında veya hedef kapsayıcıda dosya yok](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Boş bir metin dosyası oluşturun ve **FILE1. txt**olarak adlandırın. Bu metin dosyasını Depolama hesabınızdaki **kaynak** kapsayıcıya yükleyin. Bu görevleri [Azure Depolama Gezgini](https://storageexplorer.com/) gibi çeşitli araçlar kullanarak gerçekleştirebilirsiniz.   

    ![FILE1. txt oluşturun ve kaynak kapsayıcısına yükleyin](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. İşlem hattı **çalıştırmaları** görünümüne geri dönmek için **tüm işlem hattı çalıştırmaları**' nı seçin ve aynı işlem hattının otomatik olarak yeniden tetiklenmesi için bekleyin.  

    ![Tüm işlem hattı çalıştırmalarını seçin](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Gördüğünüz ikinci işlem hattı için **etkinlik çalıştırmasını görüntüle** ' yi seçin. Ardından, ayrıntıları ilk işlem hattı çalıştırmasıyla aynı şekilde gözden geçirin.  

    ![Etkinlik çalıştırmayı görüntüle ve ayrıntıları gözden geçir ' i seçin](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    **Kaynak** kapsayıcıdan BLOB depolama hesabınızın **hedef** kapsayıcısına bir dosya (FILE1. txt) kopyalandığını görürsünüz.
    
    ![FILE1. txt, kaynak kapsayıcıdan hedef kapsayıcıya kopyalanmış](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Başka bir boş metin dosyası oluşturun ve **dosya2. txt**olarak adlandırın. Bu metin dosyasını Blob Depolama hesabınızdaki **kaynak** kapsayıcıya yükleyin.   
    
16. Bu ikinci metin dosyası için 13. ve 14. adımları yineleyin. **Kaynak** kapsayıcıdan, bir sonraki işlem hattı çalıştırmasında depolama hesabınızın **hedef** kapsayıcısına yalnızca yeni dosyanın (dosya2. txt) kopyalandığını görürsünüz.  
    
    ![Dosya2. txt, kaynak kapsayıcıdan hedef kapsayıcıya kopyalanmış](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    Ayrıca, dosyaları taramak için [Azure Depolama Gezgini](https://storageexplorer.com/) kullanarak da doğrulayabilirsiniz.
    
    ![Azure Depolama Gezgini kullanarak dosyaları tarama](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Sonraki adımlar
Azure 'da Apache Spark kümesi kullanarak verileri dönüştürme hakkında bilgi edinmek için aşağıdaki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
>[Apache Spark kümesi kullanarak buluttaki verileri dönüştürme](tutorial-transform-data-spark-portal.md)