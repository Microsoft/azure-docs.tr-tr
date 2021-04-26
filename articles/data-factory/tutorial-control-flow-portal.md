---
title: Azure portal kullanarak bir işlem hattındaki dallanma ve zincirleme etkinlikleri
description: Azure portal kullanarak Azure Data Factory işlem hattının veri akışını denetlemeyi öğrenin.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/11/2018
ms.openlocfilehash: 034b0400190cadd750e7420ab37800783afa050c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783430"
---
# <a name="branching-and-chaining-activities-in-an-azure-data-factory-pipeline-using-the-azure-portal"></a>Azure portal kullanarak Azure Data Factory işlem hattında dallanan ve zincirleme etkinlikleri

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu öğreticide, bazı denetim akışı özelliklerini gösteren bir Data Factory işlem hattı oluşturacaksınız. Bu işlem hattı, Azure Blob Depolama içindeki kapsayıcıdan aynı depolama hesabındaki başka bir kapsayıcıya basit bir kopyalama işlemi yapar. Kopyalama etkinliği başarılı olursa, işlem hattı başarılı kopyalama işleminin ayrıntılarını (örneğin, yazılan veri miktarı) bir başarı e-postası ile gönderir. Kopyalama etkinliği başarısız olursa, işlem hattı kopyalama hatasının ayrıntılarını (örneğin, hata iletisi) bir hata e-postası ile gönderir. Öğretici boyunca parametreleri nasıl geçireceğinizi göreceksiniz.

Senaryoya ilişkin üst düzey bir genel bakış: ![ diyagramda, bir kopyanın hedefi olan Azure Blob depolama alanı gösterilir. Bu, başarı durumunda, ayrıntıları içeren bir e-posta gönderir veya hata durumunda hata ayrıntıları içeren bir e-posta gönderir.](media/tutorial-control-flow-portal/overview.png)

Bu öğreticide aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Azure Depolama bağlı hizmeti oluşturma.
> * Azure blob veri kümesi oluşturma
> * Kopyalama etkinliği ve bir Web etkinliği içeren işlem hattı oluşturma
> * Etkinliklerin çıktılarını sonraki etkinliklere gönderme
> * Parametre geçirme ve sistem değişkenlerini kullanma
> * Bir işlem hattı çalıştırması başlatma
> * İşlem hattı ve etkinlik çalıştırmalarını izleme

Bu öğreticide Azure portalı kullanılır. Azure Data Factory ile etkileşim kurmak için başka mekanizmalar kullanabilirsiniz; içindekiler tablosunda "Hızlı Başlangıçlar" bölümüne bakın.

## <a name="prerequisites"></a>Önkoşullar

* **Azure aboneliği**. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.
* **Azure depolama hesabı**. Blob depolama alanını **kaynak** veri deposu olarak kullanabilirsiniz. Azure depolama hesabınız yoksa, oluşturma adımları için [Depolama hesabı oluşturma](../storage/common/storage-account-create.md) makalesine bakın.
* **Azure SQL veritabanı**. Veritabanını **havuz** veri deposu olarak kullanabilirsiniz. Azure SQL veritabanında bir veritabanınız yoksa, oluşturma adımları için [Azure SQL veritabanı 'nda veritabanı oluşturma](../azure-sql/database/single-database-create-quickstart.md) makalesine bakın.

### <a name="create-blob-table"></a>Blob tablosu oluşturma

1. Not Defteri'ni başlatın. Aşağıdaki metni kopyalayıp diskinizde **input.txt** dosyası olarak kaydedin.

    ```
    John,Doe
    Jane,Doe
    ```
2. [Azure Depolama Gezgini](https://storageexplorer.com/) gibi araçları kullanarak aşağıdaki adımları uygulayın:
    1. **adfv2branch** kapsayıcısını oluşturun.
    2. **adfv2branch** kapsayıcısında **giriş** klasörünü oluşturun.
    3. **input.txt** dosyasını kapsayıcıya yükleyin.

## <a name="create-email-workflow-endpoints"></a>E-posta iş akışı uç noktaları oluşturma
İşlem hattından e-posta göndermeyi tetiklemek için [Logic Apps](../logic-apps/logic-apps-overview.md) kullanarak iş akışını tanımlarsınız. Mantıksal Uygulama iş akışı oluşturma ayrıntıları için bkz. [Mantıksal uygulama oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md).

### <a name="success-email-workflow"></a>Başarı e-postası iş akışı
`CopySuccessEmail` adlı bir Mantıksal Uygulama iş akışı oluşturun. İş akışı tetikleyicisini `When an HTTP request is received` olarak tanımlayın ve bir `Office 365 Outlook – Send an email` eylemi ekleyin.

![Başarı e-postası iş akışı](media/tutorial-control-flow-portal/success-email-workflow.png)

İstek tetikleyiciniz için `Request Body JSON Schema` alanını aşağıdaki JSON ile doldurun:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

Mantıksal Uygulama Tasarımcısı’nda İstek aşağıdaki gibi görünmelidir:

![Mantıksal Uygulama tasarımcısı - istek](media/tutorial-control-flow-portal/logic-app-designer-request.png)

**E-posta Gönder** eylemi için, isteğin Gövde JSON şemasında geçirilen özellikleri kullanarak e-posta biçimini özelleştirin. Aşağıda bir örnek verilmiştir:

![Mantıksal Uygulama tasarımcısı - e-posta gönderme eylemi](media/tutorial-control-flow-portal/send-email-action-2.png)

İş akışını kaydedin. Başarı e-postası iş akışınız için HTTP Post isteği URL’sini not alın:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

### <a name="fail-email-workflow"></a>Hata e-postası iş akışı
Aynı adımları izleyerek başka bir **CopyFailEmail** Logic Apps iş akışı oluşturun. İstek tetikleyicisinde `Request Body JSON schema` değeri aynıdır. Hata e-postasına uyarlamak için e-postanızın biçimini `Subject` olarak değiştirin. Aşağıda bir örnek verilmiştir:

![Mantıksal Uygulama tasarımcısı - hata e-postası iş akışı](media/tutorial-control-flow-portal/fail-email-workflow-2.png)

İş akışını kaydedin. Hata e-postası iş akışınız için HTTP Post isteği URL’sini not alın:

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

Şu anda iki iş akışı URL'niz olmalıdır:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. **Microsoft Edge** veya **Google Chrome** web tarayıcısını açın. Şu anda Data Factory kullanıcı arabirimi yalnızca Microsoft Edge ve Google Chrome web tarayıcılarında desteklenmektedir.
1. Sol taraftaki menüden veri ve analiz **kaynak oluştur**' u seçin  >    >  **Data Factory**:

   ![“Yeni” bölmesinde Data Factory seçimi](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. **Yeni veri fabrikası** sayfasında **ad** için **ADFTutorialDataFactory** adını girin.

     ![Yeni veri fabrikası sayfası](./media/tutorial-control-flow-portal/new-azure-data-factory.png)

   Azure veri fabrikasının adı **genel olarak benzersiz** olmalıdır. Aşağıdaki hatayı alırsanız veri fabrikasının adını değiştirin (örneğin adınızADFTutorialDataFactory) ve oluşturmayı yeniden deneyin. Data Factory yapıtlarını adlandırma kuralları için [Data Factory - Adlandırma Kuralları](naming-rules.md) makalesine bakın.

   *"ADFTutorialDataFactory" Veri Fabrikası adı kullanılamıyor.*

3. Veri fabrikasını oluşturmak istediğiniz Azure **aboneliğini** seçin.
4. **Kaynak grubu** için aşağıdaki adımlardan birini yapın:

      - **Var olanı kullan**’ı seçin ve ardından açılır listeden var olan bir kaynak grubu belirleyin.
      - **Yeni oluştur**’u seçin ve bir kaynak grubunun adını girin.   
         
        Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/management/overview.md).  
4. **Sürüm** için **V2**'yi seçin.
5. Data factory için **konum** seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Veri fabrikası tarafından kullanılan verileri depoları (Azure Depolama, Azure SQL Veritabanı vb.) ve işlemler (HDInsight vb.) başka bölgelerde olabilir.
6. **Panoya sabitle**’yi seçin.     
7. **Oluştur**’a tıklayın.      
8. Panoda şu kutucuğu ve üzerinde şu durumu görürsünüz: **Veri fabrikası dağıtılıyor**.

    ![veri fabrikası dağıtılıyor kutucuğu](media/tutorial-control-flow-portal/deploying-data-factory.png)
9. Oluşturma işlemi tamamlandıktan sonra, resimde gösterildiği gibi **Data Factory** sayfasını görürsünüz.

   ![Data factory giriş sayfası](./media/tutorial-control-flow-portal/data-factory-home-page.png)
10. Azure Data Factory kullanıcı arabirimini (UI) ayrı bir sekmede açmak için **Geliştir ve İzle** kutucuğuna tıklayın.


## <a name="create-a-pipeline"></a>İşlem hattı oluşturma
Bu adımda, bir Kopyalama etkinliği ve iki Web etkinliği ile bir işlem hattı oluşturursunuz. İşlem hattını oluşturmak için aşağıdaki özellikleri kullanırsınız:

- Veri kümeleri tarafından erişilen işlem hattına yönelik parametreler.
- Başarı/hata e-postaları göndermek için Logic Apps iş akışlarını çağıracak web etkinliği.
- Bir etkinliği başka bir etkinliğe bağlama (başarı veya hata durumunda)
- Bir etkinliğin çıktısını sonraki etkinliğin girdisi olarak kullanma

1. Data Factory kullanıcı arabiriminin **başlarken** sayfasında **İşlem hattı oluştur** kutucuğuna tıklayın.  

   ![Başlarken sayfası](./media/tutorial-control-flow-portal/get-started-page.png)
3. İşlem hattının özellikler penceresinde **Parametreler** sekmesine geçin ve **Yeni** düğmesini kullanarak String türündeki şu üç parametreyi ekleyin: sourceBlobContainer, sinkBlobContainer ve receiver.

    - **Sourceblobcontainer** -işlem hattında kaynak blob veri kümesi tarafından tüketilen parametre.
    - **Sinkblobcontainer** – işlem hattında havuz blob veri kümesi tarafından tüketilen parametre
    - **receiver** – bu parametre, işlem hattında e-posta adresi bu parametre ile belirtilen alıcıya başarı veya hata e-postaları gönderen iki Web etkinliği tarafından kullanılır.

   ![Yeni işlem hattı menüsü](./media/tutorial-control-flow-portal/pipeline-parameters.png)
4. **Etkinlikler** araç kutusunda **Veri Akışı**’nı genişletin ve **Kopyalama** etkinliğini sürükleyerek işlem hattı tasarımcısının yüzeyine bırakın.

   ![Kopyalama etkinliğini sürükleyip bırakma](./media/tutorial-control-flow-portal/drag-drop-copy-activity.png)
5. Alttaki **Kopyalama** etkinliğinin **Özellikler** penceresinde **Kaynak** sekmesine geçip **+ Yeni**’ye tıklayın. Bu adımda kopyalama etkinliği için bir kaynak veri kümesi oluşturursunuz.

   ![Kopyalama etkinliği için kaynak veri kümesi oluşturmayı gösteren ekran görüntüsü.](./media/tutorial-control-flow-portal/new-source-dataset-button.png)
6. **Yeni Veri Kümesi** penceresinde **Azure Blob Depolama Alanı**’nı seçip **Son**’a tıklayın.

   ![Azure Blob Depolama’yı seçin](./media/tutorial-control-flow-portal/select-azure-blob-storage.png)
7. **AzureBlob1** başlıklı yeni bir **sekme** görürsünüz. Veri kümesinin adını **SourceBlobDataset** olarak değiştirin.

   ![Veri kümesi genel ayarları](./media/tutorial-control-flow-portal/dataset-general-page.png)
8. **Özellikler** penceresinde **Bağlantı** sekmesine geçin ve **Bağlı hizmet** için Yeni’ye tıklayın. Bu adımda, Azure depolama hesabınızı veri fabrikasına bağlamak için bağlı bir hizmet oluşturursunuz.

   ![Veri kümesi bağlantısı - yeni bağlı hizmet](./media/tutorial-control-flow-portal/dataset-connection-new-button.png)
9. **Yeni Bağlı Hizmet** penceresinde aşağıdaki adımları izleyin:

    1. **Ad** için **AzureStorageLinkedService** adını girin.
    2. **Depolama hesabı adı** için Azure depolama hesabınızı seçin.
    3. **Kaydet**’e tıklayın.

   ![Yeni Azure Depolama bağlı hizmeti](./media/tutorial-control-flow-portal/new-azure-storage-linked-service.png)
12. Klasör için `@pipeline().parameters.sourceBlobContainer`, klasör adı için `emp.txt` adını girin. Veri kümesinin klasör yolunu ayarlamak için sourceBlobContainer işlem hattı parametresini kullanırsınız.

    ![Kaynak veri kümesi ayarları](./media/tutorial-control-flow-portal/source-dataset-settings.png)

13. **İşlem hattı** sekmesine geçin (veya) ağaç görünümünde işlem hattına tıklayın. **Kaynak Veri Kümesi** için **SourceBlobDataset**’in seçili olduğundan emin olun.
      
   ![Kaynak veri kümesi](./media/tutorial-control-flow-portal/pipeline-source-dataset-selected.png)

13. Özellikler penceresinde **Havuz** sekmesine geçin ve **Havuz Veri Kümesi** için **+ Yeni**’ye tıklayın. Bu adımda, kaynak veri kümesi oluştururken olduğu gibi kopyalama etkinliği için bir havuz veri kümesi oluşturursunuz.

    ![Yeni havuz veri kümesi düğmesi](./media/tutorial-control-flow-portal/new-sink-dataset-button.png)
14. **Yeni Veri Kümesi** penceresinde **Azure Blob Depolama Alanı**’nı seçip **Son**’a tıklayın.
15. Veri kümesinin **Genel** ayarlar sayfasında **Ad** için **SinkBlobDataset** adını girin.
16. **Bağlantı** sekmesine geçin ve aşağıdaki adımları uygulayın:

    1. **LinkedService** için **AzureStorageLinkedService** seçeneğini belirleyin.
    2. Klasör için `@pipeline().parameters.sinkBlobContainer` ifadesini girin.
    1. Dosya adı olarak `@CONCAT(pipeline().RunId, '.txt')` girin. İfade, dosya adı olarak geçerli işlem hattı çalıştırmasının kimliğini kullanır. Desteklenen sistem değişkenleri ve ifadelerin listesi için bkz. [Sistem değişkenleri](control-flow-system-variables.md) ve [İfade dili](control-flow-expression-language-functions.md).

        ![Havuz veri kümesi ayarları](./media/tutorial-control-flow-portal/sink-dataset-settings.png)
17. Üst taraftan **işlem hattı** sekmesine geçin. **Etkinlikler** araç kutusunda **Genel**’i genişletin ve bir **Web** etkinliğini sürükleyerek işlem hattı tasarımcısının yüzeyine bırakın. Etkinliğin adını **SendSuccessEmailActivity** olarak ayarlayın. Web Etkinliği herhangi bir REST uç noktasına çağrı yapmaya olanak tanır. Etkinlik hakkında daha fazla bilgi için bkz. [Web Etkinliği](control-flow-web-activity.md). Bu işlem hattı, Logic Apps e-posta iş akışını çağırmak için bir Web Etkinliği kullanır.

    ![İlk Web etkinliğini sürükleyip bırakın](./media/tutorial-control-flow-portal/success-web-activity-general.png)
18. **Genel** sekmesinden **Ayarlar** sekmesine geçin ve aşağıdaki adımları uygulayın:
    1. **URL** için başarı e-postasını gönderen Logic Apps iş akışının URL’sini belirtin.  
    2. **Metot** için **POST**’u seçin.
    3. **Üst bilgiler** bölümünde **+ Üst bilgi ekle** bağlantısına tıklayın.
    4. Bir üst bilgi **Content-Type** ekleyin ve **application/json** olarak ayarlayın.
    5. **Gövde** için aşağıdaki JSON’u belirtin.

        ```json
        {
            "message": "@{activity('Copy1').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```
        İleti gövdesi aşağıdaki özellikleri içerir:

       - İleti – `@{activity('Copy1').output.dataWritten` değerini geçirme. Önceki kopyalama etkinliğinin bir özelliğine erişir ve dataWritten değerini geçirir. Hata durumunda `@{activity('CopyBlobtoBlob').error.message` yerine hata çıktısını geçirir.
       - Veri Fabrikası Adı – `@{pipeline().DataFactory}` değerini geçiren ve ilgili veri fabrikası adına erişmenize olanak tanıyan bir sistem değişkenidir. Sistem değişkenlerinin listesi için [Sistem Değişkenleri](control-flow-system-variables.md) makalesine bakın.
       - İşlem Hattı Adı – `@{pipeline().Pipeline}` değerini geçirme. Bu da ilgili işlem hattı adına erişmenize olanak tanıyan bir sistem değişkenidir.
       - Alıcı – "\@pipeline().parameters.receiver") değerini geçirme. İşlem hattı parametrelerine erişim.

         ![İlk Web etkinliği için ayarlar](./media/tutorial-control-flow-portal/web-activity1-settings.png)         
19. Kopyalama etkinliğinin yanındaki yeşil düğmeyi sürükleyip Web etkinliğinin üzerine bırakarak **Kopyala** etkinliğini **Web** etkinliğine bağlayın.

    ![Kopyalama etkinliğini ilk Web etkinliğine bağlama](./media/tutorial-control-flow-portal/connect-copy-web-activity1.png)
20. Etkinlikler araç kutusundaki başka bir **Web** etkinliğini sürükleyip işlem hattı tasarımcısının yüzeyine bırakın ve **ad** alanını **SendFailureEmailActivity** olarak ayarlayın.

    ![İkinci Web etkinliğinin adı](./media/tutorial-control-flow-portal/web-activity2-name.png)
21. **Ayarlar** sekmesine geçin ve aşağıdaki adımları uygulayın:

    1. **URL** için hata e-postasını gönderen Logic Apps iş akışının URL’sini belirtin.  
    2. **Metot** için **POST**’u seçin.
    3. **Üst bilgiler** bölümünde **+ Üst bilgi ekle** bağlantısına tıklayın.
    4. Bir üst bilgi **Content-Type** ekleyin ve **application/json** olarak ayarlayın.
    5. **Gövde** için aşağıdaki JSON’u belirtin.

        ```json
        {
            "message": "@{activity('Copy1').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```

        ![İkinci Web etkinliği için ayarlar](./media/tutorial-control-flow-portal/web-activity2-settings.png)         
22. İşlem hattı tasarımcısında **Kopyala** etkinliğini seçip **+->** düğmesine tıklayın ve **Hata**’yı seçin.  

    ![İşlem hattı tasarımcısında kopyalama etkinliğinde nasıl hata seçdiğinin gösterildiği ekran görüntüsü.](./media/tutorial-control-flow-portal/select-copy-failure-link.png)
23. Kopyala etkinliğinin yanındaki **kırmızı** düğmeyi **SendFailureEmailActivity** adlı ikinci Web etkinliğine sürükleyin. Etkinlikleri işlem hattının aşağıdaki gibi görüneceği şekilde taşıyabilirsiniz:

    ![Tüm etkinlikleri içeren tam işlem hattı](./media/tutorial-control-flow-portal/full-pipeline.png)
24. İşlem hattını doğrulamak için araç çubuğundaki **Doğrula** düğmesine tıklayın. **>>** düğmesine tıklayarak **İşlem Hattı Doğrulama Çıktı** penceresini kapatın.

    ![İşlem hattını doğrulama](./media/tutorial-control-flow-portal/validate-pipeline.png)
24. Varlıkları (veri kümeleri, işlem hatları, vb.) Data Factory hizmetinde yayımlamak için **Tümünü Yayımla**’yı seçin. **Başarıyla yayımlandı** iletisini görene kadar bekleyin.

    ![Yayımlama](./media/tutorial-control-flow-portal/publish-button.png)

## <a name="trigger-a-pipeline-run-that-succeeds"></a>Başarılı olan bir işlem hattı çalıştırması tetikleme
1. Bir işlem hattı çalıştırması **tetiklemek** için araç çubuğunda **Tetikle** ' e tıklayın ve **Şimdi Tetikle**' ye tıklayın.

    ![İşlem hattı çalıştırmasını tetikleme](./media/tutorial-control-flow-portal/trigger-now-menu.png)
2. **İşlem Hattı Çalıştırması** penceresinde aşağıdaki adımları uygulayın:

    1. **sourceBlobContainer** parametresi için **adftutorial/adfv2branch/input** yolunu girin.
    2. **sinkBlobContainer** parametresi için **adftutorial/adfv2branch/output** yolunu girin.
    3. **Receiver** parametresine ait bir **e-posta adresi** girin.
    4. **Son** ' a tıklayın

        ![İşlem hattı çalıştırma parametreleri](./media/tutorial-control-flow-portal/pipeline-run-parameters.png)

## <a name="monitor-the-successful-pipeline-run"></a>Başarılı işlem hattı çalıştırmasını izleme

1. İşlem hattı çalıştırmasını izlemek için soldaki **İzleyici** sekmesine geçin. El ile tetiklediğiniz işlem hattı çalıştırmasını görürsünüz. Listeyi yenilemek için **Yenile** düğmesini kullanın.

    ![Başarılı işlem hattı çalıştırması](./media/tutorial-control-flow-portal/monitor-success-pipeline-run.png)
2. Bu işlem hattıyla ilişkili **etkinlik çalıştırmalarını görüntülemek** için **Eylemler** sütunundaki ilk bağlantıya tıklayın. Üst taraftan **İşlem Hatları**’na tıklayarak önceki görünüme dönebilirsiniz. Listeyi yenilemek için **Yenile** düğmesini kullanın.

    ![Etkinlik çalıştırmalarının listesinin nasıl görüntüleneceğini gösteren ekran görüntüsü.](./media/tutorial-control-flow-portal/activity-runs-success.png)

## <a name="trigger-a-pipeline-run-that-fails"></a>Başarısız olan bir işlem hattı çalıştırması tetikleme
1. Soldaki **Düzenle** sekmesine geçin.
2. Bir işlem hattı çalıştırması **tetiklemek** için araç çubuğunda **Tetikle** ' e tıklayın ve **Şimdi Tetikle**' ye tıklayın.
3. **İşlem Hattı Çalıştırması** penceresinde aşağıdaki adımları uygulayın:

    1. **sourceBlobContainer** parametresi için **adftutorial/dummy/input** yolunu girin. Sahte klasörün adftutorial kapsayıcısında bulunmadığından emin olun.
    2. **sinkBlobContainer** parametresi için **adftutorial/dummy/output** yolunu girin.
    3. **Receiver** parametresine ait bir **e-posta adresi** girin.
    4. **Finish (Son)** düğmesine tıklayın.

## <a name="monitor-the-failed-pipeline-run"></a>Başarısız olan işlem hattı çalıştırmasını izleme

1. İşlem hattı çalıştırmasını izlemek için soldaki **İzleyici** sekmesine geçin. El ile tetiklediğiniz işlem hattı çalıştırmasını görürsünüz. Listeyi yenilemek için **Yenile** düğmesini kullanın.

    ![İşlem hattı çalıştırılamadı](./media/tutorial-control-flow-portal/monitor-failure-pipeline-run.png)
2. Hatayla ilgili ayrıntıları görmek için işlem hattı çalıştırmasına yönelik **Hata** bağlantısına tıklayın.

    ![İşlem hattı hatası](./media/tutorial-control-flow-portal/pipeline-error-message.png)
2. Bu işlem hattıyla ilişkili **etkinlik çalıştırmalarını görüntülemek** için **Eylemler** sütunundaki ilk bağlantıya tıklayın. Listeyi yenilemek için **Yenile** düğmesini kullanın. İşlem hattında Kopyalama etkinliğinin başarısız olduğuna dikkat edin. Web etkinliği, hata e-postasını belirtilen alıcıya gönderdi.

    ![Etkinlik çalıştırmaları](./media/tutorial-control-flow-portal/activity-runs-failure.png)
4. Hatayla ilgili ayrıntıları görmek için **Eylemler** sütunundaki **Hata** bağlantısına tıklayın.

    ![Etkinlik çalıştırma hatası](./media/tutorial-control-flow-portal/activity-run-error.png)

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide aşağıdaki adımları gerçekleştirdiniz:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Azure Depolama bağlı hizmeti oluşturma.
> * Azure blob veri kümesi oluşturma
> * Kopyalama etkinliği ve bir web etkinliği içeren işlem hattı oluşturma
> * Etkinliklerin çıktılarını sonraki etkinliklere gönderme
> * Parametre geçirme ve sistem değişkenlerini kullanma
> * Bir işlem hattı çalıştırması başlatma
> * İşlem hattı ve etkinlik çalıştırmalarını izleme

Şimdi, Azure Data Factory hakkında daha fazla bilgi için Kavramlar bölümüne geçebilirsiniz.
> [!div class="nextstepaction"]
>[İşlem hatları ve etkinlikler](concepts-pipelines-activities.md)
